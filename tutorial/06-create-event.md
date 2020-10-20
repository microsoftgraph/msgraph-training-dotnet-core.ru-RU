<!-- markdownlint-disable MD002 MD041 -->

В этом разделе мы добавим возможность создания событий в календаре пользователя.

1. Откройте **/граф/графхелпер.КС** и добавьте приведенную ниже функцию в класс **графхелпер** .

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="CreateEventSnippet":::

    Этот код инициализирует объект **event** и использует Graph SDK для добавления его в календарь пользователя.

1. Откройте **./Program.CS** и добавьте указанные ниже функции в класс **Program** .

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="UserInputSnippet":::

    Эти функции являются вспомогательными функциями для чтения вводимых пользователем данных.

1. Добавьте указанную ниже функцию в класс **Program** .

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="CreateEventSnippet":::

    Эта функция запрашивает у пользователя тему, участников, начало, конец и текст, а затем использует эти значения для вызова `GraphHelper.CreateEvent` .

1. Добавьте следующий код сразу после `// Create a new event` комментария в `Main` функции.

    ```csharp
    CreateEvent(user.MailboxSettings.TimeZone);
    ```

1. Сохраните все изменения и запустите приложение. Выберите параметр **Добавить событие** . Ответьте на приглашения, чтобы создать новое событие в календаре пользователя.
