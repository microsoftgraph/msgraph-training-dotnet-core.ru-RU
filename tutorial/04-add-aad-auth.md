<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. На этом этапе выполняется интеграция [библиотеки проверки подлинности Microsoft (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) в приложение.

1. Инициализируйте [хранилище секрета .NET Development](/aspnet/core/security/app-secrets) , открыв его в каталоге, содержащем **графтуториал. csproj** , и выполнив следующую команду.

    ```Shell
    dotnet user-secrets init
    ```

1. Добавьте идентификатор приложения и список требуемых областей в Секретное хранилище с помощью следующих команд. Замените `YOUR_APP_ID_HERE` идентификатором приложения, созданным на портале Azure.

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;MailboxSettings.Read;Calendars.ReadWrite"
    ```

    Давайте рассмотрим только что установленные области разрешений.

    - Разрешение **User. Read** позволит приложению читать профиль вошедшего пользователя, чтобы получить такие сведения, как отображаемое имя и адрес электронной почты.
    - **MailboxSettings. Read** позволяет приложению считывать предпочтительный часовой пояс пользователя, формат даты и формат времени.
    - **Calendars. ReadWrite** позволит приложению читать существующие события в календаре пользователя и добавлять новые события.

## <a name="implement-sign-in"></a>Реализация входа

В этом разделе описывается создание поставщика проверки подлинности, который можно использовать с пакетом SDK Graph, и его также можно использовать для явного запроса маркера доступа с помощью [кода устройства](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).

### <a name="create-an-authentication-provider"></a>Создание поставщика проверки подлинности

1. Создайте новый каталог в каталоге **графтуториал** с именем **authentication**.
1. Создайте новый файл в каталоге **проверки подлинности** с именем **DeviceCodeAuthProvider.CS** и добавьте в этот файл следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/DeviceCodeAuthProvider.cs" id="AuthProviderSnippet":::

Рассмотрите, что делает этот код.

- `IPublicClientApplication`Для запроса и управления маркерами используется реализация MSAL.
- `GetAccessToken`Функция:
  - Подписывает пользователя, если они еще не выполнили вход с помощью кода устройства.
  - Гарантирует, что возвращаемый маркер всегда обновляется с помощью `AcquireTokenSilent` функции, которая возвращает кэшированный маркер, если срок его действия не истек, и обновляет маркер, если срок его действия истек.
- Он реализует `IAuthenticationProvider` интерфейс, чтобы графический пакет SDK мог использовать класс для проверки подлинности графических вызовов.

## <a name="sign-in-and-display-the-access-token"></a>Вход и отображение маркера доступа

В этом разделе вы обновите приложение, чтобы он вызывал `GetAccessToken` функцию, которая будет входить в систему пользователя. Кроме того, добавляется код для отображения маркера.

1. Добавьте к классу `Program` следующую функцию:

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="LoadAppSettingsSnippet":::

1. Добавьте следующий код в `Main` функцию сразу после `Console.WriteLine(".NET Core Graph Tutorial\n");` строки.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="InitializationSnippet":::

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
    > При возникновении ошибок Сравните **Program.CS** с [примером на сайте GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs).

1. Откройте браузер и перейдите к отображаемому URL-адресу. Введите предоставленный код и войдите в систему. После завершения вернитесь к приложению и выберите **1. Показать маркер доступа** для отображения маркера доступа.
