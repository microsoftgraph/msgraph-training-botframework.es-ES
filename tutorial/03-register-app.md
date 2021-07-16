---
ms.openlocfilehash: 883eff2860fe2555cdd816cb942a4a87918a5836
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446830"
---
<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, creará un nuevo registro de canales bot y un registro de aplicación web de Azure AD mediante Azure Portal.

## <a name="create-a-bot-channels-registration"></a>Crear un registro de canales bot

1. Abra un explorador y vaya a [Azure Portal](https://portal.azure.com). Inicie sesión con la cuenta asociada a la suscripción de Azure.

1. Seleccione el menú superior izquierdo y, **a continuación, seleccione Crear un recurso**.

    ![Captura de pantalla del menú de Azure Portal](images/create-resource.png)

1. En la **página Nuevo,** busque `Azure Bot` y seleccione Bot de **Azure**.

1. En la **página Bot de Azure,** seleccione **Crear**.

1. Rellene los campos necesarios y deje el extremo **de mensajería en** blanco. El **campo de identificador bot** debe ser único. Asegúrese de revisar los diferentes niveles de precios y seleccionar lo que tiene sentido para su escenario. Si se trata solo de un ejercicio de aprendizaje, es posible que quieras seleccionar la opción gratuita.

1. Para **Id. de aplicación de Microsoft,** **seleccione Crear nuevo identificador de aplicación de Microsoft**.

1. Seleccione **Revisar y crear**. Una vez completada la validación, seleccione **Crear**.

1. Una vez finalizada la implementación, seleccione **Ir al recurso**.

1. En **Configuración**, seleccione **Configuración**. Seleccione el **vínculo** Administrar junto a **Id. de aplicación de Microsoft**.

1. Seleccione **Nuevo secreto de cliente**. Agregue una descripción y elija una expiración y, a continuación, **seleccione Agregar**.

1. Copie el valor del secreto del cliente antes de salir de esta página. Lo necesitará en los pasos siguientes.

    > [!IMPORTANT]
    > El secreto de cliente no se vuelve a mostrar, así que asegúrese de copiarlo en este momento. Deberá escribir este valor en varios lugares para mantenerlo seguro.

1. Seleccione **Información** general en el menú de la izquierda. Copie el valor del identificador de aplicación **(cliente)** y guárdelo, lo necesitará en los pasos siguientes.

    ![Una captura de pantalla del Id. de aplicación del nuevo registro de la aplicación](./images/aad-application-id.png)

1. Vuelva a la ventana Registro del canal bot del explorador y pegue el identificador de aplicación en el campo **Id. de aplicación de Microsoft.** Pegue el secreto de cliente en el **campo Contraseña.** Seleccione **Aceptar**.

1. En la **página Registro de canales bots,** seleccione **Crear**.

1. Espere a que se cree el registro de canales de bot. Una vez creado, vuelva a la página principal en Azure Portal y, a continuación, seleccione **Bot Services**. Seleccione el nuevo registro del canal bots para ver sus propiedades.

## <a name="create-a-web-app-registration"></a>Crear un registro de aplicación web

1. Vuelva a la página principal de Azure Portal y, a continuación, **seleccione Azure Active Directory**.

1. Seleccione **Registros de aplicaciones**.

1. Seleccione **Nuevo registro**. En la página **Registrar una aplicación**, establezca los valores siguientes.

    - Establezca **Nombre** como `Graph Calendar Bot Auth`.
    - Establezca **Tipos de cuenta admitidos** en **Cuentas en cualquier directorio de organización y cuentas personales de Microsoft**.
    - En **URI de redirección**, establezca la primera lista desplegable en `Web` y establezca el valor `https://token.botframework.com/.auth/web/redirect`.

1. Seleccione **Registrar**. En la **Graph autenticación** del bot de calendario, copie el valor del identificador de aplicación **(cliente)** y guárdelo, lo necesitará en los pasos siguientes.

1. Seleccione **Certificados y secretos** en **Administrar**. Seleccione el botón **Nuevo secreto de cliente**. Escriba un valor en **Descripción**, y seleccione una de las opciones para **Expira**, y después, **Agregar**.

1. Copie el valor del secreto del cliente antes de salir de esta página. Lo necesitará en los pasos siguientes.

1. Seleccione **Permisos de API** y, a **continuación, seleccione Agregar un permiso**.

1. Seleccione **Microsoft Graph** y, a continuación, seleccione Permisos **delegados**.

1. Seleccione los siguientes permisos y, a continuación, **seleccione Agregar permisos**.

    - **openid**
    - **profile**
    - **Calendars.ReadWrite**
    - **MailboxSettings.Read**

    ![Captura de pantalla de los permisos configurados](images/configured-permissions.png)

### <a name="about-permissions"></a>Acerca de los permisos

Ten en cuenta lo que cada uno de esos ámbitos de permisos permite que haga el bot y para qué los usará el bot.

- **openid** y **profile:** permite al bot iniciar sesión en los usuarios y obtener información básica de Azure AD en el token de identidad.
- **Calendars.ReadWrite:** permite al bot leer el calendario del usuario y agregar nuevos eventos al calendario del usuario.
- **MailboxSettings.Read:** permite al bot leer la configuración del buzón del usuario. El bot usará esto para obtener la zona horaria seleccionada del usuario.
- **User.Read:** permite al bot obtener el perfil del usuario de Microsoft Graph. El bot usará esto para obtener el nombre del usuario.

## <a name="add-oauth-connection-to-the-bot"></a>Agregar conexión de OAuth al bot

1. Vaya a la página bot de Azure del bot en Azure Portal. Seleccione **Configuración** en **Configuración**.

1. Seleccione **Agregar conexión de OAuth Configuración**.

1. Rellene el formulario de la siguiente manera y, a continuación, **seleccione Guardar**.

    - **Nombre**: `GraphBotAuth`
    - **Proveedor**: **Azure Active Directory v2**
    - **Id. de** cliente: el identificador de aplicación de su **Graph registro de autenticación del bot de** calendario.
    - **Secreto de cliente:** el secreto de cliente del **registro Graph autenticación del bot de** calendario.
    - **Dirección URL Exchange token:** Dejar en blanco
    - **Identificador de inquilino**: `common`
    - **Ámbitos**: `openid profile Calendars.ReadWrite MailboxSettings.Read User.Read`

1. Seleccione la **entrada GraphBotAuth** en **OAuth Connection Configuración**.

1. Seleccione **Probar conexión**. Se abre una nueva ventana o pestaña del explorador para iniciar el flujo de OAuth.

1. Si es necesario, inicie sesión. Revise la lista de permisos solicitados y, a continuación, **seleccione Aceptar**.

1. Debería ver un mensaje **Probar conexión a 'GraphBotAuth' correctamente.**

> [!TIP]
> Puedes seleccionar el botón **Copiar token** en esta página y pegar el token en para ver las notificaciones dentro [https://jwt.ms](https://jwt.ms) del token. Esto es útil al solucionar errores de autenticación.
