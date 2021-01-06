---
title: Uso de las API de ASP.NET Core en una biblioteca de clases
author: scottaddie
description: Aprenda a usar las API de ASP.NET Core en una biblioteca de clases.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: c012658a6f48247af60c8bfd56a7d987f6aa8a68
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061514"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="3b12f-103">Uso de las API de ASP.NET Core en una biblioteca de clases</span><span class="sxs-lookup"><span data-stu-id="3b12f-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="3b12f-104">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="3b12f-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="3b12f-105">En este documento se proporcionan instrucciones para usar las API de ASP.NET Core en una biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="3b12f-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="3b12f-106">Para obtener todas las demás instrucciones de bibliotecas, consulte [Guía de la biblioteca de código abierto](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="3b12f-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="3b12f-107">Determinación de las versiones de ASP.NET Core compatibles</span><span class="sxs-lookup"><span data-stu-id="3b12f-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="3b12f-108">ASP.NET Core sigue la [directiva de compatibilidad de .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="3b12f-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="3b12f-109">Consulte la directiva de compatibilidad al determinar qué versiones de ASP.NET Core se admiten en una biblioteca.</span><span class="sxs-lookup"><span data-stu-id="3b12f-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="3b12f-110">Una biblioteca debe cumplir estas condiciones:</span><span class="sxs-lookup"><span data-stu-id="3b12f-110">A library should:</span></span>

* <span data-ttu-id="3b12f-111">Hacer todo lo posible para admitir todas las versiones de ASP.NET Core clasificadas como versiones *compatibles a largo plazo* (LTS).</span><span class="sxs-lookup"><span data-stu-id="3b12f-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="3b12f-112">No sentirse obligada a admitir versiones de ASP.NET Core clasificadas como *final del ciclo de vida* (EOL).</span><span class="sxs-lookup"><span data-stu-id="3b12f-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="3b12f-113">A medida que se lanzan versiones preliminares de ASP.NET Core, se publican los cambios importantes en el repositorio [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) de GitHub.</span><span class="sxs-lookup"><span data-stu-id="3b12f-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="3b12f-114">Las pruebas de compatibilidad de las bibliotecas se pueden llevar a cabo a medida que se desarrollan las características del marco.</span><span class="sxs-lookup"><span data-stu-id="3b12f-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="3b12f-115">Uso del marco compartido de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3b12f-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="3b12f-116">Con el lanzamiento de .NET Core 3,0, muchos ensamblados de ASP.NET Core ya no se publican en NuGet como paquetes.</span><span class="sxs-lookup"><span data-stu-id="3b12f-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="3b12f-117">Ahora, los ensamblados se incluyen en el marco compartido de `Microsoft.AspNetCore.App`, que se instala con los instaladores del SDK de .NET Core y el entorno de ejecución.</span><span class="sxs-lookup"><span data-stu-id="3b12f-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="3b12f-118">Para obtener una lista de los paquetes que ya no se publican, vea [Quitar referencias de paquetes obsoletas](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="3b12f-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="3b12f-119">A partir de .NET Core 3.0, los proyectos que usan el SDK de MSBuild `Microsoft.NET.Sdk.Web` hacen referencia implícitamente al marco compartido.</span><span class="sxs-lookup"><span data-stu-id="3b12f-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="3b12f-120">Los proyectos que usan el SDK de `Microsoft.NET.Sdk` o `Microsoft.NET.Sdk.Razor` deben hacer referencia a ASP.NET Core para usar las API de ASP.NET Core en el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="3b12f-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="3b12f-121">Para hacer referencia a ASP.NET Core, agregue el siguiente elemento `<FrameworkReference>` al archivo del proyecto:</span><span class="sxs-lookup"><span data-stu-id="3b12f-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="3b12f-122">La referencia a ASP.NET Core de esta manera solo se admite para los proyectos que tienen como destino .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3b12f-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-no-locblazor-extensibility"></a><span data-ttu-id="3b12f-123">Inclusión de la extensibilidad Blazor</span><span class="sxs-lookup"><span data-stu-id="3b12f-123">Include Blazor extensibility</span></span>

<span data-ttu-id="3b12f-124">Blazor admite los [modelos de hospedaje](xref:blazor/hosting-models) de WebAssembly (WASM) y Server.</span><span class="sxs-lookup"><span data-stu-id="3b12f-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="3b12f-125">A menos que haya un motivo específico para no hacerlo, una biblioteca de [componentes Razor](xref:blazor/components/index) debe admitir ambos modelos de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="3b12f-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components/index) library should support both hosting models.</span></span> <span data-ttu-id="3b12f-126">Una biblioteca de componentes Razor debe usar el [SDK Microsoft.NET.Sdk.Razor](xref:razor-pages/sdk)</span><span class="sxs-lookup"><span data-stu-id="3b12f-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="3b12f-127">Compatibilidad con ambos modelos de hospedaje</span><span class="sxs-lookup"><span data-stu-id="3b12f-127">Support both hosting models</span></span>

<span data-ttu-id="3b12f-128">Para permitir el uso de componentes Razor desde proyectos tanto de [Blazor Server](xref:blazor/hosting-models#blazor-server) como de [BlazorWebAssembly de ](xref:blazor/hosting-models#blazor-webassembly), use las instrucciones siguientes de su editor.</span><span class="sxs-lookup"><span data-stu-id="3b12f-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b12f-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b12f-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3b12f-130">Use la plantilla de proyecto de **Biblioteca de clases de Razor** .</span><span class="sxs-lookup"><span data-stu-id="3b12f-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="3b12f-131">Se debe desactivar la casilla **Support pages and views** (Admitir páginas y vistas) de la plantilla.</span><span class="sxs-lookup"><span data-stu-id="3b12f-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b12f-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b12f-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3b12f-133">Ejecute el comando siguiente en el terminal integrado:</span><span class="sxs-lookup"><span data-stu-id="3b12f-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b12f-134">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3b12f-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3b12f-135">Use la plantilla de proyecto de **Biblioteca de clases de Razor** .</span><span class="sxs-lookup"><span data-stu-id="3b12f-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="3b12f-136">El proyecto generado a partir de la plantilla hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3b12f-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="3b12f-137">Se dirige a .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="3b12f-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="3b12f-138">Establece la propiedad `RazorLangVersion` como `3.0`.</span><span class="sxs-lookup"><span data-stu-id="3b12f-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="3b12f-139">`3.0` es el valor predeterminado para .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3b12f-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="3b12f-140">Agrega las siguientes referencias de paquete:</span><span class="sxs-lookup"><span data-stu-id="3b12f-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="3b12f-141">Microsoft.AspNetCore.Components</span><span class="sxs-lookup"><span data-stu-id="3b12f-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="3b12f-142">Microsoft.AspNetCore.Components.Web</span><span class="sxs-lookup"><span data-stu-id="3b12f-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="3b12f-143">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3b12f-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="3b12f-144">Compatibilidad con un modelo de hospedaje específico</span><span class="sxs-lookup"><span data-stu-id="3b12f-144">Support a specific hosting model</span></span>

<span data-ttu-id="3b12f-145">Es mucho menos común admitir un único modelo de hospedaje de Blazor.</span><span class="sxs-lookup"><span data-stu-id="3b12f-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="3b12f-146">Por ejemplo, para admitir el uso de componentes Razor únicamente desde proyectos de [Blazor Server](xref:blazor/hosting-models#blazor-server):</span><span class="sxs-lookup"><span data-stu-id="3b12f-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="3b12f-147">Diríjase a .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3b12f-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="3b12f-148">Agregue un elemento `<FrameworkReference>` para el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="3b12f-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="3b12f-149">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3b12f-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="3b12f-150">Para más información sobre las bibliotecas que contienen componentes Razor, vea [Bibliotecas de clases de componentes Razor de ASP.NET Core](xref:blazor/components/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="3b12f-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/components/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="3b12f-151">Inclusión de la extensibilidad MVC</span><span class="sxs-lookup"><span data-stu-id="3b12f-151">Include MVC extensibility</span></span>

<span data-ttu-id="3b12f-152">En esta sección se describen las recomendaciones para las bibliotecas que incluyen:</span><span class="sxs-lookup"><span data-stu-id="3b12f-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="3b12f-153">Vistas de Razor o Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3b12f-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="3b12f-154">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="3b12f-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="3b12f-155">Visualización de componentes</span><span class="sxs-lookup"><span data-stu-id="3b12f-155">View components</span></span>](#view-components)

<span data-ttu-id="3b12f-156">En esta sección no se explica la compatibilidad con múltiples versiones de MVC.</span><span class="sxs-lookup"><span data-stu-id="3b12f-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="3b12f-157">Para obtener instrucciones sobre cómo admitir varias versiones de ASP.NET Core, consulte [Compatibilidad con varias versiones de ASP.NET Core](#support-multiple-aspnet-core-versions).</span><span class="sxs-lookup"><span data-stu-id="3b12f-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="no-locrazor-views-or-no-locrazor-pages"></a><span data-ttu-id="3b12f-158">Vistas de Razor o Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3b12f-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="3b12f-159">Un proyecto que incluya [vistas de Razor](xref:mvc/views/overview) o [Razor Pages](xref:razor-pages/index) debe usar el [SDK Microsoft.NET.Sdk.Razor](xref:razor-pages/sdk)</span><span class="sxs-lookup"><span data-stu-id="3b12f-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="3b12f-160">Si el proyecto tiene como destino .NET Core 3.x, requiere lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3b12f-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="3b12f-161">Una propiedad de MSBuild `AddRazorSupportForMvc` establecida en `true`.</span><span class="sxs-lookup"><span data-stu-id="3b12f-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="3b12f-162">Un elemento `<FrameworkReference>` para el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="3b12f-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="3b12f-163">La plantilla de proyecto **Biblioteca de clases de Razor** cumple los requisitos anteriores de los proyectos que tienen como destino .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3b12f-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="3b12f-164">Utilice las instrucciones siguientes para su editor.</span><span class="sxs-lookup"><span data-stu-id="3b12f-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b12f-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b12f-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3b12f-166">Use la plantilla de proyecto de **Biblioteca de clases de Razor** .</span><span class="sxs-lookup"><span data-stu-id="3b12f-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="3b12f-167">Se activa la casilla **Support pages and views** (Admitir páginas y vistas) de la plantilla.</span><span class="sxs-lookup"><span data-stu-id="3b12f-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b12f-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b12f-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3b12f-169">Ejecute el comando siguiente en el terminal integrado:</span><span class="sxs-lookup"><span data-stu-id="3b12f-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b12f-170">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3b12f-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3b12f-171">No hay compatibilidad con plantillas de proyecto en este momento.</span><span class="sxs-lookup"><span data-stu-id="3b12f-171">No project template support at this time.</span></span>

---

<span data-ttu-id="3b12f-172">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3b12f-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="3b12f-173">Si el proyecto tiene como destino .NET Standard, se requiere una referencia de paquete de [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc).</span><span class="sxs-lookup"><span data-stu-id="3b12f-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="3b12f-174">El paquete `Microsoft.AspNetCore.Mvc` se ha migrado al marco compartido en ASP.NET Core 3.0 y, por tanto, ya no se publica.</span><span class="sxs-lookup"><span data-stu-id="3b12f-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="3b12f-175">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3b12f-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="3b12f-176">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="3b12f-176">Tag Helpers</span></span>

<span data-ttu-id="3b12f-177">Un proyecto que incluye [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) debe usar el SDK `Microsoft.NET.Sdk`.</span><span class="sxs-lookup"><span data-stu-id="3b12f-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="3b12f-178">Si el destino es .NET Core 3.x, agregue un elemento `<FrameworkReference>` para el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="3b12f-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="3b12f-179">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3b12f-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="3b12f-180">Si el destino es .NET Standard (para admitir versiones anteriores a ASP.NET Core 3.x), agregue una referencia de paquete a [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span><span class="sxs-lookup"><span data-stu-id="3b12f-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="3b12f-181">El paquete de `Microsoft.AspNetCore.Mvc.Razor` se ha migrado al marco compartido y, por tanto, ya no se publica.</span><span class="sxs-lookup"><span data-stu-id="3b12f-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="3b12f-182">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3b12f-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="3b12f-183">Componentes de vista</span><span class="sxs-lookup"><span data-stu-id="3b12f-183">View components</span></span>

<span data-ttu-id="3b12f-184">Un proyecto que incluya [Componentes de vista](xref:mvc/views/view-components) debe utilizar el SDK de `Microsoft.NET.Sdk`.</span><span class="sxs-lookup"><span data-stu-id="3b12f-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="3b12f-185">Si el destino es .NET Core 3.x, agregue un elemento `<FrameworkReference>` para el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="3b12f-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="3b12f-186">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3b12f-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="3b12f-187">Si el destino es .NET Standard (para admitir versiones anteriores a ASP.NET Core 3.x), agregue una referencia de paquete a [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="3b12f-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="3b12f-188">El paquete de `Microsoft.AspNetCore.Mvc.ViewFeatures` se ha migrado al marco compartido y, por tanto, ya no se publica.</span><span class="sxs-lookup"><span data-stu-id="3b12f-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="3b12f-189">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3b12f-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="3b12f-190">Compatibilidad con varias versiones de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b12f-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="3b12f-191">Se requiere la compatibilidad con múltiples versiones para crear una biblioteca que admita diferentes variantes de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3b12f-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="3b12f-192">Piense en un escenario en el que una biblioteca de asistentes de etiquetas deba admitir las siguientes variantes de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3b12f-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="3b12f-193">ASP.NET Core 2.1 con .NET Framework 4.6.1 como destino</span><span class="sxs-lookup"><span data-stu-id="3b12f-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="3b12f-194">ASP.NET Core 2.x con .NET Core 2.x como destino</span><span class="sxs-lookup"><span data-stu-id="3b12f-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="3b12f-195">ASP.NET Core 3.x con .NET Core 3.x como destino</span><span class="sxs-lookup"><span data-stu-id="3b12f-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="3b12f-196">El siguiente archivo de proyecto admite estas variantes a través de la propiedad `TargetFrameworks`:</span><span class="sxs-lookup"><span data-stu-id="3b12f-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="3b12f-197">Con el archivo de proyecto anterior:</span><span class="sxs-lookup"><span data-stu-id="3b12f-197">With the preceding project file:</span></span>

* <span data-ttu-id="3b12f-198">El paquete de `Markdig` se agrega a todos los consumidores.</span><span class="sxs-lookup"><span data-stu-id="3b12f-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="3b12f-199">Se ha agregado una referencia a [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span><span class="sxs-lookup"><span data-stu-id="3b12f-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span></span> <span data-ttu-id="3b12f-200">para los consumidores cuyo destino es .NET Framework 4.6.1 o posterior o .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="3b12f-200">is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="3b12f-201">La versión 2.1.0 del paquete funciona con ASP.NET Core 2.2 gracias a la compatibilidad con versiones anteriores.</span><span class="sxs-lookup"><span data-stu-id="3b12f-201">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="3b12f-202">Se hace referencia al marco compartido para los consumidores que tienen como destino .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3b12f-202">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="3b12f-203">El paquete de `Microsoft.AspNetCore.Mvc.Razor` se incluye en el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="3b12f-203">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="3b12f-204">Como alternativa, se podría tomar como destino .NET Standard 2.0 en lugar de .NET Core 2.1 y .NET Framework 4.6.1:</span><span class="sxs-lookup"><span data-stu-id="3b12f-204">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="3b12f-205">Con el archivo de proyecto anterior, hay que tomar en cuenta los siguientes aspectos:</span><span class="sxs-lookup"><span data-stu-id="3b12f-205">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="3b12f-206">Dado que la biblioteca solo contiene asistentes de etiquetas, es más sencillo dirigirse a las plataformas específicas en las que se ejecuta ASP.NET Core: .NET Core y .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="3b12f-206">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="3b12f-207">Los asistentes de etiquetas no se pueden usar en otros marcos de destino compatibles con .NET Standard 2.0 como Unity, UWP y Xamarin.</span><span class="sxs-lookup"><span data-stu-id="3b12f-207">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="3b12f-208">El uso de .NET Standard 2.0 de .NET Framework tiene algunos problemas que se han solucionado en .NET Framework 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="3b12f-208">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="3b12f-209">Puede mejorar la experiencia de los consumidores que usan .NET Framework de 4.6.1 a 4.7.1 tomando como destino .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="3b12f-209">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="3b12f-210">Si la biblioteca necesita llamar a API específicas de la plataforma, diríjase a implementaciones de .NET específicas en lugar de .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="3b12f-210">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="3b12f-211">Para obtener más información, consulte [Compatibilidad con múltiples versiones](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="3b12f-211">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="3b12f-212">Uso de una API que no ha cambiado</span><span class="sxs-lookup"><span data-stu-id="3b12f-212">Use an API that hasn't changed</span></span>

<span data-ttu-id="3b12f-213">Imagine un escenario en el que está actualizando una biblioteca de middleware de .NET Core 2.2 a 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b12f-213">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="3b12f-214">Las API de middleware ASP.NET Core que se usan en la biblioteca no han cambiado entre ASP.NET Core 2.2 y 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b12f-214">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="3b12f-215">Siga estos pasos para seguir admitiendo la biblioteca de middleware en .NET Core 3.0:</span><span class="sxs-lookup"><span data-stu-id="3b12f-215">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="3b12f-216">Siga las [instrucciones de la biblioteca estándar](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="3b12f-216">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="3b12f-217">Agregue una referencia de paquete para cada paquete de NuGet de la API si el ensamblado correspondiente no existe en el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="3b12f-217">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="3b12f-218">Uso de una API que ha cambiado</span><span class="sxs-lookup"><span data-stu-id="3b12f-218">Use an API that changed</span></span>

<span data-ttu-id="3b12f-219">Imagine un escenario en el que está actualizando una biblioteca de .NET Core 2.2 a .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b12f-219">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="3b12f-220">Una API de ASP.NET Core que se usa en la biblioteca tiene un [cambio importante](/dotnet/core/compatibility/breaking-changes) en ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b12f-220">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="3b12f-221">Considere si la biblioteca se puede volver a escribir para que no use la API dañada en todas las versiones.</span><span class="sxs-lookup"><span data-stu-id="3b12f-221">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="3b12f-222">Si puede volver a escribir la biblioteca, hágalo y continúe con el marco anterior como destino (por ejemplo, .NET Standard 2.0 o .NET Framework 4.6.1) con referencias de paquete.</span><span class="sxs-lookup"><span data-stu-id="3b12f-222">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="3b12f-223">Si no puede volver a escribir la biblioteca, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="3b12f-223">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="3b12f-224">Agregue un destino para .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b12f-224">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="3b12f-225">Agregue un elemento `<FrameworkReference>` para el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="3b12f-225">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="3b12f-226">Use la [directiva de preprocesador de #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) con el símbolo del marco de destino adecuado para compilar el código de forma condicional.</span><span class="sxs-lookup"><span data-stu-id="3b12f-226">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="3b12f-227">Por ejemplo, las lecturas y escrituras sincrónicas en secuencias de respuesta y solicitud HTTP están deshabilitadas de forma predeterminada a partir de ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b12f-227">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="3b12f-228">ASP.NET Core 2.2 admite el comportamiento sincrónico de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="3b12f-228">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="3b12f-229">Considere una biblioteca de middleware en la que se deben habilitar las lecturas y escrituras sincrónicas donde se está produciendo la E/S.</span><span class="sxs-lookup"><span data-stu-id="3b12f-229">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="3b12f-230">La biblioteca debe incluir el código para habilitar las características sincrónicas en la directiva de preprocesador adecuada.</span><span class="sxs-lookup"><span data-stu-id="3b12f-230">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="3b12f-231">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3b12f-231">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="3b12f-232">Uso de una API presentada en 3.0</span><span class="sxs-lookup"><span data-stu-id="3b12f-232">Use an API introduced in 3.0</span></span>

<span data-ttu-id="3b12f-233">Imagine que quiere usar una API de ASP.NET Core que se presentó en ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b12f-233">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="3b12f-234">Pregúntese lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3b12f-234">Consider the following questions:</span></span>

1. <span data-ttu-id="3b12f-235">¿La biblioteca requiere funcionalmente la nueva API?</span><span class="sxs-lookup"><span data-stu-id="3b12f-235">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="3b12f-236">¿La biblioteca puede implementar esta característica de manera diferente?</span><span class="sxs-lookup"><span data-stu-id="3b12f-236">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="3b12f-237">Si la biblioteca requiere funcionalmente la API y no hay manera de implementarla en el nivel inferior:</span><span class="sxs-lookup"><span data-stu-id="3b12f-237">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="3b12f-238">Diríjase solo a .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="3b12f-238">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="3b12f-239">Agregue un elemento `<FrameworkReference>` para el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="3b12f-239">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="3b12f-240">Si la biblioteca puede implementar esta característica de manera diferente:</span><span class="sxs-lookup"><span data-stu-id="3b12f-240">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="3b12f-241">Agregue .NET Core 3.x como plataforma de destino.</span><span class="sxs-lookup"><span data-stu-id="3b12f-241">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="3b12f-242">Agregue un elemento `<FrameworkReference>` para el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="3b12f-242">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="3b12f-243">Use la [directiva de preprocesador de #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) con el símbolo del marco de destino adecuado para compilar el código de forma condicional.</span><span class="sxs-lookup"><span data-stu-id="3b12f-243">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="3b12f-244">Por ejemplo, el siguiente asistente de etiquetas usa la interfaz <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> presentada en ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b12f-244">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="3b12f-245">Los consumidores que tienen como destino .NET Core 3.0 ejecutan la ruta de acceso al código definida por el símbolo del marco de destino `NETCOREAPP3_0`.</span><span class="sxs-lookup"><span data-stu-id="3b12f-245">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="3b12f-246">El tipo de parámetro de constructor del asistente de etiquetas cambia a <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> para los consumidores de .NET Core 2.1 y .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="3b12f-246">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="3b12f-247">Este cambio era necesario porque ASP.NET Core 3.0 marcó `IHostingEnvironment` como obsoleto y recomendó su sustitución por `IWebHostEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="3b12f-247">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="3b12f-248">El siguiente archivo de proyecto de destino múltiple admite este escenario del asistente de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="3b12f-248">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="3b12f-249">Uso de una API que se ha quitado del marco compartido</span><span class="sxs-lookup"><span data-stu-id="3b12f-249">Use an API removed from the shared framework</span></span>

<span data-ttu-id="3b12f-250">Para usar un ensamblado de ASP.NET Core que se haya quitado del marco de trabajo compartido, agregue la referencia de paquete adecuada.</span><span class="sxs-lookup"><span data-stu-id="3b12f-250">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="3b12f-251">Para obtener una lista de los paquetes que se han quitado del marco compartido en ASP.NET Core 3.0, consulte [Quitar referencias de paquetes obsoletas](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="3b12f-251">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="3b12f-252">Por ejemplo, para agregar el cliente de la API web:</span><span class="sxs-lookup"><span data-stu-id="3b12f-252">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="3b12f-253">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3b12f-253">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [<span data-ttu-id="3b12f-254">Compatibilidad con implementaciones de .NET</span><span class="sxs-lookup"><span data-stu-id="3b12f-254">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="3b12f-255">Directivas de compatibilidad de .NET</span><span class="sxs-lookup"><span data-stu-id="3b12f-255">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
