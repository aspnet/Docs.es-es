---
title: gRPC en plataformas compatibles con .NET
author: jamesnk
description: Conozca las plataformas admitidas para gRPC en .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530169"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="0d5eb-103">gRPC en plataformas compatibles con .NET</span><span class="sxs-lookup"><span data-stu-id="0d5eb-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="0d5eb-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="0d5eb-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="0d5eb-105">En este artículo se describen los requisitos y las plataformas admitidas para usar gRPC con .NET.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="0d5eb-106">gRPC aprovecha las características avanzadas disponibles en HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="0d5eb-107">HTTP/2 no se admite en todas partes, pero hay disponible un segundo formato de conexión con HTTP/1.1 para gRPC:</span><span class="sxs-lookup"><span data-stu-id="0d5eb-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="0d5eb-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sobre HTTP/2 es la forma en que se suele usar gRPC.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="0d5eb-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifica el protocolo gRPC para que sea compatible con HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="0d5eb-110">gRPC-Web se puede usar en más lugares, en particular, las aplicaciones del explorador pueden llamarla.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="0d5eb-111">Ya no se admiten dos características avanzadas de gRPC: streaming de cliente y streaming bidireccional.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="0d5eb-112">gRPC en .NET admite ambos formatos de conexión.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="0d5eb-113">De forma predeterminada, se usa gRPC a través de HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="0d5eb-114">Para obtener información sobre la configuración de gRPC-Web, consulte <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="0d5eb-115">Requisitos de los dispositivos</span><span class="sxs-lookup"><span data-stu-id="0d5eb-115">Device requirements</span></span>

<span data-ttu-id="0d5eb-116">gRPC en .NET admite cualquier dispositivo compatible con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="0d5eb-117">Windows</span><span class="sxs-lookup"><span data-stu-id="0d5eb-117">Windows</span></span>
> * <span data-ttu-id="0d5eb-118">Linux</span><span class="sxs-lookup"><span data-stu-id="0d5eb-118">Linux</span></span>
> * <span data-ttu-id="0d5eb-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="0d5eb-119">macOS&dagger;</span></span>
> * <span data-ttu-id="0d5eb-120">Exploradores&Dagger;</span><span class="sxs-lookup"><span data-stu-id="0d5eb-120">Browsers&Dagger;</span></span>

<span data-ttu-id="0d5eb-121">&dagger;[macOS no admite el hospedaje de aplicaciones ASP.NET Core con HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="0d5eb-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="0d5eb-122">Los clientes de gRPC en macOS pueden llamar a servicios remotos que usan HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="0d5eb-123">Las aplicaciones &Dagger;Blazor WebAssembly pueden llamar a servicios gRPC con gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="0d5eb-124">Requisitos del servidor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d5eb-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="0d5eb-125">Los servicios gRPC se pueden hospedar en todos los servidores de ASP.NET Core integrados.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="0d5eb-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="0d5eb-126">Kestrel</span></span>
> * <span data-ttu-id="0d5eb-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="0d5eb-127">TestServer</span></span>
> * <span data-ttu-id="0d5eb-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="0d5eb-128">IIS&dagger;</span></span>
> * <span data-ttu-id="0d5eb-129">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="0d5eb-129">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="0d5eb-130">&dagger;IIS requiere .NET 5 y Windows 10, compilación 20241 o posterior.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-130">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="0d5eb-131">&Dagger;HTTP.sys requiere .NET 5 y Windows 10, compilación 19529 o posterior.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-131">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="0d5eb-132">Para obtener información acerca de la configuración de servidores de ASP.NET Core para ejecutar gRPC, consulte <xref:grpc/aspnetcore#server-options>.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-132">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="0d5eb-133">Requisitos de la versión de .NET</span><span class="sxs-lookup"><span data-stu-id="0d5eb-133">.NET version requirements</span></span>

<span data-ttu-id="0d5eb-134">gRPC en .NET es compatible con .NET Core 3 y .NET 5 o posterior.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-134">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="0d5eb-135">.NET 5 o posterior</span><span class="sxs-lookup"><span data-stu-id="0d5eb-135">.NET 5 or later</span></span>
> * <span data-ttu-id="0d5eb-136">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="0d5eb-136">.NET Core 3</span></span>

<span data-ttu-id="0d5eb-137">gRPC en .NET no admite la ejecución en .NET Framework y Xamarin.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-137">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="0d5eb-138">La [biblioteca de gRPC C# core](https://grpc.io/docs/languages/csharp/quickstart/) es una biblioteca de terceros que admite .NET Framework y Xamarin.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-138">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="0d5eb-139">gRPC C# core no es compatible con Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-139">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="0d5eb-140">Servicios de Azure</span><span class="sxs-lookup"><span data-stu-id="0d5eb-140">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="0d5eb-141">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="0d5eb-141">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="0d5eb-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="0d5eb-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="0d5eb-143">&dagger;Azure App Service no admite el hospedaje de gRPC a través de HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-143">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="0d5eb-144">gRPC-Web es una alternativa compatible.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-144">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="0d5eb-145">Se está trabajando para mejorar la compatibilidad con gRPC con HTTP/2 en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="0d5eb-145">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="0d5eb-146">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/9020).</span><span class="sxs-lookup"><span data-stu-id="0d5eb-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d5eb-147">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="0d5eb-147">Additional resources</span></span>

* [<span data-ttu-id="0d5eb-148">Biblioteca de gRPC C# core</span><span class="sxs-lookup"><span data-stu-id="0d5eb-148">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
