<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="9988d-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="9988d-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="9988d-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="9988d-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="9988d-103">На этом этапе выполняется интеграция [библиотеки проверки подлинности Microsoft (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) в приложение.</span><span class="sxs-lookup"><span data-stu-id="9988d-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="9988d-104">Инициализируйте [хранилище секрета .NET Development](/aspnet/core/security/app-secrets) , открыв его в каталоге, содержащем **графтуториал. csproj** , и выполнив следующую команду.</span><span class="sxs-lookup"><span data-stu-id="9988d-104">Initialize the [.NET development secret store](/aspnet/core/security/app-secrets) by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

    ```Shell
    dotnet user-secrets init
    ```

1. <span data-ttu-id="9988d-105">Добавьте идентификатор приложения и список требуемых областей в Секретное хранилище с помощью следующих команд.</span><span class="sxs-lookup"><span data-stu-id="9988d-105">Add your application ID and a list of required scopes to the secret store using the following commands.</span></span> <span data-ttu-id="9988d-106">Замените `YOUR_APP_ID_HERE` идентификатором приложения, созданным на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="9988d-106">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;MailboxSettings.Read;Calendars.ReadWrite"
    ```

    <span data-ttu-id="9988d-107">Давайте рассмотрим только что установленные области разрешений.</span><span class="sxs-lookup"><span data-stu-id="9988d-107">Let's look at the permission scopes you just set.</span></span>

    - <span data-ttu-id="9988d-108">Разрешение **User. Read** позволит приложению читать профиль вошедшего пользователя, чтобы получить такие сведения, как отображаемое имя и адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9988d-108">**User.Read** will allow the app to read the signed-in user's profile to get information such as display name and email address.</span></span>
    - <span data-ttu-id="9988d-109">**MailboxSettings. Read** позволяет приложению считывать предпочтительный часовой пояс пользователя, формат даты и формат времени.</span><span class="sxs-lookup"><span data-stu-id="9988d-109">**MailboxSettings.Read** will allow the app to read the user's preferred time zone, date format, and time format.</span></span>
    - <span data-ttu-id="9988d-110">**Calendars. ReadWrite** позволит приложению читать существующие события в календаре пользователя и добавлять новые события.</span><span class="sxs-lookup"><span data-stu-id="9988d-110">**Calendars.ReadWrite** will allow the app to read the existing events on the user's calendar and add new events.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="9988d-111">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="9988d-111">Implement sign-in</span></span>

<span data-ttu-id="9988d-112">В этом разделе описывается создание поставщика проверки подлинности, который можно использовать с пакетом SDK Graph, и его также можно использовать для явного запроса маркера доступа с помощью [кода устройства](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span><span class="sxs-lookup"><span data-stu-id="9988d-112">In this section you will create an authentication provider that can be used with the Graph SDK and can also be used to explicitly request an access token by using the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span></span>

### <a name="create-an-authentication-provider"></a><span data-ttu-id="9988d-113">Создание поставщика проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="9988d-113">Create an authentication provider</span></span>

1. <span data-ttu-id="9988d-114">Создайте новый каталог в каталоге **графтуториал** с именем **authentication**.</span><span class="sxs-lookup"><span data-stu-id="9988d-114">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>
1. <span data-ttu-id="9988d-115">Создайте новый файл в каталоге **проверки подлинности** с именем **DeviceCodeAuthProvider.CS** и добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="9988d-115">Create a new file in the **Authentication** directory named **DeviceCodeAuthProvider.cs** and add the following code to that file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/DeviceCodeAuthProvider.cs" id="AuthProviderSnippet":::

<span data-ttu-id="9988d-116">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="9988d-116">Consider what this code does.</span></span>

- <span data-ttu-id="9988d-117">`IPublicClientApplication`Для запроса и управления маркерами используется реализация MSAL.</span><span class="sxs-lookup"><span data-stu-id="9988d-117">It uses the MSAL `IPublicClientApplication` implementation to request and manage tokens.</span></span>
- <span data-ttu-id="9988d-118">`GetAccessToken`Функция:</span><span class="sxs-lookup"><span data-stu-id="9988d-118">The `GetAccessToken` function:</span></span>
  - <span data-ttu-id="9988d-119">Подписывает пользователя, если они еще не выполнили вход с помощью кода устройства.</span><span class="sxs-lookup"><span data-stu-id="9988d-119">Signs in the user if they are not already signed in using the device code flow.</span></span>
  - <span data-ttu-id="9988d-120">Гарантирует, что возвращаемый маркер всегда обновляется с помощью `AcquireTokenSilent` функции, которая возвращает кэшированный маркер, если срок его действия не истек, и обновляет маркер, если срок его действия истек.</span><span class="sxs-lookup"><span data-stu-id="9988d-120">Ensures that the token returned is always fresh by using the `AcquireTokenSilent` function, which returns the cached token if it not expired, and refreshes the token if it is expired.</span></span>
- <span data-ttu-id="9988d-121">Он реализует `IAuthenticationProvider` интерфейс, чтобы графический пакет SDK мог использовать класс для проверки подлинности графических вызовов.</span><span class="sxs-lookup"><span data-stu-id="9988d-121">It implements the `IAuthenticationProvider` interface so that the Graph SDK can use the class to authenticate Graph calls.</span></span>

## <a name="sign-in-and-display-the-access-token"></a><span data-ttu-id="9988d-122">Вход и отображение маркера доступа</span><span class="sxs-lookup"><span data-stu-id="9988d-122">Sign in and display the access token</span></span>

<span data-ttu-id="9988d-123">В этом разделе вы обновите приложение, чтобы он вызывал `GetAccessToken` функцию, которая будет входить в систему пользователя.</span><span class="sxs-lookup"><span data-stu-id="9988d-123">In this section you will update the application to call the `GetAccessToken` function, which will sign in the user.</span></span> <span data-ttu-id="9988d-124">Кроме того, добавляется код для отображения маркера.</span><span class="sxs-lookup"><span data-stu-id="9988d-124">You will also add code to display the token.</span></span>

1. <span data-ttu-id="9988d-125">Добавьте к классу `Program` следующую функцию:</span><span class="sxs-lookup"><span data-stu-id="9988d-125">Add the following function to the `Program` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="LoadAppSettingsSnippet":::

1. <span data-ttu-id="9988d-126">Добавьте следующий код в `Main` функцию сразу после `Console.WriteLine(".NET Core Graph Tutorial\n");` строки.</span><span class="sxs-lookup"><span data-stu-id="9988d-126">Add the following code to the `Main` function immediately after the `Console.WriteLine(".NET Core Graph Tutorial\n");` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="InitializationSnippet":::

1. <span data-ttu-id="9988d-127">Добавьте следующий код в `Main` функцию сразу после `// Display access token` строки.</span><span class="sxs-lookup"><span data-stu-id="9988d-127">Add the following code to the `Main` function immediately after the `// Display access token` line.</span></span>

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. <span data-ttu-id="9988d-128">Постройте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="9988d-128">Build and run the app.</span></span> <span data-ttu-id="9988d-129">Приложение отображает URL-адрес и код устройства.</span><span class="sxs-lookup"><span data-stu-id="9988d-129">The application displays a URL and device code.</span></span>

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > <span data-ttu-id="9988d-130">При возникновении ошибок Сравните **Program.CS** с [примером на сайте GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs).</span><span class="sxs-lookup"><span data-stu-id="9988d-130">If you encounter errors, compare your **Program.cs** with the [example on GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs).</span></span>

1. <span data-ttu-id="9988d-131">Откройте браузер и перейдите к отображаемому URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="9988d-131">Open a browser and browse to the URL displayed.</span></span> <span data-ttu-id="9988d-132">Введите предоставленный код и войдите в систему.</span><span class="sxs-lookup"><span data-stu-id="9988d-132">Enter the provided code and sign in.</span></span> <span data-ttu-id="9988d-133">После завершения вернитесь к приложению и выберите **1. Показать маркер доступа** для отображения маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="9988d-133">Once completed, return to the application and choose the **1. Display access token** option to display the access token.</span></span>
