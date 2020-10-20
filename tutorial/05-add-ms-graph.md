<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="fa423-101">В этом упражнении вы добавите Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="fa423-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="fa423-102">Для этого приложения вы будете использовать [клиентскую библиотеку Microsoft Graph .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , чтобы совершать вызовы в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="fa423-102">For this application, you will use the [Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-user-details"></a><span data-ttu-id="fa423-103">Получение сведений о пользователе</span><span class="sxs-lookup"><span data-stu-id="fa423-103">Get user details</span></span>

1. <span data-ttu-id="fa423-104">Создайте новый каталог в каталоге **графтуториал** с именем **Graph**.</span><span class="sxs-lookup"><span data-stu-id="fa423-104">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>
1. <span data-ttu-id="fa423-105">Создайте новый файл в каталоге **Graph** с именем **GraphHelper.CS** и добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="fa423-105">Create a new file in the **Graph** directory named **GraphHelper.cs** and add the following code to that file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace GraphTutorial
    {
        public class GraphHelper
        {
            private static GraphServiceClient graphClient;
            public static void Initialize(IAuthenticationProvider authProvider)
            {
                graphClient = new GraphServiceClient(authProvider);
            }

            public static async Task<User> GetMeAsync()
            {
                try
                {
                    // GET /me
                    return await graphClient.Me
                        .Request()
                        .Select(u => new{
                            u.DisplayName,
                            u.MailboxSettings
                        })
                        .GetAsync();
                }
                catch (ServiceException ex)
                {
                    Console.WriteLine($"Error getting signed-in user: {ex.Message}");
                    return null;
                }
            }
        }
    }
    ```

1. <span data-ttu-id="fa423-106">Добавьте следующий код в файл `Main` **./Program.CS** сразу после вызова, `GetAccessToken` чтобы получить пользователя и вывести отображаемое имя пользователя.</span><span class="sxs-lookup"><span data-stu-id="fa423-106">Add the following code in `Main` in **./Program.cs** just after the `GetAccessToken` call to get the user and output the user's display name.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

<span data-ttu-id="fa423-107">Если вы запустите приложение сейчас, после входа в приложение введите имя.</span><span class="sxs-lookup"><span data-stu-id="fa423-107">If you run the app now, after you log in the app welcomes you by name.</span></span>

## <a name="get-a-calendar-view"></a><span data-ttu-id="fa423-108">Получение представления календаря</span><span class="sxs-lookup"><span data-stu-id="fa423-108">Get a calendar view</span></span>

1. <span data-ttu-id="fa423-109">Добавьте указанную ниже функцию в `GraphHelper` класс, чтобы получить события из календаря пользователя.</span><span class="sxs-lookup"><span data-stu-id="fa423-109">Add the following function to the `GraphHelper` class to get events from the user's calendar.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

<span data-ttu-id="fa423-110">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="fa423-110">Consider what this code is doing.</span></span>

- <span data-ttu-id="fa423-111">URL-адрес, который будет вызываться — это `/me/calendarview` .</span><span class="sxs-lookup"><span data-stu-id="fa423-111">The URL that will be called is `/me/calendarview`.</span></span>
- <span data-ttu-id="fa423-112">`startDateTime`Параметры и `endDateTime` задают начало и конец представления календаря.</span><span class="sxs-lookup"><span data-stu-id="fa423-112">The `startDateTime` and `endDateTime` parameters define the start and end of the calendar view.</span></span>
- <span data-ttu-id="fa423-113">`Prefer: outlook.timezone`Заголовок приводит к `start` `end` возвращению и записи событий в часовом поясе пользователя.</span><span class="sxs-lookup"><span data-stu-id="fa423-113">The `Prefer: outlook.timezone` header causes the `start` and `end` of the events to be returned in the user's time zone.</span></span>
- <span data-ttu-id="fa423-114">`Top`Функция запрашивает не более 50 событий.</span><span class="sxs-lookup"><span data-stu-id="fa423-114">The `Top` function requests at most 50 events.</span></span>
- <span data-ttu-id="fa423-115">`Select`Функция ограничит поля, возвращаемые для каждого события, только теми, которые приложение будет использовать в действительности.</span><span class="sxs-lookup"><span data-stu-id="fa423-115">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
- <span data-ttu-id="fa423-116">`OrderBy`Функция сортирует результаты по дате и времени начала.</span><span class="sxs-lookup"><span data-stu-id="fa423-116">The `OrderBy` function sorts the results by the start date and time.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="fa423-117">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="fa423-117">Display the results</span></span>

1. <span data-ttu-id="fa423-118">Добавьте указанную ниже функцию в `Program` класс, чтобы отформатировать свойства [DateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) из Microsoft Graph в понятный для пользователя формат.</span><span class="sxs-lookup"><span data-stu-id="fa423-118">Add the following function to the `Program` class to format the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) properties from Microsoft Graph into a user-friendly format.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. <span data-ttu-id="fa423-119">Добавьте указанную ниже функцию в `Program` класс, чтобы получить события пользователя и вывести их на консоль.</span><span class="sxs-lookup"><span data-stu-id="fa423-119">Add the following function to the `Program` class to get the user's events and output them to the console.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. <span data-ttu-id="fa423-120">Добавьте следующий код сразу после `// List the calendar` комментария в `Main` функции.</span><span class="sxs-lookup"><span data-stu-id="fa423-120">Add the following just after the `// List the calendar` comment in the `Main` function.</span></span>

    ```csharp
    ListCalendarEvents(
        user.MailboxSettings.TimeZone,
        $"{user.MailboxSettings.DateFormat} {user.MailboxSettings.TimeFormat}"
    );
    ```

1. <span data-ttu-id="fa423-121">Сохраните все изменения и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="fa423-121">Save all of your changes and run the app.</span></span> <span data-ttu-id="fa423-122">Выберите параметр **Просмотреть календарь этой недели** , чтобы просмотреть список событий пользователя.</span><span class="sxs-lookup"><span data-stu-id="fa423-122">Choose the **View this week's calendar** option to see a list of the user's events.</span></span>

    ```Shell
    Welcome Lynne Robbins!

    Please choose one of the following options:
    0. Exit
    1. Display access token
    2. View this week's calendar
    3. Add an event
    2
    Events:
    Subject: Meeting
      Organizer: Lynne Robbins
      Start: 9/28/2020 10:00 AM
      End: 9/28/2020 11:30 AM
    Subject: Weekly meeting
      Organizer: Lynne Robbins
      Start: 9/28/2020 2:00 PM
      End: 9/28/2020 3:00 PM
    Subject: Carpool
      Organizer: Lynne Robbins
      Start: 9/28/2020 4:00 PM
      End: 9/28/2020 5:30 PM
    Subject: Tailspin Toys Proposal Review + Lunch
      Organizer: Lidia Holloway
      Start: 9/29/2020 12:00 PM
      End: 9/29/2020 1:00 PM
    Subject: Weekly meeting
      Organizer: Lynne Robbins
      Start: 9/29/2020 2:00 PM
      End: 9/29/2020 3:00 PM
    Subject: Project Tailspin
    ```
