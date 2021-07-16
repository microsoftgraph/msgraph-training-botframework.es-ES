---
ms.openlocfilehash: ab38560fe196ea76bdb1928f218b83aa9c769496
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446788"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5eb3d-101">En esta sección, usará el SDK de Microsoft Graph para obtener el usuario que ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-101">In this section you'll use the Microsoft Graph SDK to get the logged-in user.</span></span>

## <a name="create-a-graph-service"></a><span data-ttu-id="5eb3d-102">Crear un servicio Graph web</span><span class="sxs-lookup"><span data-stu-id="5eb3d-102">Create a Graph service</span></span>

<span data-ttu-id="5eb3d-103">Comience implementando un servicio que el bot puede usar para obtener **un GraphServiceClient** del SDK de Microsoft Graph y, a continuación, poner ese servicio a disposición del bot a través de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-103">Start by implementing a service that the bot can use to get a **GraphServiceClient** from the Microsoft Graph SDK, then making that service available to the bot via dependency injection.</span></span>

1. <span data-ttu-id="5eb3d-104">Cree un nuevo directorio en la raíz del proyecto denominado **Graph**.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-104">Create a new directory in the root of the project named **Graph**.</span></span> <span data-ttu-id="5eb3d-105">Cree un nuevo archivo en el **directorio ./Graph** denominado **IGraphClientService.cs** y agregue el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-105">Create a new file in the **./Graph** directory named **IGraphClientService.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/IGraphClientService.cs" id="IGraphClientServiceSnippet":::

1. <span data-ttu-id="5eb3d-106">Cree un nuevo archivo en el **directorio ./Graph** denominado **GraphClientService.cs** y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-106">Create a new file in the **./Graph** directory named **GraphClientService.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/GraphClientService.cs" id="GraphClientServiceSnippet":::

1. <span data-ttu-id="5eb3d-107">Abra **./Startup.cs** y agregue la siguiente `using` instrucción en la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-107">Open **./Startup.cs** and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using CalendarBot.Graph;
    ```

1. <span data-ttu-id="5eb3d-108">Agregue el siguiente código al final de la función `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-108">Add the following code to the end of the `ConfigureServices` function.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="AddGraphServiceSnippet":::

1. <span data-ttu-id="5eb3d-109">Abra **./Dialogs/MainDialog.cs**.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-109">Open **./Dialogs/MainDialog.cs**.</span></span> <span data-ttu-id="5eb3d-110">Agregue las siguientes `using` instrucciones a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-110">Add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System;
    using System.IO;
    using AdaptiveCards;
    using CalendarBot.Graph;
    using Microsoft.Graph;
    ```

1. <span data-ttu-id="5eb3d-111">Agregue la siguiente propiedad a la **clase MainDialog.**</span><span class="sxs-lookup"><span data-stu-id="5eb3d-111">Add the following property to the **MainDialog** class.</span></span>

    ```csharp
    private readonly IGraphClientService _graphClientService;
    ```

1. <span data-ttu-id="5eb3d-112">Busque el constructor de la **clase MainDialog** y actualice su firma para tomar un parámetro **IGraphServiceClient.**</span><span class="sxs-lookup"><span data-stu-id="5eb3d-112">Locate the constructor for the **MainDialog** class and update its signature to take an **IGraphServiceClient** parameter.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ConstructorSignatureSnippet" highlight="4":::

1. <span data-ttu-id="5eb3d-113">Agregue el siguiente código al constructor.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-113">Add the following code to the constructor.</span></span>

    ```csharp
    _graphClientService = graphClientService;
    ```

## <a name="get-the-logged-on-user"></a><span data-ttu-id="5eb3d-114">Obtener el usuario que ha iniciado sesión</span><span class="sxs-lookup"><span data-stu-id="5eb3d-114">Get the logged on user</span></span>

<span data-ttu-id="5eb3d-115">En esta sección, usará el Graph Microsoft para obtener el nombre, la dirección de correo electrónico y la foto del usuario.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-115">In this section you'll use the Microsoft Graph to get the user's name, email address, and photo.</span></span> <span data-ttu-id="5eb3d-116">A continuación, creará una tarjeta adaptable para mostrar la información.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-116">Then you'll create an Adaptive Card to show the information.</span></span>

1. <span data-ttu-id="5eb3d-117">Cree un nuevo archivo en la raíz del proyecto denominado **CardHelper.cs**.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-117">Create a new file in the root of the project named **CardHelper.cs**.</span></span> <span data-ttu-id="5eb3d-118">Agregue el siguiente código al archivo.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-118">Add the following code to the file.</span></span>

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

    <span data-ttu-id="5eb3d-119">Este código usa el paquete **de NuGet AdaptiveCard** para crear una tarjeta adaptable para mostrar al usuario.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-119">This code uses the **AdaptiveCard** NuGet package to build an Adaptive Card to display the user.</span></span>

1. <span data-ttu-id="5eb3d-120">Agregue la siguiente función a la **clase MainDialog.**</span><span class="sxs-lookup"><span data-stu-id="5eb3d-120">Add the following function to the **MainDialog** class.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="DisplayLoggedInUserSnippet":::

    <span data-ttu-id="5eb3d-121">Tenga en cuenta lo que hace este código.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-121">Consider what this code does.</span></span>

    - <span data-ttu-id="5eb3d-122">Usa **graphClient para** [obtener el usuario que ha iniciado sesión.](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0)</span><span class="sxs-lookup"><span data-stu-id="5eb3d-122">It uses the **graphClient** to [get the logged-in user](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0).</span></span>
        - <span data-ttu-id="5eb3d-123">Usa el método `Select` para limitar los campos que se devuelven.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-123">It uses the `Select` method to limit which fields are returned.</span></span>
    - <span data-ttu-id="5eb3d-124">Usa **graphClient para** [obtener la](https://docs.microsoft.com/graph/api/profilephoto-get?view=graph-rest-1.0)foto del usuario, solicitando el tamaño admitido más pequeño de 48 x 48 píxeles.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-124">It uses the **graphClient** to [get the user's photo](https://docs.microsoft.com/graph/api/profilephoto-get?view=graph-rest-1.0), requesting the smallest supported size of 48x48 pixels.</span></span>
    - <span data-ttu-id="5eb3d-125">Usa la clase **CardHelper** para construir una tarjeta adaptable y envía la tarjeta como datos adjuntos.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-125">It uses the **CardHelper** class to construct an Adaptive Card and sends the card as an attachment.</span></span>

1. <span data-ttu-id="5eb3d-126">Reemplace el código dentro del `else if (command.StartsWith("show me"))` bloque `ProcessStepAsync` por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-126">Replace the code inside the `else if (command.StartsWith("show me"))` block in `ProcessStepAsync` with the following.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ShowMeSnippet" highlight="3":::

1. <span data-ttu-id="5eb3d-127">Guarde todos los cambios y reinicie el bot.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-127">Save all of your changes and restart the bot.</span></span>

1. <span data-ttu-id="5eb3d-128">Use el Bot Framework Emulator para conectarse al bot e iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-128">Use the Bot Framework Emulator to connect to the bot and log in.</span></span> <span data-ttu-id="5eb3d-129">Seleccione el **botón Mostrarme** para mostrar al usuario que ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="5eb3d-129">Select the **Show me** button to display the logged-on user.</span></span>

    ![Captura de pantalla de la tarjeta adaptable que muestra al usuario](images/user-card.png)
