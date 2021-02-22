---
title: Configuración del recortador de ASP.NET Core Blazor
author: guardrex
description: Aprenda a controlar el enlazador de lenguaje intermedio (IL) (recortador) al compilar una aplicación Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975225"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a><span data-ttu-id="4a703-103">Configuración del recortador de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="4a703-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="4a703-104">Blazor WebAssembly realiza un recorte de [lenguaje intermedio (IL)](/dotnet/standard/managed-code#intermediate-language--execution) para reducir el tamaño de la salida publicada.</span><span class="sxs-lookup"><span data-stu-id="4a703-104">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span> <span data-ttu-id="4a703-105">De forma predeterminada, el recorte se produce cuando se publica una aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a703-105">By default, trimming occurs when publishing an app.</span></span>

<span data-ttu-id="4a703-106">El recorte podría tener efectos perjudiciales.</span><span class="sxs-lookup"><span data-stu-id="4a703-106">Trimming may have detrimental effects.</span></span> <span data-ttu-id="4a703-107">En las aplicaciones que usan reflexión, el recortador a menudo no puede determinar los tipos necesarios para la reflexión en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="4a703-107">In apps that use reflection, the Trimmer often can't determine the required types for reflection at runtime.</span></span> <span data-ttu-id="4a703-108">Para recortar las aplicaciones que usan reflexión, se debe informar al recortador sobre los tipos necesarios para la reflexión en el código de la aplicación y en los paquetes o marcos de los que esta depende.</span><span class="sxs-lookup"><span data-stu-id="4a703-108">To trim apps that use reflection, the Trimmer must be informed about required types for reflection in both the app's code and in the packages or frameworks that the app depends on.</span></span> <span data-ttu-id="4a703-109">El recortador tampoco puede reaccionar a un comportamiento dinámico de la aplicación en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="4a703-109">The Trimmer is also unable to react to an app's dynamic behavior at runtime.</span></span> <span data-ttu-id="4a703-110">Para asegurarse de que la aplicación recortada funciona correctamente una vez implementada, pruebe la salida publicada con frecuencia durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="4a703-110">To ensure the trimmed app works correctly once deployed, test published output frequently while developing.</span></span>

<span data-ttu-id="4a703-111">Para configurar el recortador, consulte el artículo [Opciones de recorte](/dotnet/core/deploying/trimming-options) en la documentación sobre aspectos básicos de .NET, que incluye instrucciones sobre los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a703-111">To configure the Trimmer, see the [Trimming options](/dotnet/core/deploying/trimming-options) article in the .NET Fundamentals documentation, which includes guidance on the following subjects:</span></span>

* <span data-ttu-id="4a703-112">Deshabilitación del recorte para toda la aplicación con la propiedad `<PublishTrimmed>` en el archivo de proyecto</span><span class="sxs-lookup"><span data-stu-id="4a703-112">Disable trimming for the entire app with the `<PublishTrimmed>` property in the project file.</span></span>
* <span data-ttu-id="4a703-113">Control de la agresividad con la que el recortador descarta el IL sin usar</span><span class="sxs-lookup"><span data-stu-id="4a703-113">Control how aggressively unused IL is discarded by the Trimmer.</span></span>
* <span data-ttu-id="4a703-114">Interrupción del recorte de ensamblados específicos por parte del recortador</span><span class="sxs-lookup"><span data-stu-id="4a703-114">Stop the Trimmer from trimming specific assemblies.</span></span>
* <span data-ttu-id="4a703-115">Ensamblados "raíz" para recortar</span><span class="sxs-lookup"><span data-stu-id="4a703-115">"Root" assemblies for trimming.</span></span>
* <span data-ttu-id="4a703-116">Obtención de advertencias para los tipos reflejados mediante el establecimiento de la propiedad `<SuppressTrimAnalysisWarnings>` en `false` en el archivo de proyecto</span><span class="sxs-lookup"><span data-stu-id="4a703-116">Surface warnings for reflected types by setting the `<SuppressTrimAnalysisWarnings>` property to `false` in the project file.</span></span>
* <span data-ttu-id="4a703-117">Control del recorte de símbolos y compatibilidad con el depurador</span><span class="sxs-lookup"><span data-stu-id="4a703-117">Control symbol trimming and degugger support.</span></span>
* <span data-ttu-id="4a703-118">Establecimiento de características del recortador para recortar características de la biblioteca de marcos.</span><span class="sxs-lookup"><span data-stu-id="4a703-118">Set Trimmer features for trimming framework library features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a703-119">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4a703-119">Additional resources</span></span>

* [<span data-ttu-id="4a703-120">Recorte de implementaciones autocontenidas y ejecutables</span><span class="sxs-lookup"><span data-stu-id="4a703-120">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
