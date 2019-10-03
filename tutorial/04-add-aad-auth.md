<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. На этом этапе выполняется интеграция [библиотеки проверки подлинности Microsoft (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) в приложение.

Создайте новый файл в каталоге **графтуториал** с именем **appSettings. JSON**. Добавьте в этот файл приведенный ниже текст.

```json
{
  "appId": "YOUR_APP_ID_HERE",
  "scopes": [
    "User.Read",
    "Calendars.Read"
  ]
}
```

Замените `YOUR_APP_ID_HERE` идентификатором приложения, созданным на портале Azure.

> [!IMPORTANT]
> Если вы используете систему управления версиями (например, Git), то теперь будет полезно исключить файл **appSettings. JSON** из системы управления версиями, чтобы избежать случайной утечки идентификатора приложения.

## <a name="implement-sign-in"></a>Реализация входа

В этом разделе описывается создание поставщика проверки подлинности, который можно использовать с пакетом SDK Graph, и его также можно использовать для явного запроса маркера доступа с помощью [кода устройства](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).

### <a name="create-an-authentication-provider"></a>Создание поставщика проверки подлинности

1. Создайте новый каталог в каталоге **графтуториал** с именем **authentication**.
1. Создайте новый файл в каталоге **проверки подлинности** с именем **DeviceCodeAuthProvider.CS** и добавьте в этот файл следующий код.

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

Рассмотрите, что делает этот код.

- Для запроса и управления `IPublicClientApplication` маркерами используется реализация MSAL.
- `GetAccessToken` Функция:
  - Подписывает пользователя, если они еще не выполнили вход с помощью кода устройства.
  - Гарантирует, что возвращаемый маркер всегда обновляется с помощью `AcquireTokenSilent` функции, которая возвращает кэшированный маркер, если срок его действия не истек, и обновляет маркер, если срок его действия истек.
- Он реализует `IAuthenticationProvider` интерфейс, чтобы графический пакет SDK мог использовать класс для проверки подлинности графических вызовов.

## <a name="sign-in-and-display-the-access-token"></a>Вход и отображение маркера доступа

В этом разделе вы обновите приложение, чтобы он вызывал `GetAccessToken` функцию, которая будет входить в систему пользователя. Кроме того, добавляется код для отображения маркера.

1. Откройте **Program.CS** и добавьте приведенные `using` ниже операторы в начало файла.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using System.IO;
    ```

1. Добавьте к классу `Program` следующую функцию:

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

1. Добавьте следующий код в `Main` функцию сразу после `Console.WriteLine(".NET Core Graph Tutorial\n");` строки.

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

1. Добавьте следующий код в `Main` функцию сразу после `// Display access token` строки.

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. Постройте и запустите приложение. Приложение отображает URL-адрес и код устройства.

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > При возникновении ошибок Сравните **Program.CS** с [примером на сайте GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs).

1. Откройте браузер и перейдите к отображаемому URL-адресу. Введите предоставленный код и войдите в систему. После завершения вернитесь к приложению и выберите **1. Показать маркер доступа** для отображения маркера доступа.
