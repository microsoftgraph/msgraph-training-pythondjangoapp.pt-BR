<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="66ccd-101">Neste exercício, você estenderá o aplicativo do exercício anterior para dar suporte à autenticação com o Azure AD.</span><span class="sxs-lookup"><span data-stu-id="66ccd-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="66ccd-102">Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="66ccd-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="66ccd-103">Nesta etapa, você integrará a [biblioteca MSAL para Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="66ccd-103">In this step you will integrate the [MSAL for Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) library into the application.</span></span>

1. <span data-ttu-id="66ccd-104">Crie um novo arquivo na raiz do projeto chamado `oauth_settings.yml` e adicione o seguinte conteúdo.</span><span class="sxs-lookup"><span data-stu-id="66ccd-104">Create a new file in the root of the project named `oauth_settings.yml`, and add the following content.</span></span>

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. <span data-ttu-id="66ccd-105">Substitua pela ID do aplicativo do Portal de Registro de Aplicativo e substitua `YOUR_APP_ID_HERE` `YOUR_APP_SECRET_HERE` pela senha gerada.</span><span class="sxs-lookup"><span data-stu-id="66ccd-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="66ccd-106">Se você estiver usando o controle de código-fonte como o git, agora seria um bom momento para excluir o arquivo **oauth_settings.yml** do controle de código-fonte para evitar o vazamento inadvertido da ID e da senha do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="66ccd-106">If you're using source control such as git, now would be a good time to exclude the **oauth_settings.yml** file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="66ccd-107">Implementar a login</span><span class="sxs-lookup"><span data-stu-id="66ccd-107">Implement sign-in</span></span>

1. <span data-ttu-id="66ccd-108">Crie um novo arquivo no diretório **./tutorial** nomeado `auth_helper.py` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="66ccd-108">Create a new file in the **./tutorial** directory named `auth_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="66ccd-109">Esse arquivo conterá todos os seus métodos relacionados à autenticação.</span><span class="sxs-lookup"><span data-stu-id="66ccd-109">This file will hold all of your authentication-related methods.</span></span> <span data-ttu-id="66ccd-110">O `get_sign_in_flow` gera uma URL de autorização e o método troca a resposta de `get_token_from_code` autorização por um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="66ccd-110">The `get_sign_in_flow` generates an authorization URL, and the `get_token_from_code` method exchanges the authorization response for an access token.</span></span>

1. <span data-ttu-id="66ccd-111">Adicione a `import` instrução a seguir na parte superior **de ./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="66ccd-111">Add the following `import` statement to the top of **./tutorial/views.py**.</span></span>

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code
    ```

1. <span data-ttu-id="66ccd-112">Adicione uma exibição de login no **arquivo ./tutorial/views.py.**</span><span class="sxs-lookup"><span data-stu-id="66ccd-112">Add a sign-in view in the **./tutorial/views.py** file.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. <span data-ttu-id="66ccd-113">Adicione uma exibição de retorno de chamada no **arquivo ./tutorial/views.py.**</span><span class="sxs-lookup"><span data-stu-id="66ccd-113">Add a callback view in the **./tutorial/views.py** file.</span></span>

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    <span data-ttu-id="66ccd-114">Considere o que esses exibições fazem:</span><span class="sxs-lookup"><span data-stu-id="66ccd-114">Consider what these views do:</span></span>

    - <span data-ttu-id="66ccd-115">A ação gera a URL de entrada do Azure AD, salva o fluxo gerado pelo cliente OAuth e redireciona o navegador para a página de entrada do `signin` Azure AD.</span><span class="sxs-lookup"><span data-stu-id="66ccd-115">The `signin` action generates the Azure AD signin URL, saves the flow generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

    - <span data-ttu-id="66ccd-116">A `callback` ação é onde o Azure redireciona após a conclusão da assinatura.</span><span class="sxs-lookup"><span data-stu-id="66ccd-116">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="66ccd-117">Essa ação usa o fluxo salvo e a cadeia de caracteres de consulta enviada pelo Azure para solicitar um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="66ccd-117">That action uses the saved flow and the query string sent by Azure to request an access token.</span></span> <span data-ttu-id="66ccd-118">Em seguida, redireciona de volta para a home page com a resposta no valor de erro temporário.</span><span class="sxs-lookup"><span data-stu-id="66ccd-118">It then redirects back to the home page with the response in the temporary error value.</span></span> <span data-ttu-id="66ccd-119">Você usará isso para verificar se nossa assinatura está funcionando antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="66ccd-119">You'll use this to verify that our sign-in is working before moving on.</span></span>

1. <span data-ttu-id="66ccd-120">Abra **./tutorial/urls.py** e substitua as instruções `path` existentes `signin` pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="66ccd-120">Open **./tutorial/urls.py** and replace the existing `path` statements for `signin` with the following.</span></span>

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. <span data-ttu-id="66ccd-121">Adicione um novo `path` para a `callback` exibição.</span><span class="sxs-lookup"><span data-stu-id="66ccd-121">Add a new `path` for the `callback` view.</span></span>

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. <span data-ttu-id="66ccd-122">Inicie o servidor e navegue até `https://localhost:8000` .</span><span class="sxs-lookup"><span data-stu-id="66ccd-122">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="66ccd-123">Clique no botão de login e você deve ser redirecionado `https://login.microsoftonline.com` para.</span><span class="sxs-lookup"><span data-stu-id="66ccd-123">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="66ccd-124">Entre com sua conta da Microsoft e consenta com as permissões solicitadas.</span><span class="sxs-lookup"><span data-stu-id="66ccd-124">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="66ccd-125">O navegador redireciona para o aplicativo, mostrando a resposta, incluindo o token de acesso.</span><span class="sxs-lookup"><span data-stu-id="66ccd-125">The browser redirects to the app, showing the response, including the access token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="66ccd-126">Obter detalhes do usuário</span><span class="sxs-lookup"><span data-stu-id="66ccd-126">Get user details</span></span>

1. <span data-ttu-id="66ccd-127">Crie um novo arquivo no diretório **./tutorial** nomeado `graph_helper.py` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="66ccd-127">Create a new file in the **./tutorial** directory named `graph_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="66ccd-128">O método faz uma solicitação GET ao ponto de extremidade do Microsoft Graph para obter o perfil do usuário, usando o token de acesso `get_user` `/me` adquirido anteriormente.</span><span class="sxs-lookup"><span data-stu-id="66ccd-128">The `get_user` method makes a GET request to the Microsoft Graph `/me` endpoint to get the user's profile, using the access token you acquired previously.</span></span>

1. <span data-ttu-id="66ccd-129">Atualize `callback` o método em **./tutorial/views.py** para obter o perfil do usuário do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="66ccd-129">Update the `callback` method in **./tutorial/views.py** to get the user's profile from Microsoft Graph.</span></span> <span data-ttu-id="66ccd-130">Adicione a `import` instrução a seguir na parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="66ccd-130">Add the following `import` statement to the top of the file.</span></span>

    ```python
    from tutorial.graph_helper import *
    ```

1. <span data-ttu-id="66ccd-131">Substitua o `callback` método pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="66ccd-131">Replace the `callback` method with the following code.</span></span>

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

    <span data-ttu-id="66ccd-132">O novo código chama `get_user` o método para solicitar o perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="66ccd-132">The new code calls the `get_user` method to request the user's profile.</span></span> <span data-ttu-id="66ccd-133">Ele adiciona o objeto de usuário à saída temporária para teste.</span><span class="sxs-lookup"><span data-stu-id="66ccd-133">It adds the user object to the temporary output for testing.</span></span>

1. <span data-ttu-id="66ccd-134">Adicione os novos métodos a seguir **a ./tutorial/auth_helper.py**.</span><span class="sxs-lookup"><span data-stu-id="66ccd-134">Add the following new methods to **./tutorial/auth_helper.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="SecondCodeSnippet":::

1. <span data-ttu-id="66ccd-135">Atualize `callback` a função em **./tutorial/views.py** para armazenar o usuário na sessão e redirecionar de volta para a página principal.</span><span class="sxs-lookup"><span data-stu-id="66ccd-135">Update the `callback` function in **./tutorial/views.py** to store the user in the session and redirect back to the main page.</span></span> <span data-ttu-id="66ccd-136">Substitua a `from tutorial.auth_helper import get_sign_in_flow, get_token_from_code` linha pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="66ccd-136">Replace the `from tutorial.auth_helper import get_sign_in_flow, get_token_from_code` line with the following.</span></span>

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code, store_user, remove_user_and_token, get_token
    ```

1. <span data-ttu-id="66ccd-137">Substitua o `callback` método pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="66ccd-137">Replace the `callback` method with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="66ccd-138">Implementar a saída</span><span class="sxs-lookup"><span data-stu-id="66ccd-138">Implement sign-out</span></span>

1. <span data-ttu-id="66ccd-139">Adicione um novo `sign_out` ponto de exibição **em ./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="66ccd-139">Add a new `sign_out` view in **./tutorial/views.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. <span data-ttu-id="66ccd-140">Abra **./tutorial/urls.py** e substitua as instruções `path` existentes `signout` pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="66ccd-140">Open **./tutorial/urls.py** and replace the existing `path` statements for `signout` with the following.</span></span>

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. <span data-ttu-id="66ccd-141">Reinicie o servidor e vá pelo processo de login.</span><span class="sxs-lookup"><span data-stu-id="66ccd-141">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="66ccd-142">Você deve voltar à home page, mas a interface do usuário deve mudar para indicar que você está entrar.</span><span class="sxs-lookup"><span data-stu-id="66ccd-142">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Uma captura de tela da home page após entrar](./images/add-aad-auth-01.png)

1. <span data-ttu-id="66ccd-144">Clique no avatar do usuário no canto superior direito para acessar o link **Sair.**</span><span class="sxs-lookup"><span data-stu-id="66ccd-144">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="66ccd-145">Clicar em **Sair** redefine a sessão e o retorna para a home page.</span><span class="sxs-lookup"><span data-stu-id="66ccd-145">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Uma captura de tela do menu suspenso com o link Sair](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="66ccd-147">Atualização de tokens</span><span class="sxs-lookup"><span data-stu-id="66ccd-147">Refreshing tokens</span></span>

<span data-ttu-id="66ccd-148">Neste ponto, seu aplicativo tem um token de acesso, que é enviado no `Authorization` header de chamadas de API.</span><span class="sxs-lookup"><span data-stu-id="66ccd-148">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="66ccd-149">Esse é o token que permite ao aplicativo acessar o Microsoft Graph em nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="66ccd-149">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="66ccd-150">No entanto, esse token tem curta duração.</span><span class="sxs-lookup"><span data-stu-id="66ccd-150">However, this token is short-lived.</span></span> <span data-ttu-id="66ccd-151">O token expira uma hora após sua emissão.</span><span class="sxs-lookup"><span data-stu-id="66ccd-151">The token expires an hour after it is issued.</span></span> <span data-ttu-id="66ccd-152">É aqui que o token de atualização se torna útil.</span><span class="sxs-lookup"><span data-stu-id="66ccd-152">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="66ccd-153">O token de atualização permite que o aplicativo solicite um novo token de acesso sem exigir que o usuário entre novamente.</span><span class="sxs-lookup"><span data-stu-id="66ccd-153">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="66ccd-154">Como este exemplo usa MSAL, você não precisa escrever nenhum código específico para atualizar o token.</span><span class="sxs-lookup"><span data-stu-id="66ccd-154">Because this sample uses MSAL, you do not have to write any specific code to refresh the token.</span></span> <span data-ttu-id="66ccd-155">O método da MSAL `acquire_token_silent` trata da atualização do token, se necessário.</span><span class="sxs-lookup"><span data-stu-id="66ccd-155">MSAL's `acquire_token_silent` method handles refreshing the token if needed.</span></span>
