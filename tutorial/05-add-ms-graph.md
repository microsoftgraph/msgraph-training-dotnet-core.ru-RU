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

1. Добавьте следующий код в `Main` **Program.CS** сразу после `GetAccessToken` вызова, чтобы получить пользователя и вывести отображаемое имя пользователя.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

Если вы запустите приложение сейчас, после входа в приложение введите имя.

## <a name="get-calendar-events-from-outlook"></a>Получение событий календаря из Outlook

1. Добавьте указанную ниже функцию в `GraphHelper` класс, чтобы получить события из календаря пользователя.

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

Рассмотрите, что делает этот код.

- URL-адрес, который будет вызываться — это `/me/events`.
- `Select` Функция ограничит поля, возвращаемые для каждого события, только теми, которые приложение будет использовать в действительности.
- `OrderBy` Функция сортирует результаты по дате и времени создания, начиная с самого последнего элемента.

## <a name="display-the-results"></a>Отображение результатов

1. Добавьте указанную ниже функцию в `Program` класс, чтобы отформатировать свойства [DateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) из Microsoft Graph в понятный для пользователя формат.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. Добавьте указанную ниже функцию в `Program` класс, чтобы получить события пользователя и вывести их на консоль.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. Добавьте следующий код сразу после `// List the calendar` комментария в `Main` функции.

    ```csharp
    ListCalendarEvents();
    ```

1. Сохраните все изменения и запустите приложение. Выберите параметр **список событий календаря** , чтобы просмотреть список событий пользователя.

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
