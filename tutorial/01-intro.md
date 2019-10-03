<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="406ae-101">В этом руководстве рассказывается, как создать приложение консоли .NET Core, которое использует API Microsoft Graph для получения сведений о календаре для пользователя.</span><span class="sxs-lookup"><span data-stu-id="406ae-101">This tutorial teaches you how to build a .NET Core console app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="406ae-102">Если вы предпочитаете просто скачать заполненный учебник, вы можете скачать или клонировать [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="406ae-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-dotnet-core).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="406ae-103">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="406ae-103">Prerequisites</span></span>

<span data-ttu-id="406ae-104">Прежде чем приступить к работе с этим руководством, на компьютере для разработки должен быть установлен [пакет SDK для .NET Core](https://dotnet.microsoft.com/download) .</span><span class="sxs-lookup"><span data-stu-id="406ae-104">Before you start this tutorial, you should have the [.NET Core SDK](https://dotnet.microsoft.com/download) installed on your development machine.</span></span> <span data-ttu-id="406ae-105">Если у вас нет пакета SDK или Мавен, посетите предыдущую ссылку для получения вариантов загрузки.</span><span class="sxs-lookup"><span data-stu-id="406ae-105">If you do not have the SDK or Maven, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="406ae-106">Это руководство было написано в пакете SDK для .NET Core версии 2.2.401.</span><span class="sxs-lookup"><span data-stu-id="406ae-106">This tutorial was written with .NET Core SDK version 2.2.401.</span></span> <span data-ttu-id="406ae-107">Действия, описанные в этом руководстве, могут работать с другими версиями, но не тестировались.</span><span class="sxs-lookup"><span data-stu-id="406ae-107">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="406ae-108">Отзывы</span><span class="sxs-lookup"><span data-stu-id="406ae-108">Feedback</span></span>

<span data-ttu-id="406ae-109">Сообщите о нем в [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="406ae-109">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-dotnet-core).</span></span>
