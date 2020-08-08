---
title: Plataformas admitidas de SignalR de ASP.NET Core
author: bradygaster
description: Obtenga información sobre las plataformas admitidas en SignalR de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: e6989f431d0d72241a6e1b94759585c02ef7398f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021554"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="c70ea-103">Plataformas admitidas de SignalR de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c70ea-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="c70ea-104">Requisitos del sistema de servidor de</span><span class="sxs-lookup"><span data-stu-id="c70ea-104">Server system requirements</span></span>

<span data-ttu-id="c70ea-105">SignalRpor ASP.NET Core admite cualquier plataforma de servidor que ASP.NET Core admita.</span><span class="sxs-lookup"><span data-stu-id="c70ea-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="c70ea-106">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="c70ea-106">JavaScript client</span></span>

<span data-ttu-id="c70ea-107">El [cliente de JavaScript](xref:signalr/javascript-client) se ejecuta en NodeJS 8 y versiones posteriores, y en los siguientes exploradores:</span><span class="sxs-lookup"><span data-stu-id="c70ea-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="c70ea-108">Explorador</span><span class="sxs-lookup"><span data-stu-id="c70ea-108">Browser</span></span>                         | <span data-ttu-id="c70ea-109">Versión</span><span class="sxs-lookup"><span data-stu-id="c70ea-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="c70ea-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="c70ea-110">Microsoft Edge</span></span>                  | <span data-ttu-id="c70ea-111">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="c70ea-111">Current&dagger;</span></span> |
| <span data-ttu-id="c70ea-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="c70ea-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="c70ea-113">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="c70ea-113">Current&dagger;</span></span> |
| <span data-ttu-id="c70ea-114">Google Chrome; incluye Android</span><span class="sxs-lookup"><span data-stu-id="c70ea-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="c70ea-115">Corrientes&dagger;</span><span class="sxs-lookup"><span data-stu-id="c70ea-115">Current&dagger;</span></span> |
| <span data-ttu-id="c70ea-116">Safari incluye iOS</span><span class="sxs-lookup"><span data-stu-id="c70ea-116">Safari; includes iOS</span></span>            | <span data-ttu-id="c70ea-117">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="c70ea-117">Current&dagger;</span></span> |
| <span data-ttu-id="c70ea-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="c70ea-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="c70ea-119">11</span><span class="sxs-lookup"><span data-stu-id="c70ea-119">11</span></span>              |

<span data-ttu-id="c70ea-120">&dagger;*Actual* hace referencia a la versión más reciente del explorador.</span><span class="sxs-lookup"><span data-stu-id="c70ea-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="c70ea-121">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="c70ea-121">.NET client</span></span>

<span data-ttu-id="c70ea-122">El [cliente .net](xref:signalr/dotnet-client) se ejecuta en cualquier plataforma compatible con ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c70ea-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="c70ea-123">Por ejemplo, [los desarrolladores de Xamarin SignalR pueden usar](https://github.com/aspnet/Announcements/issues/305) para compilar aplicaciones de Android con Xamarin. Android 8.4.0.1 y las aplicaciones iOS y posteriores con Xamarin. iOS 11.14.0.4 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="c70ea-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="c70ea-124">Si el servidor ejecuta IIS, el transporte de WebSockets requiere IIS 8,0 o posterior en Windows Server 2012 o posterior.</span><span class="sxs-lookup"><span data-stu-id="c70ea-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="c70ea-125">Se admiten otros transportes en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="c70ea-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="c70ea-126">Cliente de Java</span><span class="sxs-lookup"><span data-stu-id="c70ea-126">Java client</span></span>

<span data-ttu-id="c70ea-127">El [cliente de Java](xref:signalr/java-client) es compatible con Java 8 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="c70ea-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="c70ea-128">Clientes no compatibles</span><span class="sxs-lookup"><span data-stu-id="c70ea-128">Unsupported clients</span></span>

<span data-ttu-id="c70ea-129">Los siguientes clientes están disponibles pero son experimentales o no oficiales.</span><span class="sxs-lookup"><span data-stu-id="c70ea-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="c70ea-130">No se admiten actualmente y nunca pueden ser.</span><span class="sxs-lookup"><span data-stu-id="c70ea-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="c70ea-131">[Cliente de C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="c70ea-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="c70ea-132">[Cliente SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="c70ea-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
