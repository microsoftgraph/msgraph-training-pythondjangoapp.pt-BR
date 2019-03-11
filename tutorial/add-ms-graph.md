<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="caa8a-101">Neste exercício, você incorporará o Microsoft Graph no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="caa8a-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="caa8a-102">Para este aplicativo, você usará a biblioteca de [solicitações-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="caa8a-102">For this application, you will use the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="caa8a-103">Obter eventos de calendário do Outlook</span><span class="sxs-lookup"><span data-stu-id="caa8a-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="caa8a-104">Comece adicionando um método para `./tutorial/graph_helper.py` buscar os eventos do calendário.</span><span class="sxs-lookup"><span data-stu-id="caa8a-104">Start by adding a method to `./tutorial/graph_helper.py` to fetch the calendar events.</span></span> <span data-ttu-id="caa8a-105">Adicione o seguinte método.</span><span class="sxs-lookup"><span data-stu-id="caa8a-105">Add the following method.</span></span>

```python
def get_calendar_events(token):
  graph_client = OAuth2Session(token=token)

  # Configure query parameters to
  # modify the results
  query_params = {
    '$select': 'subject,organizer,start,end',
    '$orderby': 'createdDateTime DESC'
  }

  # Send GET to /me/events
  events = graph_client.get('{0}/me/events'.format(graph_url), params=query_params)
  # Return the JSON result
  return events.json()
```

<span data-ttu-id="caa8a-106">Considere o que esse código está fazendo.</span><span class="sxs-lookup"><span data-stu-id="caa8a-106">Consider what this code is doing.</span></span>

- <span data-ttu-id="caa8a-107">A URL que será chamada é `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="caa8a-107">The URL that will be called is `/v1.0/me/events`.</span></span>
- <span data-ttu-id="caa8a-108">O `$select` parâmetro limita os campos retornados para cada evento para apenas aqueles que o modo de exibição realmente usará.</span><span class="sxs-lookup"><span data-stu-id="caa8a-108">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="caa8a-109">O `$orderby` parâmetro classifica os resultados pela data e hora em que foram criados, com o item mais recente em primeiro lugar.</span><span class="sxs-lookup"><span data-stu-id="caa8a-109">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="caa8a-110">Agora, crie um modo de exibição de calendário.</span><span class="sxs-lookup"><span data-stu-id="caa8a-110">Now create a calendar view.</span></span> <span data-ttu-id="caa8a-111">Primeiro, altere `from tutorial.graph_helper import get_user` a linha para o seguinte.</span><span class="sxs-lookup"><span data-stu-id="caa8a-111">First change the `from tutorial.graph_helper import get_user` line to the following.</span></span>

```python
from tutorial.graph_helper import get_user, get_calendar_events
```

<span data-ttu-id="caa8a-112">Em seguida, adicione o seguinte modo `./tutorial/views.py`de exibição a.</span><span class="sxs-lookup"><span data-stu-id="caa8a-112">Then, add the following view to `./tutorial/views.py`.</span></span>

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

<span data-ttu-id="caa8a-113">Atualize `./tutorial/urls.py` para adicionar este novo modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="caa8a-113">Update `./tutorial/urls.py` to add this new view.</span></span>

```python
path('calendar', views.calendar, name='calendar'),
```

<span data-ttu-id="caa8a-114">Por fim, atualize \*\*\*\* o link de `./tutorial/templates/tutorial/layout.html` calendário em para vincular a este modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="caa8a-114">Finally, update  the **Calendar** link in `./tutorial/templates/tutorial/layout.html` to link to this view.</span></span> <span data-ttu-id="caa8a-115">Substitua a `<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>` linha pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="caa8a-115">Replace the `<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>` line with the following.</span></span>

```html
<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="{% url 'calendar' %}">Calendar</a>
```

<span data-ttu-id="caa8a-116">Agora você pode testar isso.</span><span class="sxs-lookup"><span data-stu-id="caa8a-116">Now you can test this.</span></span> <span data-ttu-id="caa8a-117">Entre e clique no link **calendário** na barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="caa8a-117">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="caa8a-118">Se tudo funcionar, você deverá ver um despejo JSON de eventos no calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="caa8a-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="caa8a-119">Exibir os resultados</span><span class="sxs-lookup"><span data-stu-id="caa8a-119">Display the results</span></span>

<span data-ttu-id="caa8a-120">Agora você pode adicionar um modelo para exibir os resultados de forma mais amigável.</span><span class="sxs-lookup"><span data-stu-id="caa8a-120">Now you can add a template to display the results in a more user-friendly manner.</span></span> <span data-ttu-id="caa8a-121">Crie um novo arquivo no `./tutorial/templates/tutorial` diretório chamado `calendar.html` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="caa8a-121">Create a new file in the `./tutorial/templates/tutorial` directory named `calendar.html` and add the following code.</span></span>

```html
{% extends "tutorial/layout.html" %}
{% block content %}
<h1>Calendar</h1>
<table class="table">
  <thead>
    <tr>
      <th scope="col">Organizer</th>
      <th scope="col">Subject</th>
      <th scope="col">Start</th>
      <th scope="col">End</th>
    </tr>
  </thead>
  <tbody>
    {% if events %}
      {% for event in events %}
        <tr>
          <td>{{ event.organizer.emailAddress.name }}</td>
          <td>{{ event.subject }}</td>
          <td>{{ event.start.dateTime|date:'SHORT_DATETIME_FORMAT' }}</td>
          <td>{{ event.end.dateTime|date:'SHORT_DATETIME_FORMAT' }}</td>
        </tr>
      {% endfor %}
    {% endif %}
  </tbody>
</table>
{% endblock %}
```

<span data-ttu-id="caa8a-122">Isso executará um loop através de uma coleção de eventos e adicionará uma linha de tabela para cada um.</span><span class="sxs-lookup"><span data-stu-id="caa8a-122">That will loop through a collection of events and add a table row for each one.</span></span> <span data-ttu-id="caa8a-123">Adicione a seguinte `import` instrução à parte superior do `./tutorials/views.py` arquivo.</span><span class="sxs-lookup"><span data-stu-id="caa8a-123">Add the following `import` statement to the top of the `./tutorials/views.py` file.</span></span>

```python
import dateutil.parser
```

<span data-ttu-id="caa8a-124">Substitua o `calendar` modo de `./tutorial/views.py` exibição pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="caa8a-124">Replace the `calendar` view in `./tutorial/views.py` with the following code.</span></span>

```python
def calendar(request):
  context = initialize_context(request)

  token = get_token(request)

  events = get_calendar_events(token)

  if events:
    # Convert the ISO 8601 date times to a datetime object
    # This allows the Django template to format the value nicely
    for event in events['value']:
      event['start']['dateTime'] = dateutil.parser.parse(event['start']['dateTime'])
      event['end']['dateTime'] = dateutil.parser.parse(event['end']['dateTime'])

    context['events'] = events['value']

  return render(request, 'tutorial/calendar.html', context)
```

<span data-ttu-id="caa8a-125">Atualize a página e o aplicativo agora deve renderizar uma tabela de eventos.</span><span class="sxs-lookup"><span data-stu-id="caa8a-125">Refresh the page and the app should now render a table of events.</span></span>

![Uma captura de tela da tabela de eventos](./images/add-msgraph-01.png)