---
ms.openlocfilehash: f8b2a2b4e1c5d42c74398616513204559058a5dc
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446795"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ef34e-101">En este ejercicio, usará el **OAuthPrompt** de Bot Framework para implementar la autenticación en el bot y adquirir tokens de acceso para llamar a la API Graph Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ef34e-101">In this exercise you will use the Bot Framework's **OAuthPrompt** to implement authentication in the bot, and acquire access tokens for calling the Microsoft Graph API.</span></span>

1. <span data-ttu-id="ef34e-102">Abra **./appsettings.jsy** realice los siguientes cambios.</span><span class="sxs-lookup"><span data-stu-id="ef34e-102">Open **./appsettings.json** and make the following changes.</span></span>

    - <span data-ttu-id="ef34e-103">Cambie el valor del identificador de aplicación de su Graph registro de la aplicación `MicrosoftAppId` **bot** de calendario.</span><span class="sxs-lookup"><span data-stu-id="ef34e-103">Change the value of `MicrosoftAppId` to the application ID of your **Graph Calendar Bot** app registration.</span></span>
    - <span data-ttu-id="ef34e-104">Cambie el valor del `MicrosoftAppPassword` secreto de cliente Graph bot **de** calendario.</span><span class="sxs-lookup"><span data-stu-id="ef34e-104">Change the value of `MicrosoftAppPassword` to your **Graph Calendar Bot** client secret.</span></span>
    - <span data-ttu-id="ef34e-105">Agregue un valor denominado `ConnectionName` con un valor de `GraphBotAuth` .</span><span class="sxs-lookup"><span data-stu-id="ef34e-105">Add a value named `ConnectionName` with a value of `GraphBotAuth`.</span></span>

    :::code language="json" source="../demo/GraphCalendarBot/appsettings.example.json":::

    > [!NOTE]
    > <span data-ttu-id="ef34e-106">Si usó un valor distinto del nombre de la entrada en `GraphBotAuth` **OAuth Connection Configuración** en Azure Portal, use ese valor para la `ConnectionName` entrada.</span><span class="sxs-lookup"><span data-stu-id="ef34e-106">If you used a value other than `GraphBotAuth` for the name of your entry in **OAuth Connection Settings** in the Azure Portal, use that value for the `ConnectionName` entry.</span></span>

## <a name="implement-dialogs"></a><span data-ttu-id="ef34e-107">Implementar cuadros de diálogo</span><span class="sxs-lookup"><span data-stu-id="ef34e-107">Implement dialogs</span></span>

1. <span data-ttu-id="ef34e-108">Cree un nuevo directorio en la raíz del proyecto denominado **Dialogs**.</span><span class="sxs-lookup"><span data-stu-id="ef34e-108">Create a new directory in the root of the project named **Dialogs**.</span></span> <span data-ttu-id="ef34e-109">Cree un nuevo archivo en el directorio **./Dialogs** denominado **LogoutDialog.cs** y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="ef34e-109">Create a new file in the **./Dialogs** directory named **LogoutDialog.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/LogoutDialog.cs" id="LogoutDialogSnippet":::

    <span data-ttu-id="ef34e-110">Este cuadro de diálogo proporciona una clase base para todos los demás cuadros de diálogo del bot de los que se derivará.</span><span class="sxs-lookup"><span data-stu-id="ef34e-110">This dialog provides a base class for all of the other dialogs in the bot to derive from.</span></span> <span data-ttu-id="ef34e-111">Esto permite al usuario cerrar sesión independientemente de dónde se encuentran en los cuadros de diálogo del bot.</span><span class="sxs-lookup"><span data-stu-id="ef34e-111">This allows the user to log out no matter where they are in the bot's dialogs.</span></span>

1. <span data-ttu-id="ef34e-112">Cree un nuevo archivo en el directorio **./Dialogs** denominado **MainDialog.cs** y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="ef34e-112">Create a new file in the **./Dialogs** directory named **MainDialog.cs** and add the following code.</span></span>

    ```csharp
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Dialogs.Choices;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;

    namespace CalendarBot.Dialogs
    {
        public class MainDialog : LogoutDialog
        {
            const string NO_PROMPT = "no-prompt";
            protected readonly ILogger _logger;

            public MainDialog(IConfiguration configuration, ILogger<MainDialog> logger)
                : base(nameof(MainDialog), configuration["ConnectionName"])
            {
                _logger = logger;

                // OAuthPrompt dialog handles the authentication and token
                // acquisition
                AddDialog(new OAuthPrompt(
                    nameof(OAuthPrompt),
                    new OAuthPromptSettings
                    {
                        ConnectionName = ConnectionName,
                        Text = "Please login",
                        Title = "Login",
                        Timeout = 300000, // User has 5 minutes to login
                    }));

                AddDialog(new ChoicePrompt(nameof(ChoicePrompt)));

                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    LoginPromptStepAsync,
                    ProcessLoginStepAsync,
                    PromptUserStepAsync,
                    CommandStepAsync,
                    ProcessStepAsync,
                    ReturnToPromptStepAsync
                }));

                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }

            private async Task<DialogTurnResult> LoginPromptStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // If we're going through the waterfall a second time, don't do an extra OAuthPrompt
                var options = stepContext.Options?.ToString();
                if (options == NO_PROMPT)
                {
                    return await stepContext.NextAsync(cancellationToken: cancellationToken);
                }

                return await stepContext.BeginDialogAsync(nameof(OAuthPrompt), null, cancellationToken);
            }

            private async Task<DialogTurnResult> ProcessLoginStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // If we're going through the waterfall a second time, don't do an extra OAuthPrompt
                var options = stepContext.Options?.ToString();
                if (options == NO_PROMPT)
                {
                    return await stepContext.NextAsync(cancellationToken: cancellationToken);
                }

                // Get the token from the previous step. If it's there, login was successful
                if (stepContext.Result != null)
                {
                    var tokenResponse = stepContext.Result as TokenResponse;
                    if (!string.IsNullOrEmpty(tokenResponse?.Token))
                    {
                        await stepContext.Context.SendActivityAsync(
                            MessageFactory.Text("You are now logged in."), cancellationToken);
                        return await stepContext.NextAsync(null, cancellationToken);
                    }
                }

                await stepContext.Context.SendActivityAsync(
                    MessageFactory.Text("Login was not successful please try again."), cancellationToken);
                return await stepContext.EndDialogAsync();
            }

            private async Task<DialogTurnResult> PromptUserStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                var options = new PromptOptions
                {
                    Prompt = MessageFactory.Text("Please choose an option below"),
                    Choices = new List<Choice> {
                        new Choice { Value = "Show token" },
                        new Choice { Value = "Show me" },
                        new Choice { Value = "Show calendar" },
                        new Choice { Value = "Add event" },
                        new Choice { Value = "Log out" },
                    }
                };

                return await stepContext.PromptAsync(
                    nameof(ChoicePrompt),
                    options,
                    cancellationToken);
            }

            private async Task<DialogTurnResult> CommandStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // Save the command the user entered so we can get it back after
                // the OAuthPrompt completes
                var foundChoice = stepContext.Result as FoundChoice;
                // Result could be a FoundChoice (if user selected a choice button)
                // or a string (if user just typed something)
                stepContext.Values["command"] = foundChoice?.Value ?? stepContext.Result;

                // There is no reason to store the token locally in the bot because we can always just call
                // the OAuth prompt to get the token or get a new token if needed. The prompt completes silently
                // if the user is already signed in.
                return await stepContext.BeginDialogAsync(nameof(OAuthPrompt), null, cancellationToken);
            }

            private async Task<DialogTurnResult> ProcessStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                if (stepContext.Result != null)
                {
                    var tokenResponse = stepContext.Result as TokenResponse;

                    // If we have the token use the user is authenticated so we may use it to make API calls.
                    if (tokenResponse?.Token != null)
                    {
                        var command = ((string)stepContext.Values["command"] ?? string.Empty).ToLowerInvariant();

                        if (command.StartsWith("show token"))
                        {
                            // Show the user's token - for testing and troubleshooting
                            // Generally production apps should not display access tokens
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text($"Your token is: {tokenResponse.Token}"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("show me"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("show calendar"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("add event"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I'm sorry, I didn't understand. Please try again."),
                                cancellationToken);
                        }
                    }
                }
                else
                {
                    await stepContext.Context.SendActivityAsync(
                        MessageFactory.Text("We couldn't log you in. Please try again later."),
                        cancellationToken);
                    return await stepContext.EndDialogAsync(cancellationToken: cancellationToken);
                }

                // Go to the next step
                return await stepContext.NextAsync(cancellationToken: cancellationToken);
            }

            private async Task<DialogTurnResult> ReturnToPromptStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // Restart the dialog, but skip the initial login prompt
                return await stepContext.ReplaceDialogAsync(InitialDialogId, NO_PROMPT, cancellationToken);
            }
        }
    }
    ```

    <span data-ttu-id="ef34e-113">Tómese un momento para revisar este código.</span><span class="sxs-lookup"><span data-stu-id="ef34e-113">Take a moment to review this code.</span></span>

    - <span data-ttu-id="ef34e-114">En el constructor, configura un [WaterfallDialog](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) con un conjunto de pasos que se producen en orden.</span><span class="sxs-lookup"><span data-stu-id="ef34e-114">In the constructor, it sets up a [WaterfallDialog](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) with a set of steps that occur in order.</span></span>
        - <span data-ttu-id="ef34e-115">En `LoginPromptStepAsync` él se envía un **OAuthPrompt**.</span><span class="sxs-lookup"><span data-stu-id="ef34e-115">In `LoginPromptStepAsync` it sends an **OAuthPrompt**.</span></span> <span data-ttu-id="ef34e-116">Si el usuario no ha iniciado sesión, se enviará un mensaje de interfaz de usuario al usuario.</span><span class="sxs-lookup"><span data-stu-id="ef34e-116">If the user isn't logged in, this will send a UI prompt to the user.</span></span>
        - <span data-ttu-id="ef34e-117">En `ProcessLoginStepAsync` él comprueba si el inicio de sesión se ha realizado correctamente y envía una confirmación.</span><span class="sxs-lookup"><span data-stu-id="ef34e-117">In `ProcessLoginStepAsync` it checks if the login was successful and sends a confirmation.</span></span>
        - <span data-ttu-id="ef34e-118">En `PromptUserStepAsync` él se envía un **ChoicePrompt** con los comandos disponibles.</span><span class="sxs-lookup"><span data-stu-id="ef34e-118">In `PromptUserStepAsync` it sends a **ChoicePrompt** with the available commands.</span></span>
        - <span data-ttu-id="ef34e-119">En `CommandStepAsync` ella se guarda la elección del usuario y, a continuación, se vuelve a enviar un **OAuthPrompt**.</span><span class="sxs-lookup"><span data-stu-id="ef34e-119">In `CommandStepAsync` it saves the user's choice, then resends an **OAuthPrompt**.</span></span>
        - <span data-ttu-id="ef34e-120">En `ProcessStepAsync` ella se realiza una acción basada en el comando recibido.</span><span class="sxs-lookup"><span data-stu-id="ef34e-120">In `ProcessStepAsync` it takes action based on the command received.</span></span>
        - <span data-ttu-id="ef34e-121">In `ReturnToPromptStepAsync` it starts the waterfall over, but passes a flag to skip the initial user login.</span><span class="sxs-lookup"><span data-stu-id="ef34e-121">In `ReturnToPromptStepAsync` it starts the waterfall over, but passes a flag to skip the initial user login.</span></span>

## <a name="update-calendarbot"></a><span data-ttu-id="ef34e-122">Actualizar CalendarBot</span><span class="sxs-lookup"><span data-stu-id="ef34e-122">Update CalendarBot</span></span>

<span data-ttu-id="ef34e-123">El siguiente paso es actualizar **CalendarBot** para usar estos nuevos cuadros de diálogo.</span><span class="sxs-lookup"><span data-stu-id="ef34e-123">The next step is to update **CalendarBot** to use these new dialogs.</span></span>

1. <span data-ttu-id="ef34e-124">Abra **./Bots/CalendarBot.cs** y reemplace todo su contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="ef34e-124">Open **./Bots/CalendarBot.cs** and replace its entire contents with the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Bots/CalendarBot.cs" id="CalendarBotSnippet":::

    <span data-ttu-id="ef34e-125">Este es un breve resumen de los cambios.</span><span class="sxs-lookup"><span data-stu-id="ef34e-125">Here's a brief summary of the changes.</span></span>

    - <span data-ttu-id="ef34e-126">Se **cambió la clase CalendarBot** para que sea una clase de plantilla, recibiendo un **cuadro de diálogo**.</span><span class="sxs-lookup"><span data-stu-id="ef34e-126">Changed the **CalendarBot** class to be a template class, receiving a **Dialog**.</span></span>
    - <span data-ttu-id="ef34e-127">Se **cambió la clase CalendarBot** para extender **TeamsActivityHandler,** lo que le permite iniciar sesión en Microsoft Teams.</span><span class="sxs-lookup"><span data-stu-id="ef34e-127">Changed the **CalendarBot** class to extend **TeamsActivityHandler**, allowing it to sign in in Microsoft Teams.</span></span>
    - <span data-ttu-id="ef34e-128">Se agregaron invalidaciones de método adicionales para habilitar la autenticación.</span><span class="sxs-lookup"><span data-stu-id="ef34e-128">Added additional method overrides to enable authentication.</span></span>

## <a name="update-startupcs"></a><span data-ttu-id="ef34e-129">Actualizar Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ef34e-129">Update Startup.cs</span></span>

<span data-ttu-id="ef34e-130">El último paso es actualizar el método para agregar los servicios necesarios `ConfigureServices` para la autenticación y el nuevo cuadro de diálogo.</span><span class="sxs-lookup"><span data-stu-id="ef34e-130">The final step is to update the `ConfigureServices` method to add the services needed for authentication and the new dialog.</span></span>

1. <span data-ttu-id="ef34e-131">Abra **./Startup.cs** y quite la `services.AddTransient<IBot, Bots.CalendarBot>();` línea del `ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="ef34e-131">Open **./Startup.cs** and remove the `services.AddTransient<IBot, Bots.CalendarBot>();` line from the `ConfigureServices` method.</span></span>

1. <span data-ttu-id="ef34e-132">Inserte el siguiente código al final del `ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="ef34e-132">Insert the following code at the end of the `ConfigureServices` method.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="ConfigureServiceSnippet":::

## <a name="test-authentication"></a><span data-ttu-id="ef34e-133">Autenticación de prueba</span><span class="sxs-lookup"><span data-stu-id="ef34e-133">Test authentication</span></span>

1. <span data-ttu-id="ef34e-134">Guarde todos los cambios e inicie el bot con `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="ef34e-134">Save all of your changes and start the bot with `dotnet run`.</span></span>

1. <span data-ttu-id="ef34e-135">Abra el Bot Framework Emulator.</span><span class="sxs-lookup"><span data-stu-id="ef34e-135">Open the Bot Framework Emulator.</span></span> <span data-ttu-id="ef34e-136">Seleccione el icono de engranaje &#9881; en la parte inferior izquierda.</span><span class="sxs-lookup"><span data-stu-id="ef34e-136">Select the gear icon &#9881; on the bottom left.</span></span>

1. <span data-ttu-id="ef34e-137">Escriba la ruta de acceso local a la instalación de ngrok y habilite omitir **ngrok** para direcciones locales y **Ejecutar ngrok** cuando el Emulator inicie las opciones.</span><span class="sxs-lookup"><span data-stu-id="ef34e-137">Enter the local path to your installation of ngrok, and enable the **Bypass ngrok for local addresses** and **Run ngrok when the Emulator starts up** options.</span></span> <span data-ttu-id="ef34e-138">Seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="ef34e-138">Select **Save**.</span></span>

1. <span data-ttu-id="ef34e-139">Seleccione el **menú** Archivo y, a continuación, **Nueva configuración del bot...**.</span><span class="sxs-lookup"><span data-stu-id="ef34e-139">Select the **File** menu, then **New Bot Configuration...**.</span></span>

1. <span data-ttu-id="ef34e-140">Rellene los campos de la siguiente manera.</span><span class="sxs-lookup"><span data-stu-id="ef34e-140">Fill in the fields as follows.</span></span>

    - <span data-ttu-id="ef34e-141">**Nombre del bot:**`CalendarBot`</span><span class="sxs-lookup"><span data-stu-id="ef34e-141">**Bot name:** `CalendarBot`</span></span>
    - <span data-ttu-id="ef34e-142">**Dirección URL del extremo:**`https://localhost:3978/api/messages`</span><span class="sxs-lookup"><span data-stu-id="ef34e-142">**Endpoint URL:** `https://localhost:3978/api/messages`</span></span>
    - <span data-ttu-id="ef34e-143">**Id. de aplicación de Microsoft:** el identificador de aplicación de su **Graph registro de la** aplicación bot de calendario</span><span class="sxs-lookup"><span data-stu-id="ef34e-143">**Microsoft App ID:** the application ID of your **Graph Calendar Bot** app registration</span></span>
    - <span data-ttu-id="ef34e-144">**Contraseña de la aplicación microsoft:** el **secreto Graph cliente del bot de** calendario</span><span class="sxs-lookup"><span data-stu-id="ef34e-144">**Microsoft App password:** your **Graph Calendar Bot** client secret</span></span>
    - <span data-ttu-id="ef34e-145">**Cifrar claves almacenadas en la configuración del bot:** Habilitado</span><span class="sxs-lookup"><span data-stu-id="ef34e-145">**Encrypt keys stored in your bot configuration:** Enabled</span></span>

    ![Captura de pantalla del cuadro de diálogo Nueva configuración del bot](images/new-bot-config.png)

1. <span data-ttu-id="ef34e-147">Seleccione **Guardar y conectar**.</span><span class="sxs-lookup"><span data-stu-id="ef34e-147">Select **Save and connect**.</span></span> <span data-ttu-id="ef34e-148">Después de conectar el emulador, debería ver `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`</span><span class="sxs-lookup"><span data-stu-id="ef34e-148">After the emulator connects, you should see `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`</span></span>

1. <span data-ttu-id="ef34e-149">Escriba texto y envíelo al bot.</span><span class="sxs-lookup"><span data-stu-id="ef34e-149">Type some text and send it to the bot.</span></span> <span data-ttu-id="ef34e-150">El bot responde con un mensaje de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ef34e-150">The bot responds with a login prompt.</span></span>

1. <span data-ttu-id="ef34e-151">Seleccione el **botón Inicio de** sesión.</span><span class="sxs-lookup"><span data-stu-id="ef34e-151">Select the **Login** button.</span></span> <span data-ttu-id="ef34e-152">El emulador le pide que confirme la dirección URL que empieza por `oauthlink://https://token.botframeworkcom` .</span><span class="sxs-lookup"><span data-stu-id="ef34e-152">The emulator prompts you to confirm the URL that starts with `oauthlink://https://token.botframeworkcom`.</span></span> <span data-ttu-id="ef34e-153">Seleccione **Confirmar** para continuar.</span><span class="sxs-lookup"><span data-stu-id="ef34e-153">Select **Confirm** to continue.</span></span>

1. <span data-ttu-id="ef34e-154">En la ventana emergente, inicie sesión con su Microsoft 365 cuenta.</span><span class="sxs-lookup"><span data-stu-id="ef34e-154">In the pop-up window, login with your Microsoft 365 account.</span></span> <span data-ttu-id="ef34e-155">Revise los permisos solicitados y acepte.</span><span class="sxs-lookup"><span data-stu-id="ef34e-155">Review the requested permissions and accept.</span></span>

1. <span data-ttu-id="ef34e-156">Una vez completada la autenticación y el consentimiento, la ventana emergente proporciona un código de validación.</span><span class="sxs-lookup"><span data-stu-id="ef34e-156">Once authentication and consent are complete, the pop-up window provides a validation code.</span></span> <span data-ttu-id="ef34e-157">Copie el código y cierre la ventana.</span><span class="sxs-lookup"><span data-stu-id="ef34e-157">Copy the code and close the window.</span></span>

    ![Una captura de pantalla del Bot Framework Emulator de validación](images/validation-code.png)

1. <span data-ttu-id="ef34e-159">Escriba el código de validación en la ventana de chat para completar el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ef34e-159">Enter the validation code in the chat window to complete the login.</span></span>

    ![Captura de pantalla de la conversación de inicio de sesión con el bot de ejemplo](images/bot-login.png)

1. <span data-ttu-id="ef34e-161">Si selecciona el botón **Mostrar token** (o `show token` tipo), el bot muestra el token de acceso.</span><span class="sxs-lookup"><span data-stu-id="ef34e-161">If you select the **Show token** button (or type `show token`), the bot displays the access token.</span></span> <span data-ttu-id="ef34e-162">El **botón Cerrar sesión** (o escribir ) le `log out` cerrará la sesión.</span><span class="sxs-lookup"><span data-stu-id="ef34e-162">The **Log out** button (or typing `log out`) will log you out.</span></span>

> [!TIP]
> <span data-ttu-id="ef34e-163">Es posible que reciba el siguiente mensaje de error en la Bot Framework Emulator al iniciar una conversación con el bot.</span><span class="sxs-lookup"><span data-stu-id="ef34e-163">You may receive the following error message in the Bot Framework Emulator when starting a conversation with the bot.</span></span>
>
> ```text
> Failed to generate an actual sign-in link: Error: Failed to connect to ngrok instance for OAuth postback URL:
> FetchError: request to http://127.0.0.1:4041/api/tunnels failed, reason: connect ECONNREFUSED 127.0.0.1:4041
> ```
>
> <span data-ttu-id="ef34e-164">Si esto sucede, asegúrese de habilitar la opción **Ejecutar ngrok** cuando Emulator la configuración del emulador y reinicie el emulador.</span><span class="sxs-lookup"><span data-stu-id="ef34e-164">If this happens, be sure to enable the **Run ngrok when the Emulator starts up** option in the emulator settings and restart the emulator.</span></span>
