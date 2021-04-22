<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você usará [Django para](https://www.djangoproject.com/) criar um aplicativo Web.

1. Se você ainda não tiver o Django instalado, poderá instalá-lo a partir de sua interface de linha de comando (CLI) com o seguinte comando.

    ```Shell
    pip install Django==3.2
    ```

1. Abra sua CLI, navegue até um diretório onde você tem direitos para criar arquivos e execute o seguinte comando para criar um novo aplicativo Django.

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. Navegue até o **diretório graph_tutorial** e insira o seguinte comando para iniciar um servidor Web local.

    ```Shell
    python manage.py runserver
    ```

1. Abra o navegador e vá até `http://localhost:8000`. Se tudo estiver funcionando, você verá uma página de boas-vindas django. Se você não vir isso, verifique o [guia Django de início.](https://www.djangoproject.com/start/)

1. Adicione um aplicativo ao projeto. Execute o seguinte comando em sua CLI.

    ```Shell
    python manage.py startapp tutorial
    ```

1. Abra **./graph_tutorial/settings.py** e adicione o novo `tutorial` aplicativo à `INSTALLED_APPS` configuração.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. Em sua CLI, execute o seguinte comando para inicializar o banco de dados do projeto.

    ```Shell
    python manage.py migrate
    ```

1. Adicione um [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) para o `tutorial` aplicativo. Crie um novo arquivo no **diretório ./tutorial** nomeado `urls.py` e adicione o código a seguir.

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

1. Atualize o URLconf do projeto para importar esse. Abra **./graph_tutorial/urls.py** e substitua o conteúdo pelo seguinte.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. Adicione uma exibição temporária ao `tutorials` aplicativo para verificar se o roteamento de URL está funcionando. Abra **./tutorial/views.py** e substitua todo o conteúdo pelo código a seguir.

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect
    from django.urls import reverse
    from datetime import datetime, timedelta

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. Salve todas as suas alterações e reinicie o aplicativo. Navegue até `http://localhost:8000` . Você deve ver `Welcome to the tutorial.`

## <a name="install-libraries"></a>Instale bibliotecas

Antes de continuar, instale algumas bibliotecas adicionais que você usará posteriormente:

- [Biblioteca de Autenticação da Microsoft (MSAL) para Python para](https://github.com/AzureAD/microsoft-authentication-library-for-python) lidar com fluxos de token de entrada e OAuth.
- [PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) para carregar a configuração de um arquivo YAML.
- [python-dateutil](https://pypi.org/project/python-dateutil/) para analisar as cadeias de caracteres de data ISO 8601 retornadas do Microsoft Graph.

1. Execute o seguinte comando em sua CLI.

    ```Shell
    pip install msal==1.10.0
    pip install pyyaml==5.4.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a>Design do aplicativo

1. Crie um novo diretório no **diretório ./tutorial** chamado `templates` .

1. No diretório **./tutorial/templates,** crie um novo diretório chamado `tutorial` .

1. No **diretório ./tutorial/templates/tutorial,** crie um novo arquivo chamado `layout.html` . Adicione o seguinte código nesse arquivo.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    Este código adiciona [Bootstrap](http://getbootstrap.com/) para estilo simples e [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) para alguns ícones simples. Ele também define um layout global com uma barra de nav.

1. Crie um novo diretório no **diretório ./tutorial** chamado `static` .

1. No **diretório ./tutorial/static,** crie um novo diretório chamado `tutorial` .

1. No **diretório ./tutorial/static/tutorial,** crie um novo arquivo chamado `app.css` . Adicione o seguinte código nesse arquivo.

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. Crie um modelo para a home page que usa o layout. Crie um novo arquivo no **diretório ./tutorial/templates/tutorial** nomeado `home.html` e adicione o código a seguir.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. Abra o `./tutorial/views.py` arquivo e adicione a nova função a seguir.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. Substitua o exibição `home` existente pelo seguinte.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. Adicione um arquivo PNG chamado **no-profile-photo.png** no **diretório ./tutorial/static/tutorial.**

1. Salve todas as suas alterações e reinicie o aplicativo. Agora, o aplicativo deve ter uma aparência muito diferente.

    ![Captura de tela da home page](./images/create-app-01.png)
