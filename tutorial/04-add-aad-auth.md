<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você estenderá o aplicativo do exercício anterior para oferecer suporte à autenticação com o Azure AD. Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph. Nesta etapa, você integrará o [MSAL para](https://github.com/AzureAD/microsoft-authentication-library-for-python) a Biblioteca Python no aplicativo.

1. Crie um novo arquivo na raiz do projeto chamado `oauth_settings.yml` e adicione o seguinte conteúdo.

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. Substitua `YOUR_APP_ID_HERE` pela ID do aplicativo do portal de registro do aplicativo e substitua `YOUR_APP_SECRET_HERE` pela senha gerada.

> [!IMPORTANT]
> Se você estiver usando o controle de origem como o Git, agora seria uma boa hora para excluir o arquivo **oauth_settings. yml** do controle de origem para evitar vazar inadvertidamente sua ID de aplicativo e sua senha.

## <a name="implement-sign-in"></a>Implementar logon

1. Crie um novo arquivo no diretório **./tutorial** chamado `auth_helper.py` e adicione o código a seguir.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    Este arquivo manterá todos os métodos relacionados à autenticação. O `get_sign_in_flow` gera uma URL de autorização e o `get_token_from_code` método troca a resposta de autorização para um token de acesso.

1. Adicione a seguinte `import` instrução à parte superior de **./tutorial/views.py**.

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code
    ```

1. Adicione um modo de exibição de entrada no arquivo **./tutorial/views.py** .

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. Adicione um modo de exibição de retorno de chamada no arquivo **./tutorial/views.py** .

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    Considere o que esses modos de exibição fazem:

    - A `signin` ação gera a URL de entrada do Azure AD, salva o fluxo gerado pelo cliente OAuth e redireciona o navegador para a página de entrada do Azure AD.

    - A `callback` ação é onde o Azure redireciona após a conclusão da entrada. Essa ação usa o fluxo salvo e a cadeia de caracteres de consulta enviada pelo Azure para solicitar um token de acesso. Em seguida, ele redireciona de volta para a Home Page com a resposta no valor de erro temporário. Você o usará para verificar se a entrada está funcionando antes de prosseguir.

1. Abra **./tutorial/URLs.py** e substitua as `path` instruções existentes por `signin` :

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. Adicione um novo `path` para o `callback` modo de exibição.

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. Inicie o servidor e navegue até `https://localhost:8000` . Clique no botão entrar e você deverá ser redirecionado para o `https://login.microsoftonline.com` . Faça logon com sua conta da Microsoft e concorde com as permissões solicitadas. O navegador redireciona para o aplicativo, mostrando a resposta, incluindo o token de acesso.

### <a name="get-user-details"></a>Obter detalhes do usuário

1. Crie um novo arquivo no diretório **./tutorial** chamado `graph_helper.py` e adicione o código a seguir.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    O `get_user` método faz uma solicitação get para o ponto de extremidade do Microsoft Graph `/me` para obter o perfil do usuário, usando o token de acesso adquirido anteriormente.

1. Atualize o `callback` método em **./tutorial/views.py** para obter o perfil do usuário do Microsoft Graph. Adicione a seguinte `import` instrução à parte superior do arquivo.

    ```python
    from tutorial.graph_helper import *
    ```

1. Substitua o `callback` método pelo código a seguir.

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)

      #Get the user's profile
      user = get_user(result['access_token'])
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': 'User: {0}\nToken: {1}'.format(user, result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    O novo código chama o `get_user` método para solicitar o perfil do usuário. Ele adiciona o objeto user à saída temporária para teste.

1. Adicione os seguintes métodos novos a **./tutorial/auth_helper. py**.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="SecondCodeSnippet":::

1. Atualize a `callback` função em **./tutorial/views.py** para armazenar o usuário na sessão e redirecionar de volta para a página principal. Substitua a `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` linha pelo seguinte.

    ```python
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_user, remove_user_and_token, get_token
    ```

1. Substitua o `callback` método pelo seguinte.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a>Implementar a saída

1. Adicione um novo `sign_out` modo de exibição em **./tutorial/views.py**.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. Abra **./tutorial/URLs.py** e substitua as `path` instruções existentes por `signout` :

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. Reinicie o servidor e vá pelo processo de entrada. Você deve terminar de volta na Home Page, mas a interface do usuário deve ser alterada para indicar que você está conectado.

    ![Uma captura de tela da Home Page após entrar](./images/add-aad-auth-01.png)

1. Clique no avatar do usuário no canto superior direito para **acessar o link sair.** Clicar **em sair** redefine a sessão e retorna à Home Page.

    ![Uma captura de tela do menu suspenso com o link sair](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Atualizando tokens

Nesse ponto, seu aplicativo tem um token de acesso, que é enviado no `Authorization` cabeçalho das chamadas de API. Este é o token que permite que o aplicativo acesse o Microsoft Graph em nome do usuário.

No entanto, esse token é de vida curta. O token expira uma hora após sua emissão. É onde o token de atualização se torna útil. O token de atualização permite que o aplicativo solicite um novo token de acesso sem exigir que o usuário entre novamente.

Como este exemplo usa MSAL, você não precisa escrever qualquer código específico para atualizar o token. `acquire_token_silent`O método de MSAL manipula a atualização do token, se necessário.
