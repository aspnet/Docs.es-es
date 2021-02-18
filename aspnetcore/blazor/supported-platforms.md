---
title: Plataformas admitidas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plataformas admitidas en Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280718"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="1596f-103">Plataformas admitidas de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1596f-103">ASP.NET Core Blazor supported platforms</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1596f-104">Blazor WebAssembly y Blazor Server se admiten en los exploradores que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="1596f-104">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="1596f-105">Explorador</span><span class="sxs-lookup"><span data-stu-id="1596f-105">Browser</span></span>                          | <span data-ttu-id="1596f-106">Versión</span><span class="sxs-lookup"><span data-stu-id="1596f-106">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="1596f-107">Apple Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="1596f-107">Apple Safari, including iOS</span></span>      | <span data-ttu-id="1596f-108">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-108">Current&dagger;</span></span> |
| <span data-ttu-id="1596f-109">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="1596f-109">Google Chrome, including Android</span></span> | <span data-ttu-id="1596f-110">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-110">Current&dagger;</span></span> |
| <span data-ttu-id="1596f-111">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="1596f-111">Microsoft Edge</span></span>                   | <span data-ttu-id="1596f-112">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-112">Current&dagger;</span></span> |
| <span data-ttu-id="1596f-113">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="1596f-113">Mozilla Firefox</span></span>                  | <span data-ttu-id="1596f-114">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-114">Current&dagger;</span></span> |  

<span data-ttu-id="1596f-115">&dagger;*Actual* se refiere a la versión más reciente del explorador.</span><span class="sxs-lookup"><span data-stu-id="1596f-115">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="1596f-116">Explorador</span><span class="sxs-lookup"><span data-stu-id="1596f-116">Browser</span></span>                          | <span data-ttu-id="1596f-117">Versión</span><span class="sxs-lookup"><span data-stu-id="1596f-117">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="1596f-118">Apple Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="1596f-118">Apple Safari, including iOS</span></span>      | <span data-ttu-id="1596f-119">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-119">Current&dagger;</span></span>       |
| <span data-ttu-id="1596f-120">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="1596f-120">Google Chrome, including Android</span></span> | <span data-ttu-id="1596f-121">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-121">Current&dagger;</span></span>       |
| <span data-ttu-id="1596f-122">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="1596f-122">Microsoft Edge</span></span>                   | <span data-ttu-id="1596f-123">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-123">Current&dagger;</span></span>       |
| <span data-ttu-id="1596f-124">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="1596f-124">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="1596f-125">No admitidas&Dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-125">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="1596f-126">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="1596f-126">Mozilla Firefox</span></span>                  | <span data-ttu-id="1596f-127">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-127">Current&dagger;</span></span>       |  

<span data-ttu-id="1596f-128">&dagger;*Actual* se refiere a la versión más reciente del explorador.</span><span class="sxs-lookup"><span data-stu-id="1596f-128">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="1596f-129">&Dagger;Microsoft Internet Explorer no admite [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="1596f-129">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="1596f-130">Explorador</span><span class="sxs-lookup"><span data-stu-id="1596f-130">Browser</span></span>                          | <span data-ttu-id="1596f-131">Versión</span><span class="sxs-lookup"><span data-stu-id="1596f-131">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="1596f-132">Apple Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="1596f-132">Apple Safari, including iOS</span></span>      | <span data-ttu-id="1596f-133">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-133">Current&dagger;</span></span> |
| <span data-ttu-id="1596f-134">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="1596f-134">Google Chrome, including Android</span></span> | <span data-ttu-id="1596f-135">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-135">Current&dagger;</span></span> |
| <span data-ttu-id="1596f-136">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="1596f-136">Microsoft Edge</span></span>                   | <span data-ttu-id="1596f-137">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-137">Current&dagger;</span></span> |
| <span data-ttu-id="1596f-138">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="1596f-138">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="1596f-139">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-139">11&Dagger;</span></span>      |
| <span data-ttu-id="1596f-140">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="1596f-140">Mozilla Firefox</span></span>                  | <span data-ttu-id="1596f-141">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="1596f-141">Current&dagger;</span></span> |

<span data-ttu-id="1596f-142">&dagger;*Actual* se refiere a la versión más reciente del explorador.</span><span class="sxs-lookup"><span data-stu-id="1596f-142">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="1596f-143">&Dagger;Se requieren elementos polyfill adicionales.</span><span class="sxs-lookup"><span data-stu-id="1596f-143">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="1596f-144">Por ejemplo, las promesas se pueden agregar mediante un conjunto de [`Polyfill.io`](https://polyfill.io/v3/).</span><span class="sxs-lookup"><span data-stu-id="1596f-144">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1596f-145">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1596f-145">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
