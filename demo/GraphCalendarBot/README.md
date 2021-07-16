---
ms.openlocfilehash: c60d1167e52118f06127a858e91aef6bddeb1d14
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446823"
---
# <a name="graphcalendarbot"></a>GraphCalendarBot

Ejemplo de bot Bot Framework v4 echo bot.

Este bot se ha creado con [Bot Framework,](https://dev.botframework.com)muestra cómo crear un bot simple que acepte la entrada del usuario y la vuelva a hacer eco.

## <a name="prerequisites"></a>Requisitos previos

- [SDK de .NET Core](https://dotnet.microsoft.com/download) versión 3.1

  ```bash
  # determine dotnet version
  dotnet --version
  ```

## <a name="to-try-this-sample"></a>Para probar este ejemplo

- En un terminal, vaya a `GraphCalendarBot`

    ```bash
    # change into project folder
    cd GraphCalendarBot
    ```

- Ejecute el bot desde un terminal o desde Visual Studio, elija las opciones A o B.

  A) Desde un terminal

  ```bash
  # run the bot
  dotnet run
  ```

  B) O desde Visual Studio

  - Iniciar Visual Studio
  - File -> Open -> Project/Solution
  - Navegar a la `GraphCalendarBot` carpeta
  - Seleccionar `GraphCalendarBot.csproj` archivo
  - Presione `F5` para ejecutar el proyecto

## <a name="testing-the-bot-using-bot-framework-emulator"></a>Probar el bot con Bot Framework Emulator

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) es una aplicación de escritorio que permite a los desarrolladores de bots probar y depurar sus bots en localhost o ejecutarse de forma remota a través de un túnel.

- Instale la Bot Framework Emulator versión 4.9.0 o posterior desde [aquí](https://github.com/Microsoft/BotFramework-Emulator/releases)

### <a name="connect-to-the-bot-using-bot-framework-emulator"></a>Conectar al bot mediante Bot Framework Emulator

- Iniciar Bot Framework Emulator
- File -> Open Bot
- Escriba una dirección URL de bot de `http://localhost:3978/api/messages`

## <a name="deploy-the-bot-to-azure"></a>Implementar el bot en Azure

Para obtener más información sobre cómo implementar un bot en Azure, consulte [Deploy your bot to Azure](https://aka.ms/azuredeployment) para obtener una lista completa de instrucciones de implementación.

## <a name="further-reading"></a>Lectura adicional

- [Documentación de Bot Framework](https://docs.botframework.com)
- [Conceptos básicos del bot](https://docs.microsoft.com/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0)
- [Procesamiento de actividad](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-activity-processing?view=azure-bot-service-4.0)
- [Introducción al servicio bot de Azure](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- [Documentación del servicio bot de Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)
- [Herramientas de la CLI de .NET Core](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
- [Azure Portal](https://portal.azure.com)
- [Language Understanding using LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/)
- [Canales y servicio de conector de bot](https://docs.microsoft.com/en-us/azure/bot-service/bot-concepts?view=azure-bot-service-4.0)

Generado con `dotnet new echobot` v4.13.2
