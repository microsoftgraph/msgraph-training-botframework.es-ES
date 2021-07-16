---
ms.openlocfilehash: c60d1167e52118f06127a858e91aef6bddeb1d14
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446823"
---
# <a name="graphcalendarbot"></a><span data-ttu-id="1fe39-101">GraphCalendarBot</span><span class="sxs-lookup"><span data-stu-id="1fe39-101">GraphCalendarBot</span></span>

<span data-ttu-id="1fe39-102">Ejemplo de bot Bot Framework v4 echo bot.</span><span class="sxs-lookup"><span data-stu-id="1fe39-102">Bot Framework v4 echo bot sample.</span></span>

<span data-ttu-id="1fe39-103">Este bot se ha creado con [Bot Framework,](https://dev.botframework.com)muestra cómo crear un bot simple que acepte la entrada del usuario y la vuelva a hacer eco.</span><span class="sxs-lookup"><span data-stu-id="1fe39-103">This bot has been created using [Bot Framework](https://dev.botframework.com), it shows how to create a simple bot that accepts input from the user and echoes it back.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1fe39-104">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="1fe39-104">Prerequisites</span></span>

- <span data-ttu-id="1fe39-105">[SDK de .NET Core](https://dotnet.microsoft.com/download) versión 3.1</span><span class="sxs-lookup"><span data-stu-id="1fe39-105">[.NET Core SDK](https://dotnet.microsoft.com/download) version 3.1</span></span>

  ```bash
  # determine dotnet version
  dotnet --version
  ```

## <a name="to-try-this-sample"></a><span data-ttu-id="1fe39-106">Para probar este ejemplo</span><span class="sxs-lookup"><span data-stu-id="1fe39-106">To try this sample</span></span>

- <span data-ttu-id="1fe39-107">En un terminal, vaya a `GraphCalendarBot`</span><span class="sxs-lookup"><span data-stu-id="1fe39-107">In a terminal, navigate to `GraphCalendarBot`</span></span>

    ```bash
    # change into project folder
    cd GraphCalendarBot
    ```

- <span data-ttu-id="1fe39-108">Ejecute el bot desde un terminal o desde Visual Studio, elija las opciones A o B.</span><span class="sxs-lookup"><span data-stu-id="1fe39-108">Run the bot from a terminal or from Visual Studio, choose option A or B.</span></span>

  <span data-ttu-id="1fe39-109">A) Desde un terminal</span><span class="sxs-lookup"><span data-stu-id="1fe39-109">A) From a terminal</span></span>

  ```bash
  # run the bot
  dotnet run
  ```

  <span data-ttu-id="1fe39-110">B) O desde Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fe39-110">B) Or from Visual Studio</span></span>

  - <span data-ttu-id="1fe39-111">Iniciar Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fe39-111">Launch Visual Studio</span></span>
  - <span data-ttu-id="1fe39-112">File -> Open -> Project/Solution</span><span class="sxs-lookup"><span data-stu-id="1fe39-112">File -> Open -> Project/Solution</span></span>
  - <span data-ttu-id="1fe39-113">Navegar a la `GraphCalendarBot` carpeta</span><span class="sxs-lookup"><span data-stu-id="1fe39-113">Navigate to `GraphCalendarBot` folder</span></span>
  - <span data-ttu-id="1fe39-114">Seleccionar `GraphCalendarBot.csproj` archivo</span><span class="sxs-lookup"><span data-stu-id="1fe39-114">Select `GraphCalendarBot.csproj` file</span></span>
  - <span data-ttu-id="1fe39-115">Presione `F5` para ejecutar el proyecto</span><span class="sxs-lookup"><span data-stu-id="1fe39-115">Press `F5` to run the project</span></span>

## <a name="testing-the-bot-using-bot-framework-emulator"></a><span data-ttu-id="1fe39-116">Probar el bot con Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="1fe39-116">Testing the bot using Bot Framework Emulator</span></span>

<span data-ttu-id="1fe39-117">[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) es una aplicación de escritorio que permite a los desarrolladores de bots probar y depurar sus bots en localhost o ejecutarse de forma remota a través de un túnel.</span><span class="sxs-lookup"><span data-stu-id="1fe39-117">[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) is a desktop application that allows bot developers to test and debug their bots on localhost or running remotely through a tunnel.</span></span>

- <span data-ttu-id="1fe39-118">Instale la Bot Framework Emulator versión 4.9.0 o posterior desde [aquí](https://github.com/Microsoft/BotFramework-Emulator/releases)</span><span class="sxs-lookup"><span data-stu-id="1fe39-118">Install the Bot Framework Emulator version 4.9.0 or greater from [here](https://github.com/Microsoft/BotFramework-Emulator/releases)</span></span>

### <a name="connect-to-the-bot-using-bot-framework-emulator"></a><span data-ttu-id="1fe39-119">Conectar al bot mediante Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="1fe39-119">Connect to the bot using Bot Framework Emulator</span></span>

- <span data-ttu-id="1fe39-120">Iniciar Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="1fe39-120">Launch Bot Framework Emulator</span></span>
- <span data-ttu-id="1fe39-121">File -> Open Bot</span><span class="sxs-lookup"><span data-stu-id="1fe39-121">File -> Open Bot</span></span>
- <span data-ttu-id="1fe39-122">Escriba una dirección URL de bot de `http://localhost:3978/api/messages`</span><span class="sxs-lookup"><span data-stu-id="1fe39-122">Enter a Bot URL of `http://localhost:3978/api/messages`</span></span>

## <a name="deploy-the-bot-to-azure"></a><span data-ttu-id="1fe39-123">Implementar el bot en Azure</span><span class="sxs-lookup"><span data-stu-id="1fe39-123">Deploy the bot to Azure</span></span>

<span data-ttu-id="1fe39-124">Para obtener más información sobre cómo implementar un bot en Azure, consulte [Deploy your bot to Azure](https://aka.ms/azuredeployment) para obtener una lista completa de instrucciones de implementación.</span><span class="sxs-lookup"><span data-stu-id="1fe39-124">To learn more about deploying a bot to Azure, see [Deploy your bot to Azure](https://aka.ms/azuredeployment) for a complete list of deployment instructions.</span></span>

## <a name="further-reading"></a><span data-ttu-id="1fe39-125">Lectura adicional</span><span class="sxs-lookup"><span data-stu-id="1fe39-125">Further reading</span></span>

- [<span data-ttu-id="1fe39-126">Documentación de Bot Framework</span><span class="sxs-lookup"><span data-stu-id="1fe39-126">Bot Framework Documentation</span></span>](https://docs.botframework.com)
- [<span data-ttu-id="1fe39-127">Conceptos básicos del bot</span><span class="sxs-lookup"><span data-stu-id="1fe39-127">Bot Basics</span></span>](https://docs.microsoft.com/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0)
- [<span data-ttu-id="1fe39-128">Procesamiento de actividad</span><span class="sxs-lookup"><span data-stu-id="1fe39-128">Activity processing</span></span>](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-activity-processing?view=azure-bot-service-4.0)
- [<span data-ttu-id="1fe39-129">Introducción al servicio bot de Azure</span><span class="sxs-lookup"><span data-stu-id="1fe39-129">Azure Bot Service Introduction</span></span>](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- [<span data-ttu-id="1fe39-130">Documentación del servicio bot de Azure</span><span class="sxs-lookup"><span data-stu-id="1fe39-130">Azure Bot Service Documentation</span></span>](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)
- [<span data-ttu-id="1fe39-131">Herramientas de la CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fe39-131">.NET Core CLI tools</span></span>](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x)
- [<span data-ttu-id="1fe39-132">Azure CLI</span><span class="sxs-lookup"><span data-stu-id="1fe39-132">Azure CLI</span></span>](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
- [<span data-ttu-id="1fe39-133">Azure Portal</span><span class="sxs-lookup"><span data-stu-id="1fe39-133">Azure Portal</span></span>](https://portal.azure.com)
- [<span data-ttu-id="1fe39-134">Language Understanding using LUIS</span><span class="sxs-lookup"><span data-stu-id="1fe39-134">Language Understanding using LUIS</span></span>](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/)
- [<span data-ttu-id="1fe39-135">Canales y servicio de conector de bot</span><span class="sxs-lookup"><span data-stu-id="1fe39-135">Channels and Bot Connector Service</span></span>](https://docs.microsoft.com/en-us/azure/bot-service/bot-concepts?view=azure-bot-service-4.0)

<span data-ttu-id="1fe39-136">Generado con `dotnet new echobot` v4.13.2</span><span class="sxs-lookup"><span data-stu-id="1fe39-136">Generated using `dotnet new echobot` v4.13.2</span></span>
