<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="cae2e-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="cae2e-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="cae2e-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="cae2e-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="cae2e-103">На этом этапе выполняется интеграция [библиотеки проверки подлинности Microsoft (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) в приложение.</span><span class="sxs-lookup"><span data-stu-id="cae2e-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="cae2e-104">Инициализируйте [хранилище секрета .NET Development](https://docs.microsoft.com/aspnet/core/security/app-secrets) , открыв его в каталоге, содержащем **графтуториал. csproj** , и выполнив следующую команду.</span><span class="sxs-lookup"><span data-stu-id="cae2e-104">Initialize the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets) by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

```Shell
dotnet user-secrets init
```

<span data-ttu-id="cae2e-105">Затем добавьте идентификатор приложения и список требуемых областей в Секретное хранилище с помощью следующих команд.</span><span class="sxs-lookup"><span data-stu-id="cae2e-105">Next, add your application ID and a list of required scopes to the secret store using the following commands.</span></span> <span data-ttu-id="cae2e-106">Замените `YOUR_APP_ID_HERE` идентификатором приложения, созданным на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="cae2e-106">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

```Shell
dotnet user-secrets set appId "YOUR_APP_ID_HERE"
dotnet user-secrets set scopes "User.Read;Calendars.Read"
```

## <a name="implement-sign-in"></a><span data-ttu-id="cae2e-107">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="cae2e-107">Implement sign-in</span></span>

<span data-ttu-id="cae2e-108">В этом разделе описывается создание поставщика проверки подлинности, который можно использовать с пакетом SDK Graph, и его также можно использовать для явного запроса маркера доступа с помощью [кода устройства](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span><span class="sxs-lookup"><span data-stu-id="cae2e-108">In this section you will create an authentication provider that can be used with the Graph SDK and can also be used to explicitly request an access token by using the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span></span>

### <a name="create-an-authentication-provider"></a><span data-ttu-id="cae2e-109">Создание поставщика проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="cae2e-109">Create an authentication provider</span></span>

1. <span data-ttu-id="cae2e-110">Создайте новый каталог в каталоге **графтуториал** с именем **authentication**.</span><span class="sxs-lookup"><span data-stu-id="cae2e-110">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>
1. <span data-ttu-id="cae2e-111">Создайте новый файл в каталоге **проверки подлинности** с именем **DeviceCodeAuthProvider.CS** и добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="cae2e-111">Create a new file in the **Authentication** directory named **DeviceCodeAuthProvider.cs** and add the following code to that file.</span></span>

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
                    .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount, true)
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

<span data-ttu-id="cae2e-112">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="cae2e-112">Consider what this code does.</span></span>

- <span data-ttu-id="cae2e-113">Для запроса и управления `IPublicClientApplication` маркерами используется реализация MSAL.</span><span class="sxs-lookup"><span data-stu-id="cae2e-113">It uses the MSAL `IPublicClientApplication` implementation to request and manage tokens.</span></span>
- <span data-ttu-id="cae2e-114">`GetAccessToken` Функция:</span><span class="sxs-lookup"><span data-stu-id="cae2e-114">The `GetAccessToken` function:</span></span>
  - <span data-ttu-id="cae2e-115">Подписывает пользователя, если они еще не выполнили вход с помощью кода устройства.</span><span class="sxs-lookup"><span data-stu-id="cae2e-115">Signs in the user if they are not already signed in using the device code flow.</span></span>
  - <span data-ttu-id="cae2e-116">Гарантирует, что возвращаемый маркер всегда обновляется с помощью `AcquireTokenSilent` функции, которая возвращает кэшированный маркер, если срок его действия не истек, и обновляет маркер, если срок его действия истек.</span><span class="sxs-lookup"><span data-stu-id="cae2e-116">Ensures that the token returned is always fresh by using the `AcquireTokenSilent` function, which returns the cached token if it not expired, and refreshes the token if it is expired.</span></span>
- <span data-ttu-id="cae2e-117">Он реализует `IAuthenticationProvider` интерфейс, чтобы графический пакет SDK мог использовать класс для проверки подлинности графических вызовов.</span><span class="sxs-lookup"><span data-stu-id="cae2e-117">It implements the `IAuthenticationProvider` interface so that the Graph SDK can use the class to authenticate Graph calls.</span></span>

## <a name="sign-in-and-display-the-access-token"></a><span data-ttu-id="cae2e-118">Вход и отображение маркера доступа</span><span class="sxs-lookup"><span data-stu-id="cae2e-118">Sign in and display the access token</span></span>

<span data-ttu-id="cae2e-119">В этом разделе вы обновите приложение, чтобы он вызывал `GetAccessToken` функцию, которая будет входить в систему пользователя.</span><span class="sxs-lookup"><span data-stu-id="cae2e-119">In this section you will update the application to call the `GetAccessToken` function, which will sign in the user.</span></span> <span data-ttu-id="cae2e-120">Кроме того, добавляется код для отображения маркера.</span><span class="sxs-lookup"><span data-stu-id="cae2e-120">You will also add code to display the token.</span></span>

1. <span data-ttu-id="cae2e-121">Откройте **Program.CS** и добавьте приведенные `using` ниже операторы в начало файла.</span><span class="sxs-lookup"><span data-stu-id="cae2e-121">Open **Program.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. <span data-ttu-id="cae2e-122">Добавьте к классу `Program` следующую функцию:</span><span class="sxs-lookup"><span data-stu-id="cae2e-122">Add the following function to the `Program` class.</span></span>

    ```csharp
    static IConfigurationRoot LoadAppSettings()
    {
        var appConfig = new ConfigurationBuilder()
            .AddUserSecrets<Program>()
            .Build();

        // Check for required settings
        if (string.IsNullOrEmpty(appConfig["appId"]) ||
            string.IsNullOrEmpty(appConfig["scopes"]))
        {
            return null;
        }

        return appConfig;
    }
    ```

1. <span data-ttu-id="cae2e-123">Добавьте следующий код в `Main` функцию сразу после `Console.WriteLine(".NET Core Graph Tutorial\n");` строки.</span><span class="sxs-lookup"><span data-stu-id="cae2e-123">Add the following code to the `Main` function immediately after the `Console.WriteLine(".NET Core Graph Tutorial\n");` line.</span></span>

    ```csharp
    var appConfig = LoadAppSettings();

    if (appConfig == null)
    {
        Console.WriteLine("Missing or invalid appsettings.json...exiting");
        return;
    }

    var appId = appConfig["appId"];
    var scopesString = appConfig["scopes"];
    var scopes = scopesString.Split(';');

    // Initialize the auth provider with values from appsettings.json
    var authProvider = new DeviceCodeAuthProvider(appId, scopes);

    // Request a token to sign in the user
    var accessToken = authProvider.GetAccessToken().Result;
    ```

1. <span data-ttu-id="cae2e-124">Добавьте следующий код в `Main` функцию сразу после `// Display access token` строки.</span><span class="sxs-lookup"><span data-stu-id="cae2e-124">Add the following code to the `Main` function immediately after the `// Display access token` line.</span></span>

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. <span data-ttu-id="cae2e-125">Постройте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="cae2e-125">Build and run the app.</span></span> <span data-ttu-id="cae2e-126">Приложение отображает URL-адрес и код устройства.</span><span class="sxs-lookup"><span data-stu-id="cae2e-126">The application displays a URL and device code.</span></span>

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > <span data-ttu-id="cae2e-127">При возникновении ошибок Сравните **Program.CS** с [примером на сайте GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs).</span><span class="sxs-lookup"><span data-stu-id="cae2e-127">If you encounter errors, compare your **Program.cs** with the [example on GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs).</span></span>

1. <span data-ttu-id="cae2e-128">Откройте браузер и перейдите к отображаемому URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="cae2e-128">Open a browser and browse to the URL displayed.</span></span> <span data-ttu-id="cae2e-129">Введите предоставленный код и войдите в систему.</span><span class="sxs-lookup"><span data-stu-id="cae2e-129">Enter the provided code and sign in.</span></span> <span data-ttu-id="cae2e-130">После завершения вернитесь к приложению и выберите **1. Показать маркер доступа** для отображения маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="cae2e-130">Once completed, return to the application and choose the **1. Display access token** option to display the access token.</span></span>
