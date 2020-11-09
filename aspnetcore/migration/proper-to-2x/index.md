---
title: Migración de ASP.NET a ASP.NET Core
author: isaac2004
description: Obtenga instrucciones para migrar aplicaciones existentes de ASP.NET MVC o API web a ASP.NET Core.
ms.author: scaddie
ms.date: 10/18/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: migration/proper-to-2x/index
ms.openlocfilehash: 059ddc18d0c531efaba8aab916ddbb27b42b5e2c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053558"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="82b34-103">Migración de ASP.NET a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82b34-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="82b34-104">Por [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="82b34-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="82b34-105">Este artículo sirve de guía de referencia para migrar aplicaciones de ASP.NET a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82b34-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="82b34-106">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="82b34-106">Prerequisites</span></span>

[<span data-ttu-id="82b34-107">.NET Core SDK 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="82b34-107">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="82b34-108">Versiones de .NET Framework de destino</span><span class="sxs-lookup"><span data-stu-id="82b34-108">Target frameworks</span></span>

<span data-ttu-id="82b34-109">Los proyectos de ASP.NET Core proporcionan a los desarrolladores la flexibilidad de usar la versión .NET Core, .NET Framework o ambas.</span><span class="sxs-lookup"><span data-stu-id="82b34-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="82b34-110">Vea [Selección entre .NET Core y .NET Framework para aplicaciones de servidor](/dotnet/standard/choosing-core-framework-server) para determinar qué plataforma de destino es más adecuada.</span><span class="sxs-lookup"><span data-stu-id="82b34-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="82b34-111">Cuando se usa la versión .NET Framework, es necesario que los proyectos hagan referencia a paquetes de NuGet individuales.</span><span class="sxs-lookup"><span data-stu-id="82b34-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="82b34-112">Cuando se usa .NET Core, se pueden eliminar numerosas referencias explícitas del paquete gracias al [metapaquete](xref:fundamentals/metapackage-app) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82b34-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="82b34-113">Instale el metapaquete `Microsoft.AspNetCore.App` en el proyecto:</span><span class="sxs-lookup"><span data-stu-id="82b34-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="82b34-114">Cuando se usa el metapaquete, con la aplicación no se implementa ningún paquete al que se hace referencia en el metapaquete.</span><span class="sxs-lookup"><span data-stu-id="82b34-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="82b34-115">El almacén de tiempo de ejecución de .NET Core incluye estos activos que están compilados previamente para mejorar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="82b34-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="82b34-116">Consulte [Metapaquete Microsoft.AspNetCore.App para ASP.NET Core](xref:fundamentals/metapackage-app) para más información.</span><span class="sxs-lookup"><span data-stu-id="82b34-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="82b34-117">Diferencias en la estructura de proyecto</span><span class="sxs-lookup"><span data-stu-id="82b34-117">Project structure differences</span></span>

<span data-ttu-id="82b34-118">El formato de archivo *.csproj* se ha simplificado en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82b34-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="82b34-119">Estos son algunos cambios importantes:</span><span class="sxs-lookup"><span data-stu-id="82b34-119">Some notable changes include:</span></span>

- <span data-ttu-id="82b34-120">La inclusión explícita de archivos no es necesaria para que se consideren parte del proyecto.</span><span class="sxs-lookup"><span data-stu-id="82b34-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="82b34-121">Esto reduce el riesgo de conflictos al fusionar XML cuando se trabaja en equipos grandes.</span><span class="sxs-lookup"><span data-stu-id="82b34-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="82b34-122">No hay referencias de GUID a otros proyectos, lo cual mejora la legibilidad del archivo.</span><span class="sxs-lookup"><span data-stu-id="82b34-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="82b34-123">El archivo se puede editar sin descargarlo en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="82b34-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Edición de la opción de menú contextual CSPROJ en Visual Studio de 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="82b34-125">Reemplazo del archivo Global.asax</span><span class="sxs-lookup"><span data-stu-id="82b34-125">Global.asax file replacement</span></span>

<span data-ttu-id="82b34-126">ASP.NET Core introdujo un nuevo mecanismo para arrancar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="82b34-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="82b34-127">El punto de entrada para las aplicaciones ASP.NET es el archivo *Global.asax* .</span><span class="sxs-lookup"><span data-stu-id="82b34-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="82b34-128">En el archivo *Global.asax* se controlan tareas como la configuración de enrutamiento y los registros de filtro y de área.</span><span class="sxs-lookup"><span data-stu-id="82b34-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="82b34-129">Este enfoque acopla la aplicación y el servidor en el que está implementada de forma que interfiere con la implementación.</span><span class="sxs-lookup"><span data-stu-id="82b34-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="82b34-130">En un esfuerzo por desacoplar, se introdujo [OWIN](https://owin.org/) para ofrecer una forma más limpia de usar varios marcos de trabajo de manera conjunta.</span><span class="sxs-lookup"><span data-stu-id="82b34-130">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="82b34-131">OWIN proporciona una canalización para agregar solo los módulos necesarios.</span><span class="sxs-lookup"><span data-stu-id="82b34-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="82b34-132">El entorno de hospedaje toma una función de [inicio](xref:fundamentals/startup) para configurar servicios y la canalización de solicitud de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="82b34-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="82b34-133">`Startup` registra un conjunto de middleware en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="82b34-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="82b34-134">Para cada solicitud, la aplicación llama a cada uno de los componentes de middleware con el puntero principal de una lista vinculada a un conjunto de controladores existente.</span><span class="sxs-lookup"><span data-stu-id="82b34-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="82b34-135">Cada componente de middleware puede agregar uno o varios controladores a la canalización de control de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="82b34-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="82b34-136">Esto se consigue mediante la devolución de una referencia al controlador que ahora es el primero de la lista.</span><span class="sxs-lookup"><span data-stu-id="82b34-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="82b34-137">Cada controlador se encarga de recordar e invocar el controlador siguiente en la lista.</span><span class="sxs-lookup"><span data-stu-id="82b34-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="82b34-138">Con ASP.NET Core, el punto de entrada a una aplicación es `Startup` y ya no se tiene dependencia de *Global.asax* .</span><span class="sxs-lookup"><span data-stu-id="82b34-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax* .</span></span> <span data-ttu-id="82b34-139">Cuando utilice OWIN con .NET Framework, use algo parecido a lo siguiente como canalización:</span><span class="sxs-lookup"><span data-stu-id="82b34-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="82b34-140">Esto configura las rutas predeterminadas y tiene como valor predeterminado XmlSerialization a través de Json.</span><span class="sxs-lookup"><span data-stu-id="82b34-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="82b34-141">Agregue otro middleware a esta canalización según sea necesario (carga de servicios, opciones de configuración, archivos estáticos, etcétera).</span><span class="sxs-lookup"><span data-stu-id="82b34-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="82b34-142">ASP.NET Core usa un enfoque similar, pero no depende de OWIN para controlar la entrada.</span><span class="sxs-lookup"><span data-stu-id="82b34-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="82b34-143">En su lugar, se usa el método `Main` de *Program.cs* (similar a las aplicaciones de consola), y `Startup` se carga a través de ahí.</span><span class="sxs-lookup"><span data-stu-id="82b34-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="82b34-144">`Startup` debe incluir un método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="82b34-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="82b34-145">En `Configure`, agregue el middleware necesario a la canalización.</span><span class="sxs-lookup"><span data-stu-id="82b34-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="82b34-146">En el ejemplo siguiente (de la plantilla de sitio web predeterminada), los métodos de extensión configuran la canalización con compatibilidad para:</span><span class="sxs-lookup"><span data-stu-id="82b34-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="82b34-147">Páginas de error</span><span class="sxs-lookup"><span data-stu-id="82b34-147">Error pages</span></span>
- <span data-ttu-id="82b34-148">Seguridad de transporte estricta de HTTP</span><span class="sxs-lookup"><span data-stu-id="82b34-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="82b34-149">Redireccionamiento HTTP a HTTPS</span><span class="sxs-lookup"><span data-stu-id="82b34-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="82b34-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="82b34-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="82b34-151">El host y la aplicación se han desacoplado, lo que proporciona la flexibilidad de pasar a una plataforma diferente en el futuro.</span><span class="sxs-lookup"><span data-stu-id="82b34-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="82b34-152">Para acceder a referencias más detalladas sobre el inicio de ASP.NET Core y middleware, consulte [Inicio de la aplicación en ASP.NET Core](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="82b34-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="82b34-153">Almacenamiento de valores de configuración</span><span class="sxs-lookup"><span data-stu-id="82b34-153">Store configurations</span></span>

<span data-ttu-id="82b34-154">ASP.NET admite el almacenamiento de valores de configuración.</span><span class="sxs-lookup"><span data-stu-id="82b34-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="82b34-155">Estos valores de configuración se usan, por ejemplo, para admitir el entorno donde se implementan las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="82b34-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="82b34-156">Antes se solían almacenar todos los pares de clave y valor personalizados en la sección `<appSettings>` del archivo *Web.config* :</span><span class="sxs-lookup"><span data-stu-id="82b34-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="82b34-157">Las aplicaciones leen esta configuración mediante la colección `ConfigurationManager.AppSettings` en el espacio de nombres `System.Configuration`:</span><span class="sxs-lookup"><span data-stu-id="82b34-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="82b34-158">ASP.NET Core puede almacenar datos de configuración para la aplicación en cualquier archivo y cargarlos como parte del arranque de middleware.</span><span class="sxs-lookup"><span data-stu-id="82b34-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="82b34-159">El archivo predeterminado que se usa en las plantillas de proyecto es *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="82b34-159">The default file used in the project templates is *appsettings.json* :</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="82b34-160">La carga de este archivo en una instancia de `IConfiguration` dentro de la aplicación se realiza en *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="82b34-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs* :</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="82b34-161">La aplicación lee de `Configuration` para obtener la configuración:</span><span class="sxs-lookup"><span data-stu-id="82b34-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="82b34-162">Existen extensiones de este enfoque para lograr que el proceso sea más sólido, como el uso de [inserción de dependencias](xref:fundamentals/dependency-injection) para cargar un servicio con estos valores.</span><span class="sxs-lookup"><span data-stu-id="82b34-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="82b34-163">El enfoque de la inserción de dependencias proporciona un conjunto fuertemente tipado de objetos de configuración.</span><span class="sxs-lookup"><span data-stu-id="82b34-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="82b34-164">Para acceder a referencias más detalladas sobre la configuración de ASP.NET Core, consulte [Configuración en ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="82b34-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="82b34-165">Inserción de dependencias nativa</span><span class="sxs-lookup"><span data-stu-id="82b34-165">Native dependency injection</span></span>

<span data-ttu-id="82b34-166">Un objetivo importante al compilar aplicaciones grandes y escalables es lograr el acoplamiento flexible de los componentes y los servicios.</span><span class="sxs-lookup"><span data-stu-id="82b34-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="82b34-167">La [inserción de dependencias](xref:fundamentals/dependency-injection) es una técnica popular para lograrlo y se trata de un componente nativo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82b34-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="82b34-168">En las aplicaciones ASP.NET, los desarrolladores se sirven de una biblioteca de terceros para implementar la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="82b34-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="82b34-169">Una de estas bibliotecas es [Unity](https://github.com/unitycontainer/unity), suministrada por Microsoft Patterns & Practices.</span><span class="sxs-lookup"><span data-stu-id="82b34-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="82b34-170">Un ejemplo de configuración de la inserción de dependencias con Unity consiste en implementar `IDependencyResolver` que encapsula un `UnityContainer`:</span><span class="sxs-lookup"><span data-stu-id="82b34-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="82b34-171">Cree una instancia de `UnityContainer`, registre el servicio y establezca la resolución de dependencias de `HttpConfiguration` en la nueva instancia de `UnityResolver` para el contenedor:</span><span class="sxs-lookup"><span data-stu-id="82b34-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="82b34-172">Inserte `IProductRepository` cuando sea necesario:</span><span class="sxs-lookup"><span data-stu-id="82b34-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="82b34-173">Dado que la inserción de dependencia forma parte de ASP.NET Core, puede agregar el servicio en el método `ConfigureServices` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="82b34-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs* :</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="82b34-174">El repositorio se puede insertar en cualquier lugar, como ocurría con Unity.</span><span class="sxs-lookup"><span data-stu-id="82b34-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="82b34-175">Para más información sobre la inserción de dependencias, vea [Inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="82b34-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="82b34-176">Proporcionar archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="82b34-176">Serve static files</span></span>

<span data-ttu-id="82b34-177">Una parte importante del desarrollo web es la capacidad de trabajar con activos estáticos de cliente.</span><span class="sxs-lookup"><span data-stu-id="82b34-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="82b34-178">Los ejemplos más comunes de archivos estáticos son HTML, CSS, JavaScript e imágenes.</span><span class="sxs-lookup"><span data-stu-id="82b34-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="82b34-179">Estos archivos deben guardarse en la ubicación de publicación de la aplicación (o la red de entrega de contenido) y es necesario hacer referencia a ellos para que una solicitud los pueda cargar.</span><span class="sxs-lookup"><span data-stu-id="82b34-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="82b34-180">Este proceso ha cambiado en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82b34-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="82b34-181">En ASP.NET, los archivos estáticos se almacenan en directorios distintos y se hace referencia a ellos en las vistas.</span><span class="sxs-lookup"><span data-stu-id="82b34-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="82b34-182">En ASP.NET Core, los archivos estáticos se almacenan en la "raíz web" ( *&lt;raíz del contenido&gt;/wwwroot* ), a menos que se configuren de otra manera.</span><span class="sxs-lookup"><span data-stu-id="82b34-182">In ASP.NET Core, static files are stored in the "web root" ( *&lt;content root&gt;/wwwroot* ), unless configured otherwise.</span></span> <span data-ttu-id="82b34-183">Los archivos se cargan en la canalización de solicitud mediante la invocación del método de extensión `UseStaticFiles` desde `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="82b34-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="82b34-184">Si el destino es .NET Framework, debe instalarse el paquete de NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="82b34-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="82b34-185">Por ejemplo, el explorador puede acceder a un recurso de imagen en la carpeta *wwwroot/images* en una ubicación como `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="82b34-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="82b34-186">Para acceder a referencias más detalladas sobre cómo trabajar con archivos estáticos en ASP.NET Core, consulte [Archivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="82b34-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-no-loccookies"></a><span data-ttu-id="82b34-187">cookies de varios valores</span><span class="sxs-lookup"><span data-stu-id="82b34-187">Multi-value cookies</span></span>

<span data-ttu-id="82b34-188">[cookies](xref:System.Web.HttpCookie.Values) de varios valores no se admiten en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82b34-188">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="82b34-189">Cree una cookie por valor.</span><span class="sxs-lookup"><span data-stu-id="82b34-189">Create one cookie per value.</span></span>

## <a name="partial-app-migration"></a><span data-ttu-id="82b34-190">Migración parcial de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="82b34-190">Partial app migration</span></span>

<span data-ttu-id="82b34-191">Un enfoque para la migración parcial de aplicaciones consiste en crear una subaplicación de IIS y solo trasladar determinadas rutas de ASP.NET 4.x a ASP.NET Core, al tiempo que se conserva la estructura de la dirección URL de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="82b34-191">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="82b34-192">Por ejemplo, considere la estructura de dirección URL de la aplicación en el archivo *applicationHost.config* :</span><span class="sxs-lookup"><span data-stu-id="82b34-192">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

<span data-ttu-id="82b34-193">Estructura de directorios:</span><span class="sxs-lookup"><span data-stu-id="82b34-193">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="bind-and-input-formatters"></a><span data-ttu-id="82b34-194">[BIND] y formateadores de entrada</span><span class="sxs-lookup"><span data-stu-id="82b34-194">[BIND] and Input Formatters</span></span>

<span data-ttu-id="82b34-195">[Las versiones anteriores de ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) usaban el atributo `[Bind]` para protegerse frente a ataques de publicación excesiva.</span><span class="sxs-lookup"><span data-stu-id="82b34-195">[Previous versions of ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) used the `[Bind]` attribute to protect against overposting attacks.</span></span> <span data-ttu-id="82b34-196">Los [formateadores de entrada](xref:mvc/models/model-binding#input-formatters) funcionan de manera diferente en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82b34-196">[Input formatters](xref:mvc/models/model-binding#input-formatters) work differently in ASP.NET Core.</span></span> <span data-ttu-id="82b34-197">El atributo `[Bind]` ya no está diseñado para evitar la publicación excesiva cuando se usa con formateadores de entrada para analizar JSON o XML.</span><span class="sxs-lookup"><span data-stu-id="82b34-197">The `[Bind]` attribute is no longer designed to prevent overposting when used with input formatters to parse JSON or XML.</span></span> <span data-ttu-id="82b34-198">Estos atributos afectan al enlace de modelos cuando el origen de datos es un formulario de datos publicado con el tipo de contenido `x-www-form-urlencoded`.</span><span class="sxs-lookup"><span data-stu-id="82b34-198">These attributes affect model binding when the source of data is form data posted with the `x-www-form-urlencoded` content type.</span></span>

<span data-ttu-id="82b34-199">En el caso de las aplicaciones que envían información de JSON a los controladores y usan formateadores de entrada JSON para analizar los datos, se recomienda reemplazar el atributo `[Bind]` por un modelo de vista que coincida con las propiedades definidas por el atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="82b34-199">For apps that post JSON information to controllers and use JSON Input Formatters to parse the data, we recommend replacing the `[Bind]` attribute with a view model that matches the properties defined by the `[Bind]` attribute.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82b34-200">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="82b34-200">Additional resources</span></span>

- [<span data-ttu-id="82b34-201">Traslado a .NET Core: bibliotecas</span><span class="sxs-lookup"><span data-stu-id="82b34-201">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
