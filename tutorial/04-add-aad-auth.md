---
ms.openlocfilehash: f8b2a2b4e1c5d42c74398616513204559058a5dc
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446795"
---
<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, usará el **OAuthPrompt** de Bot Framework para implementar la autenticación en el bot y adquirir tokens de acceso para llamar a la API Graph Microsoft.

1. Abra **./appsettings.jsy** realice los siguientes cambios.

    - Cambie el valor del identificador de aplicación de su Graph registro de la aplicación `MicrosoftAppId` **bot** de calendario.
    - Cambie el valor del `MicrosoftAppPassword` secreto de cliente Graph bot **de** calendario.
    - Agregue un valor denominado `ConnectionName` con un valor de `GraphBotAuth` .

    :::code language="json" source="../demo/GraphCalendarBot/appsettings.example.json":::

    > [!NOTE]
    > Si usó un valor distinto del nombre de la entrada en `GraphBotAuth` **OAuth Connection Configuración** en Azure Portal, use ese valor para la `ConnectionName` entrada.

## <a name="implement-dialogs"></a>Implementar cuadros de diálogo

1. Cree un nuevo directorio en la raíz del proyecto denominado **Dialogs**. Cree un nuevo archivo en el directorio **./Dialogs** denominado **LogoutDialog.cs** y agregue el siguiente código.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/LogoutDialog.cs" id="LogoutDialogSnippet":::

    Este cuadro de diálogo proporciona una clase base para todos los demás cuadros de diálogo del bot de los que se derivará. Esto permite al usuario cerrar sesión independientemente de dónde se encuentran en los cuadros de diálogo del bot.

1. Cree un nuevo archivo en el directorio **./Dialogs** denominado **MainDialog.cs** y agregue el siguiente código.

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

    Tómese un momento para revisar este código.

    - En el constructor, configura un [WaterfallDialog](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) con un conjunto de pasos que se producen en orden.
        - En `LoginPromptStepAsync` él se envía un **OAuthPrompt**. Si el usuario no ha iniciado sesión, se enviará un mensaje de interfaz de usuario al usuario.
        - En `ProcessLoginStepAsync` él comprueba si el inicio de sesión se ha realizado correctamente y envía una confirmación.
        - En `PromptUserStepAsync` él se envía un **ChoicePrompt** con los comandos disponibles.
        - En `CommandStepAsync` ella se guarda la elección del usuario y, a continuación, se vuelve a enviar un **OAuthPrompt**.
        - En `ProcessStepAsync` ella se realiza una acción basada en el comando recibido.
        - In `ReturnToPromptStepAsync` it starts the waterfall over, but passes a flag to skip the initial user login.

## <a name="update-calendarbot"></a>Actualizar CalendarBot

El siguiente paso es actualizar **CalendarBot** para usar estos nuevos cuadros de diálogo.

1. Abra **./Bots/CalendarBot.cs** y reemplace todo su contenido por el código siguiente.

    :::code language="csharp" source="../demo/GraphCalendarBot/Bots/CalendarBot.cs" id="CalendarBotSnippet":::

    Este es un breve resumen de los cambios.

    - Se **cambió la clase CalendarBot** para que sea una clase de plantilla, recibiendo un **cuadro de diálogo**.
    - Se **cambió la clase CalendarBot** para extender **TeamsActivityHandler,** lo que le permite iniciar sesión en Microsoft Teams.
    - Se agregaron invalidaciones de método adicionales para habilitar la autenticación.

## <a name="update-startupcs"></a>Actualizar Startup.cs

El último paso es actualizar el método para agregar los servicios necesarios `ConfigureServices` para la autenticación y el nuevo cuadro de diálogo.

1. Abra **./Startup.cs** y quite la `services.AddTransient<IBot, Bots.CalendarBot>();` línea del `ConfigureServices` método.

1. Inserte el siguiente código al final del `ConfigureServices` método.

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="ConfigureServiceSnippet":::

## <a name="test-authentication"></a>Autenticación de prueba

1. Guarde todos los cambios e inicie el bot con `dotnet run` .

1. Abra el Bot Framework Emulator. Seleccione el icono de engranaje &#9881; en la parte inferior izquierda.

1. Escriba la ruta de acceso local a la instalación de ngrok y habilite omitir **ngrok** para direcciones locales y **Ejecutar ngrok** cuando el Emulator inicie las opciones. Seleccione **Guardar**.

1. Seleccione el **menú** Archivo y, a continuación, **Nueva configuración del bot...**.

1. Rellene los campos de la siguiente manera.

    - **Nombre del bot:**`CalendarBot`
    - **Dirección URL del extremo:**`https://localhost:3978/api/messages`
    - **Id. de aplicación de Microsoft:** el identificador de aplicación de su **Graph registro de la** aplicación bot de calendario
    - **Contraseña de la aplicación microsoft:** el **secreto Graph cliente del bot de** calendario
    - **Cifrar claves almacenadas en la configuración del bot:** Habilitado

    ![Captura de pantalla del cuadro de diálogo Nueva configuración del bot](images/new-bot-config.png)

1. Seleccione **Guardar y conectar**. Después de conectar el emulador, debería ver `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`

1. Escriba texto y envíelo al bot. El bot responde con un mensaje de inicio de sesión.

1. Seleccione el **botón Inicio de** sesión. El emulador le pide que confirme la dirección URL que empieza por `oauthlink://https://token.botframeworkcom` . Seleccione **Confirmar** para continuar.

1. En la ventana emergente, inicie sesión con su Microsoft 365 cuenta. Revise los permisos solicitados y acepte.

1. Una vez completada la autenticación y el consentimiento, la ventana emergente proporciona un código de validación. Copie el código y cierre la ventana.

    ![Una captura de pantalla del Bot Framework Emulator de validación](images/validation-code.png)

1. Escriba el código de validación en la ventana de chat para completar el inicio de sesión.

    ![Captura de pantalla de la conversación de inicio de sesión con el bot de ejemplo](images/bot-login.png)

1. Si selecciona el botón **Mostrar token** (o `show token` tipo), el bot muestra el token de acceso. El **botón Cerrar sesión** (o escribir ) le `log out` cerrará la sesión.

> [!TIP]
> Es posible que reciba el siguiente mensaje de error en la Bot Framework Emulator al iniciar una conversación con el bot.
>
> ```text
> Failed to generate an actual sign-in link: Error: Failed to connect to ngrok instance for OAuth postback URL:
> FetchError: request to http://127.0.0.1:4041/api/tunnels failed, reason: connect ECONNREFUSED 127.0.0.1:4041
> ```
>
> Si esto sucede, asegúrese de habilitar la opción **Ejecutar ngrok** cuando Emulator la configuración del emulador y reinicie el emulador.
