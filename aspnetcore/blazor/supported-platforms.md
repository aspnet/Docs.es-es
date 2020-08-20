---
title: Plataformas admitidas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plataformas admitidas en Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625952"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="1df7e-103">Plataformas admitidas de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1df7e-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="1df7e-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1df7e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="1df7e-105">Requisitos del explorador</span><span class="sxs-lookup"><span data-stu-id="1df7e-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="1df7e-106">Explorador</span><span class="sxs-lookup"><span data-stu-id="1df7e-106">Browser</span></span>                          | <span data-ttu-id="1df7e-107">Versión</span><span class="sxs-lookup"><span data-stu-id="1df7e-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="1df7e-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="1df7e-108">Microsoft Edge</span></span>                   | <span data-ttu-id="1df7e-109">Current</span><span class="sxs-lookup"><span data-stu-id="1df7e-109">Current</span></span>               |
| <span data-ttu-id="1df7e-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="1df7e-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="1df7e-111">Current</span><span class="sxs-lookup"><span data-stu-id="1df7e-111">Current</span></span>               |
| <span data-ttu-id="1df7e-112">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="1df7e-112">Google Chrome, including Android</span></span> | <span data-ttu-id="1df7e-113">Current</span><span class="sxs-lookup"><span data-stu-id="1df7e-113">Current</span></span>               |
| <span data-ttu-id="1df7e-114">Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="1df7e-114">Safari, including iOS</span></span>            | <span data-ttu-id="1df7e-115">Current</span><span class="sxs-lookup"><span data-stu-id="1df7e-115">Current</span></span>               |
| <span data-ttu-id="1df7e-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="1df7e-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="1df7e-117">No admitidas&dagger;</span><span class="sxs-lookup"><span data-stu-id="1df7e-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="1df7e-118">&dagger;Microsoft Internet Explorer no admite [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="1df7e-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="1df7e-119">Explorador</span><span class="sxs-lookup"><span data-stu-id="1df7e-119">Browser</span></span>                          | <span data-ttu-id="1df7e-120">Versión</span><span class="sxs-lookup"><span data-stu-id="1df7e-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="1df7e-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="1df7e-121">Microsoft Edge</span></span>                   | <span data-ttu-id="1df7e-122">Current</span><span class="sxs-lookup"><span data-stu-id="1df7e-122">Current</span></span>    |
| <span data-ttu-id="1df7e-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="1df7e-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="1df7e-124">Current</span><span class="sxs-lookup"><span data-stu-id="1df7e-124">Current</span></span>    |
| <span data-ttu-id="1df7e-125">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="1df7e-125">Google Chrome, including Android</span></span> | <span data-ttu-id="1df7e-126">Current</span><span class="sxs-lookup"><span data-stu-id="1df7e-126">Current</span></span>    |
| <span data-ttu-id="1df7e-127">Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="1df7e-127">Safari, including iOS</span></span>            | <span data-ttu-id="1df7e-128">Current</span><span class="sxs-lookup"><span data-stu-id="1df7e-128">Current</span></span>    |
| <span data-ttu-id="1df7e-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="1df7e-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="1df7e-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="1df7e-130">11&dagger;</span></span> |

<span data-ttu-id="1df7e-131">&dagger;Se requieren más elementos polyfill (por ejemplo, se pueden agregar promesas a través de un paquete [`Polyfill.io`](https://polyfill.io/v3/)).</span><span class="sxs-lookup"><span data-stu-id="1df7e-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1df7e-132">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1df7e-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
