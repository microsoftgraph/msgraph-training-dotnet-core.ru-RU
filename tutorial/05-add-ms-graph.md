<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="73bce-101">В этом упражнении вы добавите Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="73bce-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="73bce-102">Для этого приложения вы будете использовать [клиентскую библиотеку Microsoft Graph .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , чтобы совершать вызовы в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="73bce-102">For this application, you will use the [Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-user-details"></a><span data-ttu-id="73bce-103">Получение сведений о пользователе</span><span class="sxs-lookup"><span data-stu-id="73bce-103">Get user details</span></span>

1. <span data-ttu-id="73bce-104">Создайте новый каталог в каталоге **графтуториал** с именем **Graph**.</span><span class="sxs-lookup"><span data-stu-id="73bce-104">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>
1. <span data-ttu-id="73bce-105">Создайте новый файл в каталоге **Graph** с именем **GraphHelper.CS** и добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="73bce-105">Create a new file in the **Graph** directory named **GraphHelper.cs** and add the following code to that file.</span></span>

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
                return await graphClient.Me.Request().GetAsync();
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

<span data-ttu-id="73bce-106">Добавьте следующий код в `Main` **Program.CS** сразу после `GetAccessToken` вызова, чтобы получить пользователя и вывести отображаемое имя пользователя.</span><span class="sxs-lookup"><span data-stu-id="73bce-106">Add the following code in `Main` in **Program.cs** just after the `GetAccessToken` call to get the user and output the user's display name.</span></span>

```csharp
// Initialize Graph client
GraphHelper.Initialize(authProvider);

// Get signed in user
var user = GraphHelper.GetMeAsync().Result;
Console.WriteLine($"Welcome {user.DisplayName}!\n");
```

<span data-ttu-id="73bce-107">Если вы запустите приложение сейчас, после входа в приложение введите имя.</span><span class="sxs-lookup"><span data-stu-id="73bce-107">If you run the app now, after you log in the app welcomes you by name.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="73bce-108">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="73bce-108">Get calendar events from Outlook</span></span>

<span data-ttu-id="73bce-109">Добавьте указанную ниже функцию в `GraphHelper` класс, чтобы получить события из календаря пользователя.</span><span class="sxs-lookup"><span data-stu-id="73bce-109">Add the following function to the `GraphHelper` class to get events from the user's calendar.</span></span>

```csharp
public static async Task<IEnumerable<Event>> GetEventsAsync()
{
    try
    {
        // GET /me/events
        var resultPage = await graphClient.Me.Events.Request()
            // Only return the fields used by the application
            .Select("subject,organizer,start,end")
            // Sort results by when they were created, newest first
            .OrderBy("createdDateTime DESC")
            .GetAsync();

        return resultPage.CurrentPage;
    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error getting events: {ex.Message}");
        return null;
    }
}
```

<span data-ttu-id="73bce-110">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="73bce-110">Consider what this code is doing.</span></span>

- <span data-ttu-id="73bce-111">URL-адрес, который будет вызываться — это `/me/events`.</span><span class="sxs-lookup"><span data-stu-id="73bce-111">The URL that will be called is `/me/events`.</span></span>
- <span data-ttu-id="73bce-112">`Select` Функция ограничит поля, возвращаемые для каждого события, только теми, которые приложение будет использовать в действительности.</span><span class="sxs-lookup"><span data-stu-id="73bce-112">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
- <span data-ttu-id="73bce-113">`OrderBy` Функция сортирует результаты по дате и времени создания, начиная с самого последнего элемента.</span><span class="sxs-lookup"><span data-stu-id="73bce-113">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="73bce-114">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="73bce-114">Display the results</span></span>

1. <span data-ttu-id="73bce-115">Добавьте указанную ниже функцию в `Program` класс, чтобы отформатировать свойства [DateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) из Microsoft Graph в понятный для пользователя формат.</span><span class="sxs-lookup"><span data-stu-id="73bce-115">Add the following function to the `Program` class to format the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) properties from Microsoft Graph into a user-friendly format.</span></span>

```csharp
static string FormatDateTimeTimeZone(Microsoft.Graph.DateTimeTimeZone value)
{
    // Get the timezone specified in the Graph value
    var timeZone = TimeZoneInfo.FindSystemTimeZoneById(value.TimeZone);
    // Parse the date/time string from Graph into a DateTime
    var dateTime = DateTime.Parse(value.DateTime);

    // Create a DateTimeOffset in the specific timezone indicated by Graph
    var dateTimeWithTZ = new DateTimeOffset(dateTime, timeZone.BaseUtcOffset)
        .ToLocalTime();

    return dateTimeWithTZ.ToString("g");
}
```

1. <span data-ttu-id="73bce-116">Добавьте указанную ниже функцию в `Program` класс, чтобы получить события пользователя и вывести их на консоль.</span><span class="sxs-lookup"><span data-stu-id="73bce-116">Add the following function to the `Program` class to get the user's events and output them to the console.</span></span>

```csharp
static void ListCalendarEvents()
{
    var events = GraphHelper.GetEventsAsync().Result;

    Console.WriteLine("Events:");

    foreach (var calendarEvent in events)
    {
        Console.WriteLine($"Subject: {calendarEvent.Subject}");
        Console.WriteLine($"  Organizer: {calendarEvent.Organizer.EmailAddress.Name}");
        Console.WriteLine($"  Start: {FormatDateTimeTimeZone(calendarEvent.Start)}");
        Console.WriteLine($"  End: {FormatDateTimeTimeZone(calendarEvent.End)}");
    }
}
```

<span data-ttu-id="73bce-117">Наконец, добавьте следующий код сразу после `// List the calendar` комментария в `Main` функции.</span><span class="sxs-lookup"><span data-stu-id="73bce-117">Finally, add the following just after the `// List the calendar` comment in the `Main` function.</span></span>

```csharp
ListCalendarEvents();
```

<span data-ttu-id="73bce-118">Сохраните все изменения и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="73bce-118">Save all of your changes and run the app.</span></span> <span data-ttu-id="73bce-119">Выберите параметр **список событий календаря** , чтобы просмотреть список событий пользователя.</span><span class="sxs-lookup"><span data-stu-id="73bce-119">Choose the **List calendar events** option to see a list of the user's events.</span></span>

```Shell
Welcome Adele Vance

Please choose one of the following options:
0. Exit
1. Display access token
2. List calendar events
2
Events:
Subject: Team meeting
  Organizer: Adele Vance
  Start: 5/22/19, 3:00 PM
  End: 5/22/19, 4:00 PM
Subject: Team Lunch
  Organizer: Adele Vance
  Start: 5/24/19, 6:30 PM
  End: 5/24/19, 8:00 PM
Subject: Flight to Redmond
  Organizer: Adele Vance
  Start: 5/26/19, 4:30 PM
  End: 5/26/19, 7:00 PM
Subject: Let's meet to discuss strategy
  Organizer: Patti Fernandez
  Start: 5/27/19, 10:00 PM
  End: 5/27/19, 10:30 PM
Subject: All-hands meeting
  Organizer: Adele Vance
  Start: 5/28/19, 3:30 PM
  End: 5/28/19, 5:00 PM
```
