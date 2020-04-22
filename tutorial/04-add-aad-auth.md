<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você estenderá o aplicativo do exercício anterior para oferecer suporte à autenticação com o Azure AD. Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph. Nesta etapa, você integrará a biblioteca de [solicitações-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) ao aplicativo.

1. Crie um novo arquivo na raiz do projeto chamado `oauth_settings.yml`e adicione o seguinte conteúdo.

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. Substitua `YOUR_APP_ID_HERE` pela ID do aplicativo do portal de registro do aplicativo e substitua `YOUR_APP_SECRET_HERE` pela senha gerada.

> [!IMPORTANT]
> Se você estiver usando o controle de origem como o Git, agora seria uma boa hora para excluir o arquivo **oauth_settings. yml** do controle de origem para evitar vazar inadvertidamente sua ID de aplicativo e sua senha.

## <a name="implement-sign-in"></a>Implementar logon

1. Crie um novo arquivo no diretório **./tutorial** chamado `auth_helper.py` e adicione o código a seguir.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    Este arquivo manterá todos os métodos relacionados à autenticação. O `get_sign_in_url` gera uma URL de autorização e o `get_token_from_code` método troca a resposta de autorização para um token de acesso.

1. Adicione as seguintes `import` instruções à parte superior de **./tutorial/views.py**.

    ```python
    from django.urls import reverse
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code
    ```

1. Adicione um modo de exibição de entrada no arquivo **./tutorial/views.py** .

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. Adicione um modo de exibição de retorno de chamada no arquivo **./tutorial/views.py** .

    ```python
    def callback(request):
      # Get the state saved in session
      expected_state = request.session.pop('auth_state', '')
      # Make the token request
      token = get_token_from_code(request.get_full_path(), expected_state)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(token) }
      return HttpResponseRedirect(reverse('home'))
    ```

    Considere o que esses modos de exibição fazem:

    - A `signin` ação gera a URL de entrada do Azure AD, salva `state` o valor gerado pelo cliente OAuth e redireciona o navegador para a página de entrada do Azure AD.

    - A `callback` ação é onde o Azure redireciona após a conclusão da entrada. Essa ação garante que o `state` valor corresponda ao valor salvo e, em seguida, usa o código de autorização enviado pelo Azure para solicitar um token de acesso. Em seguida, ele redireciona de volta para a Home Page com o token de acesso no valor de erro temporário. Você o usará para verificar se a entrada está funcionando antes de prosseguir.

1. Abra **./tutorial/URLs.py** e substitua as instruções `path` existentes por `signin` :

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. Adicione um novo `path` para o `callback` modo de exibição.

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. Inicie o servidor e navegue até `https://localhost:8000`. Clique no botão entrar e você deverá ser redirecionado para `https://login.microsoftonline.com`o. Faça logon com sua conta da Microsoft e concorde com as permissões solicitadas. O navegador redireciona para o aplicativo, mostrando o token.

### <a name="get-user-details"></a>Obter detalhes do usuário

1. Crie um novo arquivo no diretório **./tutorial** chamado `graph_helper.py` e adicione o código a seguir.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    O `get_user` método faz uma solicitação get para o ponto de `/me` extremidade do Microsoft Graph para obter o perfil do usuário, usando o token de acesso adquirido anteriormente.

1. Atualize o `callback` método em **./tutorial/views.py** para obter o perfil do usuário do Microsoft Graph. Adicione a seguinte `import` instrução à parte superior do arquivo.

    ```python
    from tutorial.graph_helper import get_user
    ```

1. Substitua o `callback` método pelo código a seguir.

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

1. Adicione os seguintes métodos novos a **./tutorial/auth_helper. py**.

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

1. Atualize a `callback` função em **./tutorial/views.py** para armazenar os tokens na sessão e redirecionar de volta para a página principal. Substitua a `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` linha pelo seguinte.

    ```python
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
    ```

1. Substitua o `callback` método pelo seguinte.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a>Implementar a saída

1. Adicione um novo `sign_out` modo de exibição em **./tutorial/views.py**.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. Abra **./tutorial/URLs.py** e substitua as instruções `path` existentes por `signout` :

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. Reinicie o servidor e vá pelo processo de entrada. Você deve terminar de volta na Home Page, mas a interface do usuário deve ser alterada para indicar que você está conectado.

    ![Uma captura de tela da Home Page após entrar](./images/add-aad-auth-01.png)

1. Clique no avatar do usuário no canto superior direito para **acessar o link sair.** Clicar **em sair** redefine a sessão e retorna à Home Page.

    ![Uma captura de tela do menu suspenso com o link sair](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Atualizando tokens

Nesse ponto, seu aplicativo tem um token de acesso, que é enviado no `Authorization` cabeçalho das chamadas de API. Este é o token que permite que o aplicativo acesse o Microsoft Graph em nome do usuário.

No entanto, esse token é de vida curta. O token expira uma hora após sua emissão. É onde o token de atualização se torna útil. O token de atualização permite que o aplicativo solicite um novo token de acesso sem exigir que o usuário entre novamente. Atualize o código de gerenciamento de token para implementar a atualização de token.

1. Substitua o método `get_token` existente em **./tutorial/auth_helper. py** com o seguinte.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="GetTokenSnippet":::

    Este método primeiro verifica se o token de acesso expirou ou está prestes a expirar. Se for, ele usará o token de atualização para obter novos tokens, atualizará o cache e retornará o novo token de acesso.
