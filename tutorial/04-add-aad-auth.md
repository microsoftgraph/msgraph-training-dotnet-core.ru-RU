<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="fbb73-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="fbb73-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="fbb73-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="fbb73-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="fbb73-103">На этом этапе выполняется интеграция [библиотеки проверки подлинности Microsoft (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) в приложение.</span><span class="sxs-lookup"><span data-stu-id="fbb73-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="fbb73-104">Инициализируйте [хранилище секрета .NET Development](/aspnet/core/security/app-secrets) , открыв его в каталоге, содержащем **графтуториал. csproj** , и выполнив следующую команду.</span><span class="sxs-lookup"><span data-stu-id="fbb73-104">Initialize the [.NET development secret store](/aspnet/core/security/app-secrets) by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

    ```Shell
    dotnet user-secrets init
    ```

1. <span data-ttu-id="fbb73-105">Добавьте идентификатор приложения и список требуемых областей в Секретное хранилище с помощью следующих команд.</span><span class="sxs-lookup"><span data-stu-id="fbb73-105">Add your application ID and a list of required scopes to the secret store using the following commands.</span></span> <span data-ttu-id="fbb73-106">Замените `YOUR_APP_ID_HERE` идентификатором приложения, созданным на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="fbb73-106">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;Calendars.Read"
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="fbb73-107">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="fbb73-107">Implement sign-in</span></span>

<span data-ttu-id="fbb73-108">В этом разделе описывается создание поставщика проверки подлинности, который можно использовать с пакетом SDK Graph, и его также можно использовать для явного запроса маркера доступа с помощью [кода устройства](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span><span class="sxs-lookup"><span data-stu-id="fbb73-108">In this section you will create an authentication provider that can be used with the Graph SDK and can also be used to explicitly request an access token by using the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span></span>

### <a name="create-an-authentication-provider"></a><span data-ttu-id="fbb73-109">Создание поставщика проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="fbb73-109">Create an authentication provider</span></span>

1. <span data-ttu-id="fbb73-110">Создайте новый каталог в каталоге **графтуториал** с именем **authentication**.</span><span class="sxs-lookup"><span data-stu-id="fbb73-110">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>
1. <span data-ttu-id="fbb73-111">Создайте новый файл в каталоге **проверки подлинности** с именем **DeviceCodeAuthProvider.CS** и добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="fbb73-111">Create a new file in the **Authentication** directory named **DeviceCodeAuthProvider.cs** and add the following code to that file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/DeviceCodeAuthProvider.cs" id="AuthProviderSnippet":::

<span data-ttu-id="fbb73-112">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="fbb73-112">Consider what this code does.</span></span>

- <span data-ttu-id="fbb73-113">Для запроса и управления `IPublicClientApplication` маркерами используется реализация MSAL.</span><span class="sxs-lookup"><span data-stu-id="fbb73-113">It uses the MSAL `IPublicClientApplication` implementation to request and manage tokens.</span></span>
- <span data-ttu-id="fbb73-114">`GetAccessToken` Функция:</span><span class="sxs-lookup"><span data-stu-id="fbb73-114">The `GetAccessToken` function:</span></span>
  - <span data-ttu-id="fbb73-115">Подписывает пользователя, если они еще не выполнили вход с помощью кода устройства.</span><span class="sxs-lookup"><span data-stu-id="fbb73-115">Signs in the user if they are not already signed in using the device code flow.</span></span>
  - <span data-ttu-id="fbb73-116">Гарантирует, что возвращаемый маркер всегда обновляется с помощью `AcquireTokenSilent` функции, которая возвращает кэшированный маркер, если срок его действия не истек, и обновляет маркер, если срок его действия истек.</span><span class="sxs-lookup"><span data-stu-id="fbb73-116">Ensures that the token returned is always fresh by using the `AcquireTokenSilent` function, which returns the cached token if it not expired, and refreshes the token if it is expired.</span></span>
- <span data-ttu-id="fbb73-117">Он реализует `IAuthenticationProvider` интерфейс, чтобы графический пакет SDK мог использовать класс для проверки подлинности графических вызовов.</span><span class="sxs-lookup"><span data-stu-id="fbb73-117">It implements the `IAuthenticationProvider` interface so that the Graph SDK can use the class to authenticate Graph calls.</span></span>

## <a name="sign-in-and-display-the-access-token"></a><span data-ttu-id="fbb73-118">Вход и отображение маркера доступа</span><span class="sxs-lookup"><span data-stu-id="fbb73-118">Sign in and display the access token</span></span>

<span data-ttu-id="fbb73-119">В этом разделе вы обновите приложение, чтобы он вызывал `GetAccessToken` функцию, которая будет входить в систему пользователя.</span><span class="sxs-lookup"><span data-stu-id="fbb73-119">In this section you will update the application to call the `GetAccessToken` function, which will sign in the user.</span></span> <span data-ttu-id="fbb73-120">Кроме того, добавляется код для отображения маркера.</span><span class="sxs-lookup"><span data-stu-id="fbb73-120">You will also add code to display the token.</span></span>

1. <span data-ttu-id="fbb73-121">Добавьте к классу `Program` следующую функцию:</span><span class="sxs-lookup"><span data-stu-id="fbb73-121">Add the following function to the `Program` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="LoadAppSettingsSnippet":::

1. <span data-ttu-id="fbb73-122">Добавьте следующий код в `Main` функцию сразу после `Console.WriteLine(".NET Core Graph Tutorial\n");` строки.</span><span class="sxs-lookup"><span data-stu-id="fbb73-122">Add the following code to the `Main` function immediately after the `Console.WriteLine(".NET Core Graph Tutorial\n");` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="InitializationSnippet":::

1. <span data-ttu-id="fbb73-123">Добавьте следующий код в `Main` функцию сразу после `// Display access token` строки.</span><span class="sxs-lookup"><span data-stu-id="fbb73-123">Add the following code to the `Main` function immediately after the `// Display access token` line.</span></span>

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. <span data-ttu-id="fbb73-124">Постройте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="fbb73-124">Build and run the app.</span></span> <span data-ttu-id="fbb73-125">Приложение отображает URL-адрес и код устройства.</span><span class="sxs-lookup"><span data-stu-id="fbb73-125">The application displays a URL and device code.</span></span>

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > <span data-ttu-id="fbb73-126">При возникновении ошибок Сравните **Program.CS** с [примером на сайте GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs).</span><span class="sxs-lookup"><span data-stu-id="fbb73-126">If you encounter errors, compare your **Program.cs** with the [example on GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs).</span></span>

1. <span data-ttu-id="fbb73-127">Откройте браузер и перейдите к отображаемому URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="fbb73-127">Open a browser and browse to the URL displayed.</span></span> <span data-ttu-id="fbb73-128">Введите предоставленный код и войдите в систему.</span><span class="sxs-lookup"><span data-stu-id="fbb73-128">Enter the provided code and sign in.</span></span> <span data-ttu-id="fbb73-129">После завершения вернитесь к приложению и выберите **1. Показать маркер доступа** для отображения маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="fbb73-129">Once completed, return to the application and choose the **1. Display access token** option to display the access token.</span></span>
