<!-- markdownlint-disable MD002 MD041 -->

Начните с создания нового консольного проекта .NET Core с помощью [.NET Core CLI.](/dotnet/core/tools/)

1. Откройте интерфейс командной строки (CLI) в каталоге, в котором нужно создать проект. Выполните следующую команду.

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. После создания проекта убедитесь, что он работает, изменив текущий каталог на **каталог GraphTutorial** и выполнив следующую команду в CLI.

    ```Shell
    dotnet run
    ```

    Если он работает, приложение должно вы выходные `Hello World!` данные .

## <a name="install-dependencies"></a>Установка зависимостей

Прежде чем двигаться дальше, добавьте некоторые дополнительные зависимости, которые вы будете использовать позже.

- [Microsoft.Extensions.Configuration. UserSecrets](https://github.com/aspnet/extensions) для чтения конфигурации приложения из секретного магазина [разработки .NET.](https://docs.microsoft.com/aspnet/core/security/app-secrets)
- [Microsoft Authentication Library (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) для проверки подлинности пользователя и получения маркеров доступа.
- [Клиентская библиотека .NET Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-dotnet) для вызова Microsoft Graph.
- [TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) для перевода идентификаторов часовой пояс Windows в идентификаторы IANA.

Чтобы установить зависимости, в командной библиотеке CLI запустите следующие команды.

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 5.0.0
dotnet add package Microsoft.Identity.Client --version 4.25.0
dotnet add package Microsoft.Graph --version 3.22.0
dotnet add package TimeZoneConverter
```

## <a name="design-the-app"></a>Проектирование приложения

В этом разделе мы создадим простое консольное меню.

Откройте **./Program.cs** в текстовом редакторе [(например, Visual Studio Code)](https://code.visualstudio.com/)и замените его все содержимое на следующий код.

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

При этом реализуется базовое меню и считыется выбор пользователя из командной строки.
