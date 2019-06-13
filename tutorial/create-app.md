<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você usará o [Django](https://www.djangoproject.com/) para criar um aplicativo Web. Se você ainda não tiver o Django instalado, você pode instalá-lo a partir da sua CLI (interface de linha de comando) com o seguinte comando.

```Shell
pip install Django=2.2.2
```

Abra sua CLI, navegue até um diretório onde você tem direitos para criar arquivos e execute o seguinte comando para criar um novo aplicativo do Django.

```Shell
django-admin startproject graph_tutorial
```

Django cria um novo diretório chamado `graph_tutorial` e estruturará um aplicativo Web do Django. Navegue até este novo diretório e digite o seguinte comando para iniciar um servidor Web local.

```Shell
python manage.py runserver
```

Abra o navegador e vá até `http://localhost:8000`. Se tudo estiver funcionando, você verá uma página de boas-vindas do Django. Se você não vir isso, consulte o [Guia de introdução do Django](https://www.djangoproject.com/start/).

Agora que você verificou o projeto, adicione um aplicativo ao projeto. Execute o seguinte comando em sua CLI.

```Shell
python manage.py startapp tutorial
```

Isso cria um novo aplicativo no `./tutorial` diretório. Abra o `./graph_tutorial/settings.py` e adicione o novo `tutorial` aplicativo à `INSTALLED_APPS` configuração.

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

Na sua CLI, execute o seguinte comando para inicializar o banco de dados do projeto.

```Shell
python manage.py migrate
```

Adicione um [URLconf](https://docs.djangoproject.com/en/2.1/topics/http/urls/) para o `tutorial` aplicativo. Crie um novo arquivo no `./tutorial` diretório chamado `urls.py` e adicione o código a seguir.

```python
from django.urls import path

from . import views

urlpatterns = [
  # /tutorial
  path('', views.home, name='home'),
]
```

Agora, atualize o projeto URLconf para importar esse um. Abra o `./graph_tutorial/urls.py` arquivo e substitua o conteúdo pelo seguinte.

```python
from django.contrib import admin
from django.urls import path, include
from tutorial import views

urlpatterns = [
    path('tutorial/', include('tutorial.urls')),
    path('admin/', admin.site.urls),
]
```

Por fim, adicione um modo de `tutorials` exibição temporário ao aplicativo para verificar se o roteamento de URL está funcionando. Abra o arquivo `./tutorial/views.py` e adicione o seguinte código.

```python
from django.http import HttpResponse, HttpResponseRedirect

def home(request):
  # Temporary!
  return HttpResponse("Welcome to the tutorial.")
```

Salve todas as suas alterações e reinicie o servidor. Navegue até `http://localhost:8000/tutorial`. Você deve ver`Welcome to the tutorial.`

Antes de prosseguir, instale algumas bibliotecas adicionais que serão usadas posteriormente:

- [Solicitações-OAuthlib: OAuth para seres humanos](https://requests-oauthlib.readthedocs.io/en/latest/) para lidar com fluxos de entrada e tokens OAuth e para fazer chamadas para o Microsoft Graph.
- [PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) para carregar a configuração de um arquivo do YAML.
- [Python-DateUtil](https://pypi.org/project/python-dateutil/) para analisar cadeias de caracteres de data ISO 8601 retornadas do Microsoft Graph.

Execute o seguinte comando em sua CLI.

```Shell
pip install requests_oauthlib==1.2.0
pip install pyyaml==5.1
pip install python-dateutil==2.8.0
```

## <a name="design-the-app"></a>Projetar o aplicativo

Comece criando um diretório de modelos e definindo um layout global para o aplicativo. Crie um novo diretório no `./tutorial` diretório chamado. `templates` No `templates` diretório, crie um novo diretório chamado `tutorial`. Por fim, crie um novo arquivo neste diretório chamado `layout.html`. O caminho relativo da raiz do seu projeto deve ser `./tutorial/templates/tutorial/layout.html`. Adicione o código a seguir ao arquivo.

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

Este código adiciona a [inicialização](http://getbootstrap.com/) para estilos simples e a [fonte incrível](https://fontawesome.com/) para alguns ícones simples. Também define um layout global com uma barra de navegação.

Agora, crie um novo diretório no `./tutorial` diretório chamado `static`. No `static` diretório, crie um novo diretório chamado `tutorial`. Por fim, crie um novo arquivo neste diretório chamado `app.css`. O caminho relativo da raiz do seu projeto deve ser `./tutorial/static/tutorial/app.css`. Adicione o código a seguir ao arquivo.

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

Em seguida, crie um modelo para a Home Page que usa o layout. Crie um novo arquivo no `./tutorial/templates/tutorial` diretório chamado `home.html` e adicione o código a seguir.

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

Atualize o `home` modo de exibição para usar este modelo. Abra o `./tutorial/views.py` arquivo e adicione a nova função a seguir.

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

Em seguida, substitua `home` o modo de exibição existente pelo seguinte.

```python
def home(request):
  context = initialize_context(request)

  return render(request, 'tutorial/home.html', context)
```

Salve todas as suas alterações e reinicie o servidor. Agora, o aplicativo deve ser muito diferente.

![Uma captura de tela da página inicial reprojetada](./images/create-app-01.png)
