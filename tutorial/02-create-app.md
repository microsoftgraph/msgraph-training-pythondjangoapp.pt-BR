<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="bf787-101">Neste exercício, você usará o [Django](https://www.djangoproject.com/) para criar um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="bf787-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span>

1. <span data-ttu-id="bf787-102">Se você ainda não tiver o Django instalado, você pode instalá-lo a partir da sua CLI (interface de linha de comando) com o seguinte comando.</span><span class="sxs-lookup"><span data-stu-id="bf787-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    pip install --user Django==3.1.4
    ```

1. <span data-ttu-id="bf787-103">Abra sua CLI, navegue até um diretório onde você tem direitos para criar arquivos e execute o seguinte comando para criar um novo aplicativo do Django.</span><span class="sxs-lookup"><span data-stu-id="bf787-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. <span data-ttu-id="bf787-104">Navegue até o diretório **graph_tutorial** e digite o seguinte comando para iniciar um servidor Web local.</span><span class="sxs-lookup"><span data-stu-id="bf787-104">Navigate to the **graph_tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="bf787-105">Abra o navegador e vá até `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="bf787-105">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="bf787-106">Se tudo estiver funcionando, você verá uma página de boas-vindas do Django.</span><span class="sxs-lookup"><span data-stu-id="bf787-106">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="bf787-107">Se você não vir isso, consulte o [Guia de introdução do Django](https://www.djangoproject.com/start/).</span><span class="sxs-lookup"><span data-stu-id="bf787-107">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

1. <span data-ttu-id="bf787-108">Adicione um aplicativo ao projeto.</span><span class="sxs-lookup"><span data-stu-id="bf787-108">Add an app to the project.</span></span> <span data-ttu-id="bf787-109">Execute o seguinte comando em sua CLI.</span><span class="sxs-lookup"><span data-stu-id="bf787-109">Run the following command in your CLI.</span></span>

    ```Shell
    python manage.py startapp tutorial
    ```

1. <span data-ttu-id="bf787-110">Abra **./graph_tutorial/Settings.py** e adicione o novo `tutorial` aplicativo à `INSTALLED_APPS` configuração.</span><span class="sxs-lookup"><span data-stu-id="bf787-110">Open **./graph_tutorial/settings.py** and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. <span data-ttu-id="bf787-111">Na sua CLI, execute o seguinte comando para inicializar o banco de dados do projeto.</span><span class="sxs-lookup"><span data-stu-id="bf787-111">In your CLI, run the following command to initialize the database for the project.</span></span>

    ```Shell
    python manage.py migrate
    ```

1. <span data-ttu-id="bf787-112">Adicione um [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) para o `tutorial` aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bf787-112">Add a [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="bf787-113">Crie um novo arquivo no diretório **./tutorial** chamado `urls.py` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="bf787-113">Create a new file in the **./tutorial** directory named `urls.py` and add the following code.</span></span>

    ```python
    from django.urls import path

    from . import views

    urlpatterns = [
      # /
      path('', views.home, name='home'),
      # TEMPORARY
      path('signin', views.home, name='signin'),
      path('signout', views.home, name='signout'),
      path('calendar', views.home, name='calendar'),
    ]
    ```

1. <span data-ttu-id="bf787-114">Atualize o projeto URLconf para importar este.</span><span class="sxs-lookup"><span data-stu-id="bf787-114">Update the project URLconf to import this one.</span></span> <span data-ttu-id="bf787-115">Abra **./graph_tutorial/URLs.py** e substitua o conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="bf787-115">Open **./graph_tutorial/urls.py** and replace the contents with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. <span data-ttu-id="bf787-116">Adicione um modo de exibição temporário ao `tutorials` aplicativo para verificar se o roteamento de URL está funcionando.</span><span class="sxs-lookup"><span data-stu-id="bf787-116">Add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="bf787-117">Abra **./tutorial/views.py** e substitua todo o conteúdo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="bf787-117">Open **./tutorial/views.py** and replace its entire contents with the following code.</span></span>

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect
    from django.urls import reverse
    from datetime import datetime, timedelta
    from dateutil import tz, parser

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. <span data-ttu-id="bf787-118">Salve todas as suas alterações e reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="bf787-118">Save all of your changes and restart the server.</span></span> <span data-ttu-id="bf787-119">Navegue até `http://localhost:8000` .</span><span class="sxs-lookup"><span data-stu-id="bf787-119">Browse to `http://localhost:8000`.</span></span> <span data-ttu-id="bf787-120">Você deve ver `Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="bf787-120">You should see `Welcome to the tutorial.`</span></span>

## <a name="install-libraries"></a><span data-ttu-id="bf787-121">Instale bibliotecas</span><span class="sxs-lookup"><span data-stu-id="bf787-121">Install libraries</span></span>

<span data-ttu-id="bf787-122">Antes de prosseguir, instale algumas bibliotecas adicionais que serão usadas posteriormente:</span><span class="sxs-lookup"><span data-stu-id="bf787-122">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="bf787-123">[Biblioteca de autenticação da Microsoft (MSAL) para Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) para manipulação de entrada e fluxos de token OAuth.</span><span class="sxs-lookup"><span data-stu-id="bf787-123">[Microsoft Authentication Library (MSAL) for Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="bf787-124">[Solicitações: http para seres humanos](https://requests.readthedocs.io/en/master/) para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="bf787-124">[Requests: HTTP for Humans](https://requests.readthedocs.io/en/master/) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="bf787-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) para carregar a configuração de um arquivo do YAML.</span><span class="sxs-lookup"><span data-stu-id="bf787-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="bf787-126">[Python-DateUtil](https://pypi.org/project/python-dateutil/) para analisar cadeias de caracteres de data ISO 8601 retornadas do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="bf787-126">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

1. <span data-ttu-id="bf787-127">Execute o seguinte comando em sua CLI.</span><span class="sxs-lookup"><span data-stu-id="bf787-127">Run the following command in your CLI.</span></span>

    ```Shell
    pip install msal==1.7.0
    pip install requests==2.25.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a><span data-ttu-id="bf787-128">Projetar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="bf787-128">Design the app</span></span>

1. <span data-ttu-id="bf787-129">Crie um novo diretório no diretório **./tutorial** chamado `templates` .</span><span class="sxs-lookup"><span data-stu-id="bf787-129">Create a new directory in the **./tutorial** directory named `templates`.</span></span>

1. <span data-ttu-id="bf787-130">No diretório **./tutorial/templates** , crie um novo diretório chamado `tutorial` .</span><span class="sxs-lookup"><span data-stu-id="bf787-130">In the **./tutorial/templates** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="bf787-131">No diretório **./tutorial/templates/tutorial** , crie um novo arquivo chamado `layout.html` .</span><span class="sxs-lookup"><span data-stu-id="bf787-131">In the **./tutorial/templates/tutorial** directory, create a new file named `layout.html`.</span></span> <span data-ttu-id="bf787-132">Adicione o código a seguir ao arquivo.</span><span class="sxs-lookup"><span data-stu-id="bf787-132">Add the following code in that file.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    <span data-ttu-id="bf787-133">Este código adiciona [Bootstrap](http://getbootstrap.com/) para estilo simples e [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) para alguns ícones simples.</span><span class="sxs-lookup"><span data-stu-id="bf787-133">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) for some simple icons.</span></span> <span data-ttu-id="bf787-134">Também define um layout global com uma barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="bf787-134">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="bf787-135">Crie um novo diretório no diretório **./tutorial** chamado `static` .</span><span class="sxs-lookup"><span data-stu-id="bf787-135">Create a new directory in the **./tutorial** directory named `static`.</span></span>

1. <span data-ttu-id="bf787-136">No diretório **./tutorial/static** , crie um novo diretório chamado `tutorial` .</span><span class="sxs-lookup"><span data-stu-id="bf787-136">In the **./tutorial/static** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="bf787-137">No diretório **./tutorial/static/tutorial** , crie um novo arquivo chamado `app.css` .</span><span class="sxs-lookup"><span data-stu-id="bf787-137">In the **./tutorial/static/tutorial** directory, create a new file named `app.css`.</span></span> <span data-ttu-id="bf787-138">Adicione o código a seguir ao arquivo.</span><span class="sxs-lookup"><span data-stu-id="bf787-138">Add the following code in that file.</span></span>

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. <span data-ttu-id="bf787-139">Crie um modelo para a Home Page que usa o layout.</span><span class="sxs-lookup"><span data-stu-id="bf787-139">Create a template for the home page that uses the layout.</span></span> <span data-ttu-id="bf787-140">Crie um novo arquivo no diretório **./tutorial/templates/tutorial** chamado `home.html` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="bf787-140">Create a new file in the **./tutorial/templates/tutorial** directory named `home.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. <span data-ttu-id="bf787-141">Abra o `./tutorial/views.py` arquivo e adicione a nova função a seguir.</span><span class="sxs-lookup"><span data-stu-id="bf787-141">Open the `./tutorial/views.py` file and add the following new function.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. <span data-ttu-id="bf787-142">Substitua o `home` modo de exibição existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="bf787-142">Replace the existing `home` view with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. <span data-ttu-id="bf787-143">Adicione um arquivo PNG chamado **no-profile-photo.png** no diretório **./tutorial/static/tutorial** .</span><span class="sxs-lookup"><span data-stu-id="bf787-143">Add a PNG file named **no-profile-photo.png** in the **./tutorial/static/tutorial** directory.</span></span>

1. <span data-ttu-id="bf787-144">Salve todas as suas alterações e reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="bf787-144">Save all of your changes and restart the server.</span></span> <span data-ttu-id="bf787-145">Agora, o aplicativo deve ser muito diferente.</span><span class="sxs-lookup"><span data-stu-id="bf787-145">Now, the app should look very different.</span></span>

    ![Uma captura de tela da página inicial reprojetada](./images/create-app-01.png)
