---
title: Hospedaje e implementación de ASP.NET Core Blazor WebAssembly
author: guardrex
description: Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 2b100ba029c08e0ce68d208df761f22a712fbbfd
ms.sourcegitcommit: 99c784a873b62fbd97a73c5c07f4fe7a7f5db638
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2020
ms.locfileid: "85503518"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="a9d96-103">Hospedaje e implementación de ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a9d96-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="a9d96-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) y [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="a9d96-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="a9d96-105">Con el [modelo de hospedaje de Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="a9d96-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="a9d96-106">La aplicación Blazor, sus dependencias y el entorno de ejecución de .NET se descargan en el explorador en paralelo.</span><span class="sxs-lookup"><span data-stu-id="a9d96-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="a9d96-107">La aplicación se ejecuta directamente en el subproceso de interfaz de usuario del explorador.</span><span class="sxs-lookup"><span data-stu-id="a9d96-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="a9d96-108">Se admiten las estrategias de implementación siguientes:</span><span class="sxs-lookup"><span data-stu-id="a9d96-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="a9d96-109">Una aplicación ASP.NET Core suministra la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="a9d96-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="a9d96-110">Esta estrategia se trata en la sección [Implementación hospedada con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="a9d96-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="a9d96-111">La aplicación Blazor se coloca en un servicio o servidor web de hospedaje estático, donde no se usa .NET para suministrar la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="a9d96-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="a9d96-112">Esta estrategia se trata en la sección [Implementación independiente](#standalone-deployment), que incluye información sobre cómo hospedar una aplicación Blazor WebAssembly como una subaplicación de IIS.</span><span class="sxs-lookup"><span data-stu-id="a9d96-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="a9d96-113">Compresión</span><span class="sxs-lookup"><span data-stu-id="a9d96-113">Compression</span></span>

<span data-ttu-id="a9d96-114">Cuando se publica una aplicación Blazor WebAssembly, la salida se comprime estáticamente durante la publicación para reducir el tamaño de la aplicación y acabar con la necesidad de compresión en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="a9d96-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="a9d96-115">Se usan los algoritmos de compresión siguientes:</span><span class="sxs-lookup"><span data-stu-id="a9d96-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="a9d96-116">[Brotli](https://tools.ietf.org/html/rfc7932) (el nivel más alto)</span><span class="sxs-lookup"><span data-stu-id="a9d96-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="a9d96-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="a9d96-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

Blazor<span data-ttu-id="a9d96-118"> se basa en el host para proporcionar los archivos comprimidos adecuados.</span><span class="sxs-lookup"><span data-stu-id="a9d96-118"> relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="a9d96-119">Cuando se usa un proyecto hospedado de ASP.NET Core, el proyecto host tiene capacidad para realizar la negociación de contenido y proporcionar los archivos comprimidos estáticamente.</span><span class="sxs-lookup"><span data-stu-id="a9d96-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="a9d96-120">Al hospedar una aplicación independiente Blazor WebAssembly, puede que sea necesario realizar trabajo adicional para garantizar que se proporcionan los archivos comprimidos estáticamente:</span><span class="sxs-lookup"><span data-stu-id="a9d96-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="a9d96-121">Para ver la configuración de compresión de `web.config` de IIS, vea la sección [IIS: compresión Brotli y Gzip](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="a9d96-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="a9d96-122">Al hospedar en soluciones de hospedaje estáticas que no admiten la negociación de contenido de archivos comprimidos estáticamente (como es el caso de las páginas de GitHub), considere la posibilidad de configurar la aplicación para capturar y descodificar archivos comprimidos Brotli:</span><span class="sxs-lookup"><span data-stu-id="a9d96-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="a9d96-123">Haga referencia al descodificador Brotli del [repositorio de GitHub google/brotli](https://github.com/google/brotli/) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a9d96-123">Reference the Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli/) in the app.</span></span>
  * <span data-ttu-id="a9d96-124">Actualice la aplicación para que use el descodificador.</span><span class="sxs-lookup"><span data-stu-id="a9d96-124">Update the app to use the decoder.</span></span> <span data-ttu-id="a9d96-125">Cambie el marcado de la etiqueta `<body>` de cierre en `wwwroot/index.html` por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a9d96-125">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="brotli.decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
          return (async function () {
            const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
            if (!response.ok) {
              throw new Error(response.statusText);
            }
            const originalResponseBuffer = await response.arrayBuffer();
            const originalResponseArray = new Int8Array(originalResponseBuffer);
            const decompressedResponseArray = BrotliDecode(originalResponseArray);
            const contentType = type === 
              'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
            return new Response(decompressedResponseArray, 
              { headers: { 'content-type': contentType } });
          })();
        }
      }
    });
    </script>
    ```
 
<span data-ttu-id="a9d96-126">Para deshabilitar la compresión, agregue la propiedad `BlazorEnableCompression` de MSBuild al archivo del proyecto de la aplicación y establezca el valor en `false`:</span><span class="sxs-lookup"><span data-stu-id="a9d96-126">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="a9d96-127">Reescritura de las URL para conseguir un enrutamiento correcto</span><span class="sxs-lookup"><span data-stu-id="a9d96-127">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="a9d96-128">Enrutar las solicitudes de los componentes de página de una aplicación Blazor WebAssembly no es tan sencillo como enrutar las solicitudes de una aplicación Blazor Server hospedada.</span><span class="sxs-lookup"><span data-stu-id="a9d96-128">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="a9d96-129">Imagine que tiene una aplicación Blazor WebAssembly con dos componentes:</span><span class="sxs-lookup"><span data-stu-id="a9d96-129">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="a9d96-130">`Main.razor`: se carga en la raíz de la aplicación y contiene un vínculo al componente `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="a9d96-130">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="a9d96-131">`About.razor`: componente `About`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-131">`About.razor`: `About` component.</span></span>

<span data-ttu-id="a9d96-132">Cuando se solicita el documento predeterminado de la aplicación mediante la barra de direcciones del explorador (por ejemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="a9d96-132">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="a9d96-133">El explorador realiza una solicitud.</span><span class="sxs-lookup"><span data-stu-id="a9d96-133">The browser makes a request.</span></span>
1. <span data-ttu-id="a9d96-134">Se devuelve la página predeterminada, que suele ser `index.html`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-134">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="a9d96-135">`index.html` arranca la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a9d96-135">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="a9d96-136">Se carga el enrutador de Blazor y se representa el componente `Main` de Razor.</span><span class="sxs-lookup"><span data-stu-id="a9d96-136">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="a9d96-137">En la página principal, la selección del vínculo al componente `About` funciona en el cliente porque el enrutador de Blazor impide que el explorador haga una solicitud en Internet a `www.contoso.com` sobre `About` y presenta el propio componente `About` representado.</span><span class="sxs-lookup"><span data-stu-id="a9d96-137">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="a9d96-138">Todas las solicitudes de puntos de conexión internos *dentro de la aplicación Blazor WebAssembly* funcionan del mismo modo: Las solicitudes no desencadenan solicitudes basadas en el explorador a recursos hospedados en el servidor en Internet.</span><span class="sxs-lookup"><span data-stu-id="a9d96-138">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="a9d96-139">El enrutador controla las solicitudes de forma interna.</span><span class="sxs-lookup"><span data-stu-id="a9d96-139">The router handles the requests internally.</span></span>

<span data-ttu-id="a9d96-140">Si se realiza una solicitud mediante la barra de direcciones del explorador para `www.contoso.com/About`, se produce un error.</span><span class="sxs-lookup"><span data-stu-id="a9d96-140">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="a9d96-141">Este recurso no existe en el host de Internet de la aplicación, por lo que se devuelve una respuesta *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="a9d96-141">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="a9d96-142">Dado que los exploradores solicitan páginas del lado cliente a hosts basados en Internet, los servidores web y los servicios de hospedaje deben reescribir todas las solicitudes de recursos que no estén físicamente en el servidor a la página `index.html`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-142">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="a9d96-143">Cuando se devuelve `index.html`, el enrutador Blazor de la aplicación se hace cargo y responde con el recurso correcto.</span><span class="sxs-lookup"><span data-stu-id="a9d96-143">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="a9d96-144">Al implementar en un servidor IIS, puede usar el módulo URL Rewrite con el archivo `web.config` publicado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a9d96-144">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="a9d96-145">Para más información, consulte la sección sobre [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="a9d96-145">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="a9d96-146">Implementación hospedada con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9d96-146">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="a9d96-147">Una *implementación hospedada* se encarga de suministrar la aplicación Blazor WebAssembly a los exploradores desde una [aplicación ASP.NET Core](xref:index) que se ejecuta en un servidor web.</span><span class="sxs-lookup"><span data-stu-id="a9d96-147">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="a9d96-148">La aplicación cliente de Blazor WebAssembly se publica en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` de la aplicación de servidor, junto con cualquier otro recurso web estático de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="a9d96-148">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="a9d96-149">Las dos aplicaciones se implementan juntas.</span><span class="sxs-lookup"><span data-stu-id="a9d96-149">The two apps are deployed together.</span></span> <span data-ttu-id="a9d96-150">Se requiere un servidor web que pueda hospedar una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9d96-150">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="a9d96-151">En el caso de una implementación hospedada, Visual Studio incluye la plantilla de proyecto **Aplicación de Blazor WebAssembly** (plantilla `blazorwasm` si se usa el comando [`dotnet new`](/dotnet/core/tools/dotnet-new)) con la opción **`Hosted`** seleccionada (`-ho|--hosted` si se usa el comando `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="a9d96-151">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="a9d96-152">Para obtener más información sobre la implementación y el hospedaje de aplicaciones de ASP.NET Core, consulte <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="a9d96-152">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="a9d96-153">Para obtener información sobre cómo implementar en Azure App Service, vea <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="a9d96-153">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="a9d96-154">Implementación independiente</span><span class="sxs-lookup"><span data-stu-id="a9d96-154">Standalone deployment</span></span>

<span data-ttu-id="a9d96-155">Una *implementación independiente* suministra la aplicación Blazor WebAssembly como un conjunto de archivos estáticos que los clientes solicitan directamente.</span><span class="sxs-lookup"><span data-stu-id="a9d96-155">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="a9d96-156">Cualquier servidor de archivos estático es capaz de suministrar la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="a9d96-156">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="a9d96-157">Los recursos de implementación independientes se publican en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-157">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="a9d96-158">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a9d96-158">Azure App Service</span></span>

<span data-ttu-id="a9d96-159">Las aplicaciones Blazor WebAssembly se pueden implementar en Azure App Services en Windows, que hospeda la aplicación en [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="a9d96-159">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="a9d96-160">Actualmente no se admite la implementación de una aplicación Blazor WebAssembly independiente en Azure App Service para Linux.</span><span class="sxs-lookup"><span data-stu-id="a9d96-160">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="a9d96-161">En este momento no hay disponible una imagen de servidor de Linux para hospedar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a9d96-161">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="a9d96-162">Se está trabajando para habilitar este escenario.</span><span class="sxs-lookup"><span data-stu-id="a9d96-162">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="a9d96-163">IIS</span><span class="sxs-lookup"><span data-stu-id="a9d96-163">IIS</span></span>

<span data-ttu-id="a9d96-164">IIS es un servidor de archivos estáticos compatible con las aplicaciones Blazor.</span><span class="sxs-lookup"><span data-stu-id="a9d96-164">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="a9d96-165">Para configurar IIS para hospedar Blazor, consulte [Compilación de un sitio web estático en IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="a9d96-165">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="a9d96-166">Los recursos publicados se crean en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-166">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="a9d96-167">Hospede el contenido de la carpeta `publish` en el servidor web o el servicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="a9d96-167">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="a9d96-168">web.config</span><span class="sxs-lookup"><span data-stu-id="a9d96-168">web.config</span></span>

<span data-ttu-id="a9d96-169">Cuando se publica un proyecto de Blazor, se crea un archivo `web.config` con la siguiente configuración de IIS:</span><span class="sxs-lookup"><span data-stu-id="a9d96-169">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="a9d96-170">Se establecen los tipos MIME de las siguientes extensiones de archivo:</span><span class="sxs-lookup"><span data-stu-id="a9d96-170">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="a9d96-171">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="a9d96-171">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="a9d96-172">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="a9d96-172">`.json`: `application/json`</span></span>
  * <span data-ttu-id="a9d96-173">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="a9d96-173">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="a9d96-174">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="a9d96-174">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="a9d96-175">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="a9d96-175">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="a9d96-176">Se habilita la compresión HTTP de los siguientes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="a9d96-176">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="a9d96-177">Se establecen reglas del módulo URL Rewrite:</span><span class="sxs-lookup"><span data-stu-id="a9d96-177">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="a9d96-178">Proporcione el subdirectorio donde residen los recursos estáticos de la aplicación (`wwwroot/{PATH REQUESTED}`).</span><span class="sxs-lookup"><span data-stu-id="a9d96-178">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="a9d96-179">Cree el enrutamiento de reserva de SPA para que las solicitudes de recursos que no sean archivos se redirijan al documento predeterminado de la aplicación en su carpeta de recursos estáticos (`wwwroot/index.html`).</span><span class="sxs-lookup"><span data-stu-id="a9d96-179">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="a9d96-180">Uso de un archivo web.config personalizado</span><span class="sxs-lookup"><span data-stu-id="a9d96-180">Use a custom web.config</span></span>

<span data-ttu-id="a9d96-181">Para usar un archivo `web.config` personalizado, coloque el archivo `web.config` personalizado en la raíz de la carpeta del proyecto y publique el proyecto.</span><span class="sxs-lookup"><span data-stu-id="a9d96-181">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="a9d96-182">Instalación del módulo URL Rewrite</span><span class="sxs-lookup"><span data-stu-id="a9d96-182">Install the URL Rewrite Module</span></span>

<span data-ttu-id="a9d96-183">El [módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) es necesario para reescribir las URL.</span><span class="sxs-lookup"><span data-stu-id="a9d96-183">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="a9d96-184">El módulo no se instala de forma predeterminada y no está disponible para instalar como una característica de servicio de rol del servidor web (IIS).</span><span class="sxs-lookup"><span data-stu-id="a9d96-184">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="a9d96-185">El módulo se debe descargar desde el sitio web de IIS.</span><span class="sxs-lookup"><span data-stu-id="a9d96-185">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="a9d96-186">Use el instalador de plataforma web para instalar el módulo:</span><span class="sxs-lookup"><span data-stu-id="a9d96-186">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="a9d96-187">De forma local, vaya a la [página de descargas del módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="a9d96-187">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="a9d96-188">En el caso de la versión en inglés, seleccione **WebPI** para descargar el instalador de WebPI.</span><span class="sxs-lookup"><span data-stu-id="a9d96-188">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="a9d96-189">En el caso de otros idiomas, seleccione la arquitectura adecuada del servidor (x86/x64) para descargar el instalador.</span><span class="sxs-lookup"><span data-stu-id="a9d96-189">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="a9d96-190">Copie el instalador en el servidor.</span><span class="sxs-lookup"><span data-stu-id="a9d96-190">Copy the installer to the server.</span></span> <span data-ttu-id="a9d96-191">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="a9d96-191">Run the installer.</span></span> <span data-ttu-id="a9d96-192">Haga clic en el botón **Instalar** y acepte los términos de licencia.</span><span class="sxs-lookup"><span data-stu-id="a9d96-192">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="a9d96-193">No es necesario reiniciar el servidor al finalizar la instalación.</span><span class="sxs-lookup"><span data-stu-id="a9d96-193">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="a9d96-194">Configuración del sitio web</span><span class="sxs-lookup"><span data-stu-id="a9d96-194">Configure the website</span></span>

<span data-ttu-id="a9d96-195">Configure la **ruta de acceso física** del sitio web a la carpeta de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a9d96-195">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="a9d96-196">La carpeta contiene:</span><span class="sxs-lookup"><span data-stu-id="a9d96-196">The folder contains:</span></span>

* <span data-ttu-id="a9d96-197">El archivo `web.config` que usa IIS para configurar el sitio web, incluidas las reglas de redireccionamiento y los tipos de contenido de archivo necesarios.</span><span class="sxs-lookup"><span data-stu-id="a9d96-197">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="a9d96-198">La carpeta de recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a9d96-198">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="a9d96-199">Hospedaje como subaplicación de IIS</span><span class="sxs-lookup"><span data-stu-id="a9d96-199">Host as an IIS sub-app</span></span>

<span data-ttu-id="a9d96-200">Si una aplicación independiente se hospeda como una subaplicación de IIS, realice una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="a9d96-200">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="a9d96-201">Deshabilite el controlador del módulo de ASP.NET Core heredado.</span><span class="sxs-lookup"><span data-stu-id="a9d96-201">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="a9d96-202">Para quitar el controlador del archivo `web.config` publicado de la aplicación Blazor, agregue una sección `<handlers>` al archivo:</span><span class="sxs-lookup"><span data-stu-id="a9d96-202">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="a9d96-203">Deshabilite la herencia de la sección `<system.webServer>` de la aplicación raíz (principal) mediante un elemento `<location>` con `inheritInChildApplications` establecido en `false`:</span><span class="sxs-lookup"><span data-stu-id="a9d96-203">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="a9d96-204">Además de [configurarse la ruta de acceso base de la aplicación](xref:blazor/host-and-deploy/index#app-base-path), se quita el controlador o se deshabilita la herencia.</span><span class="sxs-lookup"><span data-stu-id="a9d96-204">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="a9d96-205">En el archivo `index.html` de la aplicación, establezca la ruta de acceso base de la aplicación en el alias de IIS que ha usado al configurar la subaplicación en IIS.</span><span class="sxs-lookup"><span data-stu-id="a9d96-205">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="a9d96-206">Compresión Brotli y Gzip</span><span class="sxs-lookup"><span data-stu-id="a9d96-206">Brotli and Gzip compression</span></span>

<span data-ttu-id="a9d96-207">IIS se puede configurar a través de `web.config` para dar servicio a recursos de Blazor comprimidos con Brotli o Gzip.</span><span class="sxs-lookup"><span data-stu-id="a9d96-207">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="a9d96-208">Para ver una configuración de ejemplo, consulte [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="a9d96-208">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="a9d96-209">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="a9d96-209">Troubleshooting</span></span>

<span data-ttu-id="a9d96-210">Si se recibe un error *500 Error interno del servidor* y el administrador de IIS produce errores al intentar acceder a la configuración del sitio web, confirme que el módulo URL Rewrite está instalado.</span><span class="sxs-lookup"><span data-stu-id="a9d96-210">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="a9d96-211">Si no lo está, IIS no puede analizar el archivo `web.config`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-211">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="a9d96-212">Esto impide que el Administrador de IIS cargue la configuración del sitio web y que el sitio web suministre los archivos estáticos de Blazor.</span><span class="sxs-lookup"><span data-stu-id="a9d96-212">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="a9d96-213">Para obtener más información sobre cómo solucionar problemas de las implementaciones en IIS, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="a9d96-213">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="a9d96-214">Almacenamiento de Azure</span><span class="sxs-lookup"><span data-stu-id="a9d96-214">Azure Storage</span></span>

<span data-ttu-id="a9d96-215">El hospedaje de archivos estáticos de [Azure Storage](/azure/storage/) permite el hospedaje de aplicaciones Blazor sin servidor.</span><span class="sxs-lookup"><span data-stu-id="a9d96-215">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="a9d96-216">Se admiten nombres de dominio personalizados, Azure Content Delivery Network (CDN) y HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a9d96-216">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="a9d96-217">Cuando el servicio de blob está habilitado para el hospedaje de sitios web estáticos en una cuenta de almacenamiento:</span><span class="sxs-lookup"><span data-stu-id="a9d96-217">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="a9d96-218">Establece el **nombre de documento de índice** en `index.html`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-218">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="a9d96-219">Establece la **ruta de acceso del documento de error** en `index.html`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-219">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="a9d96-220">Los componentes Razor y otros puntos de conexión que no son de archivo no residen en las rutas de acceso físicas del contenido estático almacenado por el servicio de blob.</span><span class="sxs-lookup"><span data-stu-id="a9d96-220">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="a9d96-221">Cuando se recibe una solicitud de uno de estos recursos que debe controlar el enrutador de Blazor, el error *404 - No encontrado* generado por el servicio de blob enruta la solicitud a la **ruta de acceso del documento de error**.</span><span class="sxs-lookup"><span data-stu-id="a9d96-221">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="a9d96-222">Se devuelve el blob `index.html`, y el enrutador de Blazor carga y procesa la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="a9d96-222">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="a9d96-223">Si los archivos no se cargan en tiempo de ejecución debido a tipos MIME inadecuados en los encabezados `Content-Type` de los archivos, haga algunas de las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="a9d96-223">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="a9d96-224">Configure las herramientas para establecer los tipos MIME correctos (encabezados `Content-Type`) cuando se implementen los archivos.</span><span class="sxs-lookup"><span data-stu-id="a9d96-224">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="a9d96-225">Cambie los tipos MIME (encabezados `Content-Type`) de los archivos una vez que se implementa la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a9d96-225">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="a9d96-226">En cada archivo del Explorador de Storage (Azure Portal) haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a9d96-226">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="a9d96-227">Haga clic con el botón derecho en el archivo y seleccione **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="a9d96-227">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="a9d96-228">Establezca el valor de **ContentType** y seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="a9d96-228">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="a9d96-229">Para más información, consulte [Hospedaje de sitios web estáticos en Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="a9d96-229">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="a9d96-230">Nginx</span><span class="sxs-lookup"><span data-stu-id="a9d96-230">Nginx</span></span>

<span data-ttu-id="a9d96-231">El siguiente archivo `nginx.conf` se ha simplificado para mostrar cómo hay que configurar Nginx para enviar el archivo `index.html` siempre que no pueda encontrar un archivo correspondiente en el disco.</span><span class="sxs-lookup"><span data-stu-id="a9d96-231">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="a9d96-232">Para obtener más información sobre la configuración del servidor web de producción de Nginx, consulte [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creación de archivos de configuración de NGINX y NGINX Plus).</span><span class="sxs-lookup"><span data-stu-id="a9d96-232">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="a9d96-233">Nginx en Docker</span><span class="sxs-lookup"><span data-stu-id="a9d96-233">Nginx in Docker</span></span>

<span data-ttu-id="a9d96-234">Para hospedar Blazor en Docker mediante Nginx, configure Dockerfile para usar la imagen de Nginx basada en Alpine.</span><span class="sxs-lookup"><span data-stu-id="a9d96-234">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="a9d96-235">Actualice el Dockerfile para copiar el archivo `nginx.config` en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="a9d96-235">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="a9d96-236">Agregue una línea al Dockerfile, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a9d96-236">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="a9d96-237">Apache</span><span class="sxs-lookup"><span data-stu-id="a9d96-237">Apache</span></span>

<span data-ttu-id="a9d96-238">Para implementar una aplicación Blazor WebAssembly en CentOS 7 o posterior:</span><span class="sxs-lookup"><span data-stu-id="a9d96-238">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="a9d96-239">Cree el archivo de configuración de Apache.</span><span class="sxs-lookup"><span data-stu-id="a9d96-239">Create the Apache configuration file.</span></span> <span data-ttu-id="a9d96-240">El siguiente ejemplo es un archivo de configuración simplificado (`blazorapp.config`):</span><span class="sxs-lookup"><span data-stu-id="a9d96-240">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="a9d96-241">Coloque el archivo de configuración de Apache en el directorio `/etc/httpd/conf.d/`, que es el directorio de configuración de Apache predeterminado en CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="a9d96-241">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="a9d96-242">Coloque los archivos de la aplicación en el directorio `/var/www/blazorapp` (la ubicación especificada para `DocumentRoot` en el archivo de configuración).</span><span class="sxs-lookup"><span data-stu-id="a9d96-242">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="a9d96-243">Reinicie el servicio de Apache.</span><span class="sxs-lookup"><span data-stu-id="a9d96-243">Restart the Apache service.</span></span>

<span data-ttu-id="a9d96-244">Para más información, vea [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) y [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="a9d96-244">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="a9d96-245">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="a9d96-245">GitHub Pages</span></span>

<span data-ttu-id="a9d96-246">Para controlar las reescrituras de URL, agregue un archivo `404.html` con un script que controle el redireccionamiento de la solicitud a la página `index.html`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-246">To handle URL rewrites, add a `404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="a9d96-247">Para consultar una implementación de ejemplo que ha proporcionado la comunidad, vea [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) (Aplicaciones de página única para GitHub Pages [rafrex/spa-github-pages en GitHub]).</span><span class="sxs-lookup"><span data-stu-id="a9d96-247">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="a9d96-248">Se puede ver un ejemplo del enfoque de la comunidad en [blazor-demo/blazor-demo.github.io en GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sitio activo](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="a9d96-248">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="a9d96-249">Si usa un sitio de proyecto en lugar de un sitio de la organización, agregue o actualice la etiqueta `<base>` en `index.html`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-249">When using a project site instead of an organization site, add or update the `<base>` tag in `index.html`.</span></span> <span data-ttu-id="a9d96-250">Defina el valor del atributo `href` con el nombre del repositorio de GitHub con una barra diagonal final (por ejemplo, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="a9d96-250">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="a9d96-251">Valores de configuración de host</span><span class="sxs-lookup"><span data-stu-id="a9d96-251">Host configuration values</span></span>

<span data-ttu-id="a9d96-252">Las [aplicaciones Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) pueden aceptar los siguientes valores de configuración de host como argumentos de línea de comandos en tiempo de ejecución en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="a9d96-252">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="a9d96-253">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="a9d96-253">Content root</span></span>

<span data-ttu-id="a9d96-254">El argumento `--contentroot` establece la ruta de acceso absoluta al directorio que incluye los archivos de contenido de la aplicación ([raíz del contenido](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="a9d96-254">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="a9d96-255">En los ejemplos siguientes, `/content-root-path` es la ruta de acceso raíz del contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a9d96-255">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="a9d96-256">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="a9d96-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="a9d96-257">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a9d96-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="a9d96-258">Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="a9d96-258">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="a9d96-259">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-259">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="a9d96-260">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="a9d96-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="a9d96-261">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-261">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="a9d96-262">Ruta de acceso base</span><span class="sxs-lookup"><span data-stu-id="a9d96-262">Path base</span></span>

<span data-ttu-id="a9d96-263">El argumento `--pathbase` establece la ruta de acceso base de la aplicación para una aplicación que se ejecuta localmente con una ruta de acceso de URL relativa que no es raíz (el valor `href` de la etiqueta `<base>` se establece en una ruta de acceso que no sea `/` para ensayo y producción).</span><span class="sxs-lookup"><span data-stu-id="a9d96-263">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="a9d96-264">En los ejemplos siguientes, `/relative-URL-path` es la ruta de acceso base de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a9d96-264">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="a9d96-265">Para obtener más información, vea [Ruta de acceso base de la aplicación](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="a9d96-265">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a9d96-266">A diferencia de la ruta de acceso proporcionada al valor `href` de la etiqueta `<base>`, no incluya una barra diagonal final (`/`) al pasar el valor del argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-266">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="a9d96-267">Si se proporciona la ruta de acceso base de la aplicación en la etiqueta `<base>` como `<base href="/CoolApp/">` (se incluye una barra diagonal final), se pasa el valor del argumento de línea de comandos como `--pathbase=/CoolApp` (sin barra diagonal final).</span><span class="sxs-lookup"><span data-stu-id="a9d96-267">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="a9d96-268">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="a9d96-268">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="a9d96-269">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a9d96-269">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="a9d96-270">Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="a9d96-270">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="a9d96-271">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-271">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="a9d96-272">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="a9d96-272">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="a9d96-273">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-273">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="a9d96-274">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="a9d96-274">URLs</span></span>

<span data-ttu-id="a9d96-275">El argumento `--urls` establece las direcciones IP o las direcciones de host con los puertos y protocolos en los que escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="a9d96-275">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="a9d96-276">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="a9d96-276">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="a9d96-277">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a9d96-277">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="a9d96-278">Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="a9d96-278">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="a9d96-279">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-279">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="a9d96-280">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="a9d96-280">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="a9d96-281">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-281">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="a9d96-282">Configurar el enlazador</span><span class="sxs-lookup"><span data-stu-id="a9d96-282">Configure the Linker</span></span>

Blazor<span data-ttu-id="a9d96-283"> realiza la vinculación de lenguaje intermedio (IL) en cada compilación de lanzamiento para quitar el IL innecesario de los ensamblados de salida.</span><span class="sxs-lookup"><span data-stu-id="a9d96-283"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="a9d96-284">Para obtener más información, vea <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="a9d96-284">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="a9d96-285">Carga de recursos de arranque personalizados</span><span class="sxs-lookup"><span data-stu-id="a9d96-285">Custom boot resource loading</span></span>

<span data-ttu-id="a9d96-286">Una aplicación Blazor WebAssembly se puede inicializar con la función `loadBootResource` para invalidar el mecanismo de carga de recursos de arranque integrado.</span><span class="sxs-lookup"><span data-stu-id="a9d96-286">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="a9d96-287">Use `loadBootResource` en los siguientes escenarios:</span><span class="sxs-lookup"><span data-stu-id="a9d96-287">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="a9d96-288">Para permitir a los usuarios cargar recursos estáticos, como datos de zona horaria o `dotnet.wasm`, desde una red CDN.</span><span class="sxs-lookup"><span data-stu-id="a9d96-288">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="a9d96-289">Para cargar los ensamblados comprimidos mediante una solicitud HTTP y descomprimirlos en el cliente para aquellos hosts que no admiten la captura de contenido comprimido del servidor.</span><span class="sxs-lookup"><span data-stu-id="a9d96-289">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="a9d96-290">Para asignar otro alias a los recursos mediante el redireccionamiento de cada solicitud `fetch` a un nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="a9d96-290">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="a9d96-291">Los parámetros `loadBootResource` aparecen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="a9d96-291">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="a9d96-292">Parámetro</span><span class="sxs-lookup"><span data-stu-id="a9d96-292">Parameter</span></span>    | <span data-ttu-id="a9d96-293">Descripción</span><span class="sxs-lookup"><span data-stu-id="a9d96-293">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="a9d96-294">Tipo de recurso.</span><span class="sxs-lookup"><span data-stu-id="a9d96-294">The type of the resource.</span></span> <span data-ttu-id="a9d96-295">Tipos permitidos: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="a9d96-295">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="a9d96-296">Nombre del recurso.</span><span class="sxs-lookup"><span data-stu-id="a9d96-296">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="a9d96-297">URI relativo o absoluto del recurso.</span><span class="sxs-lookup"><span data-stu-id="a9d96-297">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="a9d96-298">Cadena de integridad que representa el contenido esperado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="a9d96-298">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="a9d96-299">`loadBootResource` devuelve cualquiera de los siguientes elementos para invalidar el proceso de carga:</span><span class="sxs-lookup"><span data-stu-id="a9d96-299">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="a9d96-300">Cadena URI.</span><span class="sxs-lookup"><span data-stu-id="a9d96-300">URI string.</span></span> <span data-ttu-id="a9d96-301">En el ejemplo siguiente (`wwwroot/index.html`), los siguientes archivos se sirven desde una red CDN en `https://my-awesome-cdn.com/`:</span><span class="sxs-lookup"><span data-stu-id="a9d96-301">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="a9d96-302">Datos de zona horaria</span><span class="sxs-lookup"><span data-stu-id="a9d96-302">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="a9d96-303">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-303">`Promise<Response>`.</span></span> <span data-ttu-id="a9d96-304">Pase el parámetro `integrity` en un encabezado para conservar el comportamiento de comprobación de integridad predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a9d96-304">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="a9d96-305">En el ejemplo siguiente (`wwwroot/index.html`), se agrega un encabezado HTTP personalizado a las solicitudes salientes y se pasa el parámetro `integrity` a través de la llamada a `fetch`:</span><span class="sxs-lookup"><span data-stu-id="a9d96-305">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="a9d96-306">`null`/`undefined`, que da lugar al comportamiento de carga predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a9d96-306">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="a9d96-307">Los orígenes externos deben devolver los encabezados CORS necesarios para que los exploradores permitan la carga de recursos entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="a9d96-307">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="a9d96-308">Normalmente, las redes CDN proporcionan los encabezados necesarios de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="a9d96-308">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="a9d96-309">Solo tiene que especificar tipos para los comportamientos personalizados.</span><span class="sxs-lookup"><span data-stu-id="a9d96-309">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="a9d96-310">Los tipos no especificados en `loadBootResource` se cargan mediante el marco de trabajo según sus comportamientos de carga predeterminados.</span><span class="sxs-lookup"><span data-stu-id="a9d96-310">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="a9d96-311">Cambio de la extensión de nombre de archivo de los archivos DLL</span><span class="sxs-lookup"><span data-stu-id="a9d96-311">Change the filename extension of DLL files</span></span>

<span data-ttu-id="a9d96-312">En caso de que necesite cambiar las extensiones de nombre de los archivos `.dll` publicados de la aplicación, siga las instrucciones de esta sección.</span><span class="sxs-lookup"><span data-stu-id="a9d96-312">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="a9d96-313">Después de publicar la aplicación, use un script de shell o una canalización de compilación de DevOps para cambiar el nombre de los archivos `.dll` a fin de usar otra extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="a9d96-313">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="a9d96-314">Establezca como destino los archivos `.dll` que están en el directorio `wwwroot` de la salida publicada de la aplicación (por ejemplo, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span><span class="sxs-lookup"><span data-stu-id="a9d96-314">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="a9d96-315">En los ejemplos siguientes se cambia el nombre de los archivos `.dll` para que usen la extensión de archivo `.bin`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-315">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="a9d96-316">En Windows:</span><span class="sxs-lookup"><span data-stu-id="a9d96-316">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="a9d96-317">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="a9d96-317">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="a9d96-318">En Linux o macOS:</span><span class="sxs-lookup"><span data-stu-id="a9d96-318">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="a9d96-319">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="a9d96-319">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="a9d96-320">Para usar una extensión de archivo diferente a `.bin`, reemplace `.bin` en los comandos anteriores.</span><span class="sxs-lookup"><span data-stu-id="a9d96-320">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="a9d96-321">Para dirigirse a los archivos comprimidos `blazor.boot.json.gz` y `blazor.boot.json.br`, adopte uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="a9d96-321">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="a9d96-322">Quite los archivos comprimidos `blazor.boot.json.gz` y `blazor.boot.json.br`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-322">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="a9d96-323">Con este enfoque, la compresión está deshabilitada.</span><span class="sxs-lookup"><span data-stu-id="a9d96-323">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="a9d96-324">Vuelva a comprimir el archivo `blazor.boot.json` actualizado.</span><span class="sxs-lookup"><span data-stu-id="a9d96-324">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="a9d96-325">Las instrucciones anteriores también se aplican cuando se usan recursos de trabajo de servicio.</span><span class="sxs-lookup"><span data-stu-id="a9d96-325">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="a9d96-326">Quite o vuelva a comprimir `wwwroot/service-worker-assets.js.br` y `wwwroot/service-worker-assets.js.gz`.</span><span class="sxs-lookup"><span data-stu-id="a9d96-326">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="a9d96-327">De lo contrario, las comprobaciones de integridad de los archivos producirán errores en el explorador.</span><span class="sxs-lookup"><span data-stu-id="a9d96-327">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="a9d96-328">En el siguiente ejemplo de Windows se usa un script de PowerShell colocado en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a9d96-328">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="a9d96-329">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="a9d96-329">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="a9d96-330">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="a9d96-330">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="a9d96-331">En el archivo del proyecto, el script se ejecuta después de publicar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a9d96-331">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="a9d96-332">Para proporcionar comentarios, visite [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="a9d96-332">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
