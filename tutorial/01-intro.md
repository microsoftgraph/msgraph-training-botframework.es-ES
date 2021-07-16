---
ms.openlocfilehash: 959ea0ceffc601baa5d87a5cd303f7d02919826f
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446816"
---
<!-- markdownlint-disable MD002 MD041 -->

Este tutorial le enseña a crear un bot de Bot Framework que use la API de Microsoft Graph para recuperar información de calendario para un usuario.

> [!TIP]
> Si prefiere descargar el tutorial completado, puede descargar o clonar el [repositorio GitHub archivo](https://github.com/microsoftgraph/msgraph-training-botframework). Consulta el archivo README en la carpeta **de demostración** para obtener instrucciones sobre cómo configurar la aplicación con un identificador de aplicación y un secreto.

## <a name="prerequisites"></a>Requisitos previos

Antes de iniciar este tutorial, debe tener lo siguiente instalado en el equipo de desarrollo.

- [SDK de .NET Core](https://dotnet.microsoft.com/download)
- [Bot Framework Emulator](https://github.com/microsoft/BotFramework-Emulator/blob/master/README.md)
- [ngrok](https://ngrok.com/)

También debe tener una cuenta personal de Microsoft con un buzón en Outlook.com, o una cuenta de Trabajo o escuela de Microsoft. Si no tienes una cuenta de Microsoft, hay un par de opciones para obtener una cuenta gratuita:

- Puede registrarse [para obtener una nueva cuenta personal de Microsoft](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Puedes [registrarte en el programa Office 365 desarrolladores](https://developer.microsoft.com/office/dev-program) para obtener una suscripción Office 365 gratuita.
- Una suscripción de Azure. Si no tiene una, cree una [cuenta gratuita antes](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) de comenzar.

> [!NOTE]
> Este tutorial se escribió con las siguientes versiones. Los pasos de esta guía pueden funcionar con otras versiones, pero eso no se ha probado.
>
> - SDK de .NET Core versión 5.0.302
> - Bot Framework Emulator 4.1.3
> - ngrok 2.3.40

## <a name="feedback"></a>Comentarios

Proporcione cualquier comentario sobre este tutorial en el [repositorio GitHub archivo](https://github.com/microsoftgraph/msgraph-training-botframework).
