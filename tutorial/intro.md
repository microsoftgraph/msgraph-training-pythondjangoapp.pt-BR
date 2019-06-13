<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="292bc-101">Este tutorial ensina como criar um aplicativo da Web do python django que usa a API do Microsoft Graph para recuperar informações de calendário de um usuário.</span><span class="sxs-lookup"><span data-stu-id="292bc-101">This tutorial teaches you how to build a Python Django web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="292bc-102">Se preferir baixar o tutorial concluído, você pode baixá-lo de duas maneiras.</span><span class="sxs-lookup"><span data-stu-id="292bc-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="292bc-103">Baixe o [início rápido do Python](https://developer.microsoft.com/graph/quick-start?platform=option-Python) para obter o código de trabalho em minutos.</span><span class="sxs-lookup"><span data-stu-id="292bc-103">Download the [Python quick start](https://developer.microsoft.com/graph/quick-start?platform=option-Python) to get working code in minutes.</span></span>
> - <span data-ttu-id="292bc-104">Baixe ou clone o [repositório do GitHub](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span><span class="sxs-lookup"><span data-stu-id="292bc-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="292bc-105">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="292bc-105">Prerequisites</span></span>

<span data-ttu-id="292bc-106">Antes de iniciar este tutorial, você deve ter o [Python](https://www.python.org/) (com [Pip](https://pypi.org/project/pip/)) instalado em sua máquina de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="292bc-106">Before you start this tutorial, you should have [Python](https://www.python.org/) (with [pip](https://pypi.org/project/pip/)) installed on your development machine.</span></span> <span data-ttu-id="292bc-107">Se você não tiver Python, visite o link anterior para opções de download.</span><span class="sxs-lookup"><span data-stu-id="292bc-107">If you do not have Python, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="292bc-108">Este tutorial foi escrito com o Python versão 3.7.0 e o Django versão 2.2.2.</span><span class="sxs-lookup"><span data-stu-id="292bc-108">This tutorial was written with Python version 3.7.0 and Django version 2.2.2.</span></span> <span data-ttu-id="292bc-109">As etapas deste guia podem funcionar com outras versões, mas que não foram testadas.</span><span class="sxs-lookup"><span data-stu-id="292bc-109">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="292bc-110">Comentários</span><span class="sxs-lookup"><span data-stu-id="292bc-110">Feedback</span></span>

<span data-ttu-id="292bc-111">Forneça comentários sobre este tutorial no [repositório do GitHub](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span><span class="sxs-lookup"><span data-stu-id="292bc-111">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span></span>
