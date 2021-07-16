---
ms.openlocfilehash: ab38560fe196ea76bdb1928f218b83aa9c769496
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446788"
---
<!-- markdownlint-disable MD002 MD041 -->

En esta sección, usará el SDK de Microsoft Graph para obtener el usuario que ha iniciado sesión.

## <a name="create-a-graph-service"></a>Crear un servicio Graph web

Comience implementando un servicio que el bot puede usar para obtener **un GraphServiceClient** del SDK de Microsoft Graph y, a continuación, poner ese servicio a disposición del bot a través de la inserción de dependencias.

1. Cree un nuevo directorio en la raíz del proyecto denominado **Graph**. Cree un nuevo archivo en el **directorio ./Graph** denominado **IGraphClientService.cs** y agregue el código siguiente.

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/IGraphClientService.cs" id="IGraphClientServiceSnippet":::

1. Cree un nuevo archivo en el **directorio ./Graph** denominado **GraphClientService.cs** y agregue el siguiente código.

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/GraphClientService.cs" id="GraphClientServiceSnippet":::

1. Abra **./Startup.cs** y agregue la siguiente `using` instrucción en la parte superior del archivo.

    ```csharp
    using CalendarBot.Graph;
    ```

1. Agregue el siguiente código al final de la función `ConfigureServices`.

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="AddGraphServiceSnippet":::

1. Abra **./Dialogs/MainDialog.cs**. Agregue las siguientes `using` instrucciones a la parte superior del archivo.

    ```csharp
    using System;
    using System.IO;
    using AdaptiveCards;
    using CalendarBot.Graph;
    using Microsoft.Graph;
    ```

1. Agregue la siguiente propiedad a la **clase MainDialog.**

    ```csharp
    private readonly IGraphClientService _graphClientService;
    ```

1. Busque el constructor de la **clase MainDialog** y actualice su firma para tomar un parámetro **IGraphServiceClient.**

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ConstructorSignatureSnippet" highlight="4":::

1. Agregue el siguiente código al constructor.

    ```csharp
    _graphClientService = graphClientService;
    ```

## <a name="get-the-logged-on-user"></a>Obtener el usuario que ha iniciado sesión

En esta sección, usará el Graph Microsoft para obtener el nombre, la dirección de correo electrónico y la foto del usuario. A continuación, creará una tarjeta adaptable para mostrar la información.

1. Cree un nuevo archivo en la raíz del proyecto denominado **CardHelper.cs**. Agregue el siguiente código al archivo.

    ```csharp
    using AdaptiveCards;
    using Microsoft.Graph;
    using System;
    using System.IO;

    namespace CalendarBot
    {
        public class CardHelper
        {
            public static AdaptiveCard GetUserCard(User user, Stream photo)
            {
              // Create an Adaptive Card to display the user
                // See https://adaptivecards.io/designer/ for possibilities
                var userCard = new AdaptiveCard("1.2");

                var columns = new AdaptiveColumnSet();
                userCard.Body.Add(columns);

                var userPhotoColumn = new AdaptiveColumn { Width = AdaptiveColumnWidth.Auto };
                columns.Columns.Add(userPhotoColumn);

                userPhotoColumn.Items.Add(new AdaptiveImage {
                    Style = AdaptiveImageStyle.Person,
                    Size = AdaptiveImageSize.Small,
                    Url = GetDataUriFromPhoto(photo)
                });

                var userInfoColumn = new AdaptiveColumn {Width = AdaptiveColumnWidth.Stretch };
                columns.Columns.Add(userInfoColumn);

                userInfoColumn.Items.Add(new AdaptiveTextBlock {
                    Weight = AdaptiveTextWeight.Bolder,
                    Wrap = true,
                    Text = user.DisplayName
                });

                userInfoColumn.Items.Add(new AdaptiveTextBlock {
                    Spacing = AdaptiveSpacing.None,
                    IsSubtle = true,
                    Wrap = true,
                    Text = user.Mail ?? user.UserPrincipalName
                });

                return userCard;
            }

            private static Uri GetDataUriFromPhoto(Stream photo)
            {
                // Copy to a MemoryStream to get access to bytes
                var photoStream = new MemoryStream();
                photo.CopyTo(photoStream);

                var photoBytes = photoStream.ToArray();

                return new Uri($"data:image/png;base64,{Convert.ToBase64String(photoBytes)}");
            }
        }
    }
    ```

    Este código usa el paquete **de NuGet AdaptiveCard** para crear una tarjeta adaptable para mostrar al usuario.

1. Agregue la siguiente función a la **clase MainDialog.**

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="DisplayLoggedInUserSnippet":::

    Tenga en cuenta lo que hace este código.

    - Usa **graphClient para** [obtener el usuario que ha iniciado sesión.](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0)
        - Usa el método `Select` para limitar los campos que se devuelven.
    - Usa **graphClient para** [obtener la](https://docs.microsoft.com/graph/api/profilephoto-get?view=graph-rest-1.0)foto del usuario, solicitando el tamaño admitido más pequeño de 48 x 48 píxeles.
    - Usa la clase **CardHelper** para construir una tarjeta adaptable y envía la tarjeta como datos adjuntos.

1. Reemplace el código dentro del `else if (command.StartsWith("show me"))` bloque `ProcessStepAsync` por lo siguiente.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ShowMeSnippet" highlight="3":::

1. Guarde todos los cambios y reinicie el bot.

1. Use el Bot Framework Emulator para conectarse al bot e iniciar sesión. Seleccione el **botón Mostrarme** para mostrar al usuario que ha iniciado sesión.

    ![Captura de pantalla de la tarjeta adaptable que muestra al usuario](images/user-card.png)
