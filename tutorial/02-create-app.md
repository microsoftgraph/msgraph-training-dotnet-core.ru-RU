<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="568e4-101">Начните с создания нового проекта консоли .NET Core с помощью [инфраструктуры .NET Core](/dotnet/core/tools/?tabs=netcore2x).</span><span class="sxs-lookup"><span data-stu-id="568e4-101">Begin by creating a new .NET Core console project using the [.NET Core CLI](/dotnet/core/tools/?tabs=netcore2x).</span></span>

1. <span data-ttu-id="568e4-102">Откройте интерфейс командной строки (CLI) в каталоге, в котором нужно создать проект.</span><span class="sxs-lookup"><span data-stu-id="568e4-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="568e4-103">Выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="568e4-103">Run the following command.</span></span>

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. <span data-ttu-id="568e4-104">После создания проекта убедитесь, что он работает, изменив текущий каталог на каталог **графтуториал** и выполнив следующую команду в командной системе CLI.</span><span class="sxs-lookup"><span data-stu-id="568e4-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    <span data-ttu-id="568e4-105">Если это сработает, приложение должно выводить `Hello World!`результаты.</span><span class="sxs-lookup"><span data-stu-id="568e4-105">If it works, the app should output `Hello World!`.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="568e4-106">Установка зависимостей</span><span class="sxs-lookup"><span data-stu-id="568e4-106">Install dependencies</span></span>

<span data-ttu-id="568e4-107">Перед перемещением добавьте дополнительные зависимости, которые будут использоваться позже.</span><span class="sxs-lookup"><span data-stu-id="568e4-107">Before moving on, add some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="568e4-108">[Microsoft. Extensions. Configuration](https://github.com/aspnet/Extensions) для считывания конфигурации приложения из JSON-файла.</span><span class="sxs-lookup"><span data-stu-id="568e4-108">[Microsoft.Extensions.Configuration](https://github.com/aspnet/Extensions) to read application configuration from a JSON file.</span></span>
- <span data-ttu-id="568e4-109">[Библиотека проверки подлинности Microsoft (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) для проверки подлинности пользователя и получения маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="568e4-109">[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) to authenticate the user and acquire access tokens.</span></span>
- <span data-ttu-id="568e4-110">[Клиентская библиотека Microsoft Graph .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , чтобы совершать вызовы в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="568e4-110">[Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="568e4-111">[Поставщики проверки подлинности для Microsoft Graph .NET SDK](https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth) позволяют клиентской библиотеке Graph автоматически запрашивать маркеры при совершении вызовов API.</span><span class="sxs-lookup"><span data-stu-id="568e4-111">[Authentication Providers for Microsoft Graph .NET SDK](https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth) to enable the Graph client library to request tokens automatically when making API calls.</span></span>

<span data-ttu-id="568e4-112">Выполните следующие команды в интерфейсе командной строки, чтобы установить зависимости.</span><span class="sxs-lookup"><span data-stu-id="568e4-112">Run the following commands in your CLI to install the dependencies.</span></span>

```Shell
dotnet add package Microsoft.Extensions.Configuration --version 2.2.0
dotnet add package Microsoft.Extensions.Configuration.FileExtensions --version 2.2.0
dotnet add package Microsoft.Extensions.Configuration.Json --version 2.2.0
dotnet add package Microsoft.Extensions.Configuration.Binder --version 2.2.0
dotnet add package Microsoft.Identity.Client --version 4.3.1
dotnet add package Microsoft.Graph --version 1.17.0
```

## <a name="design-the-app"></a><span data-ttu-id="568e4-113">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="568e4-113">Design the app</span></span>

<span data-ttu-id="568e4-114">В этом разделе вы создадите простое меню на основе консоли.</span><span class="sxs-lookup"><span data-stu-id="568e4-114">In this section you will create a simple console-based menu.</span></span>

<span data-ttu-id="568e4-115">Откройте **Program.CS** в текстовом редакторе (например, [Visual Studio Code](https://code.visualstudio.com/)) и замените все содержимое приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="568e4-115">Open **Program.cs** in a text editor (such as [Visual Studio Code](https://code.visualstudio.com/)) and replace its entire contents with the following code.</span></span>

[!code-csharp[](../demos/01-create-app/GraphTutorial/Program.cs)]

<span data-ttu-id="568e4-116">В результате будет реализовано базовое меню, в котором считывается выбор пользователя из командной строки.</span><span class="sxs-lookup"><span data-stu-id="568e4-116">This implements a basic menu and reads the user's choice from the command line.</span></span>
