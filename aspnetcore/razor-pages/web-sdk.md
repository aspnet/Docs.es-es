---
title: SDK web de ASP.NET Core
author: Rick-Anderson
description: Información general sobre Microsoft.NET.Sdk.Web.
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: a717d9dc0e326e62ec920d2a838e8b18be3db013
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020696"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="68256-103">SDK web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68256-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="68256-104">Información general</span><span class="sxs-lookup"><span data-stu-id="68256-104">Overview</span></span>

<span data-ttu-id="68256-105">`Microsoft.NET.Sdk.Web` es un [SDK de proyecto de MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) para compilar aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68256-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="68256-106">Aunque se pueden compilar aplicaciones de ASP.NET Core sin el SDK web, dicho SDK:</span><span class="sxs-lookup"><span data-stu-id="68256-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="68256-107">Está adaptado para proporcionar una experiencia al máximo nivel.</span><span class="sxs-lookup"><span data-stu-id="68256-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="68256-108">Es el destino recomendado para la mayoría de los usuarios.</span><span class="sxs-lookup"><span data-stu-id="68256-108">The recommended target for most users.</span></span>

<span data-ttu-id="68256-109">Use Web.SDK en un proyecto:</span><span class="sxs-lookup"><span data-stu-id="68256-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="68256-110">Características habilitadas al usar el SDK web:</span><span class="sxs-lookup"><span data-stu-id="68256-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="68256-111">Los proyectos que tienen como destino .NET Core 3.0 o posterior hacen referencia implícitamente:</span><span class="sxs-lookup"><span data-stu-id="68256-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="68256-112">Al [marco de trabajo compartido de ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="68256-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="68256-113">A [los analizadores](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) diseñados para compilar aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68256-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="68256-114">El SDK web importa destinos de MSBuild que permiten usar perfiles de publicación y realizar publicaciones mediante WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="68256-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="68256-115">Propiedades</span><span class="sxs-lookup"><span data-stu-id="68256-115">Properties</span></span>

| <span data-ttu-id="68256-116">Propiedad.</span><span class="sxs-lookup"><span data-stu-id="68256-116">Property</span></span> | <span data-ttu-id="68256-117">Descripción</span><span class="sxs-lookup"><span data-stu-id="68256-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="68256-118">Deshabilita la referencia implícita al marco de trabajo compartido `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="68256-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="68256-119">Deshabilita la referencia implícita a analizadores de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68256-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="68256-120">Deshabilita la referencia implícita a analizadores de componentes de Razor cuando se compilan aplicaciones de Blazor (servidor).</span><span class="sxs-lookup"><span data-stu-id="68256-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
