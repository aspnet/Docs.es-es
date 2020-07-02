---
title: Plataformas admitidas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plataformas admitidas en Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401940"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="77ef1-103">Plataformas admitidas de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77ef1-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="77ef1-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="77ef1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="77ef1-105">Requisitos del explorador</span><span class="sxs-lookup"><span data-stu-id="77ef1-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="77ef1-106">Explorador</span><span class="sxs-lookup"><span data-stu-id="77ef1-106">Browser</span></span>                          | <span data-ttu-id="77ef1-107">Versión</span><span class="sxs-lookup"><span data-stu-id="77ef1-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="77ef1-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="77ef1-108">Microsoft Edge</span></span>                   | <span data-ttu-id="77ef1-109">Current</span><span class="sxs-lookup"><span data-stu-id="77ef1-109">Current</span></span>               |
| <span data-ttu-id="77ef1-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="77ef1-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="77ef1-111">Current</span><span class="sxs-lookup"><span data-stu-id="77ef1-111">Current</span></span>               |
| <span data-ttu-id="77ef1-112">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="77ef1-112">Google Chrome, including Android</span></span> | <span data-ttu-id="77ef1-113">Current</span><span class="sxs-lookup"><span data-stu-id="77ef1-113">Current</span></span>               |
| <span data-ttu-id="77ef1-114">Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="77ef1-114">Safari, including iOS</span></span>            | <span data-ttu-id="77ef1-115">Current</span><span class="sxs-lookup"><span data-stu-id="77ef1-115">Current</span></span>               |
| <span data-ttu-id="77ef1-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="77ef1-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="77ef1-117">No admitidas&dagger;</span><span class="sxs-lookup"><span data-stu-id="77ef1-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="77ef1-118">&dagger;Microsoft Internet Explorer no admite [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="77ef1-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="77ef1-119">Explorador</span><span class="sxs-lookup"><span data-stu-id="77ef1-119">Browser</span></span>                          | <span data-ttu-id="77ef1-120">Versión</span><span class="sxs-lookup"><span data-stu-id="77ef1-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="77ef1-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="77ef1-121">Microsoft Edge</span></span>                   | <span data-ttu-id="77ef1-122">Current</span><span class="sxs-lookup"><span data-stu-id="77ef1-122">Current</span></span>    |
| <span data-ttu-id="77ef1-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="77ef1-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="77ef1-124">Current</span><span class="sxs-lookup"><span data-stu-id="77ef1-124">Current</span></span>    |
| <span data-ttu-id="77ef1-125">Google Chrome, incluido Android</span><span class="sxs-lookup"><span data-stu-id="77ef1-125">Google Chrome, including Android</span></span> | <span data-ttu-id="77ef1-126">Current</span><span class="sxs-lookup"><span data-stu-id="77ef1-126">Current</span></span>    |
| <span data-ttu-id="77ef1-127">Safari, incluido iOS</span><span class="sxs-lookup"><span data-stu-id="77ef1-127">Safari, including iOS</span></span>            | <span data-ttu-id="77ef1-128">Current</span><span class="sxs-lookup"><span data-stu-id="77ef1-128">Current</span></span>    |
| <span data-ttu-id="77ef1-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="77ef1-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="77ef1-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="77ef1-130">11&dagger;</span></span> |

<span data-ttu-id="77ef1-131">&dagger;Se requieren más elementos polyfill (por ejemplo, se pueden agregar promesas a través de un paquete [`Polyfill.io`](https://polyfill.io/v3/)).</span><span class="sxs-lookup"><span data-stu-id="77ef1-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77ef1-132">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="77ef1-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
