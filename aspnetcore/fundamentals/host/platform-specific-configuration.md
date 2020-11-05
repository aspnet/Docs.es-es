---
title: Uso de ensamblados de inicio de hospedaje en ASP.NET Core
author: rick-anderson
description: Sepa cómo mejorar una aplicación ASP.NET Core desde un ensamblado externo con una implementación de IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: c12487875db69472ee328dfc7a611ee99974c770
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061059"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="ce83f-103">Uso de ensamblados de inicio de hospedaje en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce83f-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="ce83f-104">Por [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="ce83f-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ce83f-105">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (inicio de hospedaje) agrega mejoras a una aplicación al iniciarla a partir de un ensamblado externo.</span><span class="sxs-lookup"><span data-stu-id="ce83f-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="ce83f-106">Por ejemplo, una biblioteca externa puede usar una implementación de inicio de hospedaje para suministrar más servicios o proveedores de configuración a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="ce83f-107">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ce83f-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="ce83f-108">Atributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="ce83f-108">HostingStartup attribute</span></span>

<span data-ttu-id="ce83f-109">Un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica la presencia de un ensamblado de inicio de hospedaje para activar en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ce83f-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="ce83f-110">El ensamblado de entrada o el ensamblado que contiene la clase `Startup` se analiza automáticamente para detectar el atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ce83f-111">La lista de ensamblados para buscar atributos `HostingStartup` se carga en tiempo de ejecución desde la configuración de [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="ce83f-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="ce83f-112">La lista de ensamblados que se excluirán de la detección se carga desde [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="ce83f-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="ce83f-113">En el ejemplo siguiente, el espacio de nombres del ensamblado de inicio de hospedaje es `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="ce83f-114">La clase que contiene el código de inicio de hospedaje es `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="ce83f-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ce83f-115">Normalmente el atributo `HostingStartup` se encuentra en el archivo de clase de implementación `IHostingStartup` del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="ce83f-116">Detectar los ensamblados de inicio de hospedaje cargados</span><span class="sxs-lookup"><span data-stu-id="ce83f-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="ce83f-117">Para detectar los ensamblados de inicio de hospedaje cargados, habilite el registro y consulte los registros de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="ce83f-118">En ellos se registran los errores que se producen al cargar ensamblados.</span><span class="sxs-lookup"><span data-stu-id="ce83f-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="ce83f-119">Los ensamblados de inicio de hospedaje se registran en el nivel de depuración y se registran todos los errores.</span><span class="sxs-lookup"><span data-stu-id="ce83f-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="ce83f-120">Deshabilitar la carga automática de ensamblados de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="ce83f-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="ce83f-121">Para deshabilitar la carga automática de los ensamblados de inicio de hospedaje, use uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="ce83f-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="ce83f-122">Para evitar que se carguen todos los ensamblados de inicio de hospedaje, establezca uno de los procedimientos siguientes en `true` o `1`:</span><span class="sxs-lookup"><span data-stu-id="ce83f-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="ce83f-123">La opción de configuración de hospedaje para evitar el inicio de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="ce83f-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="ce83f-124">La variable de entorno `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="ce83f-125">Para evitar la carga de ensamblados de inicio de hospedaje específicos, establezca una de las opciones siguientes en una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir durante el inicio:</span><span class="sxs-lookup"><span data-stu-id="ce83f-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="ce83f-126">La opción de configuración de host para ensamblados de exclusión de inicio de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="ce83f-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="ce83f-127">La variable de entorno `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="ce83f-128">Si se establecen la opción de configuración de host y la variable de entorno, la configuración de host controla el comportamiento.</span><span class="sxs-lookup"><span data-stu-id="ce83f-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="ce83f-129">Deshabilitar los ensamblados de inicio de hospedaje a través de la variable de entorno o de la configuración de host hace que el ensamblado se deshabilite globalmente y, asimismo, puede deshabilitar también otras características de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="ce83f-130">Proyecto</span><span class="sxs-lookup"><span data-stu-id="ce83f-130">Project</span></span>

<span data-ttu-id="ce83f-131">Cree un inicio de hospedaje con cualquiera de los siguientes tipos de proyecto:</span><span class="sxs-lookup"><span data-stu-id="ce83f-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="ce83f-132">Biblioteca de clases</span><span class="sxs-lookup"><span data-stu-id="ce83f-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="ce83f-133">Aplicación de consola sin un punto de entrada</span><span class="sxs-lookup"><span data-stu-id="ce83f-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="ce83f-134">Biblioteca de clases</span><span class="sxs-lookup"><span data-stu-id="ce83f-134">Class library</span></span>

<span data-ttu-id="ce83f-135">Una mejora de inicio de hospedaje se puede proporcionar en una biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="ce83f-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="ce83f-136">La biblioteca contiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ce83f-137">El [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) incluye una aplicación de :::no-loc(Razor)::: Pages, *HostingStartupApp* , y una biblioteca de clases, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a :::no-loc(Razor)::: Pages app, *HostingStartupApp* , and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="ce83f-138">La biblioteca de clases:</span><span class="sxs-lookup"><span data-stu-id="ce83f-138">The class library:</span></span>

* <span data-ttu-id="ce83f-139">Contiene una clase de inicio de hospedaje, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ce83f-140">`ServiceKeyInjection` agrega un par de cadenas de servicio a la configuración de la aplicación mediante el proveedor de configuración en memoria ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="ce83f-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="ce83f-141">Incluye un atributo `HostingStartup` que identifica espacio de nombres y la clase del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="ce83f-142">El método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> de la clase `ServiceKeyInjection` usa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para agregar mejoras a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="ce83f-143">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="ce83f-143">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ce83f-144">La página de índice de la aplicación lee y procesa los valores de configuración para las dos claves establecidas por el ensamblado de inicio de hospedaje de la biblioteca de clases:</span><span class="sxs-lookup"><span data-stu-id="ce83f-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="ce83f-145">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ce83f-145">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="ce83f-146">El [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) también incluye un proyecto de paquete NuGet que proporciona un inicio de hospedaje independiente, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="ce83f-147">El paquete tiene las mismas características que la biblioteca de clases que se ha descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ce83f-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="ce83f-148">El paquete:</span><span class="sxs-lookup"><span data-stu-id="ce83f-148">The package:</span></span>

* <span data-ttu-id="ce83f-149">Contiene una clase de inicio de hospedaje, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ce83f-150">`ServiceKeyInjection` agrega un par de cadenas de servicio a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="ce83f-151">Incluye un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ce83f-152">*HostingStartupPackage/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="ce83f-152">*HostingStartupPackage/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ce83f-153">La página de índice de la aplicación lee y procesa los valores de configuración para las dos claves establecidas por el ensamblado de inicio de hospedaje del paquete:</span><span class="sxs-lookup"><span data-stu-id="ce83f-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="ce83f-154">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ce83f-154">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="ce83f-155">Aplicación de consola sin un punto de entrada</span><span class="sxs-lookup"><span data-stu-id="ce83f-155">Console app without an entry point</span></span>

<span data-ttu-id="ce83f-156">*Este enfoque solo está disponible para las aplicaciones de .NET Core, no de .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="ce83f-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="ce83f-157">Se puede proporcionar una mejora del inicio de hospedaje dinámico que no requiere una referencia de tiempo de compilación para la activación en una aplicación de consola sin un punto de entrada que contiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="ce83f-158">Publicar la aplicación de consola genera un ensamblado de inicio de hospedaje que se puede consumir desde el almacén en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ce83f-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="ce83f-159">En este proceso se usa una aplicación de consola sin un punto de entrada porque:</span><span class="sxs-lookup"><span data-stu-id="ce83f-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="ce83f-160">Se requiere un archivo de dependencias para consumir el inicio de hospedaje en el ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="ce83f-161">Un archivo de dependencias es un recurso de aplicación ejecutable que se genera mediante la publicación de una aplicación, no una biblioteca.</span><span class="sxs-lookup"><span data-stu-id="ce83f-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="ce83f-162">Una biblioteca no se puede agregar directamente al [almacén de paquetes en tiempo de ejecución](/dotnet/core/deploying/runtime-store), lo que requiere un proyecto ejecutable que tenga como destino el tiempo de ejecución compartido.</span><span class="sxs-lookup"><span data-stu-id="ce83f-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="ce83f-163">En la creación de un inicio de hospedaje dinámico:</span><span class="sxs-lookup"><span data-stu-id="ce83f-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="ce83f-164">Se crea un ensamblado de inicio de hospedaje desde la aplicación de consola sin un punto de entrada que:</span><span class="sxs-lookup"><span data-stu-id="ce83f-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="ce83f-165">Incluye una clase que contiene la implementación de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="ce83f-166">Incluye un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar la clase de implementación `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="ce83f-167">La aplicación de consola se publica para obtener las dependencias del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="ce83f-168">Una consecuencia de la publicación de la aplicación de consola es que las dependencias no usadas se cortan en el archivo de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ce83f-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="ce83f-169">El archivo de dependencias se modifica para establecer la ubicación en tiempo de ejecución del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="ce83f-170">El ensamblado de inicio de hospedaje y su archivo de dependencias se colocan en el almacén de paquetes en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ce83f-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="ce83f-171">Para detectar el ensamblado de inicio de hospedaje y su archivo de dependencias, se enumeran en un par de variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="ce83f-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="ce83f-172">La aplicación de consola hace referencia al paquete [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="ce83f-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="ce83f-173">Un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica una clase como una implementación de `IHostingStartup` para cargarla e implementarla al compilar <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="ce83f-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="ce83f-174">En el siguiente ejemplo, el espacio de nombres es `StartupEnhancement` y la clase, `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="ce83f-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ce83f-175">Una clase implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="ce83f-176">El método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> de la clase usa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para agregar mejoras a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="ce83f-177">El tiempo de ejecución llama a `IHostingStartup.Configure` en el ensamblado de inicio del hospedaje antes de `Startup.Configure` en el código de usuario, lo que permite que el código de usuario sobrescriba cualquier configuración facilitada por el ensamblado de inicio del hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="ce83f-178">Al crear un proyecto de `IHostingStartup`, el archivo de dependencias ( *.deps.json* ) establece la ubicación de `runtime` del ensamblado en la carpeta *bin* :</span><span class="sxs-lookup"><span data-stu-id="ce83f-178">When building an `IHostingStartup` project, the dependencies file ( *.deps.json* ) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="ce83f-179">Solo se muestra parte del archivo.</span><span class="sxs-lookup"><span data-stu-id="ce83f-179">Only part of the file is shown.</span></span> <span data-ttu-id="ce83f-180">El nombre del ensamblado en el ejemplo es `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="ce83f-181">Configuración proporcionada por el inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="ce83f-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="ce83f-182">Existen dos enfoques para controlar la configuración según si quiere que la configuración del inicio de hospedaje tenga prioridad o si la configuración de la aplicación tenga prioridad:</span><span class="sxs-lookup"><span data-stu-id="ce83f-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="ce83f-183">Proporcione la configuración a la aplicación utilizando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para cargar la configuración después de que se ejecuten los delegados <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="ce83f-184">La configuración del inicio de hospedaje tiene prioridad sobre la configuración de la aplicación con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="ce83f-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="ce83f-185">Proporcione la configuración a la aplicación utilizando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para cargar la configuración antes de que se ejecuten los delegados <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="ce83f-186">Los valores de configuración de la aplicación tienen prioridad a sobre los proporcionados por el inicio de hospedaje con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="ce83f-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="ce83f-187">Especificar el ensamblado de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="ce83f-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="ce83f-188">En el caso de un inicio de hospedaje proporcionado por una biblioteca de clase o una aplicación de consola, especifique el nombre del ensamblado de inicio de hospedaje en la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="ce83f-189">La variable de entorno es una lista de ensamblados delimitada por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="ce83f-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="ce83f-190">Solo se examinan los ensamblados de inicio de hospedaje en busca del atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ce83f-191">En la aplicación de ejemplo, *HostingStartupApp* , para descubrir los nuevos inicios de hospedaje descritos anteriormente, la variable de entorno se establece en el siguiente valor:</span><span class="sxs-lookup"><span data-stu-id="ce83f-191">For the sample app, *HostingStartupApp* , to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="ce83f-192">Un ensamblado de inicio de hospedaje también se puede establecer mediante la configuración del host de ensamblados de inicio de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="ce83f-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="ce83f-193">Cuando existen varios ensamblados de inicio de hospedaje, sus métodos <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> se ejecutan en el orden en el que aparecen los ensamblados.</span><span class="sxs-lookup"><span data-stu-id="ce83f-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="ce83f-194">Activación</span><span class="sxs-lookup"><span data-stu-id="ce83f-194">Activation</span></span>

<span data-ttu-id="ce83f-195">Las opciones de activación del inicio de hospedaje son:</span><span class="sxs-lookup"><span data-stu-id="ce83f-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="ce83f-196">[Almacén de tiempo de ejecución](#runtime-store): no se requiere una referencia de tiempo de compilación para la activación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="ce83f-197">La aplicación de ejemplo coloca el ensamblado de inicio de hospedaje y los archivos de dependencias en una carpeta, *implementación* , para facilitar la implementación del inicio del hospedaje en un entorno de varios equipos.</span><span class="sxs-lookup"><span data-stu-id="ce83f-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment* , to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="ce83f-198">La carpeta *implementación* también incluye un script de PowerShell que crea o modifica las variables de entorno en el sistema de implementación para habilitar el inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="ce83f-199">Se requiere una referencia al tiempo de compilación para la activación</span><span class="sxs-lookup"><span data-stu-id="ce83f-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="ce83f-200">Paquete NuGet</span><span class="sxs-lookup"><span data-stu-id="ce83f-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="ce83f-201">Carpeta bin del proyecto</span><span class="sxs-lookup"><span data-stu-id="ce83f-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="ce83f-202">Almacén en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="ce83f-202">Runtime store</span></span>

<span data-ttu-id="ce83f-203">La implementación de inicio de hospedaje se coloca en el [almacén en tiempo de ejecución](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="ce83f-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="ce83f-204">No es necesario que la aplicación mejorada haga ninguna referencia de tiempo de compilación al ensamblado.</span><span class="sxs-lookup"><span data-stu-id="ce83f-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="ce83f-205">Una vez compilado el inicio de hospedaje, se genera un almacén en tiempo de ejecución mediante el archivo del proyecto de manifiesto y el comando [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="ce83f-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="ce83f-206">En la aplicación de ejemplo (proyecto *RuntimeStore* ), se usa el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="ce83f-206">In the sample app ( *RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="ce83f-207">Para que el tiempo de ejecución detecte el almacén en tiempo de ejecución, la ubicación del almacén en tiempo de ejecución se agrega a la variable de entorno `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="ce83f-208">**Modificar y colocar el archivo de dependencias del inicio de hospedaje**</span><span class="sxs-lookup"><span data-stu-id="ce83f-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="ce83f-209">Para activar la mejora sin una referencia de paquete a la mejora, especifique dependencias adicionales del tiempo de ejecución en `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="ce83f-210">`additionalDeps` permite:</span><span class="sxs-lookup"><span data-stu-id="ce83f-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="ce83f-211">Ampliar el gráfico de la biblioteca de la aplicación al proporcionar un conjunto de archivos *.deps.json* adicionales para combinarlos con el propio archivo *.deps.json* de la aplicación durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="ce83f-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="ce83f-212">Permitir la detección y la carga del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="ce83f-213">El enfoque recomendado para generar el archivo de dependencias adicionales es:</span><span class="sxs-lookup"><span data-stu-id="ce83f-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="ce83f-214">Ejecutar `dotnet publish` en el archivo de manifiesto del almacén en tiempo de ejecución al que se hace referencia en la sección anterior.</span><span class="sxs-lookup"><span data-stu-id="ce83f-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="ce83f-215">Quitar la referencia del manifiesto de las bibliotecas y de la sección `runtime` del archivo *deps.json* resultante.</span><span class="sxs-lookup"><span data-stu-id="ce83f-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="ce83f-216">En el proyecto de ejemplo, la propiedad `store.manifest/1.0.0` se quita de las secciones `targets` y `libraries`:</span><span class="sxs-lookup"><span data-stu-id="ce83f-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="ce83f-217">Coloque el archivo *.deps.json* en la siguiente ubicación:</span><span class="sxs-lookup"><span data-stu-id="ce83f-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="ce83f-218">`{ADDITIONAL DEPENDENCIES PATH}`: ubicación agregada a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="ce83f-219">`{SHARED FRAMEWORK NAME}`: marco compartido necesario para este archivo de dependencias adicionales.</span><span class="sxs-lookup"><span data-stu-id="ce83f-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="ce83f-220">`{SHARED FRAMEWORK VERSION}`: versión mínima del marco compartido.</span><span class="sxs-lookup"><span data-stu-id="ce83f-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="ce83f-221">`{ENHANCEMENT ASSEMBLY NAME}`: nombre del ensamblado de la mejora.</span><span class="sxs-lookup"><span data-stu-id="ce83f-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="ce83f-222">En la aplicación de ejemplo (proyecto *RuntimeStore* ), el archivo de dependencias adicionales se coloca en la siguiente ubicación:</span><span class="sxs-lookup"><span data-stu-id="ce83f-222">In the sample app ( *RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="ce83f-223">Para que el tiempo de ejecución detecte la ubicación del almacén en tiempo de ejecución, la ubicación del archivo de dependencias adicionales se agrega a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="ce83f-224">En la aplicación de ejemplo (proyecto *RuntimeStore* ), la creación del almacén en tiempo de ejecución y la generación del archivo de dependencias adicionales se realizan con un script de [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="ce83f-224">In the sample app ( *RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="ce83f-225">Para obtener ejemplos de cómo establecer variables de entorno en distintos sistemas operativos, vea [Uso de varios entornos](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="ce83f-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ce83f-226">**Implementación**</span><span class="sxs-lookup"><span data-stu-id="ce83f-226">**Deployment**</span></span>

<span data-ttu-id="ce83f-227">Para facilitar la implementación de un inicio de hospedaje en un entorno de varios equipos, la aplicación de ejemplo crea una carpeta de *implementación* en el resultado publicado que contiene:</span><span class="sxs-lookup"><span data-stu-id="ce83f-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="ce83f-228">El almacén en tiempo de ejecución de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="ce83f-229">El archivo de dependencias del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="ce83f-230">Un script de PowerShell que crea o modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` y `DOTNET_ADDITIONAL_DEPS` para admitir la activación del inicio del hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="ce83f-231">Ejecute el script desde un símbolo del sistema de PowerShell administrativo en el sistema de implementación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="ce83f-232">Detección de</span><span class="sxs-lookup"><span data-stu-id="ce83f-232">NuGet package</span></span>

<span data-ttu-id="ce83f-233">Una mejora de inicio de hospedaje se puede proporcionar en un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="ce83f-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="ce83f-234">El paquete tiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="ce83f-235">Los tipos de inicio de hospedaje proporcionados por el paquete están disponibles en la aplicación mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ce83f-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="ce83f-236">El archivo de proyecto de la aplicación mejorada hace una referencia al paquete para el inicio de hospedaje en el archivo de proyecto de la aplicación (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="ce83f-237">Con la referencia de tiempo de compilación realizada, el ensamblado de inicio de hospedaje y todas sus dependencias se incorporan en el archivo de dependencia de la aplicación ( *.deps.json* ).</span><span class="sxs-lookup"><span data-stu-id="ce83f-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="ce83f-238">Este enfoque se aplica a un paquete de ensamblado de inicio de hospedaje publicado en [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="ce83f-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="ce83f-239">El archivo de dependencias del inicio de hospedaje está disponible para la aplicación mejorada como se describe en la sección [Almacén en tiempo de ejecución](#runtime-store) (sin una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="ce83f-240">Para obtener más información sobre los paquetes NuGet y el almacén en tiempo de ejecución, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="ce83f-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="ce83f-241">Cómo crear un paquete NuGet con herramientas multiplataforma</span><span class="sxs-lookup"><span data-stu-id="ce83f-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="ce83f-242">Publicar paquetes</span><span class="sxs-lookup"><span data-stu-id="ce83f-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* <span data-ttu-id="ce83f-243">[Runtime package store](/dotnet/core/deploying/runtime-store) (Almacenamiento de paquetes en tiempo de ejecución)</span><span class="sxs-lookup"><span data-stu-id="ce83f-243">[Runtime package store](/dotnet/core/deploying/runtime-store)</span></span>

### <a name="project-bin-folder"></a><span data-ttu-id="ce83f-244">Carpeta bin del proyecto</span><span class="sxs-lookup"><span data-stu-id="ce83f-244">Project bin folder</span></span>

<span data-ttu-id="ce83f-245">Un ensamblado implementado por *bin* puede proporcionar una mejora del inicio de hospedaje en la aplicación mejorada.</span><span class="sxs-lookup"><span data-stu-id="ce83f-245">A hosting startup enhancement can be provided by a *bin* -deployed assembly in the enhanced app.</span></span> <span data-ttu-id="ce83f-246">Los tipos de inicio de hospedaje que proporciona el ensamblado están disponibles en la aplicación mediante uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ce83f-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="ce83f-247">El archivo de proyecto de la aplicación mejorada hace referencia de ensamblado al inicio de hospedaje (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="ce83f-248">Con la referencia de tiempo de compilación realizada, el ensamblado de inicio de hospedaje y todas sus dependencias se incorporan en el archivo de dependencia de la aplicación ( *.deps.json* ).</span><span class="sxs-lookup"><span data-stu-id="ce83f-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="ce83f-249">Este método se aplica cuando el escenario de implementación llama para realizar una referencia en tiempo de compilación al ensamblado del inicio del hospedaje (archivo *.dll* ) y mover el ensamblado a una de las siguientes ubicaciones:</span><span class="sxs-lookup"><span data-stu-id="ce83f-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly ( *.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="ce83f-250">Proyecto utilizado.</span><span class="sxs-lookup"><span data-stu-id="ce83f-250">The consuming project.</span></span>
  * <span data-ttu-id="ce83f-251">Ubicación a la que puede acceder el proyecto utilizado.</span><span class="sxs-lookup"><span data-stu-id="ce83f-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="ce83f-252">El archivo de dependencias del inicio de hospedaje está disponible para la aplicación mejorada como se describe en la sección [Almacén en tiempo de ejecución](#runtime-store) (sin una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="ce83f-253">Si el destino es .NET Framework, el ensamblado se puede cargar en el contexto de carga predeterminado, lo que en .NET Framework significa que el ensamblado se encuentra en cualquiera de las siguientes ubicaciones:</span><span class="sxs-lookup"><span data-stu-id="ce83f-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="ce83f-254">Ruta de acceso base de la aplicación: carpeta *bin* en la que se encuentra el archivo ejecutable ( *.exe* ) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-254">Application base path: The *bin* folder where the app's executable ( *.exe* ) is located.</span></span>
  * <span data-ttu-id="ce83f-255">Caché global de ensamblados (GAC): almacena los ensamblados que comparten varias aplicaciones .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ce83f-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="ce83f-256">Para obtener más información, vea [Cómo: Instalar un ensamblado en la caché global de ensamblados](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) en la documentación de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ce83f-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="ce83f-257">Código de ejemplo</span><span class="sxs-lookup"><span data-stu-id="ce83f-257">Sample code</span></span>

<span data-ttu-id="ce83f-258">En el [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([cómo descargar](xref:index#how-to-download-a-sample)) se muestran escenarios de implementación de inicio de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="ce83f-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="ce83f-259">Cada uno de los dos ensamblados de inicio de hospedaje (bibliotecas de clases) establece un par clave-valor de configuración en memoria:</span><span class="sxs-lookup"><span data-stu-id="ce83f-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="ce83f-260">Paquete NuGet ( *HostingStartupPackage* )</span><span class="sxs-lookup"><span data-stu-id="ce83f-260">NuGet package ( *HostingStartupPackage* )</span></span>
  * <span data-ttu-id="ce83f-261">Biblioteca de clases ( *HostingStartupLibrary* )</span><span class="sxs-lookup"><span data-stu-id="ce83f-261">Class library ( *HostingStartupLibrary* )</span></span>
* <span data-ttu-id="ce83f-262">Se activa un inicio de hospedaje desde un ensamblado implementado por el almacén de tiempo de ejecución ( *StartupDiagnostics* ).</span><span class="sxs-lookup"><span data-stu-id="ce83f-262">A hosting startup is activated from a runtime store-deployed assembly ( *StartupDiagnostics* ).</span></span> <span data-ttu-id="ce83f-263">Este ensamblado agrega dos middleware a la aplicación (mientras se inicia) que proporcionan información de diagnóstico en:</span><span class="sxs-lookup"><span data-stu-id="ce83f-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="ce83f-264">Servicios registrados</span><span class="sxs-lookup"><span data-stu-id="ce83f-264">Registered services</span></span>
  * <span data-ttu-id="ce83f-265">Dirección (esquema, host, ruta de acceso base, ruta de acceso, cadena de consulta)</span><span class="sxs-lookup"><span data-stu-id="ce83f-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="ce83f-266">Conexión (dirección IP remota, puerto remoto, dirección IP local, puerto local, certificado de cliente)</span><span class="sxs-lookup"><span data-stu-id="ce83f-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="ce83f-267">Encabezados de solicitud</span><span class="sxs-lookup"><span data-stu-id="ce83f-267">Request headers</span></span>
  * <span data-ttu-id="ce83f-268">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ce83f-268">Environment variables</span></span>

<span data-ttu-id="ce83f-269">Para ejecutar el ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ce83f-269">To run the sample:</span></span>

<span data-ttu-id="ce83f-270">**Activación desde un paquete NuGet**</span><span class="sxs-lookup"><span data-stu-id="ce83f-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="ce83f-271">Compile el paquete *HostingStartupPackage* con el comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="ce83f-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="ce83f-272">Agregue el nombre de ensamblado del paquete de *HostingStartupPackage* a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ce83f-273">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-273">Compile and run the app.</span></span> <span data-ttu-id="ce83f-274">Una referencia de paquete está presente en la aplicación mejorada (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="ce83f-275">El parámetro `<PropertyGroup>` del archivo del proyecto de la aplicación especifica el resultado del proyecto de paquete ( *../HostingStartupPackage/bin/Debug* ) como origen del paquete.</span><span class="sxs-lookup"><span data-stu-id="ce83f-275">A `<PropertyGroup>` in the app's project file specifies the package project's output ( *../HostingStartupPackage/bin/Debug* ) as a package source.</span></span> <span data-ttu-id="ce83f-276">Esto permite que la aplicación utilice el paquete sin cargar el paquete a [nuget.org](https://www.nuget.org/). Para obtener más información, vea las notas que encontrará en el archivo del proyecto de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="ce83f-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="ce83f-277">Observe que los valores de la clave de configuración del servicio representados por la página de índice coinciden con los valores establecidos por método `ServiceKeyInjection.Configure` del paquete.</span><span class="sxs-lookup"><span data-stu-id="ce83f-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ce83f-278">Si realiza cambios en el proyecto *HostingStartupPackage* y lo vuelve a compilar, borre las cachés del paquete NuGet local para asegurarse de que *HostingStartupApp* recibe el paquete actualizado y no un paquete en desuso de la caché local.</span><span class="sxs-lookup"><span data-stu-id="ce83f-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="ce83f-279">Para borrar las cachés de NuGet locales, ejecute el siguiente comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="ce83f-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="ce83f-280">**Activación desde una biblioteca de clases**</span><span class="sxs-lookup"><span data-stu-id="ce83f-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="ce83f-281">Compile la biblioteca de clases *HostingStartupLibrary* con el comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="ce83f-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="ce83f-282">Agregue el nombre del ensamblado de la biblioteca de clases *HostingStartupLibrary* a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ce83f-283">Implemente con *bin* -el ensamblado de la biblioteca de clases en la aplicación copiando el archivo *HostingStartupLibrary.dll* desde el resultado compilado de la biblioteca de aplicaciones en la carpeta *bin/Debug* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-283">*bin* -deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="ce83f-284">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-284">Compile and run the app.</span></span> <span data-ttu-id="ce83f-285">Un parámetro `<ItemGroup>` del archivo de proyecto de la aplicación hace referencia al ensamblado de la biblioteca de clases ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll* ) (una referencia en tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-285">An `<ItemGroup>` in the app's project file references the class library's assembly ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll* ) (a compile-time reference).</span></span> <span data-ttu-id="ce83f-286">Para obtener más información, vea las notas que encontrará en el archivo del proyecto de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="ce83f-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="ce83f-287">Observe que los valores de la clave de configuración del servicio representados por la página de índice coinciden con los valores establecidos por el método `ServiceKeyInjection.Configure` de la biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="ce83f-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ce83f-288">**Activación desde un ensamblado implementado por el almacén de tiempo de ejecución**</span><span class="sxs-lookup"><span data-stu-id="ce83f-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="ce83f-289">El proyecto *StartupDiagnostics* usa [PowerShell](/powershell/scripting/powershell-scripting) para modificar el archivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="ce83f-290">PowerShell se instala de forma predeterminada en Windows a partir de Windows 7 SP1 y Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="ce83f-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="ce83f-291">Para obtener PowerShell en otras plataformas, consulte [Instalación de varias versiones de PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="ce83f-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="ce83f-292">Ejecute el script *build.ps1* en la carpeta *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="ce83f-293">El script:</span><span class="sxs-lookup"><span data-stu-id="ce83f-293">The script:</span></span>
   * <span data-ttu-id="ce83f-294">Genera el paquete `StartupDiagnostics` en la carpeta *obj\packages*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="ce83f-295">Genera el almacén en tiempo de ejecución para `StartupDiagnostics` en la carpeta *store*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="ce83f-296">El comando `dotnet store` del script usa el [identificador de tiempo de ejecución (RID)](/dotnet/core/rid-catalog) `win7-x64` para un inicio de hospedaje implementado en Windows.</span><span class="sxs-lookup"><span data-stu-id="ce83f-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="ce83f-297">Para proporcionar el inicio de hospedaje para otro tiempo de ejecución, sustituya el RID correcto en la línea 37 del script.</span><span class="sxs-lookup"><span data-stu-id="ce83f-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="ce83f-298">El almacén en tiempo de ejecución de `StartupDiagnostics` se moverá más tarde al almacén en tiempo de ejecución del usuario o del sistema en la máquina donde se va a consumir el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="ce83f-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="ce83f-299">La ubicación de instalación del almacén en tiempo de ejecución del usuario para el ensamblado `StartupDiagnostics` es *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="ce83f-300">Genera el parámetro `additionalDeps` para `StartupDiagnostics` en la carpeta *additionalDeps*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="ce83f-301">Las dependencias adicionales se moverían posteriormente a las dependencias adicionales del usuario o del sistema.</span><span class="sxs-lookup"><span data-stu-id="ce83f-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="ce83f-302">La ubicación de instalación de las dependencias adicionales `StartupDiagnostics` del usuario es *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="ce83f-303">Coloca el archivo *deploy.ps1* en la carpeta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="ce83f-304">Ejecute el script *deploy.ps1* en la carpeta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="ce83f-305">El script anexa:</span><span class="sxs-lookup"><span data-stu-id="ce83f-305">The script appends:</span></span>
   * <span data-ttu-id="ce83f-306">`StartupDiagnostics` a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="ce83f-307">La ruta de acceso de las dependencias de inicio de hospedaje (en la carpeta *deployment* del proyecto RuntimeStore) a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="ce83f-308">La ruta de acceso del almacén en tiempo de ejecución (en la carpeta *deployment* del proyecto RuntimeStore) a la variable de entorno `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="ce83f-309">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="ce83f-309">Run the sample app.</span></span>
1. <span data-ttu-id="ce83f-310">Solicite al punto de conexión `/services` ver los servicios registrados de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="ce83f-311">Solicite al punto de conexión `/diag` ver la información de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="ce83f-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ce83f-312">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (inicio de hospedaje) agrega mejoras a una aplicación al iniciarla a partir de un ensamblado externo.</span><span class="sxs-lookup"><span data-stu-id="ce83f-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="ce83f-313">Por ejemplo, una biblioteca externa puede usar una implementación de inicio de hospedaje para suministrar más servicios o proveedores de configuración a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="ce83f-314">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ce83f-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="ce83f-315">Atributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="ce83f-315">HostingStartup attribute</span></span>

<span data-ttu-id="ce83f-316">Un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica la presencia de un ensamblado de inicio de hospedaje para activar en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ce83f-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="ce83f-317">El ensamblado de entrada o el ensamblado que contiene la clase `Startup` se analiza automáticamente para detectar el atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ce83f-318">La lista de ensamblados para buscar atributos `HostingStartup` se carga en tiempo de ejecución desde la configuración de [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="ce83f-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="ce83f-319">La lista de ensamblados que se excluirán de la detección se carga desde [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="ce83f-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="ce83f-320">Para más información, consulte [Host web: Ensamblados de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-assemblies) y [Host web: ensamblados de exclusión de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="ce83f-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="ce83f-321">En el ejemplo siguiente, el espacio de nombres del ensamblado de inicio de hospedaje es `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="ce83f-322">La clase que contiene el código de inicio de hospedaje es `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="ce83f-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ce83f-323">Normalmente el atributo `HostingStartup` se encuentra en el archivo de clase de implementación `IHostingStartup` del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="ce83f-324">Detectar los ensamblados de inicio de hospedaje cargados</span><span class="sxs-lookup"><span data-stu-id="ce83f-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="ce83f-325">Para detectar los ensamblados de inicio de hospedaje cargados, habilite el registro y consulte los registros de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="ce83f-326">En ellos se registran los errores que se producen al cargar ensamblados.</span><span class="sxs-lookup"><span data-stu-id="ce83f-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="ce83f-327">Los ensamblados de inicio de hospedaje se registran en el nivel de depuración y se registran todos los errores.</span><span class="sxs-lookup"><span data-stu-id="ce83f-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="ce83f-328">Deshabilitar la carga automática de ensamblados de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="ce83f-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="ce83f-329">Para deshabilitar la carga automática de los ensamblados de inicio de hospedaje, use uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="ce83f-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="ce83f-330">Para evitar que se carguen todos los ensamblados de inicio de hospedaje, establezca uno de los procedimientos siguientes en `true` o `1`:</span><span class="sxs-lookup"><span data-stu-id="ce83f-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="ce83f-331">La opción de configuración de hospedaje para [Evitar el inicio de hospedaje](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="ce83f-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="ce83f-332">La variable de entorno `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="ce83f-333">Para evitar la carga de ensamblados de inicio de hospedaje específicos, establezca una de las opciones siguientes en una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir durante el inicio:</span><span class="sxs-lookup"><span data-stu-id="ce83f-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="ce83f-334">La opción de configuración de host para [Ensamblados de exclusión de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="ce83f-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="ce83f-335">La variable de entorno `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="ce83f-336">Si se establecen la opción de configuración de host y la variable de entorno, la configuración de host controla el comportamiento.</span><span class="sxs-lookup"><span data-stu-id="ce83f-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="ce83f-337">Deshabilitar los ensamblados de inicio de hospedaje a través de la variable de entorno o de la configuración de host hace que el ensamblado se deshabilite globalmente y, asimismo, puede deshabilitar también otras características de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="ce83f-338">Proyecto</span><span class="sxs-lookup"><span data-stu-id="ce83f-338">Project</span></span>

<span data-ttu-id="ce83f-339">Cree un inicio de hospedaje con cualquiera de los siguientes tipos de proyecto:</span><span class="sxs-lookup"><span data-stu-id="ce83f-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="ce83f-340">Biblioteca de clases</span><span class="sxs-lookup"><span data-stu-id="ce83f-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="ce83f-341">Aplicación de consola sin un punto de entrada</span><span class="sxs-lookup"><span data-stu-id="ce83f-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="ce83f-342">Biblioteca de clases</span><span class="sxs-lookup"><span data-stu-id="ce83f-342">Class library</span></span>

<span data-ttu-id="ce83f-343">Una mejora de inicio de hospedaje se puede proporcionar en una biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="ce83f-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="ce83f-344">La biblioteca contiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ce83f-345">El [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) incluye una aplicación de :::no-loc(Razor)::: Pages, *HostingStartupApp* , y una biblioteca de clases, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a :::no-loc(Razor)::: Pages app, *HostingStartupApp* , and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="ce83f-346">La biblioteca de clases:</span><span class="sxs-lookup"><span data-stu-id="ce83f-346">The class library:</span></span>

* <span data-ttu-id="ce83f-347">Contiene una clase de inicio de hospedaje, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ce83f-348">`ServiceKeyInjection` agrega un par de cadenas de servicio a la configuración de la aplicación mediante el proveedor de configuración en memoria ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="ce83f-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="ce83f-349">Incluye un atributo `HostingStartup` que identifica espacio de nombres y la clase del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="ce83f-350">El método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> de la clase `ServiceKeyInjection` usa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para agregar mejoras a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="ce83f-351">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="ce83f-351">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ce83f-352">La página de índice de la aplicación lee y procesa los valores de configuración para las dos claves establecidas por el ensamblado de inicio de hospedaje de la biblioteca de clases:</span><span class="sxs-lookup"><span data-stu-id="ce83f-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="ce83f-353">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ce83f-353">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="ce83f-354">El [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) también incluye un proyecto de paquete NuGet que proporciona un inicio de hospedaje independiente, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="ce83f-355">El paquete tiene las mismas características que la biblioteca de clases que se ha descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ce83f-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="ce83f-356">El paquete:</span><span class="sxs-lookup"><span data-stu-id="ce83f-356">The package:</span></span>

* <span data-ttu-id="ce83f-357">Contiene una clase de inicio de hospedaje, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ce83f-358">`ServiceKeyInjection` agrega un par de cadenas de servicio a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="ce83f-359">Incluye un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ce83f-360">*HostingStartupPackage/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="ce83f-360">*HostingStartupPackage/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ce83f-361">La página de índice de la aplicación lee y procesa los valores de configuración para las dos claves establecidas por el ensamblado de inicio de hospedaje del paquete:</span><span class="sxs-lookup"><span data-stu-id="ce83f-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="ce83f-362">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ce83f-362">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="ce83f-363">Aplicación de consola sin un punto de entrada</span><span class="sxs-lookup"><span data-stu-id="ce83f-363">Console app without an entry point</span></span>

<span data-ttu-id="ce83f-364">*Este enfoque solo está disponible para las aplicaciones de .NET Core, no de .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="ce83f-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="ce83f-365">Se puede proporcionar una mejora del inicio de hospedaje dinámico que no requiere una referencia de tiempo de compilación para la activación en una aplicación de consola sin un punto de entrada que contiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="ce83f-366">Publicar la aplicación de consola genera un ensamblado de inicio de hospedaje que se puede consumir desde el almacén en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ce83f-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="ce83f-367">En este proceso se usa una aplicación de consola sin un punto de entrada porque:</span><span class="sxs-lookup"><span data-stu-id="ce83f-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="ce83f-368">Se requiere un archivo de dependencias para consumir el inicio de hospedaje en el ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="ce83f-369">Un archivo de dependencias es un recurso de aplicación ejecutable que se genera mediante la publicación de una aplicación, no una biblioteca.</span><span class="sxs-lookup"><span data-stu-id="ce83f-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="ce83f-370">Una biblioteca no se puede agregar directamente al [almacén de paquetes en tiempo de ejecución](/dotnet/core/deploying/runtime-store), lo que requiere un proyecto ejecutable que tenga como destino el tiempo de ejecución compartido.</span><span class="sxs-lookup"><span data-stu-id="ce83f-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="ce83f-371">En la creación de un inicio de hospedaje dinámico:</span><span class="sxs-lookup"><span data-stu-id="ce83f-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="ce83f-372">Se crea un ensamblado de inicio de hospedaje desde la aplicación de consola sin un punto de entrada que:</span><span class="sxs-lookup"><span data-stu-id="ce83f-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="ce83f-373">Incluye una clase que contiene la implementación de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="ce83f-374">Incluye un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar la clase de implementación `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="ce83f-375">La aplicación de consola se publica para obtener las dependencias del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="ce83f-376">Una consecuencia de la publicación de la aplicación de consola es que las dependencias no usadas se cortan en el archivo de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ce83f-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="ce83f-377">El archivo de dependencias se modifica para establecer la ubicación en tiempo de ejecución del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="ce83f-378">El ensamblado de inicio de hospedaje y su archivo de dependencias se colocan en el almacén de paquetes en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ce83f-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="ce83f-379">Para detectar el ensamblado de inicio de hospedaje y su archivo de dependencias, se enumeran en un par de variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="ce83f-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="ce83f-380">La aplicación de consola hace referencia al paquete [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="ce83f-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="ce83f-381">Un atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica una clase como una implementación de `IHostingStartup` para cargarla e implementarla al compilar <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="ce83f-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="ce83f-382">En el siguiente ejemplo, el espacio de nombres es `StartupEnhancement` y la clase, `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="ce83f-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ce83f-383">Una clase implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="ce83f-384">El método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> de la clase usa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para agregar mejoras a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="ce83f-385">El tiempo de ejecución llama a `IHostingStartup.Configure` en el ensamblado de inicio del hospedaje antes de `Startup.Configure` en el código de usuario, lo que permite que el código de usuario sobrescriba cualquier configuración facilitada por el ensamblado de inicio del hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="ce83f-386">Al crear un proyecto de `IHostingStartup`, el archivo de dependencias ( *.deps.json* ) establece la ubicación de `runtime` del ensamblado en la carpeta *bin* :</span><span class="sxs-lookup"><span data-stu-id="ce83f-386">When building an `IHostingStartup` project, the dependencies file ( *.deps.json* ) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="ce83f-387">Solo se muestra parte del archivo.</span><span class="sxs-lookup"><span data-stu-id="ce83f-387">Only part of the file is shown.</span></span> <span data-ttu-id="ce83f-388">El nombre del ensamblado en el ejemplo es `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="ce83f-389">Configuración proporcionada por el inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="ce83f-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="ce83f-390">Existen dos enfoques para controlar la configuración según si quiere que la configuración del inicio de hospedaje tenga prioridad o si la configuración de la aplicación tenga prioridad:</span><span class="sxs-lookup"><span data-stu-id="ce83f-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="ce83f-391">Proporcione la configuración a la aplicación utilizando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para cargar la configuración después de que se ejecuten los delegados <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="ce83f-392">La configuración del inicio de hospedaje tiene prioridad sobre la configuración de la aplicación con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="ce83f-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="ce83f-393">Proporcione la configuración a la aplicación utilizando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para cargar la configuración antes de que se ejecuten los delegados <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="ce83f-394">Los valores de configuración de la aplicación tienen prioridad a sobre los proporcionados por el inicio de hospedaje con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="ce83f-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="ce83f-395">Especificar el ensamblado de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="ce83f-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="ce83f-396">En el caso de un inicio de hospedaje proporcionado por una biblioteca de clase o una aplicación de consola, especifique el nombre del ensamblado de inicio de hospedaje en la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="ce83f-397">La variable de entorno es una lista de ensamblados delimitada por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="ce83f-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="ce83f-398">Solo se examinan los ensamblados de inicio de hospedaje en busca del atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ce83f-399">En la aplicación de ejemplo, *HostingStartupApp* , para descubrir los nuevos inicios de hospedaje descritos anteriormente, la variable de entorno se establece en el siguiente valor:</span><span class="sxs-lookup"><span data-stu-id="ce83f-399">For the sample app, *HostingStartupApp* , to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="ce83f-400">Un ensamblado de inicio de hospedaje también se puede establecer mediante la configuración de host [Ensamblados de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="ce83f-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="ce83f-401">Cuando existen varios ensamblados de inicio de hospedaje, sus métodos <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> se ejecutan en el orden en el que aparecen los ensamblados.</span><span class="sxs-lookup"><span data-stu-id="ce83f-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="ce83f-402">Activación</span><span class="sxs-lookup"><span data-stu-id="ce83f-402">Activation</span></span>

<span data-ttu-id="ce83f-403">Las opciones de activación del inicio de hospedaje son:</span><span class="sxs-lookup"><span data-stu-id="ce83f-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="ce83f-404">[Almacén de tiempo de ejecución](#runtime-store): no se requiere una referencia de tiempo de compilación para la activación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="ce83f-405">La aplicación de ejemplo coloca el ensamblado de inicio de hospedaje y los archivos de dependencias en una carpeta, *implementación* , para facilitar la implementación del inicio del hospedaje en un entorno de varios equipos.</span><span class="sxs-lookup"><span data-stu-id="ce83f-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment* , to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="ce83f-406">La carpeta *implementación* también incluye un script de PowerShell que crea o modifica las variables de entorno en el sistema de implementación para habilitar el inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="ce83f-407">Se requiere una referencia al tiempo de compilación para la activación</span><span class="sxs-lookup"><span data-stu-id="ce83f-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="ce83f-408">Paquete NuGet</span><span class="sxs-lookup"><span data-stu-id="ce83f-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="ce83f-409">Carpeta bin del proyecto</span><span class="sxs-lookup"><span data-stu-id="ce83f-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="ce83f-410">Almacén en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="ce83f-410">Runtime store</span></span>

<span data-ttu-id="ce83f-411">La implementación de inicio de hospedaje se coloca en el [almacén en tiempo de ejecución](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="ce83f-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="ce83f-412">No es necesario que la aplicación mejorada haga ninguna referencia de tiempo de compilación al ensamblado.</span><span class="sxs-lookup"><span data-stu-id="ce83f-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="ce83f-413">Una vez compilado el inicio de hospedaje, se genera un almacén en tiempo de ejecución mediante el archivo del proyecto de manifiesto y el comando [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="ce83f-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="ce83f-414">En la aplicación de ejemplo (proyecto *RuntimeStore* ), se usa el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="ce83f-414">In the sample app ( *RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="ce83f-415">Para que el tiempo de ejecución detecte el almacén en tiempo de ejecución, la ubicación del almacén en tiempo de ejecución se agrega a la variable de entorno `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="ce83f-416">**Modificar y colocar el archivo de dependencias del inicio de hospedaje**</span><span class="sxs-lookup"><span data-stu-id="ce83f-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="ce83f-417">Para activar la mejora sin una referencia de paquete a la mejora, especifique dependencias adicionales del tiempo de ejecución en `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="ce83f-418">`additionalDeps` permite:</span><span class="sxs-lookup"><span data-stu-id="ce83f-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="ce83f-419">Ampliar el gráfico de la biblioteca de la aplicación al proporcionar un conjunto de archivos *.deps.json* adicionales para combinarlos con el propio archivo *.deps.json* de la aplicación durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="ce83f-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="ce83f-420">Permitir la detección y la carga del ensamblado de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="ce83f-421">El enfoque recomendado para generar el archivo de dependencias adicionales es:</span><span class="sxs-lookup"><span data-stu-id="ce83f-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="ce83f-422">Ejecutar `dotnet publish` en el archivo de manifiesto del almacén en tiempo de ejecución al que se hace referencia en la sección anterior.</span><span class="sxs-lookup"><span data-stu-id="ce83f-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="ce83f-423">Quitar la referencia del manifiesto de las bibliotecas y de la sección `runtime` del archivo *deps.json* resultante.</span><span class="sxs-lookup"><span data-stu-id="ce83f-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="ce83f-424">En el proyecto de ejemplo, la propiedad `store.manifest/1.0.0` se quita de las secciones `targets` y `libraries`:</span><span class="sxs-lookup"><span data-stu-id="ce83f-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="ce83f-425">Coloque el archivo *.deps.json* en la siguiente ubicación:</span><span class="sxs-lookup"><span data-stu-id="ce83f-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="ce83f-426">`{ADDITIONAL DEPENDENCIES PATH}`: ubicación agregada a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="ce83f-427">`{SHARED FRAMEWORK NAME}`: marco compartido necesario para este archivo de dependencias adicionales.</span><span class="sxs-lookup"><span data-stu-id="ce83f-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="ce83f-428">`{SHARED FRAMEWORK VERSION}`: versión mínima del marco compartido.</span><span class="sxs-lookup"><span data-stu-id="ce83f-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="ce83f-429">`{ENHANCEMENT ASSEMBLY NAME}`: nombre del ensamblado de la mejora.</span><span class="sxs-lookup"><span data-stu-id="ce83f-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="ce83f-430">En la aplicación de ejemplo (proyecto *RuntimeStore* ), el archivo de dependencias adicionales se coloca en la siguiente ubicación:</span><span class="sxs-lookup"><span data-stu-id="ce83f-430">In the sample app ( *RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="ce83f-431">Para que el tiempo de ejecución detecte la ubicación del almacén en tiempo de ejecución, la ubicación del archivo de dependencias adicionales se agrega a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="ce83f-432">En la aplicación de ejemplo (proyecto *RuntimeStore* ), la creación del almacén en tiempo de ejecución y la generación del archivo de dependencias adicionales se realizan con un script de [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="ce83f-432">In the sample app ( *RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="ce83f-433">Para obtener ejemplos de cómo establecer variables de entorno en distintos sistemas operativos, vea [Uso de varios entornos](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="ce83f-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ce83f-434">**Implementación**</span><span class="sxs-lookup"><span data-stu-id="ce83f-434">**Deployment**</span></span>

<span data-ttu-id="ce83f-435">Para facilitar la implementación de un inicio de hospedaje en un entorno de varios equipos, la aplicación de ejemplo crea una carpeta de *implementación* en el resultado publicado que contiene:</span><span class="sxs-lookup"><span data-stu-id="ce83f-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="ce83f-436">El almacén en tiempo de ejecución de inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="ce83f-437">El archivo de dependencias del inicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="ce83f-438">Un script de PowerShell que crea o modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` y `DOTNET_ADDITIONAL_DEPS` para admitir la activación del inicio del hospedaje.</span><span class="sxs-lookup"><span data-stu-id="ce83f-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="ce83f-439">Ejecute el script desde un símbolo del sistema de PowerShell administrativo en el sistema de implementación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="ce83f-440">Detección de</span><span class="sxs-lookup"><span data-stu-id="ce83f-440">NuGet package</span></span>

<span data-ttu-id="ce83f-441">Una mejora de inicio de hospedaje se puede proporcionar en un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="ce83f-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="ce83f-442">El paquete tiene un atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="ce83f-443">Los tipos de inicio de hospedaje proporcionados por el paquete están disponibles en la aplicación mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ce83f-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="ce83f-444">El archivo de proyecto de la aplicación mejorada hace una referencia al paquete para el inicio de hospedaje en el archivo de proyecto de la aplicación (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="ce83f-445">Con la referencia de tiempo de compilación realizada, el ensamblado de inicio de hospedaje y todas sus dependencias se incorporan en el archivo de dependencia de la aplicación ( *.deps.json* ).</span><span class="sxs-lookup"><span data-stu-id="ce83f-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="ce83f-446">Este enfoque se aplica a un paquete de ensamblado de inicio de hospedaje publicado en [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="ce83f-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="ce83f-447">El archivo de dependencias del inicio de hospedaje está disponible para la aplicación mejorada como se describe en la sección [Almacén en tiempo de ejecución](#runtime-store) (sin una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="ce83f-448">Para obtener más información sobre los paquetes NuGet y el almacén en tiempo de ejecución, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="ce83f-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="ce83f-449">Cómo crear un paquete NuGet con herramientas multiplataforma</span><span class="sxs-lookup"><span data-stu-id="ce83f-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="ce83f-450">Publicar paquetes</span><span class="sxs-lookup"><span data-stu-id="ce83f-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* <span data-ttu-id="ce83f-451">[Runtime package store](/dotnet/core/deploying/runtime-store) (Almacenamiento de paquetes en tiempo de ejecución)</span><span class="sxs-lookup"><span data-stu-id="ce83f-451">[Runtime package store](/dotnet/core/deploying/runtime-store)</span></span>

### <a name="project-bin-folder"></a><span data-ttu-id="ce83f-452">Carpeta bin del proyecto</span><span class="sxs-lookup"><span data-stu-id="ce83f-452">Project bin folder</span></span>

<span data-ttu-id="ce83f-453">Un ensamblado implementado por *bin* puede proporcionar una mejora del inicio de hospedaje en la aplicación mejorada.</span><span class="sxs-lookup"><span data-stu-id="ce83f-453">A hosting startup enhancement can be provided by a *bin* -deployed assembly in the enhanced app.</span></span> <span data-ttu-id="ce83f-454">Los tipos de inicio de hospedaje que proporciona el ensamblado están disponibles en la aplicación mediante uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ce83f-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="ce83f-455">El archivo de proyecto de la aplicación mejorada hace referencia de ensamblado al inicio de hospedaje (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="ce83f-456">Con la referencia de tiempo de compilación realizada, el ensamblado de inicio de hospedaje y todas sus dependencias se incorporan en el archivo de dependencia de la aplicación ( *.deps.json* ).</span><span class="sxs-lookup"><span data-stu-id="ce83f-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="ce83f-457">Este método se aplica cuando el escenario de implementación llama para realizar una referencia en tiempo de compilación al ensamblado del inicio del hospedaje (archivo *.dll* ) y mover el ensamblado a una de las siguientes ubicaciones:</span><span class="sxs-lookup"><span data-stu-id="ce83f-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly ( *.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="ce83f-458">Proyecto utilizado.</span><span class="sxs-lookup"><span data-stu-id="ce83f-458">The consuming project.</span></span>
  * <span data-ttu-id="ce83f-459">Ubicación a la que puede acceder el proyecto utilizado.</span><span class="sxs-lookup"><span data-stu-id="ce83f-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="ce83f-460">El archivo de dependencias del inicio de hospedaje está disponible para la aplicación mejorada como se describe en la sección [Almacén en tiempo de ejecución](#runtime-store) (sin una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="ce83f-461">Si el destino es .NET Framework, el ensamblado se puede cargar en el contexto de carga predeterminado, lo que en .NET Framework significa que el ensamblado se encuentra en cualquiera de las siguientes ubicaciones:</span><span class="sxs-lookup"><span data-stu-id="ce83f-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="ce83f-462">Ruta de acceso base de la aplicación: carpeta *bin* en la que se encuentra el archivo ejecutable ( *.exe* ) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-462">Application base path: The *bin* folder where the app's executable ( *.exe* ) is located.</span></span>
  * <span data-ttu-id="ce83f-463">Caché global de ensamblados (GAC): almacena los ensamblados que comparten varias aplicaciones .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ce83f-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="ce83f-464">Para obtener más información, vea [Cómo: Instalar un ensamblado en la caché global de ensamblados](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) en la documentación de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ce83f-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="ce83f-465">Código de ejemplo</span><span class="sxs-lookup"><span data-stu-id="ce83f-465">Sample code</span></span>

<span data-ttu-id="ce83f-466">En el [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([cómo descargar](xref:index#how-to-download-a-sample)) se muestran escenarios de implementación de inicio de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="ce83f-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="ce83f-467">Cada uno de los dos ensamblados de inicio de hospedaje (bibliotecas de clases) establece un par clave-valor de configuración en memoria:</span><span class="sxs-lookup"><span data-stu-id="ce83f-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="ce83f-468">Paquete NuGet ( *HostingStartupPackage* )</span><span class="sxs-lookup"><span data-stu-id="ce83f-468">NuGet package ( *HostingStartupPackage* )</span></span>
  * <span data-ttu-id="ce83f-469">Biblioteca de clases ( *HostingStartupLibrary* )</span><span class="sxs-lookup"><span data-stu-id="ce83f-469">Class library ( *HostingStartupLibrary* )</span></span>
* <span data-ttu-id="ce83f-470">Se activa un inicio de hospedaje desde un ensamblado implementado por el almacén de tiempo de ejecución ( *StartupDiagnostics* ).</span><span class="sxs-lookup"><span data-stu-id="ce83f-470">A hosting startup is activated from a runtime store-deployed assembly ( *StartupDiagnostics* ).</span></span> <span data-ttu-id="ce83f-471">Este ensamblado agrega dos middleware a la aplicación (mientras se inicia) que proporcionan información de diagnóstico en:</span><span class="sxs-lookup"><span data-stu-id="ce83f-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="ce83f-472">Servicios registrados</span><span class="sxs-lookup"><span data-stu-id="ce83f-472">Registered services</span></span>
  * <span data-ttu-id="ce83f-473">Dirección (esquema, host, ruta de acceso base, ruta de acceso, cadena de consulta)</span><span class="sxs-lookup"><span data-stu-id="ce83f-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="ce83f-474">Conexión (dirección IP remota, puerto remoto, dirección IP local, puerto local, certificado de cliente)</span><span class="sxs-lookup"><span data-stu-id="ce83f-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="ce83f-475">Encabezados de solicitud</span><span class="sxs-lookup"><span data-stu-id="ce83f-475">Request headers</span></span>
  * <span data-ttu-id="ce83f-476">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="ce83f-476">Environment variables</span></span>

<span data-ttu-id="ce83f-477">Para ejecutar el ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ce83f-477">To run the sample:</span></span>

<span data-ttu-id="ce83f-478">**Activación desde un paquete NuGet**</span><span class="sxs-lookup"><span data-stu-id="ce83f-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="ce83f-479">Compile el paquete *HostingStartupPackage* con el comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="ce83f-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="ce83f-480">Agregue el nombre de ensamblado del paquete de *HostingStartupPackage* a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ce83f-481">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-481">Compile and run the app.</span></span> <span data-ttu-id="ce83f-482">Una referencia de paquete está presente en la aplicación mejorada (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="ce83f-483">El parámetro `<PropertyGroup>` del archivo del proyecto de la aplicación especifica el resultado del proyecto de paquete ( *../HostingStartupPackage/bin/Debug* ) como origen del paquete.</span><span class="sxs-lookup"><span data-stu-id="ce83f-483">A `<PropertyGroup>` in the app's project file specifies the package project's output ( *../HostingStartupPackage/bin/Debug* ) as a package source.</span></span> <span data-ttu-id="ce83f-484">Esto permite que la aplicación utilice el paquete sin cargar el paquete a [nuget.org](https://www.nuget.org/). Para obtener más información, vea las notas que encontrará en el archivo del proyecto de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="ce83f-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="ce83f-485">Observe que los valores de la clave de configuración del servicio representados por la página de índice coinciden con los valores establecidos por método `ServiceKeyInjection.Configure` del paquete.</span><span class="sxs-lookup"><span data-stu-id="ce83f-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ce83f-486">Si realiza cambios en el proyecto *HostingStartupPackage* y lo vuelve a compilar, borre las cachés del paquete NuGet local para asegurarse de que *HostingStartupApp* recibe el paquete actualizado y no un paquete en desuso de la caché local.</span><span class="sxs-lookup"><span data-stu-id="ce83f-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="ce83f-487">Para borrar las cachés de NuGet locales, ejecute el siguiente comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="ce83f-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="ce83f-488">**Activación desde una biblioteca de clases**</span><span class="sxs-lookup"><span data-stu-id="ce83f-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="ce83f-489">Compile la biblioteca de clases *HostingStartupLibrary* con el comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="ce83f-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="ce83f-490">Agregue el nombre del ensamblado de la biblioteca de clases *HostingStartupLibrary* a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ce83f-491">Implemente con *bin* -el ensamblado de la biblioteca de clases en la aplicación copiando el archivo *HostingStartupLibrary.dll* desde el resultado compilado de la biblioteca de aplicaciones en la carpeta *bin/Debug* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-491">*bin* -deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="ce83f-492">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-492">Compile and run the app.</span></span> <span data-ttu-id="ce83f-493">Un parámetro `<ItemGroup>` del archivo del proyecto de la aplicación hace referencia al ensamblado de la biblioteca de clases ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll* ) (una referencia de tiempo de compilación).</span><span class="sxs-lookup"><span data-stu-id="ce83f-493">An `<ItemGroup>` in the app's project file references the class library's assembly ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll* ) (a compile-time reference).</span></span> <span data-ttu-id="ce83f-494">Para obtener más información, vea las notas que encontrará en el archivo del proyecto de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="ce83f-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="ce83f-495">Observe que los valores de la clave de configuración del servicio representados por la página de índice coinciden con los valores establecidos por el método `ServiceKeyInjection.Configure` de la biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="ce83f-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ce83f-496">**Activación desde un ensamblado implementado por el almacén de tiempo de ejecución**</span><span class="sxs-lookup"><span data-stu-id="ce83f-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="ce83f-497">El proyecto *StartupDiagnostics* usa [PowerShell](/powershell/scripting/powershell-scripting) para modificar el archivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="ce83f-498">PowerShell se instala de forma predeterminada en Windows a partir de Windows 7 SP1 y Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="ce83f-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="ce83f-499">Para obtener PowerShell en otras plataformas, consulte [Instalación de varias versiones de PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="ce83f-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="ce83f-500">Ejecute el script *build.ps1* en la carpeta *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="ce83f-501">El script:</span><span class="sxs-lookup"><span data-stu-id="ce83f-501">The script:</span></span>
   * <span data-ttu-id="ce83f-502">Genera el paquete `StartupDiagnostics` en la carpeta *obj\packages*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="ce83f-503">Genera el almacén en tiempo de ejecución para `StartupDiagnostics` en la carpeta *store*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="ce83f-504">El comando `dotnet store` del script usa el [identificador de tiempo de ejecución (RID)](/dotnet/core/rid-catalog) `win7-x64` para un inicio de hospedaje implementado en Windows.</span><span class="sxs-lookup"><span data-stu-id="ce83f-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="ce83f-505">Para proporcionar el inicio de hospedaje para otro tiempo de ejecución, sustituya el RID correcto en la línea 37 del script.</span><span class="sxs-lookup"><span data-stu-id="ce83f-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="ce83f-506">El almacén en tiempo de ejecución de `StartupDiagnostics` se moverá más tarde al almacén en tiempo de ejecución del usuario o del sistema en la máquina donde se va a consumir el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="ce83f-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="ce83f-507">La ubicación de instalación del almacén en tiempo de ejecución del usuario para el ensamblado `StartupDiagnostics` es *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="ce83f-508">Genera el parámetro `additionalDeps` para `StartupDiagnostics` en la carpeta *additionalDeps*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="ce83f-509">Las dependencias adicionales se moverían posteriormente a las dependencias adicionales del usuario o del sistema.</span><span class="sxs-lookup"><span data-stu-id="ce83f-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="ce83f-510">La ubicación de instalación de las dependencias adicionales `StartupDiagnostics` del usuario es *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="ce83f-511">Coloca el archivo *deploy.ps1* en la carpeta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="ce83f-512">Ejecute el script *deploy.ps1* en la carpeta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="ce83f-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="ce83f-513">El script anexa:</span><span class="sxs-lookup"><span data-stu-id="ce83f-513">The script appends:</span></span>
   * <span data-ttu-id="ce83f-514">`StartupDiagnostics` a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="ce83f-515">La ruta de acceso de las dependencias de inicio de hospedaje (en la carpeta *deployment* del proyecto RuntimeStore) a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="ce83f-516">La ruta de acceso del almacén en tiempo de ejecución (en la carpeta *deployment* del proyecto RuntimeStore) a la variable de entorno `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="ce83f-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="ce83f-517">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="ce83f-517">Run the sample app.</span></span>
1. <span data-ttu-id="ce83f-518">Solicite al punto de conexión `/services` ver los servicios registrados de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ce83f-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="ce83f-519">Solicite al punto de conexión `/diag` ver la información de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="ce83f-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
