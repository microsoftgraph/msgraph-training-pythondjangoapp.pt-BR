<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="3ac0a-101">Neste exercício, você estenderá o aplicativo do exercício anterior para oferecer suporte à autenticação com o Azure AD.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="3ac0a-102">Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="3ac0a-103">Nesta etapa, você integrará a biblioteca de [solicitações-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-103">In this step you will integrate the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library into the application.</span></span>

<span data-ttu-id="3ac0a-104">Crie um novo arquivo na raiz do projeto chamado `oauth_settings.yml`e adicione o seguinte conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-104">Create a new file in the root of the project named `oauth_settings.yml`, and add the following content.</span></span>

```text
app_id: YOUR_APP_ID_HERE
app_secret: YOUR_APP_PASSWORD_HERE
redirect: http://localhost:8000/tutorial/callback
scopes: openid profile offline_access user.read calendars.read
authority: https://login.microsoftonline.com/common
authorize_endpoint: /oauth2/v2.0/authorize
token_endpoint: /oauth2/v2.0/token
```

<span data-ttu-id="3ac0a-105">Substitua `YOUR_APP_ID_HERE` pela ID do aplicativo do portal de registro do aplicativo e substitua `YOUR_APP_SECRET_HERE` pela senha gerada.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3ac0a-106">Se você estiver usando o controle de origem como o Git, agora seria uma boa hora para excluir `oauth_settings.yml` o arquivo do controle de origem para evitar vazar inadvertidamente sua ID de aplicativo e sua senha.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-106">If you're using source control such as git, now would be a good time to exclude the `oauth_settings.yml` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="3ac0a-107">Implementar logon</span><span class="sxs-lookup"><span data-stu-id="3ac0a-107">Implement sign-in</span></span>

<span data-ttu-id="3ac0a-108">Crie um novo arquivo no `./tutorial` diretório chamado `auth_helper.py` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-108">Create a new file in the `./tutorial` directory named `auth_helper.py` and add the following code.</span></span>

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
settings = yaml.load(stream, yaml.SafeLoader)
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

<span data-ttu-id="3ac0a-109">Este arquivo manterá todos os métodos relacionados à autenticação.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-109">This file will hold all of your authentication-related methods.</span></span> <span data-ttu-id="3ac0a-110">O `get_sign_in_url` gera uma URL de autorização e o `get_token_from_code` método troca a resposta de autorização para um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-110">The `get_sign_in_url` generates an authorization URL, and the `get_token_from_code` method exchanges the authorization response for an access token.</span></span>

<span data-ttu-id="3ac0a-111">Adicione as seguintes `import` instruções à parte superior de `./tutorial/views.py`.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-111">Add the following `import` statements to the top of `./tutorial/views.py`.</span></span>

```python
from django.urls import reverse
from tutorial.auth_helper import get_sign_in_url, get_token_from_code
```

<span data-ttu-id="3ac0a-112">Agora, adicione alguns novos modos de exibição no `./tutorial/views.py` arquivo.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-112">Now add a couple of new views in the `./tutorial/views.py` file.</span></span>

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

<span data-ttu-id="3ac0a-113">Isso define dois novos modos de `signin` exibição `callback`: e.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-113">This defines two new views: `signin` and `callback`.</span></span>

<span data-ttu-id="3ac0a-114">A `signin` ação gera a URL de entrada do Azure AD, salva `state` o valor gerado pelo cliente OAuth e redireciona o navegador para a página de entrada do Azure AD.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-114">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

<span data-ttu-id="3ac0a-115">A `callback` ação é onde o Azure redireciona após a conclusão da entrada.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-115">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="3ac0a-116">Essa ação garante que o `state` valor corresponda ao valor salvo e, em seguida, usa o código de autorização enviado pelo Azure para solicitar um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-116">That action makes sure the `state` value matches the saved value, then uses the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="3ac0a-117">Em seguida, ele redireciona de volta para a Home Page com o token de acesso no valor de erro temporário.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-117">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="3ac0a-118">Você o usará para verificar se a entrada está funcionando antes de prosseguir.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-118">You'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="3ac0a-119">Antes de testar, você precisa adicionar os modos de exibição `./tutorial/urls.py`ao.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-119">Before you test, you need to add the views to `./tutorial/urls.py`.</span></span>

```python
path('signin', views.sign_in, name='signin'),
path('callback', views.callback, name='callback'),
```

<span data-ttu-id="3ac0a-120">Substitua a `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` linha `./tutorial/templates/tutorial/home.html` pela seguinte.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-120">Replace the `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` line in `./tutorial/templates/tutorial/home.html` with the following.</span></span>

```html
<a href="{% url 'signin' %}" class="btn btn-primary btn-large">Click here to sign in</a>
```

<span data-ttu-id="3ac0a-121">Substitua a `<a href="#" class="nav-link">Sign In</a>` linha `./tutorial/templates/tutorial/layout.html` pela seguinte.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-121">Replace the `<a href="#" class="nav-link">Sign In</a>` line in `./tutorial/templates/tutorial/layout.html` with the following.</span></span>

```html
<a href="{% url 'signin' %}" class="nav-link">Sign In</a>
```

<span data-ttu-id="3ac0a-122">Inicie o servidor e navegue até `https://localhost:8000/tutorial`.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-122">Start the server and browse to `https://localhost:8000/tutorial`.</span></span> <span data-ttu-id="3ac0a-123">Clique no botão entrar e você deverá ser redirecionado para `https://login.microsoftonline.com`o.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-123">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="3ac0a-124">Faça logon com sua conta da Microsoft e concorde com as permissões solicitadas.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-124">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="3ac0a-125">O navegador redireciona para o aplicativo, mostrando o token.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-125">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="3ac0a-126">Obter detalhes do usuário</span><span class="sxs-lookup"><span data-stu-id="3ac0a-126">Get user details</span></span>

<span data-ttu-id="3ac0a-127">Crie um novo arquivo no `./tutorial` diretório chamado `graph_helper.py` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-127">Create a new file in the `./tutorial` directory named `graph_helper.py` and add the following code.</span></span>

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

<span data-ttu-id="3ac0a-128">O `get_user` método faz uma solicitação get para o ponto de `/me` extremidade do Microsoft Graph para obter o perfil do usuário, usando o token de acesso adquirido anteriormente.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-128">The `get_user` method makes a GET request to the Microsoft Graph `/me` endpoint to get the user's profile, using the access token you acquired previously.</span></span>

<span data-ttu-id="3ac0a-129">Atualize o `callback` método no `./tutorial/views.py` para obter o perfil do usuário do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-129">Update the `callback` method in `./tutorial/views.py` to get the user's profile from Microsoft Graph.</span></span>

<span data-ttu-id="3ac0a-130">Primeiro, adicione a seguinte `import` instrução à parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-130">First, add the following `import` statement to the top of the file.</span></span>

```python
from tutorial.graph_helper import get_user
```

<span data-ttu-id="3ac0a-131">Substitua o `callback` método pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-131">Replace the `callback` method with the following code.</span></span>

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

<span data-ttu-id="3ac0a-132">O novo código chama o `get_user` método para solicitar o perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-132">The new code calls the `get_user` method to request the user's profile.</span></span> <span data-ttu-id="3ac0a-133">Ele adiciona o objeto user à saída temporária para teste.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-133">It adds the user object to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="3ac0a-134">Armazenar tokens</span><span class="sxs-lookup"><span data-stu-id="3ac0a-134">Storing the tokens</span></span>

<span data-ttu-id="3ac0a-135">Agora que você pode obter tokens, é hora de implementar uma maneira de armazená-los no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-135">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="3ac0a-136">Como este é um aplicativo de exemplo, por questões de simplicidade, você os armazenará na sessão.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-136">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="3ac0a-137">Um aplicativo real usaria uma solução de armazenamento segura mais confiável, como um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-137">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="3ac0a-138">Adicione os métodos novos a seguir `./tutorial/auth_helper.py`ao.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-138">Add the following new methods to `./tutorial/auth_helper.py`.</span></span>

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

<span data-ttu-id="3ac0a-139">Em seguida, atualize `callback` a função `./tutorial/views.py` em para armazenar os tokens na sessão e redirecionar de volta para a página principal.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-139">Then, update the `callback` function in `./tutorial/views.py` to store the tokens in the session and redirect back to the main page.</span></span> <span data-ttu-id="3ac0a-140">Substitua a `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` linha pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-140">Replace the `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` line with the following.</span></span>

```python
from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
```

<span data-ttu-id="3ac0a-141">Substitua o `callback` método pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-141">Replace the `callback` method with the following.</span></span>

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

## <a name="implement-sign-out"></a><span data-ttu-id="3ac0a-142">Implementar a saída</span><span class="sxs-lookup"><span data-stu-id="3ac0a-142">Implement sign-out</span></span>

<span data-ttu-id="3ac0a-143">Antes de testar esse novo recurso, adicione uma maneira de sair. Adicionar um novo `sign_out` modo de `./tutorial/views.py`exibição no.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-143">Before you test this new feature, add a way to sign out. Add a new `sign_out` view in `./tutorial/views.py`.</span></span>

```python
def sign_out(request):
  # Clear out the user and token
  remove_user_and_token(request)

  return HttpResponseRedirect(reverse('home'))
```

<span data-ttu-id="3ac0a-144">Agora, adicione este modo `./tutorial/urls.py`de exibição a.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-144">Now add this view to `./tutorial/urls.py`.</span></span>

```python
path('signout', views.sign_out, name='signout'),
```

<span data-ttu-id="3ac0a-145">Atualize o link de **saída** `./tutorial/templates/tutorial/layout.html` para usar este novo modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-145">Update the **Sign Out** link in `./tutorial/templates/tutorial/layout.html` to use this new view.</span></span> <span data-ttu-id="3ac0a-146">Substitua a `<a href="#" class="dropdown-item">Sign Out</a>` linha pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-146">Replace the `<a href="#" class="dropdown-item">Sign Out</a>` line with the following.</span></span>

```html
<a href="{% url 'signout' %}" class="dropdown-item">Sign Out</a>
```

<span data-ttu-id="3ac0a-147">Reinicie o servidor e vá pelo processo de entrada.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-147">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="3ac0a-148">Você deve terminar de volta na Home Page, mas a interface do usuário deve ser alterada para indicar que você está conectado.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-148">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Uma captura de tela da Home Page após entrar](./images/add-aad-auth-01.png)

<span data-ttu-id="3ac0a-150">Clique no avatar do usuário no canto superior direito para acessar o \*\*\*\* link sair.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-150">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="3ac0a-151">Clicar \*\*\*\* em sair redefine a sessão e retorna à Home Page.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-151">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Uma captura de tela do menu suspenso com o link sair](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="3ac0a-153">Atualizando tokens</span><span class="sxs-lookup"><span data-stu-id="3ac0a-153">Refreshing tokens</span></span>

<span data-ttu-id="3ac0a-154">Nesse ponto, seu aplicativo tem um token de acesso, que é enviado no `Authorization` cabeçalho das chamadas de API.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-154">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="3ac0a-155">Este é o token que permite que o aplicativo acesse o Microsoft Graph em nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-155">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="3ac0a-156">No entanto, esse token é de vida curta.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-156">However, this token is short-lived.</span></span> <span data-ttu-id="3ac0a-157">O token expira uma hora após sua emissão.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-157">The token expires an hour after it is issued.</span></span> <span data-ttu-id="3ac0a-158">É onde o token de atualização se torna útil.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-158">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="3ac0a-159">O token de atualização permite que o aplicativo solicite um novo token de acesso sem exigir que o usuário entre novamente.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-159">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="3ac0a-160">Atualize o código de gerenciamento de token para implementar a atualização de token.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-160">Update the token management code to implement token refresh.</span></span>

<span data-ttu-id="3ac0a-161">Substitua o método `get_token` existente `./tutorial/auth_helper.py` pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-161">Replace the existing `get_token` method in `./tutorial/auth_helper.py` with the following.</span></span>

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

<span data-ttu-id="3ac0a-162">Este método primeiro verifica se o token de acesso expirou ou está prestes a expirar.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-162">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="3ac0a-163">Se for, ele usará o token de atualização para obter novos tokens, atualizará o cache e retornará o novo token de acesso.</span><span class="sxs-lookup"><span data-stu-id="3ac0a-163">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
