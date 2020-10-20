<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c7ceb-101">В этом разделе мы добавим возможность создания событий в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="c7ceb-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="c7ceb-102">Откройте **/граф/графхелпер.КС** и добавьте приведенную ниже функцию в класс **графхелпер** .</span><span class="sxs-lookup"><span data-stu-id="c7ceb-102">Open **./Graph/GraphHelper.cs** and add the following function to the **GraphHelper** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="CreateEventSnippet":::

    <span data-ttu-id="c7ceb-103">Этот код инициализирует объект **event** и использует Graph SDK для добавления его в календарь пользователя.</span><span class="sxs-lookup"><span data-stu-id="c7ceb-103">This code initializes an **Event** object and uses the Graph SDK to add it to the user's calendar.</span></span>

1. <span data-ttu-id="c7ceb-104">Откройте **./Program.CS** и добавьте указанные ниже функции в класс **Program** .</span><span class="sxs-lookup"><span data-stu-id="c7ceb-104">Open **./Program.cs** and add the following functions to the **Program** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="UserInputSnippet":::

    <span data-ttu-id="c7ceb-105">Эти функции являются вспомогательными функциями для чтения вводимых пользователем данных.</span><span class="sxs-lookup"><span data-stu-id="c7ceb-105">These functions are helper functions for reading user input.</span></span>

1. <span data-ttu-id="c7ceb-106">Добавьте указанную ниже функцию в класс **Program** .</span><span class="sxs-lookup"><span data-stu-id="c7ceb-106">Add the following function to the **Program** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="CreateEventSnippet":::

    <span data-ttu-id="c7ceb-107">Эта функция запрашивает у пользователя тему, участников, начало, конец и текст, а затем использует эти значения для вызова `GraphHelper.CreateEvent` .</span><span class="sxs-lookup"><span data-stu-id="c7ceb-107">This function prompts the user for subject, attendees, start, end, and body, then uses those values to call `GraphHelper.CreateEvent`.</span></span>

1. <span data-ttu-id="c7ceb-108">Добавьте следующий код сразу после `// Create a new event` комментария в `Main` функции.</span><span class="sxs-lookup"><span data-stu-id="c7ceb-108">Add the following just after the `// Create a new event` comment in the `Main` function.</span></span>

    ```csharp
    CreateEvent(user.MailboxSettings.TimeZone);
    ```

1. <span data-ttu-id="c7ceb-109">Сохраните все изменения и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="c7ceb-109">Save all of your changes and run the app.</span></span> <span data-ttu-id="c7ceb-110">Выберите параметр **Добавить событие** .</span><span class="sxs-lookup"><span data-stu-id="c7ceb-110">Choose the **Add an event** option.</span></span> <span data-ttu-id="c7ceb-111">Ответьте на приглашения, чтобы создать новое событие в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="c7ceb-111">Respond to the prompts to create a new event on the user's calendar.</span></span>
