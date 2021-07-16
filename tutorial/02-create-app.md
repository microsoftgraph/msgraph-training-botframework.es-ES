---
ms.openlocfilehash: 51878a3eebbcacb9ea325a3f97453d30006be116
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446781"
---
<!-- markdownlint-disable MD002 MD041 -->

En esta sección, creará un proyecto de Bot Framework.

1. Abra la interfaz de línea de comandos (CLI) en un directorio donde desee crear el proyecto. Ejecute el siguiente comando para crear un nuevo proyecto con la plantilla **Microsoft.Bot.Framework.CSharp.EchoBot.**

    ```dotnetcli
    dotnet new echobot -n GraphCalendarBot
    ```

    > [!NOTE]
    > Si recibe un `No templates matched the input template name: echobot.` error, instale la plantilla con el siguiente comando y vuelva a ejecutar el comando anterior.
    >
    > ```dotnetcli
    > dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
    > ```

1. Cambie el nombre de **la clase EchoBot** predeterminada **a CalendarBot**. Abra **./Bots/EchoBot.cs** y reemplace todas las instancias de `EchoBot` con `CalendarBot` . Cambie el nombre del archivo **a CalendarBot.cs**.

1. Reemplace todas las instancias de `EchoBot` with en los archivos `CalendarBot` **.cs** restantes.

1. En la CLI, cambie el directorio actual al directorio **GraphCalendarBot** y ejecute el siguiente comando para confirmar las compilaciones del proyecto.

    ```dotnetcli
    dotnet build
    ```

## <a name="add-nuget-packages"></a>Agregar paquetes NuGet

Antes de seguir, instala algunos paquetes NuGet que usarás más adelante.

- [AdaptiveCards para](https://www.nuget.org/packages/AdaptiveCards/) permitir que el bot envíe tarjetas adaptables en respuestas.
- [Microsoft.Bot.Builder.Dialogs](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) para agregar compatibilidad de cuadros de diálogo al bot.
- [Microsoft.Recognizers.Text.DataTypes.TimexExpression](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) para convertir las expresiones TIMEX devueltas de los mensajes del bot en **objetos DateTime.**
- [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) para realizar llamadas a Microsoft Graph.

1. Ejecute los siguientes comandos en la CLI para instalar las dependencias.

    ```Shell
    dotnet add package AdaptiveCards --version 2.7.1
    dotnet add package Microsoft.Bot.Builder.Dialogs --version 4.14.1
    dotnet add package Microsoft.Bot.Builder.Integration.AspNet.Core --version 4.14.1
    dotnet add package Microsoft.Recognizers.Text.DataTypes.TimexExpression --version 1.7.0
    dotnet add package Microsoft.Graph --version 4.0.0
    ```

## <a name="test-the-bot"></a>Probar el bot

Antes de agregar cualquier código, pruebe el bot para asegurarse de que funciona correctamente y de que el Bot Framework Emulator está configurado para probarlo.

1. Inicie el bot ejecutando el siguiente comando.

    ```dotnetcli
    dotnet run
    ```

    > [!TIP]
    > Aunque puede usar cualquier editor de texto para editar los archivos de origen del proyecto, se recomienda usar [Visual Studio Code](https://code.visualstudio.com/). Visual Studio Code compatibilidad con la depuración, Intellisense y mucho más. Si usa Visual Studio Code, puede iniciar el bot mediante el **menú**  ->  **Ejecutar depuración de** inicio.

1. Confirme que el bot se está ejecutando abriendo el explorador y yendo a `http://localhost:3978` . Debería ver un **bot your is ready!** Mensaje.

1. Abra el Bot Framework Emulator. Elija el **menú** Archivo y, a continuación, **Abra bot**.

1. Escriba `http://localhost:3978/api/messages` en la dirección URL del **bot** **y, a continuación, seleccione Conectar**.

1. El bot responde con `Hello and welcome!` en la ventana de chat. Envíe un mensaje al bot y confirme que lo vuelve a hacer eco.

    ![Captura de pantalla del Bot Framework Emulator conectado al bot](images/test-emulator.png)
