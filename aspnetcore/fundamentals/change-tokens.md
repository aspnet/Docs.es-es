---
title: Detección de cambios con tokens de cambio en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar tokens de cambio para realizar el seguimiento de los cambios.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
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
uid: fundamentals/change-tokens
ms.openlocfilehash: f52b210b33cd319e36570a1f731bb02a86189a96
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017485"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="1f10d-103">Detección de cambios con tokens de cambio en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f10d-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1f10d-104">Un *token de cambio* es un bloque de creación de bajo nivel y uso general que se usa para realizar el seguimiento de los cambios de estado.</span><span class="sxs-lookup"><span data-stu-id="1f10d-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="1f10d-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1f10d-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="1f10d-106">Interfaz IChangeToken</span><span class="sxs-lookup"><span data-stu-id="1f10d-106">IChangeToken interface</span></span>

<span data-ttu-id="1f10d-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="1f10d-108">`IChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1f10d-109">El paquete de NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se proporciona implícitamente con las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f10d-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="1f10d-110">`IChangeToken` tiene dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="1f10d-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="1f10d-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica si el token genera devoluciones de llamada de manera proactiva.</span><span class="sxs-lookup"><span data-stu-id="1f10d-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="1f10d-112">Si `ActiveChangedCallbacks` se establece en `false`, nunca se llama a una devolución de llamada y la aplicación debe sondear `HasChanged` en busca de cambios.</span><span class="sxs-lookup"><span data-stu-id="1f10d-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="1f10d-113">También es posible que un token nunca se cancele si no se producen cambios o si se elimina o deshabilita el agente de escucha de cambios subyacente.</span><span class="sxs-lookup"><span data-stu-id="1f10d-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="1f10d-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recibe un valor que indica si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="1f10d-115">La interfaz `IChangeToken` incluye el método [RegisterChangeCallback(Acción\<Object>, Objeto)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), que registra una devolución de llamada que se invoca cuando el token ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="1f10d-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="1f10d-116">`HasChanged` se debe establecer antes de que se invoque la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="1f10d-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="1f10d-117">Clase ChangeToken</span><span class="sxs-lookup"><span data-stu-id="1f10d-117">ChangeToken class</span></span>

<span data-ttu-id="1f10d-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> es una clase estática que se usa para propagar notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="1f10d-119">`ChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1f10d-120">El paquete de NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se proporciona implícitamente con las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f10d-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="1f10d-121">El método [ChangeToken.OnChange(Función\<IChangeToken>, Acción)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra una `Action` que se llama cada vez que cambia el token:</span><span class="sxs-lookup"><span data-stu-id="1f10d-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="1f10d-122">`Func<IChangeToken>` genera el token.</span><span class="sxs-lookup"><span data-stu-id="1f10d-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="1f10d-123">Se llama a `Action` cuando cambia el token.</span><span class="sxs-lookup"><span data-stu-id="1f10d-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="1f10d-124">La sobrecarga [ChangeToken.OnChange\<TState>(Función\<IChangeToken>, Acción\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) toma un parámetro `TState` adicional que se pasa a la `Action` de consumidor de token.</span><span class="sxs-lookup"><span data-stu-id="1f10d-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="1f10d-125">`OnChange` devuelve un valor de <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="1f10d-126">Al llamar a <xref:System.IDisposable.Dispose*> se detiene la escucha del token de futuras modificaciones y se liberan sus recursos.</span><span class="sxs-lookup"><span data-stu-id="1f10d-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="1f10d-127">Ejemplos de uso de tokens de cambio en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f10d-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="1f10d-128">Los tokens de cambio se usan en áreas principales de ASP.NET Core para supervisar los cambios en los objetos:</span><span class="sxs-lookup"><span data-stu-id="1f10d-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="1f10d-129">Para supervisar los cambios en los archivos, el método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crea un `IChangeToken` para los archivos especificados o la carpeta que se va a supervisar.</span><span class="sxs-lookup"><span data-stu-id="1f10d-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="1f10d-130">Se pueden agregar tokens `IChangeToken` a las entradas de caché para desencadenar expulsiones de caché al producirse un cambio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="1f10d-131">Para los cambios de `TOptions`, la implementación predeterminada <xref:Microsoft.Extensions.Options.OptionsMonitor`1> de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tiene una sobrecarga que acepta una o varias instancias de <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="1f10d-132">Cada instancia devuelve un `IChangeToken` para registrar una devolución de llamada de notificación de cambio a fin de realizar el seguimiento de los cambios en las opciones.</span><span class="sxs-lookup"><span data-stu-id="1f10d-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="1f10d-133">Supervisión de los cambios de configuración</span><span class="sxs-lookup"><span data-stu-id="1f10d-133">Monitor for configuration changes</span></span>

<span data-ttu-id="1f10d-134">De forma predeterminada, las plantillas de ASP.NET Core usan [archivos de configuración de JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* y *appsettings.Production.json*) para cargar parámetros de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f10d-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="1f10d-135">Estos archivos se configuran mediante el método de extensión [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) en <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que acepta un parámetro `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="1f10d-136">`reloadOnChange` indica si la configuración se debe recargar en los cambios de archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="1f10d-137">Esta configuración aparece en el método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> de <xref:Microsoft.Extensions.Hosting.Host>:</span><span class="sxs-lookup"><span data-stu-id="1f10d-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="1f10d-138">La configuración basada en archivo se presenta por medio de <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="1f10d-139">`FileConfigurationSource` use <xref:Microsoft.Extensions.FileProviders.IFileProvider> para supervisar los archivos.</span><span class="sxs-lookup"><span data-stu-id="1f10d-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="1f10d-140"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona `IFileMonitor` de manera predeterminada, que usa <xref:System.IO.FileSystemWatcher> para supervisar los cambios del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="1f10d-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="1f10d-141">En la aplicación de ejemplo se muestran dos implementaciones para supervisar los cambios de configuración.</span><span class="sxs-lookup"><span data-stu-id="1f10d-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="1f10d-142">Si se modifica cualquiera de los archivos *appsettings*, ambas implementaciones de supervisión de los archivos ejecutan un código personalizado&mdash;la aplicación de ejemplo escribe un mensaje en la consola.</span><span class="sxs-lookup"><span data-stu-id="1f10d-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="1f10d-143">El `FileSystemWatcher` de un archivo de configuración puede desencadenar varias devoluciones de llamada de token para un único cambio del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="1f10d-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="1f10d-144">Para garantizar que el código personalizado se ejecute solo una vez cuando se desencadenan varias devoluciones de llamada de token, la implementación del ejemplo comprueba los hashes de archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="1f10d-145">El ejemplo usa el algoritmo hash seguro 1.</span><span class="sxs-lookup"><span data-stu-id="1f10d-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="1f10d-146">Se implementa un reintento con una interrupción exponencial.</span><span class="sxs-lookup"><span data-stu-id="1f10d-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="1f10d-147">El reintento aparece porque se puede producir un bloqueo de archivos que impida temporalmente calcular un hash nuevo en un archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="1f10d-148">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f10d-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="1f10d-149">Token de cambio de inicio simple</span><span class="sxs-lookup"><span data-stu-id="1f10d-149">Simple startup change token</span></span>

<span data-ttu-id="1f10d-150">Registre una devolución de llamada de `Action` del consumidor de token para las notificaciones de cambio en el token de recarga de configuración.</span><span class="sxs-lookup"><span data-stu-id="1f10d-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="1f10d-151">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1f10d-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="1f10d-152">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="1f10d-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="1f10d-153">La devolución de llamada es el método `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="1f10d-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="1f10d-154">El `state` de la devolución de llamada se usa para pasar el `IWebHostEnvironment`, que resulta útil para especificar el archivo de configuración *appsettings* que se va a supervisar (por ejemplo, *appsettings.Development.json* cuando se está en el entorno de desarrollo).</span><span class="sxs-lookup"><span data-stu-id="1f10d-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="1f10d-155">Se usa el hash de archivo para evitar que se ejecute varias veces la instrucción `WriteConsole` debido a varias devoluciones de llamada de token cuando el archivo de configuración solo ha cambiado una vez.</span><span class="sxs-lookup"><span data-stu-id="1f10d-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="1f10d-156">Este sistema se ejecuta siempre que la aplicación esté en ejecución y el usuario no lo puede deshabilitar.</span><span class="sxs-lookup"><span data-stu-id="1f10d-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="1f10d-157">Supervisión de los cambios de configuración como servicio</span><span class="sxs-lookup"><span data-stu-id="1f10d-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="1f10d-158">En el ejemplo se implementa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1f10d-158">The sample implements:</span></span>

* <span data-ttu-id="1f10d-159">La supervisión del token de inicio básico.</span><span class="sxs-lookup"><span data-stu-id="1f10d-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="1f10d-160">La supervisión como servicio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-160">Monitoring as a service.</span></span>
* <span data-ttu-id="1f10d-161">Un mecanismo para habilitar y deshabilitar la supervisión.</span><span class="sxs-lookup"><span data-stu-id="1f10d-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="1f10d-162">El ejemplo establece una interfaz `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="1f10d-163">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f10d-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="1f10d-164">El constructor de la clase implementada, `ConfigurationMonitor`, registra una devolución de llamada para las notificaciones de cambio:</span><span class="sxs-lookup"><span data-stu-id="1f10d-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="1f10d-165">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="1f10d-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="1f10d-166">`InvokeChanged` es el método de devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="1f10d-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="1f10d-167">El elemento `state` de esta instancia es una referencia a la instancia de `IConfigurationMonitor` que se usa para tener acceso al estado de supervisión.</span><span class="sxs-lookup"><span data-stu-id="1f10d-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="1f10d-168">Se usan dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="1f10d-168">Two properties are used:</span></span>

* <span data-ttu-id="1f10d-169">`MonitoringEnabled`: indica si la devolución de llamada debe ejecutar su código personalizado.</span><span class="sxs-lookup"><span data-stu-id="1f10d-169">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="1f10d-170">`CurrentState`: describe el estado de supervisión actual para su uso en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="1f10d-170">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="1f10d-171">El método `InvokeChanged` es similar al enfoque anterior, excepto en que:</span><span class="sxs-lookup"><span data-stu-id="1f10d-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="1f10d-172">No ejecuta su código, a menos que `MonitoringEnabled` sea `true`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="1f10d-173">Genera el `state` actual en su salida de `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="1f10d-174">Una instancia de `ConfigurationMonitor` se registra como servicio en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1f10d-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="1f10d-175">En la página Index se ofrece al usuario el control sobre la supervisión de la configuración.</span><span class="sxs-lookup"><span data-stu-id="1f10d-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="1f10d-176">La instancia de `IConfigurationMonitor` se inserta en `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="1f10d-177">*Páginas/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f10d-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="1f10d-178">El monitor de configuración (`_monitor`) se usa para habilitar o deshabilitar la supervisión y establecer el estado actual de los comentarios de la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="1f10d-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="1f10d-179">Cuando se desencadena `OnPostStartMonitoring`, se habilita la supervisión y se borra el estado actual.</span><span class="sxs-lookup"><span data-stu-id="1f10d-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="1f10d-180">Cuando se desencadena `OnPostStopMonitoring`, se deshabilita la supervisión y se establece el estado para reflejar que no se está realizando la supervisión.</span><span class="sxs-lookup"><span data-stu-id="1f10d-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="1f10d-181">Los botones de la interfaz de usuario habilitan y deshabilitan la supervisión.</span><span class="sxs-lookup"><span data-stu-id="1f10d-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="1f10d-182">*Páginas/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1f10d-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="1f10d-183">Supervisión de los cambios de archivos en caché</span><span class="sxs-lookup"><span data-stu-id="1f10d-183">Monitor cached file changes</span></span>

<span data-ttu-id="1f10d-184">El contenido de los archivos se puede almacenar en caché en memoria mediante <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="1f10d-185">El almacenamiento en caché en memoria se describe en el tema [Cache in-memory](xref:performance/caching/memory) (Almacenamiento en caché en memoria).</span><span class="sxs-lookup"><span data-stu-id="1f10d-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="1f10d-186">Sin realizar pasos adicionales, como la implementación que se describe a continuación, si los datos de origen cambian, se devuelven datos *obsoletos* (no actualizados) de la caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="1f10d-187">Por ejemplo, si no se tiene en cuenta el estado de un archivo de origen en caché cuando se renueva un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), se pueden crear datos de archivo en caché obsoletos.</span><span class="sxs-lookup"><span data-stu-id="1f10d-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="1f10d-188">En cada solicitud de los datos se renueva el período de vencimiento variable, pero el archivo nunca se vuelve a cargar en la caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="1f10d-189">Las características de la aplicación que usen el contenido en caché del archivo están sujetas a la posible recepción de contenido obsoleto.</span><span class="sxs-lookup"><span data-stu-id="1f10d-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="1f10d-190">El uso de tokens de cambio en un escenario de almacenamiento en caché de archivos evita la presencia de contenido de archivos obsoletos en la caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="1f10d-191">En la aplicación de ejemplo se muestra una implementación del enfoque.</span><span class="sxs-lookup"><span data-stu-id="1f10d-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="1f10d-192">En el ejemplo se usa `GetFileContent` para:</span><span class="sxs-lookup"><span data-stu-id="1f10d-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="1f10d-193">Devolver el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-193">Return file content.</span></span>
* <span data-ttu-id="1f10d-194">Implementar un algoritmo de reintento con interrupción exponencial para casos en los que un bloqueo de archivo impide temporalmente leer un archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="1f10d-195">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f10d-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="1f10d-196">Se crea un `FileService` para administrar las búsquedas de archivos en caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="1f10d-197">La llamada al método `GetFileContent` del servicio intenta obtener el contenido de archivo de la caché en memoria y devolverlo al autor de la llamada (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="1f10d-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="1f10d-198">Si el contenido en caché no se encuentra mediante la clave de caché, se realizan las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="1f10d-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="1f10d-199">El contenido del archivo se obtiene mediante `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="1f10d-200">Se obtiene un token de cambio del proveedor de archivos con [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="1f10d-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="1f10d-201">La devolución de llamada del token se desencadena cuando se modifica el archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="1f10d-202">El contenido del archivo se almacena en caché con un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="1f10d-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="1f10d-203">El token de cambio se adjunta con [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para expulsar la entrada de caché si el archivo cambia mientras está almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="1f10d-204">En el ejemplo siguiente, los archivos se almacenan en la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f10d-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="1f10d-205">`IWebHostEnvironment.ContentRootFileProvider` se usa para obtener un <xref:Microsoft.Extensions.FileProviders.IFileProvider> que apunte a `IWebHostEnvironment.ContentRootPath` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f10d-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="1f10d-206">La `filePath` se obtiene con [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="1f10d-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="1f10d-207">El `FileService` se registra en el contenedor de servicios junto con el servicio de almacenamiento en caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="1f10d-208">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1f10d-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="1f10d-209">El modelo de página carga el contenido del archivo mediante el servicio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="1f10d-210">En el método `OnGet` de la página de índice (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="1f10d-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="1f10d-211">Clase CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="1f10d-211">CompositeChangeToken class</span></span>

<span data-ttu-id="1f10d-212">Para representar una o varias instancias de `IChangeToken` en un solo objeto, use la clase <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="1f10d-213">En el token compuesto, `HasChanged` notifica `true` si algún token representado `HasChanged` es `true`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="1f10d-214">En el token compuesto, `ActiveChangeCallbacks` notifica `true` si algún token representado `ActiveChangeCallbacks` es `true`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="1f10d-215">Si se producen varios eventos de cambio simultáneos, la devolución de llamada de cambio compuesto se invoca una vez.</span><span class="sxs-lookup"><span data-stu-id="1f10d-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1f10d-216">Un *token de cambio* es un bloque de creación de bajo nivel y uso general que se usa para realizar el seguimiento de los cambios de estado.</span><span class="sxs-lookup"><span data-stu-id="1f10d-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="1f10d-217">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1f10d-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="1f10d-218">Interfaz IChangeToken</span><span class="sxs-lookup"><span data-stu-id="1f10d-218">IChangeToken interface</span></span>

<span data-ttu-id="1f10d-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="1f10d-220">`IChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1f10d-221">En el caso de las aplicaciones que no usan el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), cree una referencia al paquete NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="1f10d-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="1f10d-222">`IChangeToken` tiene dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="1f10d-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="1f10d-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica si el token genera devoluciones de llamada de manera proactiva.</span><span class="sxs-lookup"><span data-stu-id="1f10d-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="1f10d-224">Si `ActiveChangedCallbacks` se establece en `false`, nunca se llama a una devolución de llamada y la aplicación debe sondear `HasChanged` en busca de cambios.</span><span class="sxs-lookup"><span data-stu-id="1f10d-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="1f10d-225">También es posible que un token nunca se cancele si no se producen cambios o si se elimina o deshabilita el agente de escucha de cambios subyacente.</span><span class="sxs-lookup"><span data-stu-id="1f10d-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="1f10d-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recibe un valor que indica si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="1f10d-227">La interfaz `IChangeToken` incluye el método [RegisterChangeCallback(Acción\<Object>, Objeto)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), que registra una devolución de llamada que se invoca cuando el token ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="1f10d-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="1f10d-228">`HasChanged` se debe establecer antes de que se invoque la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="1f10d-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="1f10d-229">Clase ChangeToken</span><span class="sxs-lookup"><span data-stu-id="1f10d-229">ChangeToken class</span></span>

<span data-ttu-id="1f10d-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> es una clase estática que se usa para propagar notificaciones que indican que se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="1f10d-231">`ChangeToken` reside en el espacio de nombres <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1f10d-232">En el caso de las aplicaciones que no usan el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), cree una referencia al paquete NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="1f10d-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="1f10d-233">El método [ChangeToken.OnChange(Función\<IChangeToken>, Acción)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra una `Action` que se llama cada vez que cambia el token:</span><span class="sxs-lookup"><span data-stu-id="1f10d-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="1f10d-234">`Func<IChangeToken>` genera el token.</span><span class="sxs-lookup"><span data-stu-id="1f10d-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="1f10d-235">Se llama a `Action` cuando cambia el token.</span><span class="sxs-lookup"><span data-stu-id="1f10d-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="1f10d-236">La sobrecarga [ChangeToken.OnChange\<TState>(Función\<IChangeToken>, Acción\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) toma un parámetro `TState` adicional que se pasa a la `Action` de consumidor de token.</span><span class="sxs-lookup"><span data-stu-id="1f10d-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="1f10d-237">`OnChange` devuelve un valor de <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="1f10d-238">Al llamar a <xref:System.IDisposable.Dispose*> se detiene la escucha del token de futuras modificaciones y se liberan sus recursos.</span><span class="sxs-lookup"><span data-stu-id="1f10d-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="1f10d-239">Ejemplos de uso de tokens de cambio en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f10d-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="1f10d-240">Los tokens de cambio se usan en áreas principales de ASP.NET Core para supervisar los cambios en los objetos:</span><span class="sxs-lookup"><span data-stu-id="1f10d-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="1f10d-241">Para supervisar los cambios en los archivos, el método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crea un `IChangeToken` para los archivos especificados o la carpeta que se va a supervisar.</span><span class="sxs-lookup"><span data-stu-id="1f10d-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="1f10d-242">Se pueden agregar tokens `IChangeToken` a las entradas de caché para desencadenar expulsiones de caché al producirse un cambio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="1f10d-243">Para los cambios de `TOptions`, la implementación predeterminada <xref:Microsoft.Extensions.Options.OptionsMonitor`1> de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tiene una sobrecarga que acepta una o varias instancias de <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="1f10d-244">Cada instancia devuelve un `IChangeToken` para registrar una devolución de llamada de notificación de cambio a fin de realizar el seguimiento de los cambios en las opciones.</span><span class="sxs-lookup"><span data-stu-id="1f10d-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="1f10d-245">Supervisión de los cambios de configuración</span><span class="sxs-lookup"><span data-stu-id="1f10d-245">Monitor for configuration changes</span></span>

<span data-ttu-id="1f10d-246">De forma predeterminada, las plantillas de ASP.NET Core usan [archivos de configuración de JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* y *appsettings.Production.json*) para cargar parámetros de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f10d-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="1f10d-247">Estos archivos se configuran mediante el método de extensión [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) en <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que acepta un parámetro `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="1f10d-248">`reloadOnChange` indica si la configuración se debe recargar en los cambios de archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="1f10d-249">Esta configuración aparece en el método <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> de <xref:Microsoft.AspNetCore.WebHost>:</span><span class="sxs-lookup"><span data-stu-id="1f10d-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="1f10d-250">La configuración basada en archivo se presenta por medio de <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="1f10d-251">`FileConfigurationSource` use <xref:Microsoft.Extensions.FileProviders.IFileProvider> para supervisar los archivos.</span><span class="sxs-lookup"><span data-stu-id="1f10d-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="1f10d-252"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona `IFileMonitor` de manera predeterminada, que usa <xref:System.IO.FileSystemWatcher> para supervisar los cambios del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="1f10d-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="1f10d-253">En la aplicación de ejemplo se muestran dos implementaciones para supervisar los cambios de configuración.</span><span class="sxs-lookup"><span data-stu-id="1f10d-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="1f10d-254">Si se modifica cualquiera de los archivos *appsettings*, ambas implementaciones de supervisión de los archivos ejecutan un código personalizado&mdash;la aplicación de ejemplo escribe un mensaje en la consola.</span><span class="sxs-lookup"><span data-stu-id="1f10d-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="1f10d-255">El `FileSystemWatcher` de un archivo de configuración puede desencadenar varias devoluciones de llamada de token para un único cambio del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="1f10d-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="1f10d-256">Para garantizar que el código personalizado se ejecute solo una vez cuando se desencadenan varias devoluciones de llamada de token, la implementación del ejemplo comprueba los hashes de archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="1f10d-257">El ejemplo usa el algoritmo hash seguro 1.</span><span class="sxs-lookup"><span data-stu-id="1f10d-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="1f10d-258">Se implementa un reintento con una interrupción exponencial.</span><span class="sxs-lookup"><span data-stu-id="1f10d-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="1f10d-259">El reintento aparece porque se puede producir un bloqueo de archivos que impida temporalmente calcular un hash nuevo en un archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="1f10d-260">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f10d-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="1f10d-261">Token de cambio de inicio simple</span><span class="sxs-lookup"><span data-stu-id="1f10d-261">Simple startup change token</span></span>

<span data-ttu-id="1f10d-262">Registre una devolución de llamada de `Action` del consumidor de token para las notificaciones de cambio en el token de recarga de configuración.</span><span class="sxs-lookup"><span data-stu-id="1f10d-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="1f10d-263">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1f10d-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="1f10d-264">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="1f10d-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="1f10d-265">La devolución de llamada es el método `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="1f10d-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="1f10d-266">El `state` de la devolución de llamada se usa para pasar el `IHostingEnvironment`, que resulta útil para especificar el archivo de configuración *appsettings* que se va a supervisar (por ejemplo, *appsettings.Development.json* cuando se está en el entorno de desarrollo).</span><span class="sxs-lookup"><span data-stu-id="1f10d-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="1f10d-267">Se usa el hash de archivo para evitar que se ejecute varias veces la instrucción `WriteConsole` debido a varias devoluciones de llamada de token cuando el archivo de configuración solo ha cambiado una vez.</span><span class="sxs-lookup"><span data-stu-id="1f10d-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="1f10d-268">Este sistema se ejecuta siempre que la aplicación esté en ejecución y el usuario no lo puede deshabilitar.</span><span class="sxs-lookup"><span data-stu-id="1f10d-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="1f10d-269">Supervisión de los cambios de configuración como servicio</span><span class="sxs-lookup"><span data-stu-id="1f10d-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="1f10d-270">En el ejemplo se implementa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1f10d-270">The sample implements:</span></span>

* <span data-ttu-id="1f10d-271">La supervisión del token de inicio básico.</span><span class="sxs-lookup"><span data-stu-id="1f10d-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="1f10d-272">La supervisión como servicio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-272">Monitoring as a service.</span></span>
* <span data-ttu-id="1f10d-273">Un mecanismo para habilitar y deshabilitar la supervisión.</span><span class="sxs-lookup"><span data-stu-id="1f10d-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="1f10d-274">El ejemplo establece una interfaz `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="1f10d-275">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f10d-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="1f10d-276">El constructor de la clase implementada, `ConfigurationMonitor`, registra una devolución de llamada para las notificaciones de cambio:</span><span class="sxs-lookup"><span data-stu-id="1f10d-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="1f10d-277">`config.GetReloadToken()` proporciona el token.</span><span class="sxs-lookup"><span data-stu-id="1f10d-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="1f10d-278">`InvokeChanged` es el método de devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="1f10d-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="1f10d-279">El elemento `state` de esta instancia es una referencia a la instancia de `IConfigurationMonitor` que se usa para tener acceso al estado de supervisión.</span><span class="sxs-lookup"><span data-stu-id="1f10d-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="1f10d-280">Se usan dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="1f10d-280">Two properties are used:</span></span>

* <span data-ttu-id="1f10d-281">`MonitoringEnabled`: indica si la devolución de llamada debe ejecutar su código personalizado.</span><span class="sxs-lookup"><span data-stu-id="1f10d-281">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="1f10d-282">`CurrentState`: describe el estado de supervisión actual para su uso en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="1f10d-282">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="1f10d-283">El método `InvokeChanged` es similar al enfoque anterior, excepto en que:</span><span class="sxs-lookup"><span data-stu-id="1f10d-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="1f10d-284">No ejecuta su código, a menos que `MonitoringEnabled` sea `true`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="1f10d-285">Genera el `state` actual en su salida de `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="1f10d-286">Una instancia de `ConfigurationMonitor` se registra como servicio en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1f10d-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="1f10d-287">En la página Index se ofrece al usuario el control sobre la supervisión de la configuración.</span><span class="sxs-lookup"><span data-stu-id="1f10d-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="1f10d-288">La instancia de `IConfigurationMonitor` se inserta en `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="1f10d-289">*Páginas/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f10d-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="1f10d-290">El monitor de configuración (`_monitor`) se usa para habilitar o deshabilitar la supervisión y establecer el estado actual de los comentarios de la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="1f10d-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="1f10d-291">Cuando se desencadena `OnPostStartMonitoring`, se habilita la supervisión y se borra el estado actual.</span><span class="sxs-lookup"><span data-stu-id="1f10d-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="1f10d-292">Cuando se desencadena `OnPostStopMonitoring`, se deshabilita la supervisión y se establece el estado para reflejar que no se está realizando la supervisión.</span><span class="sxs-lookup"><span data-stu-id="1f10d-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="1f10d-293">Los botones de la interfaz de usuario habilitan y deshabilitan la supervisión.</span><span class="sxs-lookup"><span data-stu-id="1f10d-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="1f10d-294">*Páginas/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1f10d-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="1f10d-295">Supervisión de los cambios de archivos en caché</span><span class="sxs-lookup"><span data-stu-id="1f10d-295">Monitor cached file changes</span></span>

<span data-ttu-id="1f10d-296">El contenido de los archivos se puede almacenar en caché en memoria mediante <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="1f10d-297">El almacenamiento en caché en memoria se describe en el tema [Cache in-memory](xref:performance/caching/memory) (Almacenamiento en caché en memoria).</span><span class="sxs-lookup"><span data-stu-id="1f10d-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="1f10d-298">Sin realizar pasos adicionales, como la implementación que se describe a continuación, si los datos de origen cambian, se devuelven datos *obsoletos* (no actualizados) de la caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="1f10d-299">Por ejemplo, si no se tiene en cuenta el estado de un archivo de origen en caché cuando se renueva un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration), se pueden crear datos de archivo en caché obsoletos.</span><span class="sxs-lookup"><span data-stu-id="1f10d-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="1f10d-300">En cada solicitud de los datos se renueva el período de vencimiento variable, pero el archivo nunca se vuelve a cargar en la caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="1f10d-301">Las características de la aplicación que usen el contenido en caché del archivo están sujetas a la posible recepción de contenido obsoleto.</span><span class="sxs-lookup"><span data-stu-id="1f10d-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="1f10d-302">El uso de tokens de cambio en un escenario de almacenamiento en caché de archivos evita la presencia de contenido de archivos obsoletos en la caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="1f10d-303">En la aplicación de ejemplo se muestra una implementación del enfoque.</span><span class="sxs-lookup"><span data-stu-id="1f10d-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="1f10d-304">En el ejemplo se usa `GetFileContent` para:</span><span class="sxs-lookup"><span data-stu-id="1f10d-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="1f10d-305">Devolver el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-305">Return file content.</span></span>
* <span data-ttu-id="1f10d-306">Implementar un algoritmo de reintento con interrupción exponencial para casos en los que un bloqueo de archivo impide temporalmente leer un archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="1f10d-307">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f10d-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="1f10d-308">Se crea un `FileService` para administrar las búsquedas de archivos en caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="1f10d-309">La llamada al método `GetFileContent` del servicio intenta obtener el contenido de archivo de la caché en memoria y devolverlo al autor de la llamada (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="1f10d-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="1f10d-310">Si el contenido en caché no se encuentra mediante la clave de caché, se realizan las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="1f10d-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="1f10d-311">El contenido del archivo se obtiene mediante `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="1f10d-312">Se obtiene un token de cambio del proveedor de archivos con [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="1f10d-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="1f10d-313">La devolución de llamada del token se desencadena cuando se modifica el archivo.</span><span class="sxs-lookup"><span data-stu-id="1f10d-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="1f10d-314">El contenido del archivo se almacena en caché con un período de [vencimiento variable](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="1f10d-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="1f10d-315">El token de cambio se adjunta con [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para expulsar la entrada de caché si el archivo cambia mientras está almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="1f10d-316">En el ejemplo siguiente, los archivos se almacenan en la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f10d-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="1f10d-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se usa para obtener un elemento <xref:Microsoft.Extensions.FileProviders.IFileProvider> que apunta a la <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f10d-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="1f10d-318">La `filePath` se obtiene con [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="1f10d-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="1f10d-319">El `FileService` se registra en el contenedor de servicios junto con el servicio de almacenamiento en caché.</span><span class="sxs-lookup"><span data-stu-id="1f10d-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="1f10d-320">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1f10d-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="1f10d-321">El modelo de página carga el contenido del archivo mediante el servicio.</span><span class="sxs-lookup"><span data-stu-id="1f10d-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="1f10d-322">En el método `OnGet` de la página de índice (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="1f10d-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="1f10d-323">Clase CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="1f10d-323">CompositeChangeToken class</span></span>

<span data-ttu-id="1f10d-324">Para representar una o varias instancias de `IChangeToken` en un solo objeto, use la clase <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="1f10d-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="1f10d-325">En el token compuesto, `HasChanged` notifica `true` si algún token representado `HasChanged` es `true`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="1f10d-326">En el token compuesto, `ActiveChangeCallbacks` notifica `true` si algún token representado `ActiveChangeCallbacks` es `true`.</span><span class="sxs-lookup"><span data-stu-id="1f10d-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="1f10d-327">Si se producen varios eventos de cambio simultáneos, la devolución de llamada de cambio compuesto se invoca una vez.</span><span class="sxs-lookup"><span data-stu-id="1f10d-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1f10d-328">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1f10d-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
