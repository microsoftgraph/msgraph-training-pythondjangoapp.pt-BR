<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você estenderá o aplicativo do exercício anterior para dar suporte à autenticação com o Azure AD. Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph. Nesta etapa, você integrará a [biblioteca MSAL para Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) ao aplicativo.

1. Crie um novo arquivo na raiz do projeto chamado `oauth_settings.yml` e adicione o seguinte conteúdo.

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. Substitua pela ID do aplicativo do Portal de Registro de Aplicativo e substitua `YOUR_APP_ID_HERE` `YOUR_APP_SECRET_HERE` pela senha gerada.

> [!IMPORTANT]
> Se você estiver usando o controle de código-fonte como o git, agora seria um bom momento para excluir o arquivo **oauth_settings.yml** do controle de código-fonte para evitar o vazamento inadvertido da ID e da senha do aplicativo.

## <a name="implement-sign-in"></a>Implementar a login

1. Crie um novo arquivo no diretório **./tutorial** nomeado `auth_helper.py` e adicione o código a seguir.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    Esse arquivo conterá todos os seus métodos relacionados à autenticação. O `get_sign_in_flow` gera uma URL de autorização e o método troca a resposta de `get_token_from_code` autorização por um token de acesso.

1. Adicione a `import` instrução a seguir na parte superior **de ./tutorial/views.py**.

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code
    ```

1. Adicione uma exibição de login no **arquivo ./tutorial/views.py.**

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. Adicione uma exibição de retorno de chamada no **arquivo ./tutorial/views.py.**

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    Considere o que esses exibições fazem:

    - A ação gera a URL de entrada do Azure AD, salva o fluxo gerado pelo cliente OAuth e redireciona o navegador para a página de entrada do `signin` Azure AD.

    - A `callback` ação é onde o Azure redireciona após a conclusão da assinatura. Essa ação usa o fluxo salvo e a cadeia de caracteres de consulta enviada pelo Azure para solicitar um token de acesso. Em seguida, redireciona de volta para a home page com a resposta no valor de erro temporário. Você usará isso para verificar se nossa assinatura está funcionando antes de continuar.

1. Abra **./tutorial/urls.py** e substitua as instruções `path` existentes `signin` pelo seguinte.

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. Adicione um novo `path` para a `callback` exibição.

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. Inicie o servidor e navegue até `https://localhost:8000` . Clique no botão de login e você deve ser redirecionado `https://login.microsoftonline.com` para. Entre com sua conta da Microsoft e consenta com as permissões solicitadas. O navegador redireciona para o aplicativo, mostrando a resposta, incluindo o token de acesso.

### <a name="get-user-details"></a>Obter detalhes do usuário

1. Crie um novo arquivo no diretório **./tutorial** nomeado `graph_helper.py` e adicione o código a seguir.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    O método faz uma solicitação GET ao ponto de extremidade do Microsoft Graph para obter o perfil do usuário, usando o token de acesso `get_user` `/me` adquirido anteriormente.

1. Atualize `callback` o método em **./tutorial/views.py** para obter o perfil do usuário do Microsoft Graph. Adicione a `import` instrução a seguir na parte superior do arquivo.

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

    O novo código chama `get_user` o método para solicitar o perfil do usuário. Ele adiciona o objeto de usuário à saída temporária para teste.

1. Adicione os novos métodos a seguir **a ./tutorial/auth_helper.py**.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="SecondCodeSnippet":::

1. Atualize `callback` a função em **./tutorial/views.py** para armazenar o usuário na sessão e redirecionar de volta para a página principal. Substitua a `from tutorial.auth_helper import get_sign_in_flow, get_token_from_code` linha pelo seguinte.

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code, store_user, remove_user_and_token, get_token
    ```

1. Substitua o `callback` método pelo seguinte.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a>Implementar a saída

1. Adicione um novo `sign_out` ponto de exibição **em ./tutorial/views.py**.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. Abra **./tutorial/urls.py** e substitua as instruções `path` existentes `signout` pelo seguinte.

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. Reinicie o servidor e vá pelo processo de login. Você deve voltar à home page, mas a interface do usuário deve mudar para indicar que você está entrar.

    ![Uma captura de tela da home page após entrar](./images/add-aad-auth-01.png)

1. Clique no avatar do usuário no canto superior direito para acessar o link **Sair.** Clicar em **Sair** redefine a sessão e o retorna para a home page.

    ![Uma captura de tela do menu suspenso com o link Sair](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Atualização de tokens

Neste ponto, seu aplicativo tem um token de acesso, que é enviado no `Authorization` header de chamadas de API. Esse é o token que permite ao aplicativo acessar o Microsoft Graph em nome do usuário.

No entanto, esse token tem curta duração. O token expira uma hora após sua emissão. É aqui que o token de atualização se torna útil. O token de atualização permite que o aplicativo solicite um novo token de acesso sem exigir que o usuário entre novamente.

Como este exemplo usa MSAL, você não precisa escrever nenhum código específico para atualizar o token. O método da MSAL `acquire_token_silent` trata da atualização do token, se necessário.
