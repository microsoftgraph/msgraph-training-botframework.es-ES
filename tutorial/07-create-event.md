---
ms.openlocfilehash: dc95fc9f1ca24e0a1e3cd16e93597057ebbae787
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446740"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="2bbd5-101">En esta sección, usará el SDK de Microsoft Graph para agregar un evento al calendario del usuario.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-101">In this section you'll use the Microsoft Graph SDK to add an event to the user's calendar.</span></span>

## <a name="implement-a-dialog"></a><span data-ttu-id="2bbd5-102">Implementar un cuadro de diálogo</span><span class="sxs-lookup"><span data-stu-id="2bbd5-102">Implement a dialog</span></span>

<span data-ttu-id="2bbd5-103">Comience creando un nuevo cuadro de diálogo personalizado para solicitar al usuario los valores necesarios para agregar un evento a su calendario.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-103">Start by creating a new custom dialog to prompt the user for the values needed to add an event to their calendar.</span></span> <span data-ttu-id="2bbd5-104">Este cuadro de diálogo usará **un WaterfallDialog** para realizar los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-104">This dialog will use a **WaterfallDialog** to do the following steps.</span></span>

- <span data-ttu-id="2bbd5-105">Preguntar por un asunto</span><span class="sxs-lookup"><span data-stu-id="2bbd5-105">Prompt for a subject</span></span>
- <span data-ttu-id="2bbd5-106">Preguntar si el usuario quiere invitar a personas</span><span class="sxs-lookup"><span data-stu-id="2bbd5-106">Ask if the user wants to invite people</span></span>
- <span data-ttu-id="2bbd5-107">Preguntar a los asistentes (si el usuario ha dicho que sí al paso anterior)</span><span class="sxs-lookup"><span data-stu-id="2bbd5-107">Prompt for attendees (if user said yes to previous step)</span></span>
- <span data-ttu-id="2bbd5-108">Solicitar una fecha y hora de inicio</span><span class="sxs-lookup"><span data-stu-id="2bbd5-108">Prompt for a start date and time</span></span>
- <span data-ttu-id="2bbd5-109">Solicitar una fecha y hora de finalización</span><span class="sxs-lookup"><span data-stu-id="2bbd5-109">Prompt for an end date and time</span></span>
- <span data-ttu-id="2bbd5-110">Mostrar todos los valores recopilados y pedir al usuario que confirme</span><span class="sxs-lookup"><span data-stu-id="2bbd5-110">Display all of the collected values and ask user to confirm</span></span>
- <span data-ttu-id="2bbd5-111">Si el usuario lo confirma, obtenga el token de acceso de **OAuthPrompt**</span><span class="sxs-lookup"><span data-stu-id="2bbd5-111">If user confirms, get access token from **OAuthPrompt**</span></span>
- <span data-ttu-id="2bbd5-112">Crear el evento</span><span class="sxs-lookup"><span data-stu-id="2bbd5-112">Create the event</span></span>

1. <span data-ttu-id="2bbd5-113">Cree un nuevo archivo en el directorio **./Dialogs** denominado **NewEventDialog.cs** y agregue el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-113">Create a new file in the **./Dialogs** directory named **NewEventDialog.cs** and add the following code.</span></span>

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

    <span data-ttu-id="2bbd5-114">Este es el shell de un nuevo cuadro de diálogo que pedirá al usuario los valores necesarios para agregar un evento a su calendario.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-114">This is the shell of a new dialog that will prompt the user for the values needed to add an event to their calendar.</span></span>

1. <span data-ttu-id="2bbd5-115">Agregue la siguiente función a la **clase NewEventDialog** para solicitar al usuario un asunto.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-115">Add the following function to the **NewEventDialog** class to prompt the user for a subject.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForSubjectSnippet":::

1. <span data-ttu-id="2bbd5-116">Agregue la siguiente función a la **clase NewEventDialog** para almacenar el asunto que el usuario dio en el paso anterior y para preguntar si desea agregar asistentes.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-116">Add the following function to the **NewEventDialog** class to store the subject the user gave in the previous step, and to ask if they want to add attendees.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAddAttendeesSnippet":::

1. <span data-ttu-id="2bbd5-117">Agregue la siguiente función a la **clase NewEventDialog** para comprobar la respuesta del usuario del paso anterior y solicitar al usuario una lista de asistentes si es necesario.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-117">Add the following function to the **NewEventDialog** class to check the user's response from the previous step and prompt the user for a list of attendees if needed.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAttendeesSnippet":::

1. <span data-ttu-id="2bbd5-118">Agregue la siguiente función a la **clase NewEventDialog** para almacenar la lista de asistentes del paso anterior (si está presente) y solicitar al usuario una fecha y hora de inicio.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-118">Add the following function to the **NewEventDialog** class to store the list of attendees from the previous step (if present) and prompt the user for a start date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForStartSnippet":::

1. <span data-ttu-id="2bbd5-119">Agregue la siguiente función a la **clase NewEventDialog** para almacenar el valor de inicio del paso anterior y solicitar al usuario una fecha y hora de finalización.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-119">Add the following function to the **NewEventDialog** class to store the start value from the previous step and prompt the user for an end date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForEndSnippet":::

1. <span data-ttu-id="2bbd5-120">Agregue la siguiente función a la **clase NewEventDialog** para almacenar el valor final del paso anterior y pida al usuario que confirme todas las entradas.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-120">Add the following function to the **NewEventDialog** class to store the end value from the previous step and ask the user to confirm all of the inputs.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConfirmNewEventSnippet":::

1. <span data-ttu-id="2bbd5-121">Agregue la siguiente función a la **clase NewEventDialog** para comprobar la respuesta del usuario del paso anterior.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-121">Add the following function to the **NewEventDialog** class to check the user's response from the previous step.</span></span> <span data-ttu-id="2bbd5-122">Si el usuario confirma las entradas, use la clase **OAuthPrompt** para obtener un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-122">If the user confirms the inputs, use the **OAuthPrompt** class to get an access token.</span></span> <span data-ttu-id="2bbd5-123">De lo contrario, finalice el cuadro de diálogo.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-123">Otherwise, end the dialog.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="GetTokenSnippet":::

1. <span data-ttu-id="2bbd5-124">Agregue la siguiente función a la **clase NewEventDialog** para usar el SDK de Microsoft Graph para crear el nuevo evento.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-124">Add the following function to the **NewEventDialog** class to use the Microsoft Graph SDK to create the new event.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AddEventSnippet":::

    <span data-ttu-id="2bbd5-125">Tenga en cuenta lo que hace este código.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-125">Consider what this code does.</span></span>

    - <span data-ttu-id="2bbd5-126">Obtiene mailboxSettings del **usuario** para determinar la zona horaria preferida del usuario.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-126">It gets the user's **MailboxSettings** to determine the user's preferred time zone.</span></span>
    - <span data-ttu-id="2bbd5-127">Crea un **objeto Event** con los valores proporcionados por el usuario.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-127">It creates an **Event** object using the values provided by the user.</span></span> <span data-ttu-id="2bbd5-128">Tenga en cuenta **que las propiedades Start** y **End** se establecen con la zona horaria del usuario.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-128">Note that the **Start** and **End** properties are set with the user's time zone.</span></span>
    - <span data-ttu-id="2bbd5-129">Crea el evento en el calendario del usuario.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-129">It creates the event on the user's calendar.</span></span>

## <a name="add-validation"></a><span data-ttu-id="2bbd5-130">Agregar validación</span><span class="sxs-lookup"><span data-stu-id="2bbd5-130">Add validation</span></span>

<span data-ttu-id="2bbd5-131">Ahora agregue validación a la entrada del usuario para evitar errores al crear el evento con Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-131">Now add validation to the user's input to avoid errors when creating the event with Microsoft Graph.</span></span> <span data-ttu-id="2bbd5-132">Queremos asegurarnos de que:</span><span class="sxs-lookup"><span data-stu-id="2bbd5-132">We want to make sure that:</span></span>

- <span data-ttu-id="2bbd5-133">Si el usuario proporciona una lista de asistentes, debe ser una lista delimitada por punto y coma de direcciones de correo electrónico válidas.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-133">If the user gives an attendee list, it should be a semicolon-delimited list of valid email addresses.</span></span>
- <span data-ttu-id="2bbd5-134">La fecha y hora de inicio debe ser una fecha y hora válidas.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-134">The start date/time should be a valid date and time.</span></span>
- <span data-ttu-id="2bbd5-135">La fecha y hora de finalización debe ser una fecha y hora válidas y debe ser posterior al inicio.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-135">The end date/time should be a valid date and time, and should be later than the start.</span></span>

1. <span data-ttu-id="2bbd5-136">Agregue la siguiente función a la **clase NewEventDialog** para validar la entrada del usuario para los asistentes.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-136">Add the following function to the **NewEventDialog** class to validate the user's entry for attendees.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AttendeesValidatorSnippet":::

1. <span data-ttu-id="2bbd5-137">Agregue las siguientes funciones a la **clase NewEventDialog** para validar la entrada del usuario para la fecha y hora de inicio.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-137">Add the following functions to the **NewEventDialog** class to validate the user's entry for start date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="StartValidatorSnippet":::

1. <span data-ttu-id="2bbd5-138">Agregue la siguiente función a la **clase NewEventDialog** para validar la entrada del usuario para la fecha y hora de finalización.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-138">Add the following function to the **NewEventDialog** class to validate the user's entry for end date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="EndValidatorSnippet":::

## <a name="add-steps-to-waterfalldialog"></a><span data-ttu-id="2bbd5-139">Agregar pasos a WaterfallDialog</span><span class="sxs-lookup"><span data-stu-id="2bbd5-139">Add steps to WaterfallDialog</span></span>

<span data-ttu-id="2bbd5-140">Ahora que ya tiene todos los "pasos" para el cuadro de diálogo, el último paso es agregarlos a **un WaterfallDialog** en el constructor y, a continuación, agregar **newEventDialog** a **MainDialog**.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-140">Now that you have all of the "steps" for the dialog, the last step is to add them to a **WaterfallDialog** in the constructor, then add the **NewEventDialog** to the **MainDialog**.</span></span>

1. <span data-ttu-id="2bbd5-141">Busque el constructor **NewEventDialog** y agréle el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-141">Locate the **NewEventDialog** constructor and add the following code to it.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConstructorSnippet":::

    <span data-ttu-id="2bbd5-142">Esto agrega todos los cuadros de diálogo que se usan y agrega todas las funciones que implementó como pasos en **WaterfallDialog**.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-142">This adds all of the dialogs that are used, and adds all of the functions you implemented as steps in the **WaterfallDialog**.</span></span>

1. <span data-ttu-id="2bbd5-143">Abra **./Dialogs/MainDialog.cs** y agregue la siguiente línea al constructor.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-143">Open **./Dialogs/MainDialog.cs** and add the following line to the constructor.</span></span>

    ```csharp
    AddDialog(new NewEventDialog(configuration, graphClientService));
    ```

1. <span data-ttu-id="2bbd5-144">Reemplace el código dentro del `else if (command.StartsWith("add event"))` bloque `ProcessStepAsync` por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-144">Replace the code inside the `else if (command.StartsWith("add event"))` block in `ProcessStepAsync` with the following.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="AddEventSnippet" highlight="3":::

1. <span data-ttu-id="2bbd5-145">Guarde todos los cambios y reinicie el bot.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-145">Save all of your changes and restart the bot.</span></span>

1. <span data-ttu-id="2bbd5-146">Use el Bot Framework Emulator para conectarse al bot e iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-146">Use the Bot Framework Emulator to connect to the bot and log in.</span></span> <span data-ttu-id="2bbd5-147">Seleccione el **botón Agregar evento.**</span><span class="sxs-lookup"><span data-stu-id="2bbd5-147">Select the **Add event** button.</span></span>

1. <span data-ttu-id="2bbd5-148">Responda a los avisos para crear un nuevo evento.</span><span class="sxs-lookup"><span data-stu-id="2bbd5-148">Respond to the prompts to create a new event.</span></span> <span data-ttu-id="2bbd5-149">Tenga en cuenta que cuando se le soliciten los valores de inicio y finalización, puede usar frases como "hoy a las 3PM" o "ahora".</span><span class="sxs-lookup"><span data-stu-id="2bbd5-149">Note that when prompted for start and end values, you can use phrases like "today at 3PM", or "now".</span></span>

    ![Captura de pantalla del cuadro de diálogo agregar evento en el Bot Framework Emulator](images/add-event.png)
