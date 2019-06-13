# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="52b7e-101">Como executar o projeto concluído</span><span class="sxs-lookup"><span data-stu-id="52b7e-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="52b7e-102">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="52b7e-102">Prerequisites</span></span>

<span data-ttu-id="52b7e-103">Para executar o projeto concluído nessa pasta, você precisará do seguinte:</span><span class="sxs-lookup"><span data-stu-id="52b7e-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="52b7e-104">[Python](https://www.python.org/) (com [Pip](https://pypi.org/project/pip/)) instalado em sua máquina de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="52b7e-104">[Python](https://www.python.org/) (with [pip](https://pypi.org/project/pip/)) installed on your development machine.</span></span> <span data-ttu-id="52b7e-105">Se você não tiver Python, visite o link anterior para opções de download.</span><span class="sxs-lookup"><span data-stu-id="52b7e-105">If you do not have Python, visit the previous link for download options.</span></span> <span data-ttu-id="52b7e-106">(**Observação:** este tutorial foi escrito com o Python versão 3.7.0 e o Django versão 2,1.</span><span class="sxs-lookup"><span data-stu-id="52b7e-106">(**Note:** This tutorial was written with Python version 3.7.0 and Django version 2.1.</span></span> <span data-ttu-id="52b7e-107">As etapas deste guia podem funcionar com outras versões, mas que não foram testadas.</span><span class="sxs-lookup"><span data-stu-id="52b7e-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="52b7e-108">Uma conta pessoal da Microsoft com uma caixa de correio no Outlook.com ou uma conta corporativa ou de estudante da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="52b7e-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="52b7e-109">Se você não tem uma conta da Microsoft, há algumas opções para obter uma conta gratuita:</span><span class="sxs-lookup"><span data-stu-id="52b7e-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="52b7e-110">Você pode [se inscrever para uma nova conta pessoal da Microsoft](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="52b7e-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="52b7e-111">Você pode [se inscrever no programa para desenvolvedores do office 365](https://developer.microsoft.com/office/dev-program) para obter uma assinatura gratuita do Office 365.</span><span class="sxs-lookup"><span data-stu-id="52b7e-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="52b7e-112">Registrar um aplicativo Web com o centro de administração do Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="52b7e-112">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="52b7e-113">Abra um navegador e navegue até o [centro de administração do Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="52b7e-113">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="52b7e-114">Faça logon usando uma **conta pessoal** (também conhecida como Conta da Microsoft) ou **Conta Corporativa ou de Estudante**.</span><span class="sxs-lookup"><span data-stu-id="52b7e-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="52b7e-115">Selecione **Azure Active Directory** na navegação à esquerda e, em seguida, selecione **registros de aplicativo** em **gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="52b7e-115">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="52b7e-116">Uma captura de tela dos registros de aplicativo</span><span class="sxs-lookup"><span data-stu-id="52b7e-116">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="52b7e-117">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="52b7e-117">Select **New registration**.</span></span> <span data-ttu-id="52b7e-118">Na página **Registrar um aplicativo**, defina os valores da seguinte forma.</span><span class="sxs-lookup"><span data-stu-id="52b7e-118">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="52b7e-119">Defina **Nome** para `Python Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="52b7e-119">Set **Name** to `Python Graph Tutorial`.</span></span>
    - <span data-ttu-id="52b7e-120">Defina **Tipos de conta com suporte** para **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="52b7e-120">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="52b7e-121">Em **URI de Redirecionamento**, defina o primeiro menu suspenso para `Web` e defina o valor como `http://localhost:8000/tutorial/callback`.</span><span class="sxs-lookup"><span data-stu-id="52b7e-121">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `http://localhost:8000/tutorial/callback`.</span></span>

    ![Uma captura de tela da página registrar um aplicativo](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="52b7e-123">Escolha **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="52b7e-123">Choose **Register**.</span></span> <span data-ttu-id="52b7e-124">Na página **tutorial do gráfico do Python** , copie o valor da **ID do aplicativo (cliente)** e salve-o, você precisará dele na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="52b7e-124">On the **Python Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Uma captura de tela da ID do aplicativo do novo registro de aplicativo](/tutorial/images/aad-application-id.png)

1. <span data-ttu-id="52b7e-126">Selecione **Certificados e segredos** sob **Gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="52b7e-126">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="52b7e-127">Selecione o botão **Novo segredo do cliente**.</span><span class="sxs-lookup"><span data-stu-id="52b7e-127">Select the **New client secret** button.</span></span> <span data-ttu-id="52b7e-128">Insira um valor em **Descrição**, selecione uma das opções para **Expira** e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="52b7e-128">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![Uma captura de tela da caixa de diálogo Adicionar um segredo do cliente](/tutorial/images/aad-new-client-secret.png)

1. <span data-ttu-id="52b7e-130">Copie o valor secreto do cliente antes de sair desta página.</span><span class="sxs-lookup"><span data-stu-id="52b7e-130">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="52b7e-131">Você precisará dele na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="52b7e-131">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="52b7e-132">Este segredo do cliente nunca é mostrado novamente, portanto, copie-o agora.</span><span class="sxs-lookup"><span data-stu-id="52b7e-132">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Uma captura de tela do novo segredo do cliente recentemente adicionado](/tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a><span data-ttu-id="52b7e-134">Configurar o exemplo</span><span class="sxs-lookup"><span data-stu-id="52b7e-134">Configure the sample</span></span>

1. <span data-ttu-id="52b7e-135">Renomear `oauth_settings.yml.example` o `oauth_settings.yml`arquivo para.</span><span class="sxs-lookup"><span data-stu-id="52b7e-135">Rename the `oauth_settings.yml.example` file to `oauth_settings.yml`.</span></span>
1. <span data-ttu-id="52b7e-136">Edite `oauth_settings.yml` o arquivo e faça as seguintes alterações.</span><span class="sxs-lookup"><span data-stu-id="52b7e-136">Edit the `oauth_settings.yml` file and make the following changes.</span></span>
    1. <span data-ttu-id="52b7e-137">Substitua `YOUR_APP_ID_HERE` pela **ID do aplicativo** obtida do portal de registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="52b7e-137">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="52b7e-138">Substitua `YOUR_APP_PASSWORD_HERE` pela senha obtida do portal de registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="52b7e-138">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="52b7e-139">Na sua interface de linha de comando (CLI), navegue até este diretório e execute o seguinte comando para instalar os requisitos.</span><span class="sxs-lookup"><span data-stu-id="52b7e-139">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    pip install -r requirements.txt
    ```

1. <span data-ttu-id="52b7e-140">Na sua CLI, execute o seguinte comando para inicializar o banco de dados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="52b7e-140">In your CLI, run the following command to initialize the app's database.</span></span>

    ```Shell
    python manage.py migrate
    ```

## <a name="run-the-sample"></a><span data-ttu-id="52b7e-141">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="52b7e-141">Run the sample</span></span>

1. <span data-ttu-id="52b7e-142">Execute o comando a seguir em sua CLI para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="52b7e-142">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="52b7e-143">Abra um navegador e navegue até `http://localhost:8000/tutorial`.</span><span class="sxs-lookup"><span data-stu-id="52b7e-143">Open a browser and browse to `http://localhost:8000/tutorial`.</span></span>
