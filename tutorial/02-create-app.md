<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você usará o [Django](https://www.djangoproject.com/) para criar um aplicativo Web.

1. Se você ainda não tiver o Django instalado, você pode instalá-lo a partir da sua CLI (interface de linha de comando) com o seguinte comando.

    ```Shell
    pip install Django==3.0.4
    ```

1. Abra sua CLI, navegue até um diretório onde você tem direitos para criar arquivos e execute o seguinte comando para criar um novo aplicativo do Django.

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. Navegue até o diretório **graph_tutorial** e digite o seguinte comando para iniciar um servidor Web local.

    ```Shell
    python manage.py runserver
    ```

1. Abra o navegador e vá até `http://localhost:8000`. Se tudo estiver funcionando, você verá uma página de boas-vindas do Django. Se você não vir isso, consulte o [Guia de introdução do Django](https://www.djangoproject.com/start/).

1. Adicione um aplicativo ao projeto. Execute o seguinte comando em sua CLI.

    ```Shell
    python manage.py startapp tutorial
    ```

1. Abra **./graph_tutorial/Settings.py** e adicione o novo `tutorial` aplicativo à `INSTALLED_APPS` configuração.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. Na sua CLI, execute o seguinte comando para inicializar o banco de dados do projeto.

    ```Shell
    python manage.py migrate
    ```

1. Adicione um [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) para o `tutorial` aplicativo. Crie um novo arquivo no diretório **./tutorial** chamado `urls.py` e adicione o código a seguir.

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

1. Atualize o projeto URLconf para importar este. Abra **./graph_tutorial/URLs.py** e substitua o conteúdo pelo seguinte.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. Adicione um modo de exibição temporário `tutorials` ao aplicativo para verificar se o roteamento de URL está funcionando. Abra **./tutorial/views.py** e adicione o código a seguir.

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. Salve todas as suas alterações e reinicie o servidor. Navegue até `http://localhost:8000`. Você deve ver`Welcome to the tutorial.`

## <a name="install-libraries"></a>Instale bibliotecas

Antes de prosseguir, instale algumas bibliotecas adicionais que serão usadas posteriormente:

- [Solicitações-OAuthlib: OAuth para seres humanos](https://requests-oauthlib.readthedocs.io/en/latest/) para lidar com fluxos de entrada e tokens OAuth e para fazer chamadas para o Microsoft Graph.
- [PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) para carregar a configuração de um arquivo do YAML.
- [Python-DateUtil](https://pypi.org/project/python-dateutil/) para analisar cadeias de caracteres de data ISO 8601 retornadas do Microsoft Graph.

1. Execute o seguinte comando em sua CLI.

    ```Shell
    pip install requests_oauthlib==1.3.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a>Projetar o aplicativo

1. Crie um novo diretório no diretório **./tutorial** chamado `templates`.

1. No diretório **./tutorial/templates** , crie um novo diretório chamado `tutorial`.

1. No diretório **./tutorial/templates/tutorial** , crie um novo arquivo chamado `layout.html`. Adicione o código a seguir ao arquivo.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    Este código adiciona a [inicialização](http://getbootstrap.com/) para estilos simples e a [fonte incrível](https://fontawesome.com/) para alguns ícones simples. Também define um layout global com uma barra de navegação.

1. Crie um novo diretório no diretório **./tutorial** chamado `static`.

1. No diretório **./tutorial/static** , crie um novo diretório chamado `tutorial`.

1. No diretório **./tutorial/static/tutorial** , crie um novo arquivo chamado `app.css`. Adicione o código a seguir ao arquivo.

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. Crie um modelo para a Home Page que usa o layout. Crie um novo arquivo no diretório **./tutorial/templates/tutorial** chamado `home.html` e adicione o código a seguir.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. Abra o `./tutorial/views.py` arquivo e adicione a nova função a seguir.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. Substitua o modo `home` de exibição existente pelo seguinte.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. Salve todas as suas alterações e reinicie o servidor. Agora, o aplicativo deve ser muito diferente.

    ![Uma captura de tela da página inicial reprojetada](./images/create-app-01.png)
