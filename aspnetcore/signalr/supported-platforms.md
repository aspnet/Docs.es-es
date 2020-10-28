---
title: 'Plataformas admitidas de :::no-loc(SignalR)::: de ASP.NET Core'
author: bradygaster
description: 'Obtenga información sobre las plataformas admitidas en :::no-loc(SignalR)::: de ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/supported-platforms
ms.openlocfilehash: 761edbbe7bab28d2340207a0adea0718b37c7ec1
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690331"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="3a2aa-103">Plataformas admitidas de :::no-loc(SignalR)::: de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a2aa-103">ASP.NET Core :::no-loc(SignalR)::: supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="3a2aa-104">Requisitos del sistema de servidor de</span><span class="sxs-lookup"><span data-stu-id="3a2aa-104">Server system requirements</span></span>

<span data-ttu-id="3a2aa-105">:::no-loc(SignalR)::: por ASP.NET Core admite cualquier plataforma de servidor que ASP.NET Core admita.</span><span class="sxs-lookup"><span data-stu-id="3a2aa-105">:::no-loc(SignalR)::: for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="3a2aa-106">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="3a2aa-106">JavaScript client</span></span>

<span data-ttu-id="3a2aa-107">El [cliente de JavaScript](xref:signalr/javascript-client) se ejecuta en NodeJS 8 y versiones posteriores, y en los siguientes exploradores:</span><span class="sxs-lookup"><span data-stu-id="3a2aa-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="3a2aa-108">Explorador</span><span class="sxs-lookup"><span data-stu-id="3a2aa-108">Browser</span></span>                          | <span data-ttu-id="3a2aa-109">Versión</span><span class="sxs-lookup"><span data-stu-id="3a2aa-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="3a2aa-110">Apple Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="3a2aa-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="3a2aa-111">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="3a2aa-111">Current&dagger;</span></span> |
| <span data-ttu-id="3a2aa-112">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="3a2aa-112">Google Chrome, including Android</span></span> | <span data-ttu-id="3a2aa-113">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="3a2aa-113">Current&dagger;</span></span> |
| <span data-ttu-id="3a2aa-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="3a2aa-114">Microsoft Edge</span></span>                   | <span data-ttu-id="3a2aa-115">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="3a2aa-115">Current&dagger;</span></span> |
| <span data-ttu-id="3a2aa-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="3a2aa-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="3a2aa-117">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="3a2aa-117">Current&dagger;</span></span> |

<span data-ttu-id="3a2aa-118">&dagger;*Actual* se refiere a la versión más reciente del explorador.</span><span class="sxs-lookup"><span data-stu-id="3a2aa-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="3a2aa-119">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="3a2aa-119">.NET client</span></span>

<span data-ttu-id="3a2aa-120">El [cliente .net](xref:signalr/dotnet-client) se ejecuta en cualquier plataforma compatible con ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="3a2aa-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="3a2aa-121">Por ejemplo, [los desarrolladores de Xamarin :::no-loc(SignalR)::: pueden usar](https://github.com/aspnet/Announcements/issues/305) para compilar aplicaciones de Android con Xamarin. Android 8.4.0.1 y las aplicaciones iOS y posteriores con Xamarin. iOS 11.14.0.4 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="3a2aa-121">For example, [Xamarin developers can use :::no-loc(SignalR):::](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="3a2aa-122">Si el servidor ejecuta IIS, el transporte de WebSockets requiere IIS 8,0 o posterior en Windows Server 2012 o posterior.</span><span class="sxs-lookup"><span data-stu-id="3a2aa-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="3a2aa-123">Se admiten otros transportes en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="3a2aa-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="3a2aa-124">Cliente de Java</span><span class="sxs-lookup"><span data-stu-id="3a2aa-124">Java client</span></span>

<span data-ttu-id="3a2aa-125">El [cliente de Java](xref:signalr/java-client) es compatible con Java 8 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="3a2aa-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="3a2aa-126">Clientes no compatibles</span><span class="sxs-lookup"><span data-stu-id="3a2aa-126">Unsupported clients</span></span>

<span data-ttu-id="3a2aa-127">Los siguientes clientes están disponibles pero son experimentales o no oficiales.</span><span class="sxs-lookup"><span data-stu-id="3a2aa-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="3a2aa-128">No se admiten actualmente y nunca pueden ser.</span><span class="sxs-lookup"><span data-stu-id="3a2aa-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="3a2aa-129">[Cliente de C++](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="3a2aa-129">[C++ client](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span></span>

* <span data-ttu-id="3a2aa-130">[Cliente SWIFT](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="3a2aa-130">[Swift client](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span></span>
