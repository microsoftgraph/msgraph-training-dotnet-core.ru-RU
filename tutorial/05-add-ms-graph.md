<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы добавите Microsoft Graph в приложение. Для этого приложения вы будете использовать [клиентскую библиотеку Microsoft Graph .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , чтобы совершать вызовы в Microsoft Graph.

## <a name="get-user-details"></a>Получение сведений о пользователе

1. Создайте новый каталог в каталоге **графтуториал** с именем **Graph**.
1. Создайте новый файл в каталоге **Graph** с именем **GraphHelper.CS** и добавьте в этот файл следующий код.

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

1. Добавьте следующий код в файл `Main` **./Program.CS** сразу после вызова, `GetAccessToken` чтобы получить пользователя и вывести отображаемое имя пользователя.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

Если вы запустите приложение сейчас, после входа в приложение введите имя.

## <a name="get-a-calendar-view"></a>Получение представления календаря

1. Добавьте указанную ниже функцию в `GraphHelper` класс, чтобы получить события из календаря пользователя.

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

Рассмотрите, что делает этот код.

- URL-адрес, который будет вызываться — это `/me/calendarview` .
- `startDateTime`Параметры и `endDateTime` задают начало и конец представления календаря.
- `Prefer: outlook.timezone`Заголовок приводит к `start` `end` возвращению и записи событий в часовом поясе пользователя.
- `Top`Функция запрашивает не более 50 событий.
- `Select`Функция ограничит поля, возвращаемые для каждого события, только теми, которые приложение будет использовать в действительности.
- `OrderBy`Функция сортирует результаты по дате и времени начала.

## <a name="display-the-results"></a>Отображение результатов

1. Добавьте указанную ниже функцию в `Program` класс, чтобы отформатировать свойства [DateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) из Microsoft Graph в понятный для пользователя формат.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. Добавьте указанную ниже функцию в `Program` класс, чтобы получить события пользователя и вывести их на консоль.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. Добавьте следующий код сразу после `// List the calendar` комментария в `Main` функции.

    ```csharp
    ListCalendarEvents(
        user.MailboxSettings.TimeZone,
        $"{user.MailboxSettings.DateFormat} {user.MailboxSettings.TimeFormat}"
    );
    ```

1. Сохраните все изменения и запустите приложение. Выберите параметр **Просмотреть календарь этой недели** , чтобы просмотреть список событий пользователя.

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
