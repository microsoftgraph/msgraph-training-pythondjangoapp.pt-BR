<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="26700-101">Neste exercício, você usará o [Django](https://www.djangoproject.com/) para criar um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="26700-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span>

1. <span data-ttu-id="26700-102">Se você ainda não tiver o Django instalado, você pode instalá-lo a partir da sua CLI (interface de linha de comando) com o seguinte comando.</span><span class="sxs-lookup"><span data-stu-id="26700-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    pip install Django==3.0.4
    ```

1. <span data-ttu-id="26700-103">Abra sua CLI, navegue até um diretório onde você tem direitos para criar arquivos e execute o seguinte comando para criar um novo aplicativo do Django.</span><span class="sxs-lookup"><span data-stu-id="26700-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. <span data-ttu-id="26700-104">Navegue até o diretório **graph_tutorial** e digite o seguinte comando para iniciar um servidor Web local.</span><span class="sxs-lookup"><span data-stu-id="26700-104">Navigate to the **graph_tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="26700-105">Abra o navegador e vá até `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="26700-105">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="26700-106">Se tudo estiver funcionando, você verá uma página de boas-vindas do Django.</span><span class="sxs-lookup"><span data-stu-id="26700-106">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="26700-107">Se você não vir isso, consulte o [Guia de introdução do Django](https://www.djangoproject.com/start/).</span><span class="sxs-lookup"><span data-stu-id="26700-107">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

1. <span data-ttu-id="26700-108">Adicione um aplicativo ao projeto.</span><span class="sxs-lookup"><span data-stu-id="26700-108">Add an app to the project.</span></span> <span data-ttu-id="26700-109">Execute o seguinte comando em sua CLI.</span><span class="sxs-lookup"><span data-stu-id="26700-109">Run the following command in your CLI.</span></span>

    ```Shell
    python manage.py startapp tutorial
    ```

1. <span data-ttu-id="26700-110">Abra **./graph_tutorial/Settings.py** e adicione o novo `tutorial` aplicativo à `INSTALLED_APPS` configuração.</span><span class="sxs-lookup"><span data-stu-id="26700-110">Open **./graph_tutorial/settings.py** and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. <span data-ttu-id="26700-111">Na sua CLI, execute o seguinte comando para inicializar o banco de dados do projeto.</span><span class="sxs-lookup"><span data-stu-id="26700-111">In your CLI, run the following command to initialize the database for the project.</span></span>

    ```Shell
    python manage.py migrate
    ```

1. <span data-ttu-id="26700-112">Adicione um [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) para o `tutorial` aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26700-112">Add a [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="26700-113">Crie um novo arquivo no diretório **./tutorial** chamado `urls.py` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="26700-113">Create a new file in the **./tutorial** directory named `urls.py` and add the following code.</span></span>

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

1. <span data-ttu-id="26700-114">Atualize o projeto URLconf para importar este.</span><span class="sxs-lookup"><span data-stu-id="26700-114">Update the project URLconf to import this one.</span></span> <span data-ttu-id="26700-115">Abra **./graph_tutorial/URLs.py** e substitua o conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="26700-115">Open **./graph_tutorial/urls.py** and replace the contents with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. <span data-ttu-id="26700-116">Adicione um modo de exibição temporário `tutorials` ao aplicativo para verificar se o roteamento de URL está funcionando.</span><span class="sxs-lookup"><span data-stu-id="26700-116">Add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="26700-117">Abra **./tutorial/views.py** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="26700-117">Open **./tutorial/views.py** and add the following code.</span></span>

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. <span data-ttu-id="26700-118">Salve todas as suas alterações e reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="26700-118">Save all of your changes and restart the server.</span></span> <span data-ttu-id="26700-119">Navegue até `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="26700-119">Browse to `http://localhost:8000`.</span></span> <span data-ttu-id="26700-120">Você deve ver`Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="26700-120">You should see `Welcome to the tutorial.`</span></span>

## <a name="install-libraries"></a><span data-ttu-id="26700-121">Instale bibliotecas</span><span class="sxs-lookup"><span data-stu-id="26700-121">Install libraries</span></span>

<span data-ttu-id="26700-122">Antes de prosseguir, instale algumas bibliotecas adicionais que serão usadas posteriormente:</span><span class="sxs-lookup"><span data-stu-id="26700-122">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="26700-123">[Solicitações-OAuthlib: OAuth para seres humanos](https://requests-oauthlib.readthedocs.io/en/latest/) para lidar com fluxos de entrada e tokens OAuth e para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="26700-123">[Requests-OAuthlib: OAuth for Humans](https://requests-oauthlib.readthedocs.io/en/latest/) for handling sign-in and OAuth token flows, and for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="26700-124">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) para carregar a configuração de um arquivo do YAML.</span><span class="sxs-lookup"><span data-stu-id="26700-124">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="26700-125">[Python-DateUtil](https://pypi.org/project/python-dateutil/) para analisar cadeias de caracteres de data ISO 8601 retornadas do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="26700-125">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

1. <span data-ttu-id="26700-126">Execute o seguinte comando em sua CLI.</span><span class="sxs-lookup"><span data-stu-id="26700-126">Run the following command in your CLI.</span></span>

    ```Shell
    pip install requests_oauthlib==1.3.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a><span data-ttu-id="26700-127">Projetar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="26700-127">Design the app</span></span>

1. <span data-ttu-id="26700-128">Crie um novo diretório no diretório **./tutorial** chamado `templates`.</span><span class="sxs-lookup"><span data-stu-id="26700-128">Create a new directory in the **./tutorial** directory named `templates`.</span></span>

1. <span data-ttu-id="26700-129">No diretório **./tutorial/templates** , crie um novo diretório chamado `tutorial`.</span><span class="sxs-lookup"><span data-stu-id="26700-129">In the **./tutorial/templates** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="26700-130">No diretório **./tutorial/templates/tutorial** , crie um novo arquivo chamado `layout.html`.</span><span class="sxs-lookup"><span data-stu-id="26700-130">In the **./tutorial/templates/tutorial** directory, create a new file named `layout.html`.</span></span> <span data-ttu-id="26700-131">Adicione o código a seguir ao arquivo.</span><span class="sxs-lookup"><span data-stu-id="26700-131">Add the following code in that file.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    <span data-ttu-id="26700-132">Este código adiciona a [inicialização](http://getbootstrap.com/) para estilos simples e a [fonte incrível](https://fontawesome.com/) para alguns ícones simples.</span><span class="sxs-lookup"><span data-stu-id="26700-132">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="26700-133">Também define um layout global com uma barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="26700-133">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="26700-134">Crie um novo diretório no diretório **./tutorial** chamado `static`.</span><span class="sxs-lookup"><span data-stu-id="26700-134">Create a new directory in the **./tutorial** directory named `static`.</span></span>

1. <span data-ttu-id="26700-135">No diretório **./tutorial/static** , crie um novo diretório chamado `tutorial`.</span><span class="sxs-lookup"><span data-stu-id="26700-135">In the **./tutorial/static** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="26700-136">No diretório **./tutorial/static/tutorial** , crie um novo arquivo chamado `app.css`.</span><span class="sxs-lookup"><span data-stu-id="26700-136">In the **./tutorial/static/tutorial** directory, create a new file named `app.css`.</span></span> <span data-ttu-id="26700-137">Adicione o código a seguir ao arquivo.</span><span class="sxs-lookup"><span data-stu-id="26700-137">Add the following code in that file.</span></span>

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. <span data-ttu-id="26700-138">Crie um modelo para a Home Page que usa o layout.</span><span class="sxs-lookup"><span data-stu-id="26700-138">Create a template for the home page that uses the layout.</span></span> <span data-ttu-id="26700-139">Crie um novo arquivo no diretório **./tutorial/templates/tutorial** chamado `home.html` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="26700-139">Create a new file in the **./tutorial/templates/tutorial** directory named `home.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. <span data-ttu-id="26700-140">Abra o `./tutorial/views.py` arquivo e adicione a nova função a seguir.</span><span class="sxs-lookup"><span data-stu-id="26700-140">Open the `./tutorial/views.py` file and add the following new function.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. <span data-ttu-id="26700-141">Substitua o modo `home` de exibição existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="26700-141">Replace the existing `home` view with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. <span data-ttu-id="26700-142">Salve todas as suas alterações e reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="26700-142">Save all of your changes and restart the server.</span></span> <span data-ttu-id="26700-143">Agora, o aplicativo deve ser muito diferente.</span><span class="sxs-lookup"><span data-stu-id="26700-143">Now, the app should look very different.</span></span>

    ![Uma captura de tela da página inicial reprojetada](./images/create-app-01.png)
