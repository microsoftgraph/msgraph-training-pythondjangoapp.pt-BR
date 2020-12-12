<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5ac04-101">Neste exercício, você incorporará o Microsoft Graph no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5ac04-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="5ac04-102">Obter eventos de calendário do Outlook</span><span class="sxs-lookup"><span data-stu-id="5ac04-102">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="5ac04-103">Comece adicionando um método a **./tutorial/graph_helper. py** para obter um modo de exibição do calendário entre duas datas.</span><span class="sxs-lookup"><span data-stu-id="5ac04-103">Start by adding a method to **./tutorial/graph_helper.py** to fetch a view of the calendar between two dates.</span></span> <span data-ttu-id="5ac04-104">Adicione o seguinte método.</span><span class="sxs-lookup"><span data-stu-id="5ac04-104">Add the following method.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    <span data-ttu-id="5ac04-105">Considere o que esse código está fazendo.</span><span class="sxs-lookup"><span data-stu-id="5ac04-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="5ac04-106">A URL que será chamada é `/v1.0/me/calendarview` .</span><span class="sxs-lookup"><span data-stu-id="5ac04-106">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
        - <span data-ttu-id="5ac04-107">O `Prefer: outlook.timezone` cabeçalho faz com que as horas de início e de término nos resultados sejam ajustadas para o fuso horário do usuário.</span><span class="sxs-lookup"><span data-stu-id="5ac04-107">The `Prefer: outlook.timezone` header causes the start and end times in the results to be adjusted to the user's time zone.</span></span>
        - <span data-ttu-id="5ac04-108">Os `startDateTime` `endDateTime` parâmetros e definem o início e o fim do modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="5ac04-108">The `startDateTime` and `endDateTime` parameters set the start and end of the view.</span></span>
        - <span data-ttu-id="5ac04-109">O `$select` parâmetro limita os campos retornados para cada evento para apenas aqueles que o modo de exibição realmente usará.</span><span class="sxs-lookup"><span data-stu-id="5ac04-109">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
        - <span data-ttu-id="5ac04-110">O `$orderby` parâmetro classifica os resultados por hora de início.</span><span class="sxs-lookup"><span data-stu-id="5ac04-110">The `$orderby` parameter sorts the results by start time.</span></span>
        - <span data-ttu-id="5ac04-111">O `$top` parâmetro limita os resultados a 50 eventos.</span><span class="sxs-lookup"><span data-stu-id="5ac04-111">The `$top` parameter limits the results to 50 events.</span></span>

1. <span data-ttu-id="5ac04-112">Adicione o código a seguir a **./tutorial/graph_helper. py** para pesquisar um [identificador de fuso horário IANA](https://www.iana.org/time-zones) com base em um nome de fuso horário do Windows.</span><span class="sxs-lookup"><span data-stu-id="5ac04-112">Add the following code to **./tutorial/graph_helper.py** to lookup an [IANA time zone identifier](https://www.iana.org/time-zones) based on a Windows time zone name.</span></span> <span data-ttu-id="5ac04-113">Isso é necessário porque o Microsoft Graph pode retornar fusos horários como nomes de fuso horário do Windows e a biblioteca de **DateTime** do Python requer identificadores de fuso horário da IANA.</span><span class="sxs-lookup"><span data-stu-id="5ac04-113">This is necessary because Microsoft Graph can return time zones as Windows time zone names, and the Python **datetime** library requires IANA time zone identifiers.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="ZoneMappingsSnippet":::

1. <span data-ttu-id="5ac04-114">Adicione o seguinte modo de exibição a **./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="5ac04-114">Add the following view to **./tutorial/views.py**.</span></span>

    ```python
    def calendar(request):
      context = initialize_context(request)
      user = context['user']

      # Load the user's time zone
      # Microsoft Graph can return the user's time zone as either
      # a Windows time zone name or an IANA time zone identifier
      # Python datetime requires IANA, so convert Windows to IANA
      time_zone = get_iana_from_windows(user['timeZone'])
      tz_info = tz.gettz(time_zone)

      # Get midnight today in user's time zone
      today = datetime.now(tz_info).replace(
        hour=0,
        minute=0,
        second=0,
        microsecond=0)

      # Based on today, get the start of the week (Sunday)
      if (today.weekday() != 6):
        start = today - timedelta(days=today.isoweekday())
      else:
        start = today

      end = start + timedelta(days=7)

      token = get_token(request)

      events = get_calendar_events(
        token,
        start.isoformat(timespec='seconds'),
        end.isoformat(timespec='seconds'),
        user['timeZone'])

      context['errors'] = [
        { 'message': 'Events', 'debug': format(events)}
      ]

      return render(request, 'tutorial/home.html', context)
    ```

1. <span data-ttu-id="5ac04-115">Abra **./tutorial/URLs.py** e substitua as `path` instruções existentes por `calendar` :</span><span class="sxs-lookup"><span data-stu-id="5ac04-115">Open **./tutorial/urls.py** and replace the existing `path` statements for `calendar` with the following.</span></span>

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. <span data-ttu-id="5ac04-116">Entre e clique no link **calendário** na barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="5ac04-116">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="5ac04-117">Se tudo funcionar, você deverá ver um despejo JSON de eventos no calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="5ac04-117">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="5ac04-118">Exibir os resultados</span><span class="sxs-lookup"><span data-stu-id="5ac04-118">Display the results</span></span>

<span data-ttu-id="5ac04-119">Agora você pode adicionar um modelo para exibir os resultados de forma mais amigável.</span><span class="sxs-lookup"><span data-stu-id="5ac04-119">Now you can add a template to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="5ac04-120">Crie um novo arquivo no diretório **./tutorial/templates/tutorial** chamado `calendar.html` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="5ac04-120">Create a new file in the **./tutorial/templates/tutorial** directory named `calendar.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    <span data-ttu-id="5ac04-121">Isso executará um loop através de uma coleção de eventos e adicionará uma linha de tabela para cada um.</span><span class="sxs-lookup"><span data-stu-id="5ac04-121">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="5ac04-122">Substitua o `calendar` modo de exibição no **./tutorial/views.py** pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="5ac04-122">Replace the `calendar` view in **./tutorial/views.py** with the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. <span data-ttu-id="5ac04-123">Atualize a página e o aplicativo agora deve renderizar uma tabela de eventos.</span><span class="sxs-lookup"><span data-stu-id="5ac04-123">Refresh the page and the app should now render a table of events.</span></span>

    ![Uma captura de tela da tabela de eventos](./images/add-msgraph-01.png)
