---
title: Migración de ASP.NET Core 1.x a 2.0
author: scottaddie
description: En este artículo se indican los requisitos previos y los pasos más comunes para migrar un proyecto de ASP.NET Core 1.x a ASP.NET Core 2.0.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: migration/1x-to-2x/index
ms.openlocfilehash: cad1cd868ef8c972d7e0fbfec79cd1d8b49c3b6e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015340"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a><span data-ttu-id="af2ac-103">Migración de ASP.NET Core 1.x a 2.0</span><span class="sxs-lookup"><span data-stu-id="af2ac-103">Migrate from ASP.NET Core 1.x to 2.0</span></span>

<span data-ttu-id="af2ac-104">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="af2ac-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="af2ac-105">En este artículo se le guía a lo largo del proceso de actualización de un proyecto existente de ASP.NET Core 1.x a ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="af2ac-105">In this article, we walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="af2ac-106">La migración de la aplicación a ASP.NET Core 2.0 permite aprovechar [muchas características nuevas y mejoras de rendimiento](xref:aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="af2ac-106">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](xref:aspnetcore-2.0).</span></span>

<span data-ttu-id="af2ac-107">Las aplicaciones existentes de ASP.NET Core 1.x se basan en plantillas de proyecto específicas de la versión.</span><span class="sxs-lookup"><span data-stu-id="af2ac-107">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="af2ac-108">A medida que el marco de trabajo de ASP.NET Core evoluciona, también lo hacen las plantillas de proyecto y el código de inicio incluido en ellas.</span><span class="sxs-lookup"><span data-stu-id="af2ac-108">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="af2ac-109">Además de actualizar el marco de trabajo de ASP.NET Core, debe actualizar el código de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af2ac-109">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="af2ac-110">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="af2ac-110">Prerequisites</span></span>

<span data-ttu-id="af2ac-111">Consulte [Introducción a ASP.NET Core](xref:getting-started).</span><span class="sxs-lookup"><span data-stu-id="af2ac-111">See [Get Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="af2ac-112">Actualización del moniker de la plataforma de destino (TFM)</span><span class="sxs-lookup"><span data-stu-id="af2ac-112">Update Target Framework Moniker (TFM)</span></span>

<span data-ttu-id="af2ac-113">Los proyectos para .NET Core deben usar el [TFM](/dotnet/standard/frameworks) de una versión mayor o igual que .NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="af2ac-113">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="af2ac-114">Busque el nodo `<TargetFramework>` del archivo *.csproj* y reemplace su texto interno por `netcoreapp2.0`:</span><span class="sxs-lookup"><span data-stu-id="af2ac-114">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

<span data-ttu-id="af2ac-115">Los proyectos para .NET Framework deben usar el TFM de una versión mayor o igual que .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="af2ac-115">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="af2ac-116">Busque el nodo `<TargetFramework>` del archivo *.csproj* y reemplace su texto interno por `net461`:</span><span class="sxs-lookup"><span data-stu-id="af2ac-116">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> <span data-ttu-id="af2ac-117">.NET Core 2.0 ofrece un área expuesta mucho mayor que .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="af2ac-117">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="af2ac-118">Si el destino es .NET Framework solo porque faltan API en .NET Core 1.x, el uso de .NET Core 2.0 como destino es probable que dé resultado.</span><span class="sxs-lookup"><span data-stu-id="af2ac-118">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<span data-ttu-id="af2ac-119">Si el archivo de proyecto contiene `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span><span class="sxs-lookup"><span data-stu-id="af2ac-119">If the project file contains `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="af2ac-120">Actualización de la versión del SDK de .NET Core en global.json</span><span class="sxs-lookup"><span data-stu-id="af2ac-120">Update .NET Core SDK version in global.json</span></span>

<span data-ttu-id="af2ac-121">Si la solución se basa en un archivo [global.json](/dotnet/core/tools/global-json) para que el destino sea una versión específica del SDK de .NET Core, actualice su propiedad `version` para usar la versión 2.0 instalada en el equipo:</span><span class="sxs-lookup"><span data-stu-id="af2ac-121">If your solution relies upon a [global.json](/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="af2ac-122">Actualización de las referencias del paquete</span><span class="sxs-lookup"><span data-stu-id="af2ac-122">Update package references</span></span>

<span data-ttu-id="af2ac-123">El archivo *.csproj* de un proyecto de 1.x enumera cada paquete NuGet usado por el proyecto.</span><span class="sxs-lookup"><span data-stu-id="af2ac-123">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="af2ac-124">En un proyecto de ASP.NET Core 2.0 para .NET Core 2.0, una sola referencia de [metapaquete](xref:fundamentals/metapackage) del archivo *.csproj* reemplaza a la colección de paquetes:</span><span class="sxs-lookup"><span data-stu-id="af2ac-124">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

<span data-ttu-id="af2ac-125">Todas las características de ASP.NET Core 2.0 y Entity Framework Core 2.0 están incluidas en el metapaquete.</span><span class="sxs-lookup"><span data-stu-id="af2ac-125">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="af2ac-126">Los proyectos de ASP.NET Core 2.0 para .NET Framework deben seguir haciendo referencia a paquetes NuGet individuales.</span><span class="sxs-lookup"><span data-stu-id="af2ac-126">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="af2ac-127">Actualización del atributo `Version` de cada nodo `<PackageReference />` a 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="af2ac-127">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="af2ac-128">Por ejemplo, esta es la lista de nodos `<PackageReference />` usados en un proyecto típico de ASP.NET Core 2.0 para .NET Framework:</span><span class="sxs-lookup"><span data-stu-id="af2ac-128">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="af2ac-129">Actualización de las herramientas de la CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="af2ac-129">Update .NET Core CLI tools</span></span>

<span data-ttu-id="af2ac-130">En el archivo *.csproj*, actualice el atributo `Version` de cada nodo `<DotNetCliToolReference />` a 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="af2ac-130">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="af2ac-131">Por ejemplo, esta es la lista de herramientas de la CLI usadas en un proyecto típico de ASP.NET Core 2.0 para .NET Core 2.0:</span><span class="sxs-lookup"><span data-stu-id="af2ac-131">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="af2ac-132">Cambio de nombre de la propiedad Package Target Fallback</span><span class="sxs-lookup"><span data-stu-id="af2ac-132">Rename Package Target Fallback property</span></span>

<span data-ttu-id="af2ac-133">El archivo *.csproj* de un proyecto de 1.x usa un nodo `PackageTargetFallback` y una variable:</span><span class="sxs-lookup"><span data-stu-id="af2ac-133">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

<span data-ttu-id="af2ac-134">Cambie el nombre del nodo y la variable a `AssetTargetFallback`:</span><span class="sxs-lookup"><span data-stu-id="af2ac-134">Rename both the node and variable to `AssetTargetFallback`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="af2ac-135">Actualización del método Main de Program.cs</span><span class="sxs-lookup"><span data-stu-id="af2ac-135">Update Main method in Program.cs</span></span>

<span data-ttu-id="af2ac-136">En los proyectos de 1.x, el método `Main` de *Program.cs* tenía este aspecto:</span><span class="sxs-lookup"><span data-stu-id="af2ac-136">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

<span data-ttu-id="af2ac-137">En los proyectos de 2.0, el método `Main` de *Program.cs* se ha simplificado:</span><span class="sxs-lookup"><span data-stu-id="af2ac-137">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

<span data-ttu-id="af2ac-138">La adopción de este nuevo patrón 2.0 es muy recomendable y necesaria para que funcionen características de producto como las [migraciones de Entity Framework (EF) Core](xref:data/ef-mvc/migrations).</span><span class="sxs-lookup"><span data-stu-id="af2ac-138">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="af2ac-139">Por ejemplo, la ejecución de `Update-Database` desde la ventana Consola del Administrador de paquetes o de `dotnet ef database update` desde la línea de comandos (en proyectos convertidos a ASP.NET Core 2.0) genera el error siguiente:</span><span class="sxs-lookup"><span data-stu-id="af2ac-139">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a><span data-ttu-id="af2ac-140">Incorporación de proveedores de configuración</span><span class="sxs-lookup"><span data-stu-id="af2ac-140">Add configuration providers</span></span>

<span data-ttu-id="af2ac-141">En los proyectos de 1.x, la incorporación de proveedores de configuración a una aplicación se lograba a través del constructor `Startup`.</span><span class="sxs-lookup"><span data-stu-id="af2ac-141">In 1.x projects, adding configuration providers to an app was accomplished via the `Startup` constructor.</span></span> <span data-ttu-id="af2ac-142">Para ello, era necesario crear una instancia de `ConfigurationBuilder`, cargar los proveedores aplicables (variables de entorno, configuración de la aplicación, etc.) e inicializar un miembro de `IConfigurationRoot`.</span><span class="sxs-lookup"><span data-stu-id="af2ac-142">The steps involved creating an instance of `ConfigurationBuilder`, loading applicable providers (environment variables, app settings, etc.), and initializing a member of `IConfigurationRoot`.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

<span data-ttu-id="af2ac-143">En el ejemplo anterior se carga el miembro `Configuration` con opciones de configuración de *appsettings.json*, así como cualquier archivo *appsettings.\<EnvironmentName\>.json* que coincida con la propiedad `IHostingEnvironment.EnvironmentName`.</span><span class="sxs-lookup"><span data-stu-id="af2ac-143">The preceding example loads the `Configuration` member with configuration settings from *appsettings.json* as well as any *appsettings.\<EnvironmentName\>.json* file matching the `IHostingEnvironment.EnvironmentName` property.</span></span> <span data-ttu-id="af2ac-144">La ubicación de estos archivos está en la misma ruta de acceso que *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="af2ac-144">The location of these files is at the same path as *Startup.cs*.</span></span>

<span data-ttu-id="af2ac-145">En los proyectos de 2.0, el código de configuración reutilizable inherente a los proyectos de 1.x se ejecuta en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="af2ac-145">In 2.0 projects, the boilerplate configuration code inherent to 1.x projects runs behind-the-scenes.</span></span> <span data-ttu-id="af2ac-146">Por ejemplo, las variables de entorno y la configuración de la aplicación se cargan durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="af2ac-146">For example, environment variables and app settings are loaded at startup.</span></span> <span data-ttu-id="af2ac-147">El código de *Startup.cs* equivalente se reduce a la inicialización de `IConfiguration` con la instancia insertada:</span><span class="sxs-lookup"><span data-stu-id="af2ac-147">The equivalent *Startup.cs* code is reduced to `IConfiguration` initialization with the injected instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

<span data-ttu-id="af2ac-148">Para quitar los proveedores predeterminados que `WebHostBuilder.CreateDefaultBuilder` agrega, invoque el método `Clear` en la propiedad `IConfigurationBuilder.Sources` dentro de `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="af2ac-148">To remove the default providers added by `WebHostBuilder.CreateDefaultBuilder`, invoke the `Clear` method on the `IConfigurationBuilder.Sources` property inside of `ConfigureAppConfiguration`.</span></span> <span data-ttu-id="af2ac-149">Para volver a agregar proveedores, use el método `ConfigureAppConfiguration` en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="af2ac-149">To add providers back, utilize the `ConfigureAppConfiguration` method in *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

<span data-ttu-id="af2ac-150">La configuración que el método `CreateDefaultBuilder` utiliza en el fragmento de código anterior puede verse [aquí](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="af2ac-150">The configuration used by the `CreateDefaultBuilder` method in the preceding code snippet can be seen [here](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span></span>

<span data-ttu-id="af2ac-151">Para más información, consulte [Configuración en ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="af2ac-151">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a><span data-ttu-id="af2ac-152">Mover el código de inicialización de la base de datos</span><span class="sxs-lookup"><span data-stu-id="af2ac-152">Move database initialization code</span></span>

<span data-ttu-id="af2ac-153">En proyectos de 1.x que usen EF Core 1.x, un comando como `dotnet ef migrations add` hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="af2ac-153">In 1.x projects using EF Core 1.x, a command such as `dotnet ef migrations add` does the following:</span></span>

1. <span data-ttu-id="af2ac-154">Crea una instancia de `Startup`.</span><span class="sxs-lookup"><span data-stu-id="af2ac-154">Instantiates a `Startup` instance</span></span>
1. <span data-ttu-id="af2ac-155">Invoca el método `ConfigureServices` para registrar todos los servicios de la inserción de dependencias (como los tipos `DbContext`).</span><span class="sxs-lookup"><span data-stu-id="af2ac-155">Invokes the `ConfigureServices` method to register all services with dependency injection (including `DbContext` types)</span></span>
1. <span data-ttu-id="af2ac-156">Realiza las tareas necesarias.</span><span class="sxs-lookup"><span data-stu-id="af2ac-156">Performs its requisite tasks</span></span>

<span data-ttu-id="af2ac-157">En proyectos 2.0 que usen EF Core 2.0, se invoca `Program.BuildWebHost` para obtener los servicios de aplicación.</span><span class="sxs-lookup"><span data-stu-id="af2ac-157">In 2.0 projects using EF Core 2.0, `Program.BuildWebHost` is invoked to obtain the application services.</span></span> <span data-ttu-id="af2ac-158">A diferencia de 1.x, se invoca `Startup.Configure` como efecto secundario adicional.</span><span class="sxs-lookup"><span data-stu-id="af2ac-158">Unlike 1.x, this has the additional side effect of invoking `Startup.Configure`.</span></span> <span data-ttu-id="af2ac-159">Si la aplicación 1.x ha invocado el código de inicialización de la aplicación en el método `Configure`, pueden producirse errores inesperados.</span><span class="sxs-lookup"><span data-stu-id="af2ac-159">If your 1.x app invoked database initialization code in its `Configure` method, unexpected problems can occur.</span></span> <span data-ttu-id="af2ac-160">Por ejemplo, si todavía no existe la base de datos, el código de propagación se ejecuta antes que el comando EF Core Migrations.</span><span class="sxs-lookup"><span data-stu-id="af2ac-160">For example, if the database doesn't yet exist, the seeding code runs before the EF Core Migrations command execution.</span></span> <span data-ttu-id="af2ac-161">Este problema provocará un error en un comando `dotnet ef migrations list` si la base de datos no existe.</span><span class="sxs-lookup"><span data-stu-id="af2ac-161">This problem causes a `dotnet ef migrations list` command to fail if the database doesn't yet exist.</span></span>

<span data-ttu-id="af2ac-162">Tenga en cuenta el código de propagación 1.x siguiente en el método `Configure` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="af2ac-162">Consider the following 1.x seed initialization code in the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

<span data-ttu-id="af2ac-163">En los proyectos 2.0, mueva la llamada `SeedData.Initialize` al método `Main` de *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="af2ac-163">In 2.0 projects, move the `SeedData.Initialize` call to the `Main` method of *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

<span data-ttu-id="af2ac-164">A partir de 2.0 se desaconseja cualquier acción en `BuildWebHost`, excepto la compilación y la configuración del host web.</span><span class="sxs-lookup"><span data-stu-id="af2ac-164">As of 2.0, it's bad practice to do anything in `BuildWebHost` except build and configure the web host.</span></span> <span data-ttu-id="af2ac-165">Todo lo relacionado con la ejecución de la aplicación deberá gestionarse fuera de `BuildWebHost` &mdash;, normalmente en el método `Main` de *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="af2ac-165">Anything that's about running the application should be handled outside of `BuildWebHost` &mdash; typically in the `Main` method of *Program.cs*.</span></span>

<a name="view-compilation"></a>

## <a name="review-no-locrazor-view-compilation-setting"></a><span data-ttu-id="af2ac-166">Revisión de la configuración de compilación de la vista de Razor</span><span class="sxs-lookup"><span data-stu-id="af2ac-166">Review Razor view compilation setting</span></span>

<span data-ttu-id="af2ac-167">Un tiempo de inicio de aplicación más rápido y unos lotes publicados más pequeños son de la máxima importancia para el usuario.</span><span class="sxs-lookup"><span data-stu-id="af2ac-167">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="af2ac-168">Por ello, la [compilación de la vista de Razor](xref:mvc/views/view-compilation) está habilitada de forma predeterminada en ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="af2ac-168">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="af2ac-169">Ya no es necesario establecer la propiedad `MvcRazorCompileOnPublish` en true.</span><span class="sxs-lookup"><span data-stu-id="af2ac-169">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="af2ac-170">A menos que se esté deshabilitando la compilación de la vista, se puede quitar la propiedad del archivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="af2ac-170">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="af2ac-171">Si el destino es .NET Framework, se debe hacer referencia de forma explícita al paquete NuGet [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) en el archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="af2ac-171">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="af2ac-172">Características "light-up" de Application Insights como base</span><span class="sxs-lookup"><span data-stu-id="af2ac-172">Rely on Application Insights "light-up" features</span></span>

<span data-ttu-id="af2ac-173">Es importante configurar sin esfuerzo la instrumentación de rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af2ac-173">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="af2ac-174">Ahora puede basarse en las nuevas características "light-up" de [Application Insights](/azure/application-insights/app-insights-overview) disponibles en las herramientas de Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="af2ac-174">You can now rely on the new [Application Insights](/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="af2ac-175">Los proyectos de ASP.NET Core 1.1 creados en Visual Studio 2017 han agregado Application Insights de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="af2ac-175">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="af2ac-176">Si no usa el SDK de Application Insights directamente, fuera de *Program.cs* y *Startup.cs*, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="af2ac-176">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs*, follow these steps:</span></span>

1. <span data-ttu-id="af2ac-177">Si el destino es .NET Core, elimine el siguiente nodo `<PackageReference />` del archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="af2ac-177">If targeting .NET Core, remove the following `<PackageReference />` node from the *.csproj* file:</span></span>

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. <span data-ttu-id="af2ac-178">Si el destino es .NET Core, elimine la invocación del método de extensión `UseApplicationInsights` de *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="af2ac-178">If targeting .NET Core, remove the `UseApplicationInsights` extension method invocation from *Program.cs*:</span></span>

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. <span data-ttu-id="af2ac-179">Quite la llamada API del lado cliente de Application Insights de *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="af2ac-179">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="af2ac-180">Comprende las dos líneas de código siguientes:</span><span class="sxs-lookup"><span data-stu-id="af2ac-180">It comprises the following two lines of code:</span></span>

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

<span data-ttu-id="af2ac-181">Si usa el SDK de Application Insights directamente, siga haciéndolo.</span><span class="sxs-lookup"><span data-stu-id="af2ac-181">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="af2ac-182">El [metapaquete](xref:fundamentals/metapackage) 2.0 incluye la versión más reciente de Application Insights, por lo que si se hace referencia a una versión anterior, aparece un error de degradación de paquete.</span><span class="sxs-lookup"><span data-stu-id="af2ac-182">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationno-locidentity-improvements"></a><span data-ttu-id="af2ac-183">Mejoras de Identity y adopción de la autenticación</span><span class="sxs-lookup"><span data-stu-id="af2ac-183">Adopt authentication/Identity improvements</span></span>

<span data-ttu-id="af2ac-184">ASP.NET Core 2.0 tiene un nuevo modelo de autenticación y presenta una serie de cambios significativos en ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="af2ac-184">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="af2ac-185">Si ha creado el proyecto con la opción Cuentas de usuario individuales habilitada, o bien si ha agregado manualmente la autenticación o Identity, vea [Migración de la autenticación e Identity a ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span><span class="sxs-lookup"><span data-stu-id="af2ac-185">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrate Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af2ac-186">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="af2ac-186">Additional resources</span></span>

* [<span data-ttu-id="af2ac-187">Cambios importantes en ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="af2ac-187">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
