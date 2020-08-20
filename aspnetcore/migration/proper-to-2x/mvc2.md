---
title: Migración de ASP.NET a ASP.NET Core 2.0
author: isaac2004
description: Reciba una guía para migrar aplicaciones de ASP.NET MVC o Web API existentes a ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
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
uid: migration/mvc2
ms.openlocfilehash: bd2c33d35a3433532b48f6615a81adac8d03b9ee
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634545"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="99f88-103">Migración de ASP.NET a ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="99f88-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="99f88-104">Por [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="99f88-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="99f88-105">Este artículo sirve de guía de referencia para migrar aplicaciones de ASP.NET a ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="99f88-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="99f88-106">Prerrequisitos</span><span class="sxs-lookup"><span data-stu-id="99f88-106">Prerequisites</span></span>

<span data-ttu-id="99f88-107">Instale **una** de las siguientes opciones desde [descargas de .net: Windows](https://dotnet.microsoft.com/download):</span><span class="sxs-lookup"><span data-stu-id="99f88-107">Install **one** of the following from [.NET Downloads: Windows](https://dotnet.microsoft.com/download):</span></span>

* <span data-ttu-id="99f88-108">SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="99f88-108">.NET Core SDK</span></span>
* <span data-ttu-id="99f88-109">Visual Studio para Windows</span><span class="sxs-lookup"><span data-stu-id="99f88-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="99f88-110">Carga de trabajo de **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="99f88-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="99f88-111">Carga de trabajo **Desarrollo multiplataforma de .NET Core**</span><span class="sxs-lookup"><span data-stu-id="99f88-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="99f88-112">Versiones de .NET Framework de destino</span><span class="sxs-lookup"><span data-stu-id="99f88-112">Target frameworks</span></span>

<span data-ttu-id="99f88-113">Los proyectos de ASP.NET Core 2.0 proporcionan a los desarrolladores la flexibilidad de usar la versión .NET Core, .NET Framework o ambas.</span><span class="sxs-lookup"><span data-stu-id="99f88-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="99f88-114">Vea [Selección entre .NET Core y .NET Framework para aplicaciones de servidor](/dotnet/standard/choosing-core-framework-server) para determinar qué plataforma de destino es más adecuada.</span><span class="sxs-lookup"><span data-stu-id="99f88-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="99f88-115">Cuando se usa la versión .NET Framework, es necesario que los proyectos hagan referencia a paquetes de NuGet individuales.</span><span class="sxs-lookup"><span data-stu-id="99f88-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="99f88-116">Cuando se usa .NET Core, se pueden eliminar numerosas referencias del paquete explícitas, gracias al [metapaquete](xref:fundamentals/metapackage) de ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="99f88-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="99f88-117">Instale el metapaquete `Microsoft.AspNetCore.All` en el proyecto:</span><span class="sxs-lookup"><span data-stu-id="99f88-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="99f88-118">Cuando se usa el metapaquete, con la aplicación no se implementa ningún paquete al que se hace referencia en el metapaquete.</span><span class="sxs-lookup"><span data-stu-id="99f88-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="99f88-119">El almacén de tiempo de ejecución de .NET Core incluye estos activos que están compilados previamente para mejorar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="99f88-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="99f88-120">Vea <xref:fundamentals/metapackage> para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="99f88-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="99f88-121">Diferencias en la estructura de proyecto</span><span class="sxs-lookup"><span data-stu-id="99f88-121">Project structure differences</span></span>

<span data-ttu-id="99f88-122">El formato de archivo *.csproj* se ha simplificado en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99f88-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="99f88-123">Estos son algunos cambios importantes:</span><span class="sxs-lookup"><span data-stu-id="99f88-123">Some notable changes include:</span></span>

* <span data-ttu-id="99f88-124">La inclusión explícita de archivos no es necesaria para que se consideren parte del proyecto.</span><span class="sxs-lookup"><span data-stu-id="99f88-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="99f88-125">Esto reduce el riesgo de conflictos al fusionar XML cuando se trabaja en equipos grandes.</span><span class="sxs-lookup"><span data-stu-id="99f88-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="99f88-126">No hay referencias de GUID a otros proyectos, lo cual mejora la legibilidad del archivo.</span><span class="sxs-lookup"><span data-stu-id="99f88-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="99f88-127">El archivo se puede editar sin descargarlo en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="99f88-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Edición de la opción de menú contextual CSPROJ en Visual Studio de 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="99f88-129">Reemplazo del archivo Global.asax</span><span class="sxs-lookup"><span data-stu-id="99f88-129">Global.asax file replacement</span></span>

<span data-ttu-id="99f88-130">ASP.NET Core introdujo un nuevo mecanismo para arrancar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="99f88-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="99f88-131">El punto de entrada para las aplicaciones ASP.NET es el archivo *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="99f88-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="99f88-132">En el archivo *Global.asax* se controlan tareas como la configuración de enrutamiento y los registros de filtro y de área.</span><span class="sxs-lookup"><span data-stu-id="99f88-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="99f88-133">Este enfoque acopla la aplicación y el servidor en el que está implementada de forma que interfiere con la implementación.</span><span class="sxs-lookup"><span data-stu-id="99f88-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="99f88-134">En un esfuerzo por desacoplar, se introdujo [OWIN](https://owin.org/) para ofrecer una forma más limpia de usar varios marcos de trabajo de manera conjunta.</span><span class="sxs-lookup"><span data-stu-id="99f88-134">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="99f88-135">OWIN proporciona una canalización para agregar solo los módulos necesarios.</span><span class="sxs-lookup"><span data-stu-id="99f88-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="99f88-136">El entorno de hospedaje toma una función de [inicio](xref:fundamentals/startup) para configurar servicios y la canalización de solicitud de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99f88-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="99f88-137">`Startup` registra un conjunto de middleware en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99f88-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="99f88-138">Para cada solicitud, la aplicación llama a cada uno de los componentes de middleware con el puntero principal de una lista vinculada a un conjunto de controladores existente.</span><span class="sxs-lookup"><span data-stu-id="99f88-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="99f88-139">Cada componente de middleware puede agregar uno o varios controladores a la canalización de control de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="99f88-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="99f88-140">Esto se consigue mediante la devolución de una referencia al controlador que ahora es el primero de la lista.</span><span class="sxs-lookup"><span data-stu-id="99f88-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="99f88-141">Cada controlador se encarga de recordar e invocar el controlador siguiente en la lista.</span><span class="sxs-lookup"><span data-stu-id="99f88-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="99f88-142">Con ASP.NET Core, el punto de entrada a una aplicación es `Startup` y ya no se tiene dependencia de *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="99f88-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="99f88-143">Cuando utilice OWIN con .NET Framework, use algo parecido a lo siguiente como canalización:</span><span class="sxs-lookup"><span data-stu-id="99f88-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="99f88-144">Esto configura las rutas predeterminadas y tiene como valor predeterminado XmlSerialization a través de Json.</span><span class="sxs-lookup"><span data-stu-id="99f88-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="99f88-145">Agregue otro middleware a esta canalización según sea necesario (carga de servicios, opciones de configuración, archivos estáticos, etcétera).</span><span class="sxs-lookup"><span data-stu-id="99f88-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="99f88-146">ASP.NET Core usa un enfoque similar, pero no depende de OWIN para controlar la entrada.</span><span class="sxs-lookup"><span data-stu-id="99f88-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="99f88-147">En su lugar, se usa el método `Main` de *Program.cs* (similar a las aplicaciones de consola), y `Startup` se carga a través de ahí.</span><span class="sxs-lookup"><span data-stu-id="99f88-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="99f88-148">`Startup` debe incluir un método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="99f88-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="99f88-149">En `Configure`, agregue el middleware necesario a la canalización.</span><span class="sxs-lookup"><span data-stu-id="99f88-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="99f88-150">En el ejemplo siguiente (de la plantilla de sitio web predeterminada), se usan varios métodos de extensión para configurar la canalización con compatibilidad para:</span><span class="sxs-lookup"><span data-stu-id="99f88-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="99f88-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="99f88-151">BrowserLink</span></span>](https://vswebessentials.com/features/browserlink)
* <span data-ttu-id="99f88-152">Páginas de error</span><span class="sxs-lookup"><span data-stu-id="99f88-152">Error pages</span></span>
* <span data-ttu-id="99f88-153">Archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="99f88-153">Static files</span></span>
* <span data-ttu-id="99f88-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="99f88-154">ASP.NET Core MVC</span></span>
* Identity

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="99f88-155">El host y la aplicación se han desacoplado, lo que proporciona la flexibilidad de pasar a una plataforma diferente en el futuro.</span><span class="sxs-lookup"><span data-stu-id="99f88-155">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="99f88-156">Para obtener una referencia más detallada sobre el inicio y el middleware de ASP.NET Core, consulte <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="99f88-156">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="99f88-157">Almacenamiento de configuraciones</span><span class="sxs-lookup"><span data-stu-id="99f88-157">Storing configurations</span></span>

<span data-ttu-id="99f88-158">ASP.NET admite el almacenamiento de valores de configuración.</span><span class="sxs-lookup"><span data-stu-id="99f88-158">ASP.NET supports storing settings.</span></span> <span data-ttu-id="99f88-159">Estos valores de configuración se usan, por ejemplo, para admitir el entorno donde se implementan las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="99f88-159">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="99f88-160">Antes se solían almacenar todos los pares de clave y valor personalizados en la sección `<appSettings>` del archivo *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="99f88-160">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="99f88-161">Las aplicaciones leen esta configuración mediante la colección `ConfigurationManager.AppSettings` en el espacio de nombres `System.Configuration`:</span><span class="sxs-lookup"><span data-stu-id="99f88-161">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="99f88-162">ASP.NET Core puede almacenar datos de configuración para la aplicación en cualquier archivo y cargarlos como parte del arranque de middleware.</span><span class="sxs-lookup"><span data-stu-id="99f88-162">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="99f88-163">El archivo predeterminado que se usa en las plantillas de proyecto es *appSettings.JSON*:</span><span class="sxs-lookup"><span data-stu-id="99f88-163">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="99f88-164">La carga de este archivo en una instancia de `IConfiguration` dentro de la aplicación se realiza en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="99f88-164">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="99f88-165">La aplicación lee de `Configuration` para obtener la configuración:</span><span class="sxs-lookup"><span data-stu-id="99f88-165">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="99f88-166">Existen extensiones de este enfoque para lograr que el proceso sea más sólido, como el uso de [inserción de dependencias](xref:fundamentals/dependency-injection) para cargar un servicio con estos valores.</span><span class="sxs-lookup"><span data-stu-id="99f88-166">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="99f88-167">El enfoque de la inserción de dependencias proporciona un conjunto fuertemente tipado de objetos de configuración.</span><span class="sxs-lookup"><span data-stu-id="99f88-167">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

<span data-ttu-id="99f88-168">**Nota:** Para obtener una referencia más detallada sobre la configuración de ASP.NET Core, vea <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="99f88-168">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="99f88-169">Inserción de dependencias nativa</span><span class="sxs-lookup"><span data-stu-id="99f88-169">Native dependency injection</span></span>

<span data-ttu-id="99f88-170">Un objetivo importante al compilar aplicaciones grandes y escalables es lograr el acoplamiento flexible de los componentes y los servicios.</span><span class="sxs-lookup"><span data-stu-id="99f88-170">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="99f88-171">La [inserción de dependencias](xref:fundamentals/dependency-injection) es una técnica popular para lograrlo y es un componente nativo de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="99f88-171">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="99f88-172">En las aplicaciones de ASP.NET, los desarrolladores confían en una biblioteca de terceros para implementar la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="99f88-172">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="99f88-173">Una de estas bibliotecas es [Unity](https://github.com/unitycontainer/unity), suministrada por Microsoft Patterns & Practices.</span><span class="sxs-lookup"><span data-stu-id="99f88-173">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="99f88-174">Un ejemplo de configuración de la inserción de dependencias con Unity es la implementación de `IDependencyResolver` que incluye `UnityContainer` :</span><span class="sxs-lookup"><span data-stu-id="99f88-174">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="99f88-175">Cree una instancia de `UnityContainer`, registre el servicio y establezca la resolución de dependencias de `HttpConfiguration` en la nueva instancia de `UnityResolver` para el contenedor:</span><span class="sxs-lookup"><span data-stu-id="99f88-175">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="99f88-176">Inserte `IProductRepository` cuando sea necesario:</span><span class="sxs-lookup"><span data-stu-id="99f88-176">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="99f88-177">Dado que la inserción de dependencias forma parte de ASP.NET Core, puede Agregar el servicio en el `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="99f88-177">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="99f88-178">El repositorio se puede insertar en cualquier lugar, como ocurría con Unity.</span><span class="sxs-lookup"><span data-stu-id="99f88-178">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="99f88-179">Para obtener más información sobre la inserción de dependencias en ASP.NET Core, vea <xref:fundamentals/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="99f88-179">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="99f88-180">Trabajar con archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="99f88-180">Serving static files</span></span>

<span data-ttu-id="99f88-181">Una parte importante del desarrollo web es la capacidad de trabajar con activos estáticos de cliente.</span><span class="sxs-lookup"><span data-stu-id="99f88-181">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="99f88-182">Los ejemplos más comunes de archivos estáticos son HTML, CSS, JavaScript e imágenes.</span><span class="sxs-lookup"><span data-stu-id="99f88-182">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="99f88-183">Estos archivos deben guardarse en la ubicación de publicación de la aplicación (o la red de entrega de contenido) y es necesario hacer referencia a ellos para que una solicitud los pueda cargar.</span><span class="sxs-lookup"><span data-stu-id="99f88-183">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="99f88-184">Este proceso ha cambiado en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99f88-184">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="99f88-185">En ASP.NET, los archivos estáticos se almacenan en directorios distintos y se hace referencia a ellos en las vistas.</span><span class="sxs-lookup"><span data-stu-id="99f88-185">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="99f88-186">En ASP.NET Core, los archivos estáticos se almacenan en la "raíz web" ( *&lt;raíz del contenido&gt;/wwwroot*), a menos que se configuren de otra manera.</span><span class="sxs-lookup"><span data-stu-id="99f88-186">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="99f88-187">Los archivos se cargan en la canalización de solicitud mediante la invocación del método de extensión `UseStaticFiles` desde `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="99f88-187">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="99f88-188">**Nota:** Si el destino es .NET Framework, debe instalarse el paquete de NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="99f88-188">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="99f88-189">Por ejemplo, el explorador puede acceder a un recurso de imagen en la carpeta *wwwroot/images* en una ubicación como `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="99f88-189">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="99f88-190">**Nota:** Para obtener una referencia más detallada sobre cómo servir archivos estáticos en ASP.NET Core, vea <xref:fundamentals/static-files> .</span><span class="sxs-lookup"><span data-stu-id="99f88-190">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99f88-191">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="99f88-191">Additional resources</span></span>

* [<span data-ttu-id="99f88-192">Traslado a .NET Core: bibliotecas</span><span class="sxs-lookup"><span data-stu-id="99f88-192">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
