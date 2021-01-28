<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы включаете Microsoft Graph в приложение. Для этого приложения вы будете использовать клиентскую библиотеку [Microsoft Graph .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) для вызова Microsoft Graph.

## <a name="get-user-details"></a>Получить сведения о пользователе

1. Создайте новый каталог в **каталоге GraphTutorial** с именем **Graph.**
1. Создайте файл в **каталоге Graph** с именем **GraphHelper.cs** добавьте в этот файл следующий код.

    ```csharp
    using Microsoft.Graph;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using TimeZoneConverter;

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

1. Добавьте следующий код в ./Program.cs сразу после вызова, чтобы получить пользователя и выходные `Main`  `GetAccessToken` отображаемом имени пользователя.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

Если вы запустите приложение сейчас, после входа в приложение вас приветствует по имени.

## <a name="get-a-calendar-view"></a>Просмотр календаря

1. Добавьте в класс следующую функцию для получения событий из `GraphHelper` календаря пользователя.

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

Подумайте, что делает этот код.

- Будет вызван URL-адрес `/me/calendarview` .
- Параметры `startDateTime` `endDateTime` и параметров определяют начало и конец представления календаря.
- Заглавная часть приводит к возвращению событий в часовом поясе `Prefer: outlook.timezone` `start` `end` пользователя.
- Функция `Top` запрашивает не более 50 событий.
- Функция `Select` ограничивает поля, возвращаемые для каждого события, только теми, которые будут фактически использовать приложение.
- Функция `OrderBy` сортировать результаты по дате и времени начала.

## <a name="display-the-results"></a>Отображение результатов

1. Добавьте в класс следующую функцию для формата свойств `Program` [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) из Microsoft Graph в удобном для пользователя формате.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. Добавьте в класс следующую функцию для получения событий пользователя и вывода `Program` их в консоль.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. Добавьте следующее сразу после `// List the calendar` комментария в `Main` функции.

    ```csharp
    ListCalendarEvents(
        user.MailboxSettings.TimeZone,
        $"{user.MailboxSettings.DateFormat} {user.MailboxSettings.TimeFormat}"
    );
    ```

1. Сохраните все изменения и запустите приложение. Выберите параметр **"Просмотреть календарь** на этой неделе", чтобы просмотреть список событий пользователя.

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
