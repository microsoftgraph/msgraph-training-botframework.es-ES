---
ms.openlocfilehash: 883eff2860fe2555cdd816cb942a4a87918a5836
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446830"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="11b93-101">En este ejercicio, creará un nuevo registro de canales bot y un registro de aplicación web de Azure AD mediante Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="11b93-101">In this exercise, you will create a new Bot Channels registration and an Azure AD web application registration using the Azure Portal.</span></span>

## <a name="create-a-bot-channels-registration"></a><span data-ttu-id="11b93-102">Crear un registro de canales bot</span><span class="sxs-lookup"><span data-stu-id="11b93-102">Create a Bot Channels registration</span></span>

1. <span data-ttu-id="11b93-103">Abra un explorador y vaya a [Azure Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="11b93-103">Open a browser and navigate to the [Azure Portal](https://portal.azure.com).</span></span> <span data-ttu-id="11b93-104">Inicie sesión con la cuenta asociada a la suscripción de Azure.</span><span class="sxs-lookup"><span data-stu-id="11b93-104">Login using the account associated with your Azure subscription.</span></span>

1. <span data-ttu-id="11b93-105">Seleccione el menú superior izquierdo y, **a continuación, seleccione Crear un recurso**.</span><span class="sxs-lookup"><span data-stu-id="11b93-105">Select the upper-left menu, then select **Create a resource**.</span></span>

    ![Captura de pantalla del menú de Azure Portal](images/create-resource.png)

1. <span data-ttu-id="11b93-107">En la **página Nuevo,** busque `Azure Bot` y seleccione Bot de **Azure**.</span><span class="sxs-lookup"><span data-stu-id="11b93-107">On the **New** page, search for `Azure Bot` and select **Azure Bot**.</span></span>

1. <span data-ttu-id="11b93-108">En la **página Bot de Azure,** seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="11b93-108">On the **Azure Bot** page, select **Create**.</span></span>

1. <span data-ttu-id="11b93-109">Rellene los campos necesarios y deje el extremo **de mensajería en** blanco.</span><span class="sxs-lookup"><span data-stu-id="11b93-109">Fill in the required fields, and leave **Messaging endpoint** blank.</span></span> <span data-ttu-id="11b93-110">El **campo de identificador bot** debe ser único.</span><span class="sxs-lookup"><span data-stu-id="11b93-110">The **Bot handle** field must be unique.</span></span> <span data-ttu-id="11b93-111">Asegúrese de revisar los diferentes niveles de precios y seleccionar lo que tiene sentido para su escenario.</span><span class="sxs-lookup"><span data-stu-id="11b93-111">Be sure to review the different pricing tiers and select what makes sense for your scenario.</span></span> <span data-ttu-id="11b93-112">Si se trata solo de un ejercicio de aprendizaje, es posible que quieras seleccionar la opción gratuita.</span><span class="sxs-lookup"><span data-stu-id="11b93-112">If this is just a learning exercise, you may want to select the free option.</span></span>

1. <span data-ttu-id="11b93-113">Para **Id. de aplicación de Microsoft,** **seleccione Crear nuevo identificador de aplicación de Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="11b93-113">For **Microsoft App ID**, select **Create new Microsoft App ID**.</span></span>

1. <span data-ttu-id="11b93-114">Seleccione **Revisar y crear**.</span><span class="sxs-lookup"><span data-stu-id="11b93-114">Select **Review + create**.</span></span> <span data-ttu-id="11b93-115">Una vez completada la validación, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="11b93-115">Once validation completes, select **Create**.</span></span>

1. <span data-ttu-id="11b93-116">Una vez finalizada la implementación, seleccione **Ir al recurso**.</span><span class="sxs-lookup"><span data-stu-id="11b93-116">Once deployment has finished, select **Go to resource**.</span></span>

1. <span data-ttu-id="11b93-117">En **Configuración**, seleccione **Configuración**.</span><span class="sxs-lookup"><span data-stu-id="11b93-117">Under **Settings**, select **Configuration**.</span></span> <span data-ttu-id="11b93-118">Seleccione el **vínculo** Administrar junto a **Id. de aplicación de Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="11b93-118">Select the **Manage** link next to **Microsoft App ID**.</span></span>

1. <span data-ttu-id="11b93-119">Seleccione **Nuevo secreto de cliente**.</span><span class="sxs-lookup"><span data-stu-id="11b93-119">Select **New client secret**.</span></span> <span data-ttu-id="11b93-120">Agregue una descripción y elija una expiración y, a continuación, **seleccione Agregar**.</span><span class="sxs-lookup"><span data-stu-id="11b93-120">Add a description and choose an expiration, then select **Add**.</span></span>

1. <span data-ttu-id="11b93-121">Copie el valor del secreto del cliente antes de salir de esta página.</span><span class="sxs-lookup"><span data-stu-id="11b93-121">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="11b93-122">Lo necesitará en los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="11b93-122">You will need it in the following steps.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="11b93-123">El secreto de cliente no se vuelve a mostrar, así que asegúrese de copiarlo en este momento.</span><span class="sxs-lookup"><span data-stu-id="11b93-123">This client secret is never shown again, so make sure you copy it now.</span></span> <span data-ttu-id="11b93-124">Deberá escribir este valor en varios lugares para mantenerlo seguro.</span><span class="sxs-lookup"><span data-stu-id="11b93-124">You will need to enter this value in multiple places so keep it safe.</span></span>

1. <span data-ttu-id="11b93-125">Seleccione **Información** general en el menú de la izquierda.</span><span class="sxs-lookup"><span data-stu-id="11b93-125">Select **Overview** in the left-hand menu.</span></span> <span data-ttu-id="11b93-126">Copie el valor del identificador de aplicación **(cliente)** y guárdelo, lo necesitará en los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="11b93-126">Copy the value of the **Application (client) ID** and save it, you will need it in the following steps.</span></span>

    ![Una captura de pantalla del Id. de aplicación del nuevo registro de la aplicación](./images/aad-application-id.png)

1. <span data-ttu-id="11b93-128">Vuelva a la ventana Registro del canal bot del explorador y pegue el identificador de aplicación en el campo **Id. de aplicación de Microsoft.**</span><span class="sxs-lookup"><span data-stu-id="11b93-128">Return to the Bot Channel Registration window in your browser, and paste the application ID into the **Microsoft App ID** field.</span></span> <span data-ttu-id="11b93-129">Pegue el secreto de cliente en el **campo Contraseña.**</span><span class="sxs-lookup"><span data-stu-id="11b93-129">Paste your client secret into the **Password** field.</span></span> <span data-ttu-id="11b93-130">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="11b93-130">Select **OK**.</span></span>

1. <span data-ttu-id="11b93-131">En la **página Registro de canales bots,** seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="11b93-131">On the **Bots Channels Registration** page, select **Create**.</span></span>

1. <span data-ttu-id="11b93-132">Espere a que se cree el registro de canales de bot.</span><span class="sxs-lookup"><span data-stu-id="11b93-132">Wait for the Bot Channels registration to be created.</span></span> <span data-ttu-id="11b93-133">Una vez creado, vuelva a la página principal en Azure Portal y, a continuación, seleccione **Bot Services**.</span><span class="sxs-lookup"><span data-stu-id="11b93-133">Once created, return to the Home page in the Azure Portal, then select **Bot Services**.</span></span> <span data-ttu-id="11b93-134">Seleccione el nuevo registro del canal bots para ver sus propiedades.</span><span class="sxs-lookup"><span data-stu-id="11b93-134">Select your new Bots Channel registration to view its properties.</span></span>

## <a name="create-a-web-app-registration"></a><span data-ttu-id="11b93-135">Crear un registro de aplicación web</span><span class="sxs-lookup"><span data-stu-id="11b93-135">Create a web app registration</span></span>

1. <span data-ttu-id="11b93-136">Vuelva a la página principal de Azure Portal y, a continuación, **seleccione Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="11b93-136">Return to the home page of the Azure portal, then select **Azure Active Directory**.</span></span>

1. <span data-ttu-id="11b93-137">Seleccione **Registros de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="11b93-137">Select **App registrations**.</span></span>

1. <span data-ttu-id="11b93-138">Seleccione **Nuevo registro**.</span><span class="sxs-lookup"><span data-stu-id="11b93-138">Select **New registration**.</span></span> <span data-ttu-id="11b93-139">En la página **Registrar una aplicación**, establezca los valores siguientes.</span><span class="sxs-lookup"><span data-stu-id="11b93-139">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="11b93-140">Establezca **Nombre** como `Graph Calendar Bot Auth`.</span><span class="sxs-lookup"><span data-stu-id="11b93-140">Set **Name** to `Graph Calendar Bot Auth`.</span></span>
    - <span data-ttu-id="11b93-141">Establezca **Tipos de cuenta admitidos** en **Cuentas en cualquier directorio de organización y cuentas personales de Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="11b93-141">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="11b93-142">En **URI de redirección**, establezca la primera lista desplegable en `Web` y establezca el valor `https://token.botframework.com/.auth/web/redirect`.</span><span class="sxs-lookup"><span data-stu-id="11b93-142">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `https://token.botframework.com/.auth/web/redirect`.</span></span>

1. <span data-ttu-id="11b93-143">Seleccione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="11b93-143">Select **Register**.</span></span> <span data-ttu-id="11b93-144">En la **Graph autenticación** del bot de calendario, copie el valor del identificador de aplicación **(cliente)** y guárdelo, lo necesitará en los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="11b93-144">On the **Graph Calendar Bot Auth** page, copy the value of the **Application (client) ID** and save it, you will need it in the following steps.</span></span>

1. <span data-ttu-id="11b93-145">Seleccione **Certificados y secretos** en **Administrar**.</span><span class="sxs-lookup"><span data-stu-id="11b93-145">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="11b93-146">Seleccione el botón **Nuevo secreto de cliente**.</span><span class="sxs-lookup"><span data-stu-id="11b93-146">Select the **New client secret** button.</span></span> <span data-ttu-id="11b93-147">Escriba un valor en **Descripción**, y seleccione una de las opciones para **Expira**, y después, **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="11b93-147">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

1. <span data-ttu-id="11b93-148">Copie el valor del secreto del cliente antes de salir de esta página.</span><span class="sxs-lookup"><span data-stu-id="11b93-148">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="11b93-149">Lo necesitará en los pasos siguientes.</span><span class="sxs-lookup"><span data-stu-id="11b93-149">You will need it in the following steps.</span></span>

1. <span data-ttu-id="11b93-150">Seleccione **Permisos de API** y, a **continuación, seleccione Agregar un permiso**.</span><span class="sxs-lookup"><span data-stu-id="11b93-150">Select **API permissions**, then select **Add a permission**.</span></span>

1. <span data-ttu-id="11b93-151">Seleccione **Microsoft Graph** y, a continuación, seleccione Permisos **delegados**.</span><span class="sxs-lookup"><span data-stu-id="11b93-151">Select **Microsoft Graph**, then select **Delegated permissions**.</span></span>

1. <span data-ttu-id="11b93-152">Seleccione los siguientes permisos y, a continuación, **seleccione Agregar permisos**.</span><span class="sxs-lookup"><span data-stu-id="11b93-152">Select the following permissions, then select **Add permissions**.</span></span>

    - <span data-ttu-id="11b93-153">**openid**</span><span class="sxs-lookup"><span data-stu-id="11b93-153">**openid**</span></span>
    - <span data-ttu-id="11b93-154">**profile**</span><span class="sxs-lookup"><span data-stu-id="11b93-154">**profile**</span></span>
    - <span data-ttu-id="11b93-155">**Calendars.ReadWrite**</span><span class="sxs-lookup"><span data-stu-id="11b93-155">**Calendars.ReadWrite**</span></span>
    - <span data-ttu-id="11b93-156">**MailboxSettings.Read**</span><span class="sxs-lookup"><span data-stu-id="11b93-156">**MailboxSettings.Read**</span></span>

    ![Captura de pantalla de los permisos configurados](images/configured-permissions.png)

### <a name="about-permissions"></a><span data-ttu-id="11b93-158">Acerca de los permisos</span><span class="sxs-lookup"><span data-stu-id="11b93-158">About permissions</span></span>

<span data-ttu-id="11b93-159">Ten en cuenta lo que cada uno de esos ámbitos de permisos permite que haga el bot y para qué los usará el bot.</span><span class="sxs-lookup"><span data-stu-id="11b93-159">Consider what each of those permission scopes allows the bot to do, and what the bot will use them for.</span></span>

- <span data-ttu-id="11b93-160">**openid** y **profile:** permite al bot iniciar sesión en los usuarios y obtener información básica de Azure AD en el token de identidad.</span><span class="sxs-lookup"><span data-stu-id="11b93-160">**openid** and **profile**: allows the bot to sign users in and get basic information from Azure AD in the identity token.</span></span>
- <span data-ttu-id="11b93-161">**Calendars.ReadWrite:** permite al bot leer el calendario del usuario y agregar nuevos eventos al calendario del usuario.</span><span class="sxs-lookup"><span data-stu-id="11b93-161">**Calendars.ReadWrite**: allows the bot to read the user's calendar and to add new events to the user's calendar.</span></span>
- <span data-ttu-id="11b93-162">**MailboxSettings.Read:** permite al bot leer la configuración del buzón del usuario.</span><span class="sxs-lookup"><span data-stu-id="11b93-162">**MailboxSettings.Read**: allows the bot to read the user's mailbox settings.</span></span> <span data-ttu-id="11b93-163">El bot usará esto para obtener la zona horaria seleccionada del usuario.</span><span class="sxs-lookup"><span data-stu-id="11b93-163">The bot will use this to get the user's selected time zone.</span></span>
- <span data-ttu-id="11b93-164">**User.Read:** permite al bot obtener el perfil del usuario de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="11b93-164">**User.Read**: allows the bot to get the user's profile from Microsoft Graph.</span></span> <span data-ttu-id="11b93-165">El bot usará esto para obtener el nombre del usuario.</span><span class="sxs-lookup"><span data-stu-id="11b93-165">The bot will use this to get the user's name.</span></span>

## <a name="add-oauth-connection-to-the-bot"></a><span data-ttu-id="11b93-166">Agregar conexión de OAuth al bot</span><span class="sxs-lookup"><span data-stu-id="11b93-166">Add OAuth connection to the bot</span></span>

1. <span data-ttu-id="11b93-167">Vaya a la página bot de Azure del bot en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="11b93-167">Navigate to your bot's Azure Bot page in the Azure Portal.</span></span> <span data-ttu-id="11b93-168">Seleccione **Configuración** en **Configuración**.</span><span class="sxs-lookup"><span data-stu-id="11b93-168">Select **Configuration** under **Settings**.</span></span>

1. <span data-ttu-id="11b93-169">Seleccione **Agregar conexión de OAuth Configuración**.</span><span class="sxs-lookup"><span data-stu-id="11b93-169">Select **Add OAuth Connection Settings**.</span></span>

1. <span data-ttu-id="11b93-170">Rellene el formulario de la siguiente manera y, a continuación, **seleccione Guardar**.</span><span class="sxs-lookup"><span data-stu-id="11b93-170">Fill in the form as follows, then select **Save**.</span></span>

    - <span data-ttu-id="11b93-171">**Nombre**: `GraphBotAuth`</span><span class="sxs-lookup"><span data-stu-id="11b93-171">**Name**: `GraphBotAuth`</span></span>
    - <span data-ttu-id="11b93-172">**Proveedor**: **Azure Active Directory v2**</span><span class="sxs-lookup"><span data-stu-id="11b93-172">**Provider**: **Azure Active Directory v2**</span></span>
    - <span data-ttu-id="11b93-173">**Id. de** cliente: el identificador de aplicación de su **Graph registro de autenticación del bot de** calendario.</span><span class="sxs-lookup"><span data-stu-id="11b93-173">**Client id**: The application ID of your **Graph Calendar Bot Auth** registration.</span></span>
    - <span data-ttu-id="11b93-174">**Secreto de cliente:** el secreto de cliente del **registro Graph autenticación del bot de** calendario.</span><span class="sxs-lookup"><span data-stu-id="11b93-174">**Client secret**: The client secret of your **Graph Calendar Bot Auth** registration.</span></span>
    - <span data-ttu-id="11b93-175">**Dirección URL Exchange token:** Dejar en blanco</span><span class="sxs-lookup"><span data-stu-id="11b93-175">**Token Exchange URL**: Leave blank</span></span>
    - <span data-ttu-id="11b93-176">**Identificador de inquilino**: `common`</span><span class="sxs-lookup"><span data-stu-id="11b93-176">**Tenant ID**: `common`</span></span>
    - <span data-ttu-id="11b93-177">**Ámbitos**: `openid profile Calendars.ReadWrite MailboxSettings.Read User.Read`</span><span class="sxs-lookup"><span data-stu-id="11b93-177">**Scopes**: `openid profile Calendars.ReadWrite MailboxSettings.Read User.Read`</span></span>

1. <span data-ttu-id="11b93-178">Seleccione la **entrada GraphBotAuth** en **OAuth Connection Configuración**.</span><span class="sxs-lookup"><span data-stu-id="11b93-178">Select the **GraphBotAuth** entry under **OAuth Connection Settings**.</span></span>

1. <span data-ttu-id="11b93-179">Seleccione **Probar conexión**.</span><span class="sxs-lookup"><span data-stu-id="11b93-179">Select **Test Connection**.</span></span> <span data-ttu-id="11b93-180">Se abre una nueva ventana o pestaña del explorador para iniciar el flujo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="11b93-180">This opens a new browser window or tab to start the OAuth flow.</span></span>

1. <span data-ttu-id="11b93-181">Si es necesario, inicie sesión.</span><span class="sxs-lookup"><span data-stu-id="11b93-181">If necessary, sign in.</span></span> <span data-ttu-id="11b93-182">Revise la lista de permisos solicitados y, a continuación, **seleccione Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="11b93-182">Review the list of requested permissions, then select **Accept**.</span></span>

1. <span data-ttu-id="11b93-183">Debería ver un mensaje **Probar conexión a 'GraphBotAuth' correctamente.**</span><span class="sxs-lookup"><span data-stu-id="11b93-183">You should see a **Test Connection to 'GraphBotAuth' Succeeded** message.</span></span>

> [!TIP]
> <span data-ttu-id="11b93-184">Puedes seleccionar el botón **Copiar token** en esta página y pegar el token en para ver las notificaciones dentro [https://jwt.ms](https://jwt.ms) del token.</span><span class="sxs-lookup"><span data-stu-id="11b93-184">You can select the **Copy Token** button on this page, and paste the token into [https://jwt.ms](https://jwt.ms) to see the claims inside the token.</span></span> <span data-ttu-id="11b93-185">Esto es útil al solucionar errores de autenticación.</span><span class="sxs-lookup"><span data-stu-id="11b93-185">This is useful when troubleshooting authentication errors.</span></span>
