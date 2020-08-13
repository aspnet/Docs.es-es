---
title: Plataformas admitidas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plataformas admitidas en Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013533"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="98aa0-103">Plataformas admitidas de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98aa0-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="98aa0-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="98aa0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="98aa0-105">Requisitos del explorador</span><span class="sxs-lookup"><span data-stu-id="98aa0-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="98aa0-106">Explorador</span><span class="sxs-lookup"><span data-stu-id="98aa0-106">Browser</span></span>                          | <span data-ttu-id="98aa0-107">Versión</span><span class="sxs-lookup"><span data-stu-id="98aa0-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="98aa0-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="98aa0-108">Microsoft Edge</span></span>                   | <span data-ttu-id="98aa0-109">Current</span><span class="sxs-lookup"><span data-stu-id="98aa0-109">Current</span></span>               |
| <span data-ttu-id="98aa0-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="98aa0-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="98aa0-111">Current</span><span class="sxs-lookup"><span data-stu-id="98aa0-111">Current</span></span>               |
| <span data-ttu-id="98aa0-112">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="98aa0-112">Google Chrome, including Android</span></span> | <span data-ttu-id="98aa0-113">Current</span><span class="sxs-lookup"><span data-stu-id="98aa0-113">Current</span></span>               |
| <span data-ttu-id="98aa0-114">Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="98aa0-114">Safari, including iOS</span></span>            | <span data-ttu-id="98aa0-115">Current</span><span class="sxs-lookup"><span data-stu-id="98aa0-115">Current</span></span>               |
| <span data-ttu-id="98aa0-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="98aa0-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="98aa0-117">No admitidas&dagger;</span><span class="sxs-lookup"><span data-stu-id="98aa0-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="98aa0-118">&dagger;Microsoft Internet Explorer no admite [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="98aa0-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="98aa0-119">Explorador</span><span class="sxs-lookup"><span data-stu-id="98aa0-119">Browser</span></span>                          | <span data-ttu-id="98aa0-120">Versión</span><span class="sxs-lookup"><span data-stu-id="98aa0-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="98aa0-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="98aa0-121">Microsoft Edge</span></span>                   | <span data-ttu-id="98aa0-122">Current</span><span class="sxs-lookup"><span data-stu-id="98aa0-122">Current</span></span>    |
| <span data-ttu-id="98aa0-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="98aa0-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="98aa0-124">Current</span><span class="sxs-lookup"><span data-stu-id="98aa0-124">Current</span></span>    |
| <span data-ttu-id="98aa0-125">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="98aa0-125">Google Chrome, including Android</span></span> | <span data-ttu-id="98aa0-126">Current</span><span class="sxs-lookup"><span data-stu-id="98aa0-126">Current</span></span>    |
| <span data-ttu-id="98aa0-127">Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="98aa0-127">Safari, including iOS</span></span>            | <span data-ttu-id="98aa0-128">Current</span><span class="sxs-lookup"><span data-stu-id="98aa0-128">Current</span></span>    |
| <span data-ttu-id="98aa0-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="98aa0-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="98aa0-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="98aa0-130">11&dagger;</span></span> |

<span data-ttu-id="98aa0-131">&dagger;Se requieren más elementos polyfill (por ejemplo, se pueden agregar promesas a través de un paquete [`Polyfill.io`](https://polyfill.io/v3/)).</span><span class="sxs-lookup"><span data-stu-id="98aa0-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98aa0-132">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="98aa0-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
