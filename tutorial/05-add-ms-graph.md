<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="bdc2d-101">Neste exercício, você incorporará o Microsoft Graph no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="bdc2d-102">Para este aplicativo, você usará a biblioteca de [solicitações-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-102">For this application, you will use the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="bdc2d-103">Obter eventos de calendário do Outlook</span><span class="sxs-lookup"><span data-stu-id="bdc2d-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="bdc2d-104">Comece adicionando um método a **./tutorial/graph_helper. py** para buscar os eventos do calendário.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-104">Start by adding a method to **./tutorial/graph_helper.py** to fetch the calendar events.</span></span> <span data-ttu-id="bdc2d-105">Adicione o seguinte método.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-105">Add the following method.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    <span data-ttu-id="bdc2d-106">Considere o que esse código está fazendo.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-106">Consider what this code is doing.</span></span>

    - <span data-ttu-id="bdc2d-107">A URL que será chamada é `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-107">The URL that will be called is `/v1.0/me/events`.</span></span>
    - <span data-ttu-id="bdc2d-108">O `$select` parâmetro limita os campos retornados para cada evento para apenas aqueles que o modo de exibição realmente usará.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-108">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="bdc2d-109">O `$orderby` parâmetro classifica os resultados pela data e hora em que foram criados, com o item mais recente em primeiro lugar.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-109">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="bdc2d-110">Em **./tutorial/views.py**, altere a `from tutorial.graph_helper import get_user` linha para o seguinte.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-110">In **./tutorial/views.py**, change the `from tutorial.graph_helper import get_user` line to the following.</span></span>

    ```python
    from tutorial.graph_helper import get_user, get_calendar_events
    ```

1. <span data-ttu-id="bdc2d-111">Adicione o seguinte modo de exibição a **./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-111">Add the following view to **./tutorial/views.py**.</span></span>

    ```python
    def calendar(request):
      context = initialize_context(request)

      token = get_token(request)

      events = get_calendar_events(token)

      context['errors'] = [
        { 'message': 'Events', 'debug': format(events)}
      ]

      return render(request, 'tutorial/home.html', context)
    ```

1. <span data-ttu-id="bdc2d-112">Abra **./tutorial/URLs.py** e substitua as instruções `path` existentes por `calendar` :</span><span class="sxs-lookup"><span data-stu-id="bdc2d-112">Open **./tutorial/urls.py** and replace the existing `path` statements for `calendar` with the following.</span></span>

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. <span data-ttu-id="bdc2d-113">Entre e clique no link **calendário** na barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-113">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="bdc2d-114">Se tudo funcionar, você deverá ver um despejo JSON de eventos no calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-114">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="bdc2d-115">Exibir os resultados</span><span class="sxs-lookup"><span data-stu-id="bdc2d-115">Display the results</span></span>

<span data-ttu-id="bdc2d-116">Agora você pode adicionar um modelo para exibir os resultados de forma mais amigável.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-116">Now you can add a template to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="bdc2d-117">Crie um novo arquivo no diretório **./tutorial/templates/tutorial** chamado `calendar.html` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-117">Create a new file in the **./tutorial/templates/tutorial** directory named `calendar.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    <span data-ttu-id="bdc2d-118">Isso executará um loop através de uma coleção de eventos e adicionará uma linha de tabela para cada um.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-118">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="bdc2d-119">Adicione a seguinte `import` instrução à parte superior do arquivo **./Tutorials/views.py** .</span><span class="sxs-lookup"><span data-stu-id="bdc2d-119">Add the following `import` statement to the top of the **./tutorials/views.py** file.</span></span>

    ```python
    import dateutil.parser
    ```

1. <span data-ttu-id="bdc2d-120">Substitua o `calendar` modo de exibição no **./tutorial/views.py** pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-120">Replace the `calendar` view in **./tutorial/views.py** with the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. <span data-ttu-id="bdc2d-121">Atualize a página e o aplicativo agora deve renderizar uma tabela de eventos.</span><span class="sxs-lookup"><span data-stu-id="bdc2d-121">Refresh the page and the app should now render a table of events.</span></span>

    ![Uma captura de tela da tabela de eventos](./images/add-msgraph-01.png)
