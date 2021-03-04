---
title: Ejemplos de autenticación para ASP.NET Core
author: rick-anderson
description: Proporciona vínculos a los ejemplos de autenticación en el repositorio de ASP.NET Core.
ms.author: riande
ms.date: 02/21/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110123"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="72aea-103">Ejemplos de autenticación para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="72aea-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="72aea-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="72aea-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="72aea-105">El [repositorio ASP.net Core](https://github.com/dotnet/aspnetcore) contiene los siguientes ejemplos de autenticación ( `main` bifurcación):</span><span class="sxs-lookup"><span data-stu-id="72aea-105">The [ASP.NET Core repository](https://github.com/dotnet/aspnetcore) contains the following authentication samples (`main` branch):</span></span>

* [<span data-ttu-id="72aea-106">Transformación de notificaciones</span><span class="sxs-lookup"><span data-stu-id="72aea-106">Claims transformation</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="72aea-107">[Cookie autenticación](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="72aea-107">[Cookie authentication](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="72aea-108">Respuesta de error de autorización personalizada</span><span class="sxs-lookup"><span data-stu-id="72aea-108">Custom authorization failure response</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [<span data-ttu-id="72aea-109">Proveedor de directivas personalizadas: IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="72aea-109">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="72aea-110">Opciones y esquemas de autenticación dinámica</span><span class="sxs-lookup"><span data-stu-id="72aea-110">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="72aea-111">[Notificaciones externas](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="72aea-111">[External claims](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="72aea-112">Seleccionar entre cookie y otro esquema de autenticación en función de la solicitud</span><span class="sxs-lookup"><span data-stu-id="72aea-112">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="72aea-113">Restringe el acceso a los archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="72aea-113">Restricts access to static files</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a><span data-ttu-id="72aea-114">Obtención y ejecución de los ejemplos</span><span class="sxs-lookup"><span data-stu-id="72aea-114">Obtain and run the samples</span></span>

<span data-ttu-id="72aea-115">En los vínculos de ejemplo que se proporcionan en este artículo se proporcionan ejemplos de la próxima versión de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="72aea-115">The sample links provided in this article provide samples for the upcoming release of ASP.NET Core.</span></span> <span data-ttu-id="72aea-116">Para obtener un ejemplo de la versión actual o de una versión anterior, realice los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="72aea-116">To obtain a sample for the current release or a prior release, perform the following steps:</span></span>

* <span data-ttu-id="72aea-117">Seleccione la rama de versión del [repositorio de ASP.net Core](https://github.com/dotnet/aspnetcore)] ( https://github.com/dotnet/aspnetcore) .</span><span class="sxs-lookup"><span data-stu-id="72aea-117">Select the release branch of the [ASP.NET Core repository](https://github.com/dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore).</span></span> <span data-ttu-id="72aea-118">Por ejemplo, la `release/5.0` rama contiene los ejemplos de la versión ASP.NET Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="72aea-118">For example, the `release/5.0` branch contains the samples for the ASP.NET Core 5.0 release.</span></span>
* <span data-ttu-id="72aea-119">Clone o descargue el repositorio de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="72aea-119">Clone or download the ASP.NET Core repository.</span></span>
* <span data-ttu-id="72aea-120">En el sistema local, Compruebe la instalación de la versión de [SDK de .net Core](https://dotnet.microsoft.com/download/dotnet-core) que coincida con el clon del repositorio de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="72aea-120">On your local system, verify installation of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="72aea-121">Navegue hasta un ejemplo en la `aspnetcore/src/Security/samples` carpeta y ejecute el ejemplo con el [ `dotnet run` comando](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="72aea-121">Navigate to a sample in `aspnetcore/src/Security/samples` folder and run the sample with the [`dotnet run` command](/dotnet/core/tools/dotnet-run).</span></span>
