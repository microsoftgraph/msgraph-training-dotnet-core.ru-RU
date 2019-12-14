<!-- markdownlint-disable MD002 MD041 -->

Начните с создания нового проекта консоли .NET Core с помощью [инфраструктуры .NET Core](/dotnet/core/tools/?tabs=netcore2x).

1. Откройте интерфейс командной строки (CLI) в каталоге, в котором нужно создать проект. Выполните следующую команду.

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. После создания проекта убедитесь, что он работает, изменив текущий каталог на каталог **графтуториал** и выполнив следующую команду в командной системе CLI.

    ```Shell
    dotnet run
    ```

    Если это сработает, приложение должно выводить `Hello World!`результаты.

## <a name="install-dependencies"></a>Установка зависимостей

Перед перемещением добавьте дополнительные зависимости, которые будут использоваться позже.

- [Microsoft. Extensions. Configuration](https://github.com/aspnet/Extensions) для считывания конфигурации приложения из JSON-файла.
- [Библиотека проверки подлинности Microsoft (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) для проверки подлинности пользователя и получения маркеров доступа.
- [Клиентская библиотека Microsoft Graph .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , чтобы совершать вызовы в Microsoft Graph.
- [Поставщики проверки подлинности для Microsoft Graph .NET SDK](https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth) позволяют клиентской библиотеке Graph автоматически запрашивать маркеры при совершении вызовов API.

Выполните следующие команды в интерфейсе командной строки, чтобы установить зависимости.

```Shell
dotnet add package Microsoft.Extensions.Configuration --version 3.1.0
dotnet add package Microsoft.Extensions.Configuration.FileExtensions --version 3.1.0
dotnet add package Microsoft.Extensions.Configuration.Json --version 3.1.0
dotnet add package Microsoft.Extensions.Configuration.Binder --version 3.1.0
dotnet add package Microsoft.Identity.Client --version 4.7.1
dotnet add package Microsoft.Graph --version 1.21.0
```

## <a name="design-the-app"></a>Проектирование приложения

В этом разделе вы создадите простое меню на основе консоли.

Откройте **Program.CS** в текстовом редакторе (например, [Visual Studio Code](https://code.visualstudio.com/)) и замените все содержимое приведенным ниже кодом.

[!code-csharp[](../demos/01-create-app/GraphTutorial/Program.cs)]

В результате будет реализовано базовое меню, в котором считывается выбор пользователя из командной строки.
