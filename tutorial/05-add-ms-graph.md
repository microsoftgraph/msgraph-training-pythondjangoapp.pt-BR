<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você incorporará o Microsoft Graph no aplicativo.

## <a name="get-calendar-events-from-outlook"></a>Obter eventos de calendário do Outlook

1. Comece adicionando um método a **./tutorial/graph_helper. py** para obter um modo de exibição do calendário entre duas datas. Adicione o seguinte método.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    Considere o que esse código está fazendo.

    - A URL que será chamada é `/v1.0/me/calendarview` .
        - O `Prefer: outlook.timezone` cabeçalho faz com que as horas de início e de término nos resultados sejam ajustadas para o fuso horário do usuário.
        - Os `startDateTime` `endDateTime` parâmetros e definem o início e o fim do modo de exibição.
        - O `$select` parâmetro limita os campos retornados para cada evento para apenas aqueles que o modo de exibição realmente usará.
        - O `$orderby` parâmetro classifica os resultados por hora de início.
        - O `$top` parâmetro limita os resultados a 50 eventos.

1. Adicione o código a seguir a **./tutorial/graph_helper. py** para pesquisar um [identificador de fuso horário IANA](https://www.iana.org/time-zones) com base em um nome de fuso horário do Windows. Isso é necessário porque o Microsoft Graph pode retornar fusos horários como nomes de fuso horário do Windows e a biblioteca de **DateTime** do Python requer identificadores de fuso horário da IANA.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="ZoneMappingsSnippet":::

1. Adicione o seguinte modo de exibição a **./tutorial/views.py**.

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

1. Abra **./tutorial/URLs.py** e substitua as `path` instruções existentes por `calendar` :

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. Entre e clique no link **calendário** na barra de navegação. Se tudo funcionar, você deverá ver um despejo JSON de eventos no calendário do usuário.

## <a name="display-the-results"></a>Exibir os resultados

Agora você pode adicionar um modelo para exibir os resultados de forma mais amigável.

1. Crie um novo arquivo no diretório **./tutorial/templates/tutorial** chamado `calendar.html` e adicione o código a seguir.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    Isso executará um loop através de uma coleção de eventos e adicionará uma linha de tabela para cada um.

1. Substitua o `calendar` modo de exibição no **./tutorial/views.py** pelo código a seguir.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. Atualize a página e o aplicativo agora deve renderizar uma tabela de eventos.

    ![Uma captura de tela da tabela de eventos](./images/add-msgraph-01.png)
