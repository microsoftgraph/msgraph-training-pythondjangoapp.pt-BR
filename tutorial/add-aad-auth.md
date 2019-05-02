<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você estenderá o aplicativo do exercício anterior para oferecer suporte à autenticação com o Azure AD. Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph. Nesta etapa, você integrará a biblioteca de [solicitações-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) ao aplicativo.

Crie um novo arquivo na raiz do projeto chamado `oauth_settings.yml`e adicione o seguinte conteúdo.

```text
app_id: YOUR_APP_ID_HERE
app_secret: YOUR_APP_PASSWORD_HERE
redirect: http://localhost:8000/tutorial/callback
scopes: openid profile offline_access user.read calendars.read
authority: https://login.microsoftonline.com/common
authorize_endpoint: /oauth2/v2.0/authorize
token_endpoint: /oauth2/v2.0/token
```

Substitua `YOUR_APP_ID_HERE` pela ID do aplicativo do portal de registro do aplicativo e substitua `YOUR_APP_SECRET_HERE` pela senha gerada.

> [!IMPORTANT]
> Se você estiver usando o controle de origem como o Git, agora seria uma boa hora para excluir `oauth_settings.yml` o arquivo do controle de origem para evitar vazar inadvertidamente sua ID de aplicativo e sua senha.

## <a name="implement-sign-in"></a>Implementar logon

Crie um novo arquivo no `./tutorial` diretório chamado `auth_helper.py` e adicione o código a seguir.

```python
import yaml
from requests_oauthlib import OAuth2Session
import os
import time

# This is necessary for testing with non-HTTPS localhost
# Remove this if deploying to production
os.environ['OAUTHLIB_INSECURE_TRANSPORT'] = '1'

# This is necessary because Azure does not guarantee
# to return scopes in the same case and order as requested
os.environ['OAUTHLIB_RELAX_TOKEN_SCOPE'] = '1'
os.environ['OAUTHLIB_IGNORE_SCOPE_CHANGE'] = '1'

# Load the oauth_settings.yml file
stream = open('oauth_settings.yml', 'r')
settings = yaml.load(stream)
authorize_url = '{0}{1}'.format(settings['authority'], settings['authorize_endpoint'])
token_url = '{0}{1}'.format(settings['authority'], settings['token_endpoint'])

# Method to generate a sign-in url
def get_sign_in_url():
  # Initialize the OAuth client
  aad_auth = OAuth2Session(settings['app_id'],
    scope=settings['scopes'],
    redirect_uri=settings['redirect'])

  sign_in_url, state = aad_auth.authorization_url(authorize_url, prompt='login')

  return sign_in_url, state

# Method to exchange auth code for access token
def get_token_from_code(callback_url, expected_state):
  # Initialize the OAuth client
  aad_auth = OAuth2Session(settings['app_id'],
    state=expected_state,
    scope=settings['scopes'],
    redirect_uri=settings['redirect'])

  token = aad_auth.fetch_token(token_url,
    client_secret = settings['app_secret'],
    authorization_response=callback_url)

  return token
```

Este arquivo manterá todos os métodos relacionados à autenticação. O `get_sign_in_url` gera uma URL de autorização e o `get_token_from_code` método troca a resposta de autorização para um token de acesso.

Adicione as seguintes `import` instruções à parte superior de `./tutorial/views.py`.

```python
from django.urls import reverse
from tutorial.auth_helper import get_sign_in_url, get_token_from_code
```

Agora, adicione alguns novos modos de exibição no `./tutorial/views.py` arquivo.

```python
def sign_in(request):
  # Get the sign-in URL
  sign_in_url, state = get_sign_in_url()
  # Save the expected state so we can validate in the callback
  request.session['auth_state'] = state
  # Redirect to the Azure sign-in page
  return HttpResponseRedirect(sign_in_url)

def callback(request):
  # Get the state saved in session
  expected_state = request.session.pop('auth_state', '')
  # Make the token request
  token = get_token_from_code(request.get_full_path(), expected_state)
  # Temporary! Save the response in an error so it's displayed
  request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(token) }
  return HttpResponseRedirect(reverse('home'))
```

Isso define dois novos modos de `signin` exibição `callback`: e.

A `signin` ação gera a URL de entrada do Azure AD, salva `state` o valor gerado pelo cliente OAuth e redireciona o navegador para a página de entrada do Azure AD.

A `callback` ação é onde o Azure redireciona após a conclusão da entrada. Essa ação garante que o `state` valor corresponda ao valor salvo e, em seguida, usa o código de autorização enviado pelo Azure para solicitar um token de acesso. Em seguida, ele redireciona de volta para a Home Page com o token de acesso no valor de erro temporário. Você o usará para verificar se a entrada está funcionando antes de prosseguir. Antes de testar, você precisa adicionar os modos de exibição `./tutorial/urls.py`ao.

```python
path('signin', views.sign_in, name='signin'),
path('callback', views.callback, name='callback'),
```

Substitua a `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` linha `./tutorial/templates/tutorial/home.html` pela seguinte.

```html
<a href="{% url 'signin' %}" class="btn btn-primary btn-large">Click here to sign in</a>
```

Substitua a `<a href="#" class="nav-link">Sign In</a>` linha `./tutorial/templates/tutorial/layout.html` pela seguinte.

```html
<a href="{% url 'signin' %}" class="nav-link">Sign In</a>
```

Inicie o servidor e navegue até `https://localhost:8000/tutorial`. Clique no botão entrar e você deverá ser redirecionado para `https://login.microsoftonline.com`o. Faça logon com sua conta da Microsoft e concorde com as permissões solicitadas. O navegador redireciona para o aplicativo, mostrando o token.

### <a name="get-user-details"></a>Obter detalhes do usuário

Crie um novo arquivo no `./tutorial` diretório chamado `graph_helper.py` e adicione o código a seguir.

```python
from requests_oauthlib import OAuth2Session

graph_url = 'https://graph.microsoft.com/v1.0'

def get_user(token):
  graph_client = OAuth2Session(token=token)
  # Send GET to /me
  user = graph_client.get('{0}/me'.format(graph_url))
  # Return the JSON result
  return user.json()
```

O `get_user` método faz uma solicitação get para o ponto de `/me` extremidade do Microsoft Graph para obter o perfil do usuário, usando o token de acesso adquirido anteriormente.

Atualize o `callback` método no `./tutorial/views.py` para obter o perfil do usuário do Microsoft Graph.

Primeiro, adicione a seguinte `import` instrução à parte superior do arquivo.

```python
from tutorial.graph_helper import get_user
```

Substitua o `callback` método pelo código a seguir.

```python
def callback(request):
  # Get the state saved in session
  expected_state = request.session.pop('auth_state', '')
  # Make the token request
  token = get_token_from_code(request.get_full_path(), expected_state)

  # Get the user's profile
  user = get_user(token)
  # Temporary! Save the response in an error so it's displayed
  request.session['flash_error'] = { 'message': 'Token retrieved',
    'debug': 'User: {0}\nToken: {1}'.format(user, token) }
  return HttpResponseRedirect(reverse('home'))
```

O novo código chama o `get_user` método para solicitar o perfil do usuário. Ele adiciona o objeto user à saída temporária para teste.

## <a name="storing-the-tokens"></a>Armazenar tokens

Agora que você pode obter tokens, é hora de implementar uma maneira de armazená-los no aplicativo. Como este é um aplicativo de exemplo, por questões de simplicidade, você os armazenará na sessão. Um aplicativo real usaria uma solução de armazenamento segura mais confiável, como um banco de dados.

Adicione os métodos novos a seguir `./tutorial/auth_helper.py`ao.

```python
def store_token(request, token):
  request.session['oauth_token'] = token

def store_user(request, user):
  request.session['user'] = {
    'is_authenticated': True,
    'name': user['displayName'],
    'email': user['mail'] if (user['mail'] != None) else user['userPrincipalName']
  }

def get_token(request):
  token = request.session['oauth_token']
  return token

def remove_user_and_token(request):
  if 'oauth_token' in request.session:
    del request.session['oauth_token']

  if 'user' in request.session:
    del request.session['user']
```

Em seguida, atualize `callback` a função `./tutorial/views.py` em para armazenar os tokens na sessão e redirecionar de volta para a página principal. Substitua a `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` linha pelo seguinte.

```python
from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
```

Substitua o `callback` método pelo seguinte.

```python
def callback(request):
  # Get the state saved in session
  expected_state = request.session.pop('auth_state', '')
  # Make the token request
  token = get_token_from_code(request.get_full_path(), expected_state)

  # Get the user's profile
  user = get_user(token)

  # Save token and user
  store_token(request, token)
  store_user(request, user)

  return HttpResponseRedirect(reverse('home'))
```

## <a name="implement-sign-out"></a>Implementar a saída

Antes de testar esse novo recurso, adicione uma maneira de sair. Adicionar um novo `sign_out` modo de `./tutorial/views.py`exibição no.

```python
def sign_out(request):
  # Clear out the user and token
  remove_user_and_token(request)

  return HttpResponseRedirect(reverse('home'))
```

Agora, adicione este modo `./tutorial/urls.py`de exibição a.

```python
path('signout', views.sign_out, name='signout'),
```

Atualize o link de **saída** `./tutorial/templates/tutorial/layout.html` para usar este novo modo de exibição. Substitua a `<a href="#" class="dropdown-item">Sign Out</a>` linha pelo seguinte.

```html
<a href="{% url 'signout' %}" class="dropdown-item">Sign Out</a>
```

Reinicie o servidor e vá pelo processo de entrada. Você deve terminar de volta na Home Page, mas a interface do usuário deve ser alterada para indicar que você está conectado.

![Uma captura de tela da Home Page após entrar](./images/add-aad-auth-01.png)

Clique no avatar do usuário no canto superior direito para acessar o **** link sair. Clicar **** em sair redefine a sessão e retorna à Home Page.

![Uma captura de tela do menu suspenso com o link sair](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Atualizando tokens

Nesse ponto, seu aplicativo tem um token de acesso, que é enviado no `Authorization` cabeçalho das chamadas de API. Este é o token que permite que o aplicativo acesse o Microsoft Graph em nome do usuário.

No entanto, esse token é de vida curta. O token expira uma hora após sua emissão. É onde o token de atualização se torna útil. O token de atualização permite que o aplicativo solicite um novo token de acesso sem exigir que o usuário entre novamente. Atualize o código de gerenciamento de token para implementar a atualização de token.

Substitua o método `get_token` existente `./tutorial/auth_helper.py` pelo seguinte.

```python
def get_token(request):
  token = request.session['oauth_token']
  if token != None:
    # Check expiration
    now = time.time()
    # Subtract 5 minutes from expiration to account for clock skew
    expire_time = token['expires_at'] - 300
    if now >= expire_time:
      # Refresh the token
      aad_auth = OAuth2Session(settings['app_id'],
        token = token,
        scope=settings['scopes'],
        redirect_uri=settings['redirect'])

      refresh_params = {
        'client_id': settings['app_id'],
        'client_secret': settings['app_secret'],
      }
      new_token = aad_auth.refresh_token(token_url, **refresh_params)

      # Save new token
      store_token(request, new_token)

      # Return new access token
      return new_token

    else:
      # Token still valid, just return it
      return token
```

Este método primeiro verifica se o token de acesso expirou ou está prestes a expirar. Se for, ele usará o token de atualização para obter novos tokens, atualizará o cache e retornará o novo token de acesso.