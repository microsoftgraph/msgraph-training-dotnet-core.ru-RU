<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c267e-101">Начните с создания нового консольного проекта .NET Core с помощью [.NET Core CLI.](/dotnet/core/tools/)</span><span class="sxs-lookup"><span data-stu-id="c267e-101">Begin by creating a new .NET Core console project using the [.NET Core CLI](/dotnet/core/tools/).</span></span>

1. <span data-ttu-id="c267e-102">Откройте интерфейс командной строки (CLI) в каталоге, в котором нужно создать проект.</span><span class="sxs-lookup"><span data-stu-id="c267e-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="c267e-103">Выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="c267e-103">Run the following command.</span></span>

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. <span data-ttu-id="c267e-104">После создания проекта убедитесь, что он работает, изменив текущий каталог на **каталог GraphTutorial** и выполнив следующую команду в CLI.</span><span class="sxs-lookup"><span data-stu-id="c267e-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    <span data-ttu-id="c267e-105">Если он работает, приложение должно вы выходные `Hello World!` данные .</span><span class="sxs-lookup"><span data-stu-id="c267e-105">If it works, the app should output `Hello World!`.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="c267e-106">Установка зависимостей</span><span class="sxs-lookup"><span data-stu-id="c267e-106">Install dependencies</span></span>

<span data-ttu-id="c267e-107">Прежде чем двигаться дальше, добавьте некоторые дополнительные зависимости, которые вы будете использовать позже.</span><span class="sxs-lookup"><span data-stu-id="c267e-107">Before moving on, add some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="c267e-108">[Microsoft.Extensions.Configuration. UserSecrets](https://github.com/aspnet/extensions) для чтения конфигурации приложения из секретного магазина [разработки .NET.](https://docs.microsoft.com/aspnet/core/security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="c267e-108">[Microsoft.Extensions.Configuration.UserSecrets](https://github.com/aspnet/extensions) to read application configuration from the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span></span>
- <span data-ttu-id="c267e-109">[Microsoft Authentication Library (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) для проверки подлинности пользователя и получения маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="c267e-109">[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) to authenticate the user and acquire access tokens.</span></span>
- <span data-ttu-id="c267e-110">[Клиентская библиотека .NET Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-dotnet) для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c267e-110">[Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="c267e-111">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) для перевода идентификаторов часовой пояс Windows в идентификаторы IANA.</span><span class="sxs-lookup"><span data-stu-id="c267e-111">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for translating Windows time zone identifiers to IANA identifiers.</span></span>

<span data-ttu-id="c267e-112">Чтобы установить зависимости, в командной библиотеке CLI запустите следующие команды.</span><span class="sxs-lookup"><span data-stu-id="c267e-112">Run the following commands in your CLI to install the dependencies.</span></span>

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 5.0.0
dotnet add package Microsoft.Identity.Client --version 4.25.0
dotnet add package Microsoft.Graph --version 3.22.0
dotnet add package TimeZoneConverter
```

## <a name="design-the-app"></a><span data-ttu-id="c267e-113">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="c267e-113">Design the app</span></span>

<span data-ttu-id="c267e-114">В этом разделе мы создадим простое консольное меню.</span><span class="sxs-lookup"><span data-stu-id="c267e-114">In this section you will create a simple console-based menu.</span></span>

<span data-ttu-id="c267e-115">Откройте **./Program.cs** в текстовом редакторе [(например, Visual Studio Code)](https://code.visualstudio.com/)и замените его все содержимое на следующий код.</span><span class="sxs-lookup"><span data-stu-id="c267e-115">Open **./Program.cs** in a text editor (such as [Visual Studio Code](https://code.visualstudio.com/)) and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="c267e-116">При этом реализуется базовое меню и считыется выбор пользователя из командной строки.</span><span class="sxs-lookup"><span data-stu-id="c267e-116">This implements a basic menu and reads the user's choice from the command line.</span></span>
