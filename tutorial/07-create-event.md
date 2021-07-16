---
ms.openlocfilehash: dc95fc9f1ca24e0a1e3cd16e93597057ebbae787
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446740"
---
<!-- markdownlint-disable MD002 MD041 -->

En esta sección, usará el SDK de Microsoft Graph para agregar un evento al calendario del usuario.

## <a name="implement-a-dialog"></a>Implementar un cuadro de diálogo

Comience creando un nuevo cuadro de diálogo personalizado para solicitar al usuario los valores necesarios para agregar un evento a su calendario. Este cuadro de diálogo usará **un WaterfallDialog** para realizar los pasos siguientes.

- Preguntar por un asunto
- Preguntar si el usuario quiere invitar a personas
- Preguntar a los asistentes (si el usuario ha dicho que sí al paso anterior)
- Solicitar una fecha y hora de inicio
- Solicitar una fecha y hora de finalización
- Mostrar todos los valores recopilados y pedir al usuario que confirme
- Si el usuario lo confirma, obtenga el token de acceso de **OAuthPrompt**
- Crear el evento

1. Cree un nuevo archivo en el directorio **./Dialogs** denominado **NewEventDialog.cs** y agregue el código siguiente.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using CalendarBot.Graph;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.Graph;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    using TimexTypes = Microsoft.Recognizers.Text.DataTypes.TimexExpression.Constants.TimexTypes;

    namespace CalendarBot.Dialogs
    {
        public class NewEventDialog : LogoutDialog
        {
            protected readonly ILogger _logger;
            private readonly IGraphClientService _graphClientService;

            public NewEventDialog(
                IConfiguration configuration,
                IGraphClientService graphClientService)
                : base(nameof(NewEventDialog), configuration["ConnectionName"])
            {

            }

            // Generate a DateTime from the list of
            // DateTimeResolutions provided by the DateTimePrompt
            private static DateTime GetDateTimeFromResolutions(IList<DateTimeResolution> resolutions)
            {
                var timex = new TimexProperty(resolutions[0].Timex);

                // Handle the "now" case
                if (timex.Now ?? false)
                {
                    return DateTime.Now;
                }

                // Otherwise generate a DateTime
                return TimexHelpers.DateFromTimex(timex);
            }
        }
    }
    ```

    Este es el shell de un nuevo cuadro de diálogo que pedirá al usuario los valores necesarios para agregar un evento a su calendario.

1. Agregue la siguiente función a la **clase NewEventDialog** para solicitar al usuario un asunto.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForSubjectSnippet":::

1. Agregue la siguiente función a la **clase NewEventDialog** para almacenar el asunto que el usuario dio en el paso anterior y para preguntar si desea agregar asistentes.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAddAttendeesSnippet":::

1. Agregue la siguiente función a la **clase NewEventDialog** para comprobar la respuesta del usuario del paso anterior y solicitar al usuario una lista de asistentes si es necesario.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAttendeesSnippet":::

1. Agregue la siguiente función a la **clase NewEventDialog** para almacenar la lista de asistentes del paso anterior (si está presente) y solicitar al usuario una fecha y hora de inicio.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForStartSnippet":::

1. Agregue la siguiente función a la **clase NewEventDialog** para almacenar el valor de inicio del paso anterior y solicitar al usuario una fecha y hora de finalización.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForEndSnippet":::

1. Agregue la siguiente función a la **clase NewEventDialog** para almacenar el valor final del paso anterior y pida al usuario que confirme todas las entradas.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConfirmNewEventSnippet":::

1. Agregue la siguiente función a la **clase NewEventDialog** para comprobar la respuesta del usuario del paso anterior. Si el usuario confirma las entradas, use la clase **OAuthPrompt** para obtener un token de acceso. De lo contrario, finalice el cuadro de diálogo.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="GetTokenSnippet":::

1. Agregue la siguiente función a la **clase NewEventDialog** para usar el SDK de Microsoft Graph para crear el nuevo evento.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AddEventSnippet":::

    Tenga en cuenta lo que hace este código.

    - Obtiene mailboxSettings del **usuario** para determinar la zona horaria preferida del usuario.
    - Crea un **objeto Event** con los valores proporcionados por el usuario. Tenga en cuenta **que las propiedades Start** y **End** se establecen con la zona horaria del usuario.
    - Crea el evento en el calendario del usuario.

## <a name="add-validation"></a>Agregar validación

Ahora agregue validación a la entrada del usuario para evitar errores al crear el evento con Microsoft Graph. Queremos asegurarnos de que:

- Si el usuario proporciona una lista de asistentes, debe ser una lista delimitada por punto y coma de direcciones de correo electrónico válidas.
- La fecha y hora de inicio debe ser una fecha y hora válidas.
- La fecha y hora de finalización debe ser una fecha y hora válidas y debe ser posterior al inicio.

1. Agregue la siguiente función a la **clase NewEventDialog** para validar la entrada del usuario para los asistentes.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AttendeesValidatorSnippet":::

1. Agregue las siguientes funciones a la **clase NewEventDialog** para validar la entrada del usuario para la fecha y hora de inicio.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="StartValidatorSnippet":::

1. Agregue la siguiente función a la **clase NewEventDialog** para validar la entrada del usuario para la fecha y hora de finalización.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="EndValidatorSnippet":::

## <a name="add-steps-to-waterfalldialog"></a>Agregar pasos a WaterfallDialog

Ahora que ya tiene todos los "pasos" para el cuadro de diálogo, el último paso es agregarlos a **un WaterfallDialog** en el constructor y, a continuación, agregar **newEventDialog** a **MainDialog**.

1. Busque el constructor **NewEventDialog** y agréle el siguiente código.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConstructorSnippet":::

    Esto agrega todos los cuadros de diálogo que se usan y agrega todas las funciones que implementó como pasos en **WaterfallDialog**.

1. Abra **./Dialogs/MainDialog.cs** y agregue la siguiente línea al constructor.

    ```csharp
    AddDialog(new NewEventDialog(configuration, graphClientService));
    ```

1. Reemplace el código dentro del `else if (command.StartsWith("add event"))` bloque `ProcessStepAsync` por lo siguiente.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="AddEventSnippet" highlight="3":::

1. Guarde todos los cambios y reinicie el bot.

1. Use el Bot Framework Emulator para conectarse al bot e iniciar sesión. Seleccione el **botón Agregar evento.**

1. Responda a los avisos para crear un nuevo evento. Tenga en cuenta que cuando se le soliciten los valores de inicio y finalización, puede usar frases como "hoy a las 3PM" o "ahora".

    ![Captura de pantalla del cuadro de diálogo agregar evento en el Bot Framework Emulator](images/add-event.png)
