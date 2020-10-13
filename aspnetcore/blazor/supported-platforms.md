---
title: Plataformas admitidas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plataformas admitidas en Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
no-loc:
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
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754546"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="6f63f-103">Plataformas admitidas de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f63f-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="6f63f-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6f63f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6f63f-105">Blazor WebAssembly y Blazor Server se admiten en los exploradores que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f63f-105">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="6f63f-106">Explorador</span><span class="sxs-lookup"><span data-stu-id="6f63f-106">Browser</span></span>                          | <span data-ttu-id="6f63f-107">Versión</span><span class="sxs-lookup"><span data-stu-id="6f63f-107">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="6f63f-108">Apple Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="6f63f-108">Apple Safari, including iOS</span></span>      | <span data-ttu-id="6f63f-109">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-109">Current&dagger;</span></span> |
| <span data-ttu-id="6f63f-110">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="6f63f-110">Google Chrome, including Android</span></span> | <span data-ttu-id="6f63f-111">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-111">Current&dagger;</span></span> |
| <span data-ttu-id="6f63f-112">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="6f63f-112">Microsoft Edge</span></span>                   | <span data-ttu-id="6f63f-113">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-113">Current&dagger;</span></span> |
| <span data-ttu-id="6f63f-114">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="6f63f-114">Mozilla Firefox</span></span>                  | <span data-ttu-id="6f63f-115">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-115">Current&dagger;</span></span> |  

<span data-ttu-id="6f63f-116">&dagger;*Actual* se refiere a la versión más reciente del explorador.</span><span class="sxs-lookup"><span data-stu-id="6f63f-116">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="6f63f-117">Explorador</span><span class="sxs-lookup"><span data-stu-id="6f63f-117">Browser</span></span>                          | <span data-ttu-id="6f63f-118">Versión</span><span class="sxs-lookup"><span data-stu-id="6f63f-118">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="6f63f-119">Apple Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="6f63f-119">Apple Safari, including iOS</span></span>      | <span data-ttu-id="6f63f-120">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-120">Current&dagger;</span></span>       |
| <span data-ttu-id="6f63f-121">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="6f63f-121">Google Chrome, including Android</span></span> | <span data-ttu-id="6f63f-122">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-122">Current&dagger;</span></span>       |
| <span data-ttu-id="6f63f-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="6f63f-123">Microsoft Edge</span></span>                   | <span data-ttu-id="6f63f-124">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-124">Current&dagger;</span></span>       |
| <span data-ttu-id="6f63f-125">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="6f63f-125">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="6f63f-126">No admitidas&Dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-126">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="6f63f-127">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="6f63f-127">Mozilla Firefox</span></span>                  | <span data-ttu-id="6f63f-128">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-128">Current&dagger;</span></span>       |  

<span data-ttu-id="6f63f-129">&dagger;*Actual* se refiere a la versión más reciente del explorador.</span><span class="sxs-lookup"><span data-stu-id="6f63f-129">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="6f63f-130">&Dagger;Microsoft Internet Explorer no admite [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="6f63f-130">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="6f63f-131">Explorador</span><span class="sxs-lookup"><span data-stu-id="6f63f-131">Browser</span></span>                          | <span data-ttu-id="6f63f-132">Versión</span><span class="sxs-lookup"><span data-stu-id="6f63f-132">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="6f63f-133">Apple Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="6f63f-133">Apple Safari, including iOS</span></span>      | <span data-ttu-id="6f63f-134">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-134">Current&dagger;</span></span> |
| <span data-ttu-id="6f63f-135">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="6f63f-135">Google Chrome, including Android</span></span> | <span data-ttu-id="6f63f-136">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-136">Current&dagger;</span></span> |
| <span data-ttu-id="6f63f-137">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="6f63f-137">Microsoft Edge</span></span>                   | <span data-ttu-id="6f63f-138">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-138">Current&dagger;</span></span> |
| <span data-ttu-id="6f63f-139">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="6f63f-139">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="6f63f-140">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-140">11&Dagger;</span></span>      |
| <span data-ttu-id="6f63f-141">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="6f63f-141">Mozilla Firefox</span></span>                  | <span data-ttu-id="6f63f-142">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6f63f-142">Current&dagger;</span></span> |

<span data-ttu-id="6f63f-143">&dagger;*Actual* se refiere a la versión más reciente del explorador.</span><span class="sxs-lookup"><span data-stu-id="6f63f-143">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="6f63f-144">&Dagger;Se requieren elementos polyfill adicionales.</span><span class="sxs-lookup"><span data-stu-id="6f63f-144">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="6f63f-145">Por ejemplo, las promesas se pueden agregar mediante un conjunto de [`Polyfill.io`](https://polyfill.io/v3/).</span><span class="sxs-lookup"><span data-stu-id="6f63f-145">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6f63f-146">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6f63f-146">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
