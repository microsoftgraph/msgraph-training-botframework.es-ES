---
ms.openlocfilehash: 51878a3eebbcacb9ea325a3f97453d30006be116
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446781"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="31997-101">En esta sección, creará un proyecto de Bot Framework.</span><span class="sxs-lookup"><span data-stu-id="31997-101">In this section you'll create a Bot Framework project.</span></span>

1. <span data-ttu-id="31997-102">Abra la interfaz de línea de comandos (CLI) en un directorio donde desee crear el proyecto.</span><span class="sxs-lookup"><span data-stu-id="31997-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="31997-103">Ejecute el siguiente comando para crear un nuevo proyecto con la plantilla **Microsoft.Bot.Framework.CSharp.EchoBot.**</span><span class="sxs-lookup"><span data-stu-id="31997-103">Run the following command to create new project using the **Microsoft.Bot.Framework.CSharp.EchoBot** template.</span></span>

    ```dotnetcli
    dotnet new echobot -n GraphCalendarBot
    ```

    > [!NOTE]
    > <span data-ttu-id="31997-104">Si recibe un `No templates matched the input template name: echobot.` error, instale la plantilla con el siguiente comando y vuelva a ejecutar el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="31997-104">If you receive an `No templates matched the input template name: echobot.` error, install the template with the following command and re-run the previous command.</span></span>
    >
    > ```dotnetcli
    > dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
    > ```

1. <span data-ttu-id="31997-105">Cambie el nombre de **la clase EchoBot** predeterminada **a CalendarBot**.</span><span class="sxs-lookup"><span data-stu-id="31997-105">Rename the default **EchoBot** class to **CalendarBot**.</span></span> <span data-ttu-id="31997-106">Abra **./Bots/EchoBot.cs** y reemplace todas las instancias de `EchoBot` con `CalendarBot` .</span><span class="sxs-lookup"><span data-stu-id="31997-106">Open **./Bots/EchoBot.cs** and replace all instances of `EchoBot` with `CalendarBot`.</span></span> <span data-ttu-id="31997-107">Cambie el nombre del archivo **a CalendarBot.cs**.</span><span class="sxs-lookup"><span data-stu-id="31997-107">Rename the file to **CalendarBot.cs**.</span></span>

1. <span data-ttu-id="31997-108">Reemplace todas las instancias de `EchoBot` with en los archivos `CalendarBot` **.cs** restantes.</span><span class="sxs-lookup"><span data-stu-id="31997-108">Replace all instances of `EchoBot` with `CalendarBot` in the remaining **.cs** files.</span></span>

1. <span data-ttu-id="31997-109">En la CLI, cambie el directorio actual al directorio **GraphCalendarBot** y ejecute el siguiente comando para confirmar las compilaciones del proyecto.</span><span class="sxs-lookup"><span data-stu-id="31997-109">In your CLI, change the current directory to the **GraphCalendarBot** directory and run the following command to confirm the project builds.</span></span>

    ```dotnetcli
    dotnet build
    ```

## <a name="add-nuget-packages"></a><span data-ttu-id="31997-110">Agregar paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="31997-110">Add NuGet packages</span></span>

<span data-ttu-id="31997-111">Antes de seguir, instala algunos paquetes NuGet que usarás más adelante.</span><span class="sxs-lookup"><span data-stu-id="31997-111">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="31997-112">[AdaptiveCards para](https://www.nuget.org/packages/AdaptiveCards/) permitir que el bot envíe tarjetas adaptables en respuestas.</span><span class="sxs-lookup"><span data-stu-id="31997-112">[AdaptiveCards](https://www.nuget.org/packages/AdaptiveCards/) to allow the bot to send Adaptive Cards in responses.</span></span>
- <span data-ttu-id="31997-113">[Microsoft.Bot.Builder.Dialogs](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) para agregar compatibilidad de cuadros de diálogo al bot.</span><span class="sxs-lookup"><span data-stu-id="31997-113">[Microsoft.Bot.Builder.Dialogs](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) to add dialog support to the bot.</span></span>
- <span data-ttu-id="31997-114">[Microsoft.Recognizers.Text.DataTypes.TimexExpression](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) para convertir las expresiones TIMEX devueltas de los mensajes del bot en **objetos DateTime.**</span><span class="sxs-lookup"><span data-stu-id="31997-114">[Microsoft.Recognizers.Text.DataTypes.TimexExpression](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) to convert the TIMEX expressions returned from bot prompts into **DateTime** objects.</span></span>
- <span data-ttu-id="31997-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="31997-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to Microsoft Graph.</span></span>

1. <span data-ttu-id="31997-116">Ejecute los siguientes comandos en la CLI para instalar las dependencias.</span><span class="sxs-lookup"><span data-stu-id="31997-116">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package AdaptiveCards --version 2.7.1
    dotnet add package Microsoft.Bot.Builder.Dialogs --version 4.14.1
    dotnet add package Microsoft.Bot.Builder.Integration.AspNet.Core --version 4.14.1
    dotnet add package Microsoft.Recognizers.Text.DataTypes.TimexExpression --version 1.7.0
    dotnet add package Microsoft.Graph --version 4.0.0
    ```

## <a name="test-the-bot"></a><span data-ttu-id="31997-117">Probar el bot</span><span class="sxs-lookup"><span data-stu-id="31997-117">Test the bot</span></span>

<span data-ttu-id="31997-118">Antes de agregar cualquier código, pruebe el bot para asegurarse de que funciona correctamente y de que el Bot Framework Emulator está configurado para probarlo.</span><span class="sxs-lookup"><span data-stu-id="31997-118">Before adding any code, test the bot to make sure that it works correctly, and that the Bot Framework Emulator is configured to test it.</span></span>

1. <span data-ttu-id="31997-119">Inicie el bot ejecutando el siguiente comando.</span><span class="sxs-lookup"><span data-stu-id="31997-119">Start the bot by running the following command.</span></span>

    ```dotnetcli
    dotnet run
    ```

    > [!TIP]
    > <span data-ttu-id="31997-120">Aunque puede usar cualquier editor de texto para editar los archivos de origen del proyecto, se recomienda usar [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="31997-120">While you can use any text editor to edit the source files in the project, we recommend using [Visual Studio Code](https://code.visualstudio.com/).</span></span> <span data-ttu-id="31997-121">Visual Studio Code compatibilidad con la depuración, Intellisense y mucho más.</span><span class="sxs-lookup"><span data-stu-id="31997-121">Visual Studio Code offers debugging support, Intellisense, and more.</span></span> <span data-ttu-id="31997-122">Si usa Visual Studio Code, puede iniciar el bot mediante el **menú**  ->  **Ejecutar depuración de** inicio.</span><span class="sxs-lookup"><span data-stu-id="31997-122">If using Visual Studio Code, you can start the bot using the **Run** -> **Start Debugging** menu.</span></span>

1. <span data-ttu-id="31997-123">Confirme que el bot se está ejecutando abriendo el explorador y yendo a `http://localhost:3978` .</span><span class="sxs-lookup"><span data-stu-id="31997-123">Confirm the bot is running by opening your browser and going to `http://localhost:3978`.</span></span> <span data-ttu-id="31997-124">Debería ver un **bot your is ready!**</span><span class="sxs-lookup"><span data-stu-id="31997-124">You should see a **Your bot is ready!**</span></span> <span data-ttu-id="31997-125">Mensaje.</span><span class="sxs-lookup"><span data-stu-id="31997-125">message.</span></span>

1. <span data-ttu-id="31997-126">Abra el Bot Framework Emulator.</span><span class="sxs-lookup"><span data-stu-id="31997-126">Open the Bot Framework Emulator.</span></span> <span data-ttu-id="31997-127">Elija el **menú** Archivo y, a continuación, **Abra bot**.</span><span class="sxs-lookup"><span data-stu-id="31997-127">Choose the **File** menu, then **Open Bot**.</span></span>

1. <span data-ttu-id="31997-128">Escriba `http://localhost:3978/api/messages` en la dirección URL del **bot** **y, a continuación, seleccione Conectar**.</span><span class="sxs-lookup"><span data-stu-id="31997-128">Enter `http://localhost:3978/api/messages` in the **Bot URL**, then select **Connect**.</span></span>

1. <span data-ttu-id="31997-129">El bot responde con `Hello and welcome!` en la ventana de chat.</span><span class="sxs-lookup"><span data-stu-id="31997-129">The bot responds with `Hello and welcome!` in the chat window.</span></span> <span data-ttu-id="31997-130">Envíe un mensaje al bot y confirme que lo vuelve a hacer eco.</span><span class="sxs-lookup"><span data-stu-id="31997-130">Send a message to the bot and confirm it echoes it back.</span></span>

    ![Captura de pantalla del Bot Framework Emulator conectado al bot](images/test-emulator.png)
