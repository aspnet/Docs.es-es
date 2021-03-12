---
title: Metapaquete Microsoft.AspNetCore.App para ASP.NET Core
author: Rick-Anderson
description: El marco compartido de Microsoft.AspNetCore.App
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
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
uid: fundamentals/metapackage-app
ms.openlocfilehash: 5d9d9cd446a61cc3e573712a4626af04dc284e99
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589587"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="1bd82-103">Microsoft.AspNetCore.App para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bd82-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="1bd82-104">El marco compartido de ASP.NET Core (`Microsoft.AspNetCore.App`) contiene ensamblados desarrollados y admitidos por Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1bd82-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="1bd82-105">`Microsoft.AspNetCore.App` se instala cuando se instala el [SDK de .NET Core 3.0 o posterior](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span><span class="sxs-lookup"><span data-stu-id="1bd82-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="1bd82-106">El *marco compartido* es el conjunto de ensamblados (archivos *.dll*) que se instalan en la máquina e incluye un componente de entorno de ejecución y un paquete de destino.</span><span class="sxs-lookup"><span data-stu-id="1bd82-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="1bd82-107">Para más información, consulte este artículo sobre el [marco de trabajo compartido](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="1bd82-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="1bd82-108">Los proyectos que tienen como destino el SDK de `Microsoft.NET.Sdk.Web` hacen referencia implícitamente al marco `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="1bd82-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="1bd82-109">No se requieren referencias adicionales para estos proyectos:</span><span class="sxs-lookup"><span data-stu-id="1bd82-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="1bd82-110">El marco compartido de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1bd82-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="1bd82-111">No incluye dependencias de terceros.</span><span class="sxs-lookup"><span data-stu-id="1bd82-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="1bd82-112">Incluye todos los paquetes admitidos por el equipo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bd82-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1bd82-113">Esta característica requiere ASP.NET Core 2.x con .NET Core 2.x como destino.</span><span class="sxs-lookup"><span data-stu-id="1bd82-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="1bd82-114">El [metapaquete](/dotnet/core/packages#metapackages) [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) para ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1bd82-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="1bd82-115">No incluye dependencias de terceros excepto [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) e [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span><span class="sxs-lookup"><span data-stu-id="1bd82-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="1bd82-116">Estas dependencias de terceros se consideran necesarias para garantizar el funcionamiento de las características de los principales marcos.</span><span class="sxs-lookup"><span data-stu-id="1bd82-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="1bd82-117">Incluye todos los paquetes admitidos por el equipo de ASP.NET Core, excepto aquellos que contienen dependencias de terceros (distintos de los mencionados anteriormente).</span><span class="sxs-lookup"><span data-stu-id="1bd82-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="1bd82-118">Incluye todos los paquetes admitidos por el equipo de Entity Framework Core, excepto aquellos que contienen dependencias de terceros (distintos de los mencionados anteriormente).</span><span class="sxs-lookup"><span data-stu-id="1bd82-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="1bd82-119">Todas las características de ASP.NET Core 2.x y Entity Framework Core 2.x están incluidas en el paquete `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="1bd82-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="1bd82-120">Las plantillas de proyecto predeterminadas que tienen ASP.NET 2 como destino usan este paquete.</span><span class="sxs-lookup"><span data-stu-id="1bd82-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="1bd82-121">Se recomienda que las aplicaciones que tengan como destino ASP.NET Core 2.x y Entity Framework Core 2.x usen el paquete `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="1bd82-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="1bd82-122">El número de versión del metapaquete `Microsoft.AspNetCore.App` representa las versiones mínimas de ASP.NET Core y Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1bd82-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="1bd82-123">Mediante el metapaquete `Microsoft.AspNetCore.App` se proporcionan restricciones de versión que protegen la aplicación:</span><span class="sxs-lookup"><span data-stu-id="1bd82-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="1bd82-124">Si se incluye un paquete que tiene una dependencia transitiva (no directa) en un paquete en `Microsoft.AspNetCore.App` y los números de versión son distintos, NuGet generará un error.</span><span class="sxs-lookup"><span data-stu-id="1bd82-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="1bd82-125">Los demás paquetes agregados a la aplicación no pueden cambiar la versión de los paquetes que se incluyen en `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="1bd82-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="1bd82-126">La coherencia de versiones garantiza una experiencia fiable.</span><span class="sxs-lookup"><span data-stu-id="1bd82-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="1bd82-127">`Microsoft.AspNetCore.App` se ha diseñado para evitar las combinaciones de versiones no probadas de bits relacionados que se usan conjuntamente en la misma aplicación.</span><span class="sxs-lookup"><span data-stu-id="1bd82-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="1bd82-128">Las aplicaciones que usan el metapaquete `Microsoft.AspNetCore.App` pueden aprovechar automáticamente el marco de uso compartido de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1bd82-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="1bd82-129">Al usar el metapaquete `Microsoft.AspNetCore.App`, ningún&mdash; recurso de los paquetes NuGet de ASP.NET Core a los que se hace referencia se implementa con la aplicación: el marco compartido de ASP.NET Core contiene esos recursos.</span><span class="sxs-lookup"><span data-stu-id="1bd82-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="1bd82-130">Los recursos del marco de uso compartido se precompilan para mejorar el tiempo de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1bd82-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="1bd82-131">Para más información, consulte este artículo sobre el [marco de trabajo compartido](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="1bd82-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="1bd82-132">El archivo de proyecto siguiente hace referencia al metapaquete `Microsoft.AspNetCore.App` de ASP.NET Core y representa una típica plantilla de ASP.NET Core 2.2:</span><span class="sxs-lookup"><span data-stu-id="1bd82-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="1bd82-133">El marcado anterior representa una plantilla típica de ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="1bd82-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="1bd82-134">No especifica ningún número de versión para la referencia del paquete `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="1bd82-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="1bd82-135">Si no se especifica la versión, el SDK define una versión [implícita](https://github.com/dotnet/core/blob/main/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md), es decir, `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="1bd82-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/main/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="1bd82-136">Es recomendable confiar en la versión implícita especificada por el SDK y no establecer de forma explícita el número de versión en la referencia del paquete.</span><span class="sxs-lookup"><span data-stu-id="1bd82-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="1bd82-137">Si tiene alguna pregunta sobre este enfoque, deje un comentario de GitHub en [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430) (Debate sobre la versión implícita Microsoft.AspNetCore.App).</span><span class="sxs-lookup"><span data-stu-id="1bd82-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="1bd82-138">La versión implícita se establece en `major.minor.0` para las aplicaciones portátiles.</span><span class="sxs-lookup"><span data-stu-id="1bd82-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="1bd82-139">El mecanismo de puesta al día del marco de uso compartido ejecutará la aplicación en la versión más reciente compatible entre los marcos de uso compartidos instalados.</span><span class="sxs-lookup"><span data-stu-id="1bd82-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="1bd82-140">Para garantizar que se use la misma versión en el desarrollo, las pruebas y la producción, asegúrese de que en todos los entornos esté instalada la misma versión del marco de uso compartido.</span><span class="sxs-lookup"><span data-stu-id="1bd82-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="1bd82-141">Para las aplicaciones autocontenidas, el número de versión implícita se establece en el valor `major.minor.patch` del marco de uso compartido incluido en el SDK instalado.</span><span class="sxs-lookup"><span data-stu-id="1bd82-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="1bd82-142">El hecho de especificar un número de versión en la referencia de `Microsoft.AspNetCore.App`**no** garantiza que se vaya a elegir la versión del marco de uso compartido.</span><span class="sxs-lookup"><span data-stu-id="1bd82-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="1bd82-143">Por ejemplo, suponga que se especifica la versión "2.2.1", pero está instalada la "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="1bd82-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="1bd82-144">En ese caso, la aplicación usará el valor "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="1bd82-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="1bd82-145">Aunque no se recomienda, puede deshabilitar la puesta al día (revisión o secundaria).</span><span class="sxs-lookup"><span data-stu-id="1bd82-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="1bd82-146">Para obtener más información sobre la puesta al día del host de dotnet y cómo configurar su comportamiento, vea [Dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md) (Puesta al día del host de dotnet).</span><span class="sxs-lookup"><span data-stu-id="1bd82-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="1bd82-147">`<Project Sdk` debe establecerse en `Microsoft.NET.Sdk.Web` para usar la versión implícita `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="1bd82-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="1bd82-148">Cuando `<Project Sdk="Microsoft.NET.Sdk">` (sin la terminación `.Web`) se usa:</span><span class="sxs-lookup"><span data-stu-id="1bd82-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="1bd82-149">Se genera la siguiente advertencia:</span><span class="sxs-lookup"><span data-stu-id="1bd82-149">The following warning is generated:</span></span>

  <span data-ttu-id="1bd82-150">*Advertencia NU1604: La dependencia de proyecto Microsoft.AspNetCore.App no contiene un límite inferior inclusivo. Incluya un límite inferior en la versión de dependencia para garantizar que los resultados de restauración son coherentes.*</span><span class="sxs-lookup"><span data-stu-id="1bd82-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="1bd82-151">Se trata de un problema conocido en el SDK de .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="1bd82-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="1bd82-152">Actualización de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bd82-152">Update ASP.NET Core</span></span>

<span data-ttu-id="1bd82-153">El  [metapaquete](/dotnet/core/packages#metapackages)`Microsoft.AspNetCore.App` no es un paquete habitual que se actualice desde NuGet.</span><span class="sxs-lookup"><span data-stu-id="1bd82-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="1bd82-154">De forma similar a `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` representa un tiempo de ejecución compartido, con una semántica especial de control de versiones controlada de forma ajena a NuGet.</span><span class="sxs-lookup"><span data-stu-id="1bd82-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="1bd82-155">Para obtener más información, vea [Paquetes, metapaquetes y marcos de trabajo](/dotnet/core/packages).</span><span class="sxs-lookup"><span data-stu-id="1bd82-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="1bd82-156">Para actualizar ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1bd82-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="1bd82-157">En los equipos de desarrollo y los servidores de compilación: descargue e instale el [SDK de .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="1bd82-157">On development machines and build servers: Download and install the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="1bd82-158">En los servidores de implementación: descargue e instale el [.NET Core Runtime](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="1bd82-158">On deployment servers: Download and install the [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>

 <span data-ttu-id="1bd82-159">Las aplicaciones se pondrán al día con la última versión instalada al reiniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1bd82-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="1bd82-160">No es necesario actualizar el número de versión de `Microsoft.AspNetCore.App` en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="1bd82-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="1bd82-161">Para obtener más información, vea [Puesta al día de las aplicaciones dependientes de la plataforma](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span><span class="sxs-lookup"><span data-stu-id="1bd82-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="1bd82-162">Si la aplicación ha usado `Microsoft.AspNetCore.All` anteriormente, consulte [Migración desde Microsoft.AspNetCore.All a Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span><span class="sxs-lookup"><span data-stu-id="1bd82-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
