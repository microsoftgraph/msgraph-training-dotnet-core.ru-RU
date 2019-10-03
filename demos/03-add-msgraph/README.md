# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="ed200-101">Выполнение завершенного проекта</span><span class="sxs-lookup"><span data-stu-id="ed200-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ed200-102">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ed200-102">Prerequisites</span></span>

<span data-ttu-id="ed200-103">Чтобы запустить завершенный проект в этой папке, вам потребуются следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="ed200-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="ed200-104">[Пакет SDK .NET Core](https://dotnet.microsoft.com/download) , установленный на компьютере для разработки.</span><span class="sxs-lookup"><span data-stu-id="ed200-104">The [.NET Core SDK](https://dotnet.microsoft.com/download) installed on your development machine.</span></span> <span data-ttu-id="ed200-105">(**Примечание:** данное руководство написано в пакете SDK для .NET Core версии 2.2.401.</span><span class="sxs-lookup"><span data-stu-id="ed200-105">(**Note:** This tutorial was written with .NET Core SDK version 2.2.401.</span></span> <span data-ttu-id="ed200-106">Действия, описанные в этом руководстве, могут работать с другими версиями, но не тестировались.</span><span class="sxs-lookup"><span data-stu-id="ed200-106">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="ed200-107">Рабочая или учебная учетная запись Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="ed200-107">A Microsoft work or school account.</span></span>

<span data-ttu-id="ed200-108">Если у вас нет учетной записи Майкрософт, вы можете [зарегистрироваться в программе для разработчиков office 365](https://developer.microsoft.com/office/dev-program) , чтобы получить бесплатную подписку на Office 365.</span><span class="sxs-lookup"><span data-stu-id="ed200-108">If you don't have a Microsoft account, you can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="ed200-109">Регистрация веб-приложения с помощью центра администрирования Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="ed200-109">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="ed200-110">Откройте браузер и перейдите в [Центр администрирования Azure Active Directory](https://aad.portal.azure.com) и войдите с помощью **личной учетной записи** (т.е. учетной записи Майкрософт) или **рабочей или учебной учетной записи**.</span><span class="sxs-lookup"><span data-stu-id="ed200-110">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="ed200-111">Выберите **Azure Active Directory** в левой панели навигации, а затем выберите **Регистрация приложений** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="ed200-111">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="ed200-112">Снимок экрана с регистрациями приложений</span><span class="sxs-lookup"><span data-stu-id="ed200-112">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="ed200-113">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="ed200-113">Select **New registration**.</span></span> <span data-ttu-id="ed200-114">На странице**Зарегистрировать приложение** задайте необходимые значения следующим образом.</span><span class="sxs-lookup"><span data-stu-id="ed200-114">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="ed200-115">Введите **имя** `.NET Core Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="ed200-115">Set **Name** to `.NET Core Graph Tutorial`.</span></span>
    - <span data-ttu-id="ed200-116">Установите **Поддерживаемые типы учетных** записей для **учетных записей в любом организационном каталоге**.</span><span class="sxs-lookup"><span data-stu-id="ed200-116">Set **Supported account types** to **Accounts in any organizational directory**.</span></span>
    - <span data-ttu-id="ed200-117">Оставьте поле **URI перенаправления** пустым.</span><span class="sxs-lookup"><span data-stu-id="ed200-117">Leave **Redirect URI** empty.</span></span>

    ![Снимок страницы "регистрация приложения"](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="ed200-119">Выберите **регистр**.</span><span class="sxs-lookup"><span data-stu-id="ed200-119">Select **Register**.</span></span> <span data-ttu-id="ed200-120">На странице **учебника по базовому графику .NET** СКОПИРУЙТЕ значение **идентификатора Application (Client)** и сохраните его, он понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="ed200-120">On the **.NET Core Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Снимок экрана с ИДЕНТИФИКАТОРом приложения для новой регистрации приложения](/tutorial/images/aad-application-id.png)

1. <span data-ttu-id="ed200-122">Выберите ссылку **Добавить URI перенаправления** .</span><span class="sxs-lookup"><span data-stu-id="ed200-122">Select the **Add a Redirect URI** link.</span></span> <span data-ttu-id="ed200-123">На странице " **Перенаправление URI** " выберите раздел **Перенаправление URI для общедоступных клиентов (мобильный, Настольный)** .</span><span class="sxs-lookup"><span data-stu-id="ed200-123">On the **Redirect URIs** page, locate the **Suggested Redirect URIs for public clients (mobile, desktop)** section.</span></span> <span data-ttu-id="ed200-124">Выберите `https://login.microsoftonline.com/common/oauth2/nativeclient` универсальный код ресурса (URI).</span><span class="sxs-lookup"><span data-stu-id="ed200-124">Select the `https://login.microsoftonline.com/common/oauth2/nativeclient` URI.</span></span>

    ![Снимок экрана со страницей URI перенаправления](/tutorial/images/aad-redirect-uris.png)

1. <span data-ttu-id="ed200-126">Найдите раздел **Тип клиента по умолчанию** и измените **приложение рассматривать как общедоступный клиент** на **Да**, а затем нажмите кнопку **сохранить**.</span><span class="sxs-lookup"><span data-stu-id="ed200-126">Locate the **Default client type** section and change the **Treat application as a public client** toggle to **Yes**, then choose **Save**.</span></span>

    ![Снимок экрана: раздел "тип клиента по умолчанию"](/tutorial/images/aad-default-client-type.png)

## <a name="configure-the-sample"></a><span data-ttu-id="ed200-128">Настройка примера</span><span class="sxs-lookup"><span data-stu-id="ed200-128">Configure the sample</span></span>

1. <span data-ttu-id="ed200-129">Переименуйте `appsettings.json.example` файл в `appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="ed200-129">Rename the `appsettings.json.example` file to `appsettings.json`.</span></span>
1. <span data-ttu-id="ed200-130">Измените `appsettings.json` файл и внесите следующие изменения.</span><span class="sxs-lookup"><span data-stu-id="ed200-130">Edit the `appsettings.json` file and make the following changes.</span></span>
    1. <span data-ttu-id="ed200-131">Замените `YOUR_APP_ID_HERE` **идентификатором приложения** , полученным на портале регистрации приложений.</span><span class="sxs-lookup"><span data-stu-id="ed200-131">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>

## <a name="build-and-run-the-sample"></a><span data-ttu-id="ed200-132">Сборка и запуск примера</span><span class="sxs-lookup"><span data-stu-id="ed200-132">Build and run the sample</span></span>

<span data-ttu-id="ed200-133">В интерфейсе командной строки (CLI) перейдите в каталог проекта и выполните приведенные ниже команды.</span><span class="sxs-lookup"><span data-stu-id="ed200-133">In your command-line interface (CLI), navigate to the project directory and run the following commands.</span></span>

```Shell
dotnet restore
dotnet build
dotnet run
```
