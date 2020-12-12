<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5b860-101">Nesta seção, você adicionará a capacidade de criar eventos no calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="5b860-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="5b860-102">Adicione o seguinte método a **./tutorial/graph_helper. py** para criar um novo evento.</span><span class="sxs-lookup"><span data-stu-id="5b860-102">Add the following method to **./tutorial/graph_helper.py** to create a new event.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="CreateEventSnippet":::

## <a name="create-a-new-event-form"></a><span data-ttu-id="5b860-103">Criar um novo formulário de eventos</span><span class="sxs-lookup"><span data-stu-id="5b860-103">Create a new event form</span></span>

1. <span data-ttu-id="5b860-104">Crie um novo arquivo no diretório **./tutorial/templates/tutorial** chamado `newevent.html` e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="5b860-104">Create a new file in the **./tutorial/templates/tutorial** directory named `newevent.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/newevent.html" id="NewEventSnippet":::

1. <span data-ttu-id="5b860-105">Adicione o seguinte modo de exibição a **./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="5b860-105">Add the following view to **./tutorial/views.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="NewEventViewSnippet":::

1. <span data-ttu-id="5b860-106">Abra **./tutorial/URLs.py** e adicione uma `path` instrução para o `newevent` modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="5b860-106">Open **./tutorial/urls.py** and add a `path` statements for the `newevent` view.</span></span>

    ```python
    path('calendar/new', views.newevent, name='newevent'),
    ```

1. <span data-ttu-id="5b860-107">Salve suas alterações e atualize o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5b860-107">Save your changes and refresh the app.</span></span> <span data-ttu-id="5b860-108">Na página **calendário** , selecione o botão **novo evento** .</span><span class="sxs-lookup"><span data-stu-id="5b860-108">On the **Calendar** page, select the **New event** button.</span></span> <span data-ttu-id="5b860-109">Preencha o formulário e selecione **criar** para criar o evento.</span><span class="sxs-lookup"><span data-stu-id="5b860-109">Fill in the form and select **Create** to create the event.</span></span>

    ![Uma captura de tela do novo formulário de evento](images/create-event-01.png)
