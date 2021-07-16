---
ms.openlocfilehash: 959ea0ceffc601baa5d87a5cd303f7d02919826f
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446816"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ddfab-101">Este tutorial le enseña a crear un bot de Bot Framework que use la API de Microsoft Graph para recuperar información de calendario para un usuario.</span><span class="sxs-lookup"><span data-stu-id="ddfab-101">This tutorial teaches you how to build a Bot Framework bot that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="ddfab-102">Si prefiere descargar el tutorial completado, puede descargar o clonar el [repositorio GitHub archivo](https://github.com/microsoftgraph/msgraph-training-botframework).</span><span class="sxs-lookup"><span data-stu-id="ddfab-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-botframework).</span></span> <span data-ttu-id="ddfab-103">Consulta el archivo README en la carpeta **de demostración** para obtener instrucciones sobre cómo configurar la aplicación con un identificador de aplicación y un secreto.</span><span class="sxs-lookup"><span data-stu-id="ddfab-103">See the README file in the **demo** folder for instructions on configuring the app with an app ID and secret.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ddfab-104">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="ddfab-104">Prerequisites</span></span>

<span data-ttu-id="ddfab-105">Antes de iniciar este tutorial, debe tener lo siguiente instalado en el equipo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ddfab-105">Before you start this tutorial, you should have the following installed on your development machine.</span></span>

- [<span data-ttu-id="ddfab-106">SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="ddfab-106">.NET Core SDK</span></span>](https://dotnet.microsoft.com/download)
- [<span data-ttu-id="ddfab-107">Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="ddfab-107">Bot Framework Emulator</span></span>](https://github.com/microsoft/BotFramework-Emulator/blob/master/README.md)
- [<span data-ttu-id="ddfab-108">ngrok</span><span class="sxs-lookup"><span data-stu-id="ddfab-108">ngrok</span></span>](https://ngrok.com/)

<span data-ttu-id="ddfab-109">También debe tener una cuenta personal de Microsoft con un buzón en Outlook.com, o una cuenta de Trabajo o escuela de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ddfab-109">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="ddfab-110">Si no tienes una cuenta de Microsoft, hay un par de opciones para obtener una cuenta gratuita:</span><span class="sxs-lookup"><span data-stu-id="ddfab-110">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="ddfab-111">Puede registrarse [para obtener una nueva cuenta personal de Microsoft](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="ddfab-111">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="ddfab-112">Puedes [registrarte en el programa Office 365 desarrolladores](https://developer.microsoft.com/office/dev-program) para obtener una suscripción Office 365 gratuita.</span><span class="sxs-lookup"><span data-stu-id="ddfab-112">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>
- <span data-ttu-id="ddfab-113">Una suscripción de Azure.</span><span class="sxs-lookup"><span data-stu-id="ddfab-113">An Azure subscription.</span></span> <span data-ttu-id="ddfab-114">Si no tiene una, cree una [cuenta gratuita antes](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) de comenzar.</span><span class="sxs-lookup"><span data-stu-id="ddfab-114">If you do not have one, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

> [!NOTE]
> <span data-ttu-id="ddfab-115">Este tutorial se escribió con las siguientes versiones.</span><span class="sxs-lookup"><span data-stu-id="ddfab-115">This tutorial was written with the following versions.</span></span> <span data-ttu-id="ddfab-116">Los pasos de esta guía pueden funcionar con otras versiones, pero eso no se ha probado.</span><span class="sxs-lookup"><span data-stu-id="ddfab-116">The steps in this guide may work with other versions, but that has not been tested.</span></span>
>
> - <span data-ttu-id="ddfab-117">SDK de .NET Core versión 5.0.302</span><span class="sxs-lookup"><span data-stu-id="ddfab-117">.NET Core SDK version 5.0.302</span></span>
> - <span data-ttu-id="ddfab-118">Bot Framework Emulator 4.1.3</span><span class="sxs-lookup"><span data-stu-id="ddfab-118">Bot Framework Emulator 4.1.3</span></span>
> - <span data-ttu-id="ddfab-119">ngrok 2.3.40</span><span class="sxs-lookup"><span data-stu-id="ddfab-119">ngrok 2.3.40</span></span>

## <a name="feedback"></a><span data-ttu-id="ddfab-120">Comentarios</span><span class="sxs-lookup"><span data-stu-id="ddfab-120">Feedback</span></span>

<span data-ttu-id="ddfab-121">Proporcione cualquier comentario sobre este tutorial en el [repositorio GitHub archivo](https://github.com/microsoftgraph/msgraph-training-botframework).</span><span class="sxs-lookup"><span data-stu-id="ddfab-121">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-botframework).</span></span>
