---
title: Metapaquete Microsoft.AspNetCore.All para ASP.NET Core 2.0
author: Rick-Anderson
description: El metapaquete Microsoft.AspNetCore.All no se recomienda para ASP.NET Core 2.1 y versiones posteriores.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
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
uid: fundamentals/metapackage
ms.openlocfilehash: 133eee71fcd85a95ac53684c3c6e475a54592967
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634571"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="22bb4-103">Metapaquete Microsoft.AspNetCore.All para ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="22bb4-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="22bb4-104">El metapaquete `Microsoft.AspNetCore.All` no se incluye en ASP.NET Core 3.0 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="22bb4-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="22bb4-105">Para más información, consulte [este problema de GitHub](https://github.com/aspnet/Announcements/issues/314).</span><span class="sxs-lookup"><span data-stu-id="22bb4-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="22bb4-106">Se recomienda que las aplicaciones que tengan como destino ASP.NET Core 2.1 y versiones posteriores usen el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) en lugar de este paquete.</span><span class="sxs-lookup"><span data-stu-id="22bb4-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="22bb4-107">Consulte [Migración desde Microsoft.AspNetCore.All a Microsoft.AspNetCore.App](#migrate) en este artículo.</span><span class="sxs-lookup"><span data-stu-id="22bb4-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="22bb4-108">Esta característica requiere ASP.NET Core 2.x con .NET Core 2.x como destino.</span><span class="sxs-lookup"><span data-stu-id="22bb4-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="22bb4-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) es un metapaquete que hace referencia a un marco compartido.</span><span class="sxs-lookup"><span data-stu-id="22bb4-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="22bb4-110">Un *marco compartido* es un conjunto de ensamblados (archivos *.dll*) que no están en las carpetas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="22bb4-110">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="22bb4-111">El marco de trabajo compartido debe instalarse en la máquina para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="22bb4-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="22bb4-112">Para más información, consulte este artículo sobre el [marco de trabajo compartido](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="22bb4-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="22bb4-113">El marco de trabajo compartido al que hace referencia `Microsoft.AspNetCore.All` incluye:</span><span class="sxs-lookup"><span data-stu-id="22bb4-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="22bb4-114">Todos los paquetes admitidos por el equipo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="22bb4-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="22bb4-115">Todos los paquetes admitidos por Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="22bb4-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="22bb4-116">Dependencias internas y de terceros usadas por ASP.NET Core y Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="22bb4-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="22bb4-117">Todas las características de ASP.NET Core 2.x y Entity Framework Core 2.x están incluidas en el paquete `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="22bb4-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="22bb4-118">Las plantillas de proyecto predeterminada destinadas a ASP.NET 2.0 usan este paquete.</span><span class="sxs-lookup"><span data-stu-id="22bb4-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="22bb4-119">El número de versión del metapaquete `Microsoft.AspNetCore.All` representa las versiones mínimas de ASP.NET Core y Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="22bb4-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="22bb4-120">El siguiente archivo *.csproj* hace referencia al metapaquete `Microsoft.AspNetCore.All` de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="22bb4-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="22bb4-121">Control de versiones implícitas</span><span class="sxs-lookup"><span data-stu-id="22bb4-121">Implicit versioning</span></span>

<span data-ttu-id="22bb4-122">En ASP.NET Core 2.1 o una versión posterior, puede especificar la referencia de paquete `Microsoft.AspNetCore.All` sin una versión.</span><span class="sxs-lookup"><span data-stu-id="22bb4-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="22bb4-123">Si no se especifica la versión, el SDK define una versión implícita (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="22bb4-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="22bb4-124">Es recomendable confiar en la versión implícita especificada por el SDK y no establecer de forma explícita el número de versión en la referencia del paquete.</span><span class="sxs-lookup"><span data-stu-id="22bb4-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="22bb4-125">Si tiene alguna pregunta sobre este enfoque, deje un comentario de GitHub en [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430) (Debate sobre la versión implícita de Microsoft.AspNetCore.App).</span><span class="sxs-lookup"><span data-stu-id="22bb4-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="22bb4-126">La versión implícita se establece en `major.minor.0` para las aplicaciones portátiles.</span><span class="sxs-lookup"><span data-stu-id="22bb4-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="22bb4-127">El mecanismo de puesta al día del marco de uso compartido ejecuta la aplicación en la versión más reciente compatible entre los marcos de uso compartidos instalados.</span><span class="sxs-lookup"><span data-stu-id="22bb4-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="22bb4-128">Para garantizar que se use la misma versión en el desarrollo, las pruebas y la producción, asegúrese de que en todos los entornos esté instalada la misma versión del marco de uso compartido.</span><span class="sxs-lookup"><span data-stu-id="22bb4-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="22bb4-129">Para las aplicaciones autocontenidas, el número de versión implícita se establece en el valor `major.minor.patch` del marco de uso compartido incluido en el SDK instalado.</span><span class="sxs-lookup"><span data-stu-id="22bb4-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="22bb4-130">Especificar un número de versión en la referencia de paquete `Microsoft.AspNetCore.All`**no** garantiza que se seleccione la versión del marco de uso compartido.</span><span class="sxs-lookup"><span data-stu-id="22bb4-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="22bb4-131">Por ejemplo, suponga que se especifica la versión "2.1.1", pero está instalada la "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="22bb4-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="22bb4-132">En ese caso, la aplicación usará el valor "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="22bb4-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="22bb4-133">Aunque no se recomienda, puede deshabilitar la puesta al día (revisión o secundaria).</span><span class="sxs-lookup"><span data-stu-id="22bb4-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="22bb4-134">Para obtener más información sobre la puesta al día del host de dotnet y cómo configurar su comportamiento, vea [Dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md) (Puesta al día del host de dotnet).</span><span class="sxs-lookup"><span data-stu-id="22bb4-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="22bb4-135">El SDK del proyecto se debe establecer en `Microsoft.NET.Sdk.Web` en el archivo de proyecto para usar la versión implícita de `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="22bb4-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="22bb4-136">Cuando se especifica el SDK `Microsoft.NET.Sdk` (`<Project Sdk="Microsoft.NET.Sdk">` en la parte superior del archivo del proyecto), se genera la advertencia siguiente:</span><span class="sxs-lookup"><span data-stu-id="22bb4-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="22bb4-137">*Advertencia NU1604: La dependencia del proyecto Microsoft.AspNetCore.All no contiene un límite inferior inclusivo. Incluya un límite inferior en la versión de dependencia para garantizar que los resultados de restauración son coherentes.*</span><span class="sxs-lookup"><span data-stu-id="22bb4-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="22bb4-138">Este es un problema conocido con el SDK de .NET Core 2.1 y se corregirá en el SDK de .NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="22bb4-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="22bb4-139">Migración desde Microsoft.AspNetCore.All a Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="22bb4-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="22bb4-140">En `Microsoft.AspNetCore.All` se incluyen los siguientes paquetes, pero no el paquete `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="22bb4-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

<span data-ttu-id="22bb4-141">Para pasar de `Microsoft.AspNetCore.All` a `Microsoft.AspNetCore.App`, si su aplicación usa las API de los paquetes anteriores, o bien paquetes incluidos en ellos, agregue las referencias correspondientes a dichos paquetes en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="22bb4-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="22bb4-142">No se incluye implícitamente ninguna dependencia de los paquetes anteriores que no sea, de otro modo, una dependencia de `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="22bb4-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="22bb4-143">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="22bb4-143">For example:</span></span>

* <span data-ttu-id="22bb4-144">`StackExchange.Redis` como dependencia de `Microsoft.Extensions.Caching.Redis`</span><span class="sxs-lookup"><span data-stu-id="22bb4-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="22bb4-145">`Microsoft.ApplicationInsights` como dependencia de `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span><span class="sxs-lookup"><span data-stu-id="22bb4-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="22bb4-146">Actualización de ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="22bb4-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="22bb4-147">Se recomienda migrar al metapaquete `Microsoft.AspNetCore.App` con la versión 2.1 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="22bb4-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="22bb4-148">Para seguir usando el metapaquete `Microsoft.AspNetCore.All` y asegurarse de que se ha implementado la última versión de la revisión:</span><span class="sxs-lookup"><span data-stu-id="22bb4-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="22bb4-149">En las máquinas de desarrollo y los servidores de compilación: Instale la versión más reciente del [SDK de .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="22bb4-149">On development machines and build servers: Install the latest [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="22bb4-150">En los servidores de implementación: Instale el [entorno de ejecución de .NET Core](https://dotnet.microsoft.com/download) más reciente.</span><span class="sxs-lookup"><span data-stu-id="22bb4-150">On deployment servers: Install the latest [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>
 <span data-ttu-id="22bb4-151">Su aplicación se pondrá al día con la última versión instalada al reiniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="22bb4-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
