<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a1bbd-101">Neste exercício, você usará o [Django](https://www.djangoproject.com/) para criar um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span> <span data-ttu-id="a1bbd-102">Se você ainda não tiver o Django instalado, você pode instalá-lo a partir da sua CLI (interface de linha de comando) com o seguinte comando.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

```Shell
pip install Django=2.2.5
```

<span data-ttu-id="a1bbd-103">Abra sua CLI, navegue até um diretório onde você tem direitos para criar arquivos e execute o seguinte comando para criar um novo aplicativo do Django.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

```Shell
django-admin startproject graph_tutorial
```

<span data-ttu-id="a1bbd-104">Django cria um novo diretório chamado `graph_tutorial` e estruturará um aplicativo Web do Django.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-104">Django creates a new directory called `graph_tutorial` and scaffolds a Django web app.</span></span> <span data-ttu-id="a1bbd-105">Navegue até este novo diretório e digite o seguinte comando para iniciar um servidor Web local.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-105">Navigate to this new directory and enter the following command to start a local web server.</span></span>

```Shell
python manage.py runserver
```

<span data-ttu-id="a1bbd-106">Abra o navegador e vá até `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-106">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="a1bbd-107">Se tudo estiver funcionando, você verá uma página de boas-vindas do Django.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-107">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="a1bbd-108">Se você não vir isso, consulte o [Guia de introdução do Django](https://www.djangoproject.com/start/).</span><span class="sxs-lookup"><span data-stu-id="a1bbd-108">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

<span data-ttu-id="a1bbd-109">Agora que você verificou o projeto, adicione um aplicativo ao projeto.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-109">Now that you've verified the project, add an app to the project.</span></span> <span data-ttu-id="a1bbd-110">Execute o seguinte comando em sua CLI.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-110">Run the following command in your CLI.</span></span>

```Shell
python manage.py startapp tutorial
```

<span data-ttu-id="a1bbd-111">Isso cria um novo aplicativo no `./tutorial` diretório.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-111">This creates a new app in the `./tutorial` directory.</span></span> <span data-ttu-id="a1bbd-112">Abra o `./graph_tutorial/settings.py` e adicione o novo `tutorial` aplicativo à `INSTALLED_APPS` configuração.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-112">Open the `./graph_tutorial/settings.py` and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'tutorial',
]
```

<span data-ttu-id="a1bbd-113">Na sua CLI, execute o seguinte comando para inicializar o banco de dados do projeto.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-113">In your CLI, run the following command to initialize the database for the project.</span></span>

```Shell
python manage.py migrate
```

<span data-ttu-id="a1bbd-114">Adicione um [URLconf](https://docs.djangoproject.com/en/2.1/topics/http/urls/) para o `tutorial` aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-114">Add a [URLconf](https://docs.djangoproject.com/en/2.1/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="a1bbd-115">Crie um novo arquivo no `./tutorial` diretório chamado `urls.py` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-115">Create a new file in the `./tutorial` directory named `urls.py` and add the following code.</span></span>

```python
from django.urls import path

from . import views

urlpatterns = [
  # /tutorial
  path('', views.home, name='home'),
]
```

<span data-ttu-id="a1bbd-116">Agora, atualize o projeto URLconf para importar esse um.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-116">Now update the project URLconf to import this one.</span></span> <span data-ttu-id="a1bbd-117">Abra o `./graph_tutorial/urls.py` arquivo e substitua o conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-117">Open the `./graph_tutorial/urls.py` file and replace the contents with the following.</span></span>

```python
from django.contrib import admin
from django.urls import path, include
from tutorial import views

urlpatterns = [
    path('tutorial/', include('tutorial.urls')),
    path('admin/', admin.site.urls),
]
```

<span data-ttu-id="a1bbd-118">Por fim, adicione um modo de `tutorials` exibição temporário ao aplicativo para verificar se o roteamento de URL está funcionando.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-118">Finally add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="a1bbd-119">Abra o arquivo `./tutorial/views.py` e adicione o seguinte código.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-119">Open the `./tutorial/views.py` file and add the following code.</span></span>

```python
from django.http import HttpResponse, HttpResponseRedirect

def home(request):
  # Temporary!
  return HttpResponse("Welcome to the tutorial.")
```

<span data-ttu-id="a1bbd-120">Salve todas as suas alterações e reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-120">Save all of your changes and restart the server.</span></span> <span data-ttu-id="a1bbd-121">Navegue até `http://localhost:8000/tutorial`.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-121">Browse to `http://localhost:8000/tutorial`.</span></span> <span data-ttu-id="a1bbd-122">Você deve ver`Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="a1bbd-122">You should see `Welcome to the tutorial.`</span></span>

<span data-ttu-id="a1bbd-123">Antes de prosseguir, instale algumas bibliotecas adicionais que serão usadas posteriormente:</span><span class="sxs-lookup"><span data-stu-id="a1bbd-123">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="a1bbd-124">[Solicitações-OAuthlib: OAuth para seres humanos](https://requests-oauthlib.readthedocs.io/en/latest/) para lidar com fluxos de entrada e tokens OAuth e para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-124">[Requests-OAuthlib: OAuth for Humans](https://requests-oauthlib.readthedocs.io/en/latest/) for handling sign-in and OAuth token flows, and for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="a1bbd-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) para carregar a configuração de um arquivo do YAML.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="a1bbd-126">[Python-DateUtil](https://pypi.org/project/python-dateutil/) para analisar cadeias de caracteres de data ISO 8601 retornadas do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-126">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

<span data-ttu-id="a1bbd-127">Execute o seguinte comando em sua CLI.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-127">Run the following command in your CLI.</span></span>

```Shell
pip install requests_oauthlib==1.2.0
pip install pyyaml==5.1
pip install python-dateutil==2.8.0
```

## <a name="design-the-app"></a><span data-ttu-id="a1bbd-128">Projetar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a1bbd-128">Design the app</span></span>

<span data-ttu-id="a1bbd-129">Comece criando um diretório de modelos e definindo um layout global para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-129">Start by creating a templates directory and defining a global layout for the app.</span></span> <span data-ttu-id="a1bbd-130">Crie um novo diretório no `./tutorial` diretório chamado. `templates`</span><span class="sxs-lookup"><span data-stu-id="a1bbd-130">Create a new directory in the `./tutorial` directory named `templates`.</span></span> <span data-ttu-id="a1bbd-131">No `templates` diretório, crie um novo diretório chamado `tutorial`.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-131">In the `templates` directory, create a new directory named `tutorial`.</span></span> <span data-ttu-id="a1bbd-132">Por fim, crie um novo arquivo neste diretório chamado `layout.html`.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-132">Finally, create a new file in this directory named `layout.html`.</span></span> <span data-ttu-id="a1bbd-133">O caminho relativo da raiz do seu projeto deve ser `./tutorial/templates/tutorial/layout.html`.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-133">The relative path from the root of your project should be `./tutorial/templates/tutorial/layout.html`.</span></span> <span data-ttu-id="a1bbd-134">Adicione o código a seguir ao arquivo.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-134">Add the following code in that file.</span></span>

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Python Graph Tutorial</title>

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css"
      integrity="sha384-WskhaSGFgHYWDcbwN70/dfYBj47jz9qbsMId/iRN3ewGhXQFZCSftd1LZCfmhktB" crossorigin="anonymous">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
      integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt" crossorigin="anonymous">
    {% load static %}
    <link rel="stylesheet" href="{% static "tutorial/app.css" %}">
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js"
      integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js"
      integrity="sha384-smHYKdLADwkXOn1EmN1qk/HfnUcbVRZyYmZ4qpPea6sjB/pTJ0euyQp0Mk8ck+5T" crossorigin="anonymous"></script>
  </head>

  <body>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
      <div class="container">
        <a href="{% url 'home' %}" class="navbar-brand">Python Graph Tutorial</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
          aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarCollapse">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item">
              <a href="{% url 'home' %}" class="nav-link{% if request.resolver_match.view_name == 'home' %} active{% endif %}">Home</a>
            </li>
            {% if user.is_authenticated %}
              <li class="nav-item" data-turbolinks="false">
                <a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>
              </li>
            {% endif %}
          </ul>
          <ul class="navbar-nav justify-content-end">
            <li class="nav-item">
              <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                <i class="fas fa-external-link-alt mr-1"></i>Docs
              </a>
            </li>
            {% if user.is_authenticated %}
              <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true" aria-expanded="false">
                  {% if user.avatar %}
                    <img src="{{ user.avatar }}" class="rounded-circle align-self-center mr-2" style="width: 32px;">
                  {% else %}
                    <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                  {% endif %}
                </a>
                <div class="dropdown-menu dropdown-menu-right">
                  <h5 class="dropdown-item-text mb-0">{{ user.name }}</h5>
                  <p class="dropdown-item-text text-muted mb-0">{{ user.email }}</p>
                  <div class="dropdown-divider"></div>
                  <a href="#" class="dropdown-item">Sign Out</a>
                </div>
              </li>
            {% else %}
              <li class="nav-item">
                <a href="#" class="nav-link">Sign In</a>
              </li>
            {% endif %}
          </ul>
        </div>
      </div>
    </nav>
    <main role="main" class="container">
      {% if errors %}
        {% for error in errors %}
          <div class="alert alert-danger" role="alert">
            <p class="mb-3">{{ error.message }}</p>
            {% if error.debug %}
              <pre class="alert-pre border bg-light p-2"><code>{{ error.debug }}</code></pre>
            {% endif %}
          </div>
        {% endfor %}
      {% endif %}
      {% block content %}{% endblock %}
    </main>
  </body>
</html>
```

<span data-ttu-id="a1bbd-135">Este código adiciona a [inicialização](http://getbootstrap.com/) para estilos simples e a [fonte incrível](https://fontawesome.com/) para alguns ícones simples.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-135">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="a1bbd-136">Também define um layout global com uma barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-136">It also defines a global layout with a nav bar.</span></span>

<span data-ttu-id="a1bbd-137">Agora, crie um novo diretório no `./tutorial` diretório chamado `static`.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-137">Now create a new directory in the `./tutorial` directory named `static`.</span></span> <span data-ttu-id="a1bbd-138">No `static` diretório, crie um novo diretório chamado `tutorial`.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-138">In the `static` directory, create a new directory named `tutorial`.</span></span> <span data-ttu-id="a1bbd-139">Por fim, crie um novo arquivo neste diretório chamado `app.css`.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-139">Finally, create a new file in this directory named `app.css`.</span></span> <span data-ttu-id="a1bbd-140">O caminho relativo da raiz do seu projeto deve ser `./tutorial/static/tutorial/app.css`.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-140">The relative path from the root of your project should be `./tutorial/static/tutorial/app.css`.</span></span> <span data-ttu-id="a1bbd-141">Adicione o código a seguir ao arquivo.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-141">Add the following code in that file.</span></span>

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

<span data-ttu-id="a1bbd-142">Em seguida, crie um modelo para a Home Page que usa o layout.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-142">Next, create a template for the home page that uses the layout.</span></span> <span data-ttu-id="a1bbd-143">Crie um novo arquivo no `./tutorial/templates/tutorial` diretório chamado `home.html` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-143">Create a new file in the `./tutorial/templates/tutorial` directory named `home.html` and add the following code.</span></span>

```html
{% extends "tutorial/layout.html" %}
{% block content %}
<div class="jumbotron">
  <h1>Python Graph Tutorial</h1>
  <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from Python</p>
  {% if user.is_authenticated %}
    <h4>Welcome {{ user.name }}!</h4>
    <p>Use the navigation bar at the top of the page to get started.</p>
  {% else %}
    <a href="#" class="btn btn-primary btn-large">Click here to sign in</a>
  {% endif %}
</div>
{% endblock %}
```

<span data-ttu-id="a1bbd-144">Atualize o `home` modo de exibição para usar este modelo.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-144">Update the `home` view to use this template.</span></span> <span data-ttu-id="a1bbd-145">Abra o `./tutorial/views.py` arquivo e adicione a nova função a seguir.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-145">Open the `./tutorial/views.py` file and add the following new function.</span></span>

```python
def initialize_context(request):
  context = {}

  # Check for any errors in the session
  error = request.session.pop('flash_error', None)

  if error != None:
    context['errors'] = []
    context['errors'].append(error)

  # Check for user in the session
  context['user'] = request.session.get('user', {'is_authenticated': False})
  return context
```

<span data-ttu-id="a1bbd-146">Em seguida, substitua `home` o modo de exibição existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-146">Then replace the existing `home` view with the following.</span></span>

```python
def home(request):
  context = initialize_context(request)

  return render(request, 'tutorial/home.html', context)
```

<span data-ttu-id="a1bbd-147">Salve todas as suas alterações e reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-147">Save all of your changes and restart the server.</span></span> <span data-ttu-id="a1bbd-148">Agora, o aplicativo deve ser muito diferente.</span><span class="sxs-lookup"><span data-stu-id="a1bbd-148">Now, the app should look very different.</span></span>

![Uma captura de tela da página inicial reprojetada](./images/create-app-01.png)
