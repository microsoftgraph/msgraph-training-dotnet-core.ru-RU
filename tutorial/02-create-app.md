<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="94fb8-101">Начните с создания нового проекта консоли .NET Core с помощью [инфраструктуры .NET Core](/dotnet/core/tools/).</span><span class="sxs-lookup"><span data-stu-id="94fb8-101">Begin by creating a new .NET Core console project using the [.NET Core CLI](/dotnet/core/tools/).</span></span>

1. <span data-ttu-id="94fb8-102">Откройте интерфейс командной строки (CLI) в каталоге, в котором нужно создать проект.</span><span class="sxs-lookup"><span data-stu-id="94fb8-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="94fb8-103">Выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="94fb8-103">Run the following command.</span></span>

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. <span data-ttu-id="94fb8-104">После создания проекта убедитесь, что он работает, изменив текущий каталог на каталог **графтуториал** и выполнив следующую команду в командной системе CLI.</span><span class="sxs-lookup"><span data-stu-id="94fb8-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    <span data-ttu-id="94fb8-105">Если это сработает, приложение должно выводить результаты `Hello World!` .</span><span class="sxs-lookup"><span data-stu-id="94fb8-105">If it works, the app should output `Hello World!`.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="94fb8-106">Установка зависимостей</span><span class="sxs-lookup"><span data-stu-id="94fb8-106">Install dependencies</span></span>

<span data-ttu-id="94fb8-107">Перед перемещением добавьте дополнительные зависимости, которые будут использоваться позже.</span><span class="sxs-lookup"><span data-stu-id="94fb8-107">Before moving on, add some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="94fb8-108">[Microsoft.Extensions.Configуратион. Усерсекретс](https://github.com/aspnet/extensions) чтение конфигурации приложения из [хранилища секретов для разработки .NET](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="94fb8-108">[Microsoft.Extensions.Configuration.UserSecrets](https://github.com/aspnet/extensions) to read application configuration from the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span></span>
- <span data-ttu-id="94fb8-109">[Библиотека проверки подлинности Microsoft (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) для проверки подлинности пользователя и получения маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="94fb8-109">[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) to authenticate the user and acquire access tokens.</span></span>
- <span data-ttu-id="94fb8-110">[Клиентская библиотека Microsoft Graph .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , чтобы совершать вызовы в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="94fb8-110">[Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="94fb8-111">[Тимезонеконвертер](https://github.com/mj1856/TimeZoneConverter) для преобразования идентификаторов часовых поясов Windows в идентификаторы IANA.</span><span class="sxs-lookup"><span data-stu-id="94fb8-111">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for translating Windows time zone identifiers to IANA identifiers.</span></span>

<span data-ttu-id="94fb8-112">Выполните следующие команды в интерфейсе командной строки, чтобы установить зависимости.</span><span class="sxs-lookup"><span data-stu-id="94fb8-112">Run the following commands in your CLI to install the dependencies.</span></span>

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 3.1.8
dotnet add package Microsoft.Identity.Client --version 4.19.0
dotnet add package Microsoft.Graph --version 3.15.0
dotnet add package TimeZoneConverter
```

## <a name="design-the-app"></a><span data-ttu-id="94fb8-113">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="94fb8-113">Design the app</span></span>

<span data-ttu-id="94fb8-114">В этом разделе вы создадите простое меню на основе консоли.</span><span class="sxs-lookup"><span data-stu-id="94fb8-114">In this section you will create a simple console-based menu.</span></span>

<span data-ttu-id="94fb8-115">Откройте **./Program.CS** в текстовом редакторе (например, [Visual Studio Code](https://code.visualstudio.com/)) и замените все содержимое приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="94fb8-115">Open **./Program.cs** in a text editor (such as [Visual Studio Code](https://code.visualstudio.com/)) and replace its entire contents with the following code.</span></span>

```csharp
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;

namespace GraphTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine(".NET Core Graph Tutorial\n");

            int choice = -1;

            while (choice != 0) {
                Console.WriteLine("Please choose one of the following options:");
                Console.WriteLine("0. Exit");
                Console.WriteLine("1. Display access token");
                Console.WriteLine("2. View this week's calendar");
                Console.WriteLine("3. Add an event");

                try
                {
                    choice = int.Parse(Console.ReadLine());
                }
                catch (System.FormatException)
                {
                    // Set to invalid value
                    choice = -1;
                }

                switch(choice)
                {
                    case 0:
                        // Exit the program
                        Console.WriteLine("Goodbye...");
                        break;
                    case 1:
                        // Display access token
                        break;
                    case 2:
                        // List the calendar
                        break;
                    case 3:
                        // Create a new event
                        break;
                    default:
                        Console.WriteLine("Invalid choice! Please try again.");
                        break;
                }
            }
        }
    }
}
```

<span data-ttu-id="94fb8-116">В результате будет реализовано базовое меню, в котором считывается выбор пользователя из командной строки.</span><span class="sxs-lookup"><span data-stu-id="94fb8-116">This implements a basic menu and reads the user's choice from the command line.</span></span>
