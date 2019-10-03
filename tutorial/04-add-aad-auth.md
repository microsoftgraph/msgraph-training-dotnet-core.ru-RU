<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e338f-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="e338f-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="e338f-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="e338f-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="e338f-103">На этом этапе выполняется интеграция [библиотеки проверки подлинности Microsoft (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) в приложение.</span><span class="sxs-lookup"><span data-stu-id="e338f-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="e338f-104">Создайте новый файл в каталоге **графтуториал** с именем **appSettings. JSON**.</span><span class="sxs-lookup"><span data-stu-id="e338f-104">Create a new file in the **GraphTutorial** directory named **appsettings.json**.</span></span> <span data-ttu-id="e338f-105">Добавьте в этот файл приведенный ниже текст.</span><span class="sxs-lookup"><span data-stu-id="e338f-105">Add the following text in that file.</span></span>

```json
{
  "appId": "YOUR_APP_ID_HERE",
  "scopes": [
    "User.Read",
    "Calendars.Read"
  ]
}
```

<span data-ttu-id="e338f-106">Замените `YOUR_APP_ID_HERE` идентификатором приложения, созданным на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="e338f-106">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e338f-107">Если вы используете систему управления версиями (например, Git), то теперь будет полезно исключить файл **appSettings. JSON** из системы управления версиями, чтобы избежать случайной утечки идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="e338f-107">If you're using source control such as git, now would be a good time to exclude the **appsettings.json** file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="e338f-108">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="e338f-108">Implement sign-in</span></span>

<span data-ttu-id="e338f-109">В этом разделе описывается создание поставщика проверки подлинности, который можно использовать с пакетом SDK Graph, и его также можно использовать для явного запроса маркера доступа с помощью [кода устройства](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span><span class="sxs-lookup"><span data-stu-id="e338f-109">In this section you will create an authentication provider that can be used with the Graph SDK and can also be used to explicitly request an access token by using the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span></span>

### <a name="create-an-authentication-provider"></a><span data-ttu-id="e338f-110">Создание поставщика проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="e338f-110">Create an authentication provider</span></span>

1. <span data-ttu-id="e338f-111">Создайте новый каталог в каталоге **графтуториал** с именем **authentication**.</span><span class="sxs-lookup"><span data-stu-id="e338f-111">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>
1. <span data-ttu-id="e338f-112">Создайте новый файл в каталоге **проверки подлинности** с именем **DeviceCodeAuthProvider.CS** и добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="e338f-112">Create a new file in the **Authentication** directory named **DeviceCodeAuthProvider.cs** and add the following code to that file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using Microsoft.Identity.Client;
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace GraphTutorial
    {
        public class DeviceCodeAuthProvider : IAuthenticationProvider
        {
            private IPublicClientApplication _msalClient;
            private string[] _scopes;
            private IAccount _userAccount;

            public DeviceCodeAuthProvider(string appId, string[] scopes)
            {
                _scopes = scopes;

                _msalClient = PublicClientApplicationBuilder
                    .Create(appId)
                    // Set the tenant ID to "organizations" to disable personal accounts
                    // Azure OAuth does not support device code flow for personal accounts
                    // See https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code
                    .WithTenantId("organizations")
                    .Build();
            }

            public async Task<string> GetAccessToken()
            {
                // If there is no saved user account, the user must sign-in
                if (_userAccount == null)
                {
                    try
                    {
                        // Invoke device code flow so user can sign-in with a browser
                        var result = await _msalClient.AcquireTokenWithDeviceCode(_scopes, callback => {
                            Console.WriteLine(callback.Message);
                            return Task.FromResult(0);
                        }).ExecuteAsync();

                        _userAccount = result.Account;
                        return result.AccessToken;
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"Error getting access token: {exception.Message}");
                        return null;
                    }
                }
                else
                {
                    // If there is an account, call AcquireTokenSilent
                    // By doing this, MSAL will refresh the token automatically if
                    // it is expired. Otherwise it returns the cached token.

                        var result = await _msalClient
                            .AcquireTokenSilent(_scopes, _userAccount)
                            .ExecuteAsync();

                       return result.AccessToken;
                }
            }

            // This is the required function to implement IAuthenticationProvider
            // The Graph SDK will call this function each time it makes a Graph
            // call.
            public async Task AuthenticateRequestAsync(HttpRequestMessage requestMessage)
            {
                requestMessage.Headers.Authorization =
                    new AuthenticationHeaderValue("bearer", await GetAccessToken());
            }
        }
    }
    ```

<span data-ttu-id="e338f-113">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="e338f-113">Consider what this code does.</span></span>

- <span data-ttu-id="e338f-114">Для запроса и управления `IPublicClientApplication` маркерами используется реализация MSAL.</span><span class="sxs-lookup"><span data-stu-id="e338f-114">It uses the MSAL `IPublicClientApplication` implementation to request and manage tokens.</span></span>
- <span data-ttu-id="e338f-115">`GetAccessToken` Функция:</span><span class="sxs-lookup"><span data-stu-id="e338f-115">The `GetAccessToken` function:</span></span>
  - <span data-ttu-id="e338f-116">Подписывает пользователя, если они еще не выполнили вход с помощью кода устройства.</span><span class="sxs-lookup"><span data-stu-id="e338f-116">Signs in the user if they are not already signed in using the device code flow.</span></span>
  - <span data-ttu-id="e338f-117">Гарантирует, что возвращаемый маркер всегда обновляется с помощью `AcquireTokenSilent` функции, которая возвращает кэшированный маркер, если срок его действия не истек, и обновляет маркер, если срок его действия истек.</span><span class="sxs-lookup"><span data-stu-id="e338f-117">Ensures that the token returned is always fresh by using the `AcquireTokenSilent` function, which returns the cached token if it not expired, and refreshes the token if it is expired.</span></span>
- <span data-ttu-id="e338f-118">Он реализует `IAuthenticationProvider` интерфейс, чтобы графический пакет SDK мог использовать класс для проверки подлинности графических вызовов.</span><span class="sxs-lookup"><span data-stu-id="e338f-118">It implements the `IAuthenticationProvider` interface so that the Graph SDK can use the class to authenticate Graph calls.</span></span>

## <a name="sign-in-and-display-the-access-token"></a><span data-ttu-id="e338f-119">Вход и отображение маркера доступа</span><span class="sxs-lookup"><span data-stu-id="e338f-119">Sign in and display the access token</span></span>

<span data-ttu-id="e338f-120">В этом разделе вы обновите приложение, чтобы он вызывал `GetAccessToken` функцию, которая будет входить в систему пользователя.</span><span class="sxs-lookup"><span data-stu-id="e338f-120">In this section you will update the application to call the `GetAccessToken` function, which will sign in the user.</span></span> <span data-ttu-id="e338f-121">Кроме того, добавляется код для отображения маркера.</span><span class="sxs-lookup"><span data-stu-id="e338f-121">You will also add code to display the token.</span></span>

1. <span data-ttu-id="e338f-122">Откройте **Program.CS** и добавьте приведенные `using` ниже операторы в начало файла.</span><span class="sxs-lookup"><span data-stu-id="e338f-122">Open **Program.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Microsoft.Extensions.Configuration;
    using System.IO;
    ```

1. <span data-ttu-id="e338f-123">Добавьте к классу `Program` следующую функцию:</span><span class="sxs-lookup"><span data-stu-id="e338f-123">Add the following function to the `Program` class.</span></span>

    ```csharp
    static IConfigurationRoot LoadAppSettings()
    {
        var appConfig = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json", false, true)
            .Build();

        // Check for required settings
        if (string.IsNullOrEmpty(appConfig["appId"]) ||
            // Make sure there's at least one value in the scopes array
            string.IsNullOrEmpty(appConfig["scopes:0"]))
        {
            return null;
        }

        return appConfig;
    }
    ```

1. <span data-ttu-id="e338f-124">Добавьте следующий код в `Main` функцию сразу после `Console.WriteLine(".NET Core Graph Tutorial\n");` строки.</span><span class="sxs-lookup"><span data-stu-id="e338f-124">Add the following code to the `Main` function immediately after the `Console.WriteLine(".NET Core Graph Tutorial\n");` line.</span></span>

    ```csharp
    var appConfig = LoadAppSettings();

    if (appConfig == null)
    {
        Console.WriteLine("Missing or invalid appsettings.json...exiting");
        return;
    }

    var appId = appConfig["appId"];
    var scopes = appConfig.GetSection("scopes").Get<string[]>();

    // Initialize the auth provider with values from appsettings.json
    var authProvider = new DeviceCodeAuthProvider(appId, scopes);

    // Request a token to sign in the user
    var accessToken = authProvider.GetAccessToken().Result;
    ```

1. <span data-ttu-id="e338f-125">Добавьте следующий код в `Main` функцию сразу после `// Display access token` строки.</span><span class="sxs-lookup"><span data-stu-id="e338f-125">Add the following code to the `Main` function immediately after the `// Display access token` line.</span></span>

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. <span data-ttu-id="e338f-126">Постройте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="e338f-126">Build and run the app.</span></span> <span data-ttu-id="e338f-127">Приложение отображает URL-адрес и код устройства.</span><span class="sxs-lookup"><span data-stu-id="e338f-127">The application displays a URL and device code.</span></span>

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > <span data-ttu-id="e338f-128">При возникновении ошибок Сравните **Program.CS** с [примером на сайте GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs).</span><span class="sxs-lookup"><span data-stu-id="e338f-128">If you encounter errors, compare your **Program.cs** with the [example on GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs).</span></span>

1. <span data-ttu-id="e338f-129">Откройте браузер и перейдите к отображаемому URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="e338f-129">Open a browser and browse to the URL displayed.</span></span> <span data-ttu-id="e338f-130">Введите предоставленный код и войдите в систему.</span><span class="sxs-lookup"><span data-stu-id="e338f-130">Enter the provided code and sign in.</span></span> <span data-ttu-id="e338f-131">После завершения вернитесь к приложению и выберите **1. Показать маркер доступа** для отображения маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="e338f-131">Once completed, return to the application and choose the **1. Display access token** option to display the access token.</span></span>
