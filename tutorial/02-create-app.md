<!-- markdownlint-disable MD002 MD041 -->

Начните с создания нового проекта консоли .NET Core с помощью [инфраструктуры .NET Core](/dotnet/core/tools/).

1. Откройте интерфейс командной строки (CLI) в каталоге, в котором нужно создать проект. Выполните следующую команду.

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. После создания проекта убедитесь, что он работает, изменив текущий каталог на каталог **графтуториал** и выполнив следующую команду в командной системе CLI.

    ```Shell
    dotnet run
    ```

    Если это сработает, приложение должно выводить результаты `Hello World!` .

## <a name="install-dependencies"></a>Установка зависимостей

Перед перемещением добавьте дополнительные зависимости, которые будут использоваться позже.

- [Microsoft.Extensions.Configуратион. Усерсекретс](https://github.com/aspnet/extensions) чтение конфигурации приложения из [хранилища секретов для разработки .NET](https://docs.microsoft.com/aspnet/core/security/app-secrets).
- [Библиотека проверки подлинности Microsoft (MSAL) для .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) для проверки подлинности пользователя и получения маркеров доступа.
- [Клиентская библиотека Microsoft Graph .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , чтобы совершать вызовы в Microsoft Graph.
- [Тимезонеконвертер](https://github.com/mj1856/TimeZoneConverter) для преобразования идентификаторов часовых поясов Windows в идентификаторы IANA.

Выполните следующие команды в интерфейсе командной строки, чтобы установить зависимости.

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 3.1.8
dotnet add package Microsoft.Identity.Client --version 4.19.0
dotnet add package Microsoft.Graph --version 3.15.0
dotnet add package TimeZoneConverter
```

## <a name="design-the-app"></a>Проектирование приложения

В этом разделе вы создадите простое меню на основе консоли.

Откройте **./Program.CS** в текстовом редакторе (например, [Visual Studio Code](https://code.visualstudio.com/)) и замените все содержимое приведенным ниже кодом.

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

В результате будет реализовано базовое меню, в котором считывается выбор пользователя из командной строки.
