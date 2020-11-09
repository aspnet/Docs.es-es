---
title: 'Hospedaje e implementación de ASP.NET Core Blazor WebAssembly'
author: guardrex
description: 'Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 0912b3fbcd0b891deb4985eaa18841c22f4f3264
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055755"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="31d01-103">Hospedaje e implementación de ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="31d01-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="31d01-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) y [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="31d01-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="31d01-105">Con el [modelo de hospedaje de Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="31d01-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="31d01-106">La aplicación Blazor, sus dependencias y el entorno de ejecución de .NET se descargan en el explorador en paralelo.</span><span class="sxs-lookup"><span data-stu-id="31d01-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="31d01-107">La aplicación se ejecuta directamente en el subproceso de interfaz de usuario del explorador.</span><span class="sxs-lookup"><span data-stu-id="31d01-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="31d01-108">Se admiten las estrategias de implementación siguientes:</span><span class="sxs-lookup"><span data-stu-id="31d01-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="31d01-109">Una aplicación ASP.NET Core suministra la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="31d01-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="31d01-110">Esta estrategia se trata en la sección [Implementación hospedada con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="31d01-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="31d01-111">La aplicación Blazor se coloca en un servicio o servidor web de hospedaje estático, donde no se usa .NET para suministrar la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="31d01-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="31d01-112">Esta estrategia se trata en la sección [Implementación independiente](#standalone-deployment), que incluye información sobre cómo hospedar una aplicación Blazor WebAssembly como una subaplicación de IIS.</span><span class="sxs-lookup"><span data-stu-id="31d01-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="31d01-113">Compresión</span><span class="sxs-lookup"><span data-stu-id="31d01-113">Compression</span></span>

<span data-ttu-id="31d01-114">Cuando se publica una aplicación Blazor WebAssembly, la salida se comprime estáticamente durante la publicación para reducir el tamaño de la aplicación y acabar con la necesidad de compresión en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="31d01-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="31d01-115">Se usan los algoritmos de compresión siguientes:</span><span class="sxs-lookup"><span data-stu-id="31d01-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="31d01-116">[Brotli](https://tools.ietf.org/html/rfc7932) (el nivel más alto)</span><span class="sxs-lookup"><span data-stu-id="31d01-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="31d01-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="31d01-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="31d01-118">Blazor se basa en el host para proporcionar los archivos comprimidos adecuados.</span><span class="sxs-lookup"><span data-stu-id="31d01-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="31d01-119">Cuando se usa un proyecto hospedado de ASP.NET Core, el proyecto host tiene capacidad para realizar la negociación de contenido y proporcionar los archivos comprimidos estáticamente.</span><span class="sxs-lookup"><span data-stu-id="31d01-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="31d01-120">Al hospedar una aplicación independiente Blazor WebAssembly, puede que sea necesario realizar trabajo adicional para garantizar que se proporcionan los archivos comprimidos estáticamente:</span><span class="sxs-lookup"><span data-stu-id="31d01-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="31d01-121">Para ver la configuración de compresión de `web.config` de IIS, vea la sección [IIS: compresión Brotli y Gzip](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="31d01-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="31d01-122">Al hospedar en soluciones de hospedaje estáticas que no admiten la negociación de contenido de archivos comprimidos estáticamente (como es el caso de las páginas de GitHub), considere la posibilidad de configurar la aplicación para capturar y descodificar archivos comprimidos Brotli:</span><span class="sxs-lookup"><span data-stu-id="31d01-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="31d01-123">Obtenga el descodificador Brotli de JavaScript del [repositorio de GitHub google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="31d01-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="31d01-124">A partir de septiembre de 2020, el archivo del descodificador se llama `decode.js` y se encuentra en la [carpeta `js`](https://github.com/google/brotli/tree/master/js) del repositorio.</span><span class="sxs-lookup"><span data-stu-id="31d01-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="31d01-125">En la versión reducida del script `decode.js` (`decode.min.js`) en el [repositorio de GitHub google/brotli](https://github.com/google/brotli) se encuentra una regresión.</span><span class="sxs-lookup"><span data-stu-id="31d01-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="31d01-126">Minimice el script por su cuenta o use el [paquete npm](https://www.npmjs.com/package/brotli) hasta que se resuelva la incidencia [Window.BrotliDecode no se ha establecido en decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844).</span><span class="sxs-lookup"><span data-stu-id="31d01-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="31d01-127">En el código de ejemplo de esta sección se usa la versión **no reducida** del script.</span><span class="sxs-lookup"><span data-stu-id="31d01-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="31d01-128">Actualice la aplicación para que use el descodificador.</span><span class="sxs-lookup"><span data-stu-id="31d01-128">Update the app to use the decoder.</span></span> <span data-ttu-id="31d01-129">Cambie el marcado de la etiqueta `<body>` de cierre en `wwwroot/index.html` por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="31d01-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
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
 
<span data-ttu-id="31d01-130">Para deshabilitar la compresión, agregue la propiedad `BlazorEnableCompression` de MSBuild al archivo del proyecto de la aplicación y establezca el valor en `false`:</span><span class="sxs-lookup"><span data-stu-id="31d01-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="31d01-131">La propiedad `BlazorEnableCompression` se puede pasar al comando [`dotnet publish`](/dotnet/core/tools/dotnet-publish) con la sintaxis siguiente en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="31d01-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="31d01-132">Reescritura de las URL para conseguir un enrutamiento correcto</span><span class="sxs-lookup"><span data-stu-id="31d01-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="31d01-133">Enrutar las solicitudes de los componentes de página de una aplicación Blazor WebAssembly no es tan sencillo como enrutar las solicitudes de una aplicación Blazor Server hospedada.</span><span class="sxs-lookup"><span data-stu-id="31d01-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="31d01-134">Imagine que tiene una aplicación Blazor WebAssembly con dos componentes:</span><span class="sxs-lookup"><span data-stu-id="31d01-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="31d01-135">`Main.razor`: se carga en la raíz de la aplicación y contiene un vínculo al componente `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="31d01-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="31d01-136">`About.razor`: componente `About`.</span><span class="sxs-lookup"><span data-stu-id="31d01-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="31d01-137">Cuando se solicita el documento predeterminado de la aplicación mediante la barra de direcciones del explorador (por ejemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="31d01-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="31d01-138">El explorador realiza una solicitud.</span><span class="sxs-lookup"><span data-stu-id="31d01-138">The browser makes a request.</span></span>
1. <span data-ttu-id="31d01-139">Se devuelve la página predeterminada, que suele ser `index.html`.</span><span class="sxs-lookup"><span data-stu-id="31d01-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="31d01-140">`index.html` arranca la aplicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="31d01-141">Se carga el enrutador de Blazor y se representa el componente `Main` de Razor.</span><span class="sxs-lookup"><span data-stu-id="31d01-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="31d01-142">En la página principal, la selección del vínculo al componente `About` funciona en el cliente porque el enrutador de Blazor impide que el explorador haga una solicitud en Internet a `www.contoso.com` sobre `About` y presenta el propio componente `About` representado.</span><span class="sxs-lookup"><span data-stu-id="31d01-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="31d01-143">Todas las solicitudes de puntos de conexión internos *dentro de la aplicación Blazor WebAssembly* funcionan del mismo modo: Las solicitudes no desencadenan solicitudes basadas en el explorador a recursos hospedados en el servidor en Internet.</span><span class="sxs-lookup"><span data-stu-id="31d01-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="31d01-144">El enrutador controla las solicitudes de forma interna.</span><span class="sxs-lookup"><span data-stu-id="31d01-144">The router handles the requests internally.</span></span>

<span data-ttu-id="31d01-145">Si se realiza una solicitud mediante la barra de direcciones del explorador para `www.contoso.com/About`, se produce un error.</span><span class="sxs-lookup"><span data-stu-id="31d01-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="31d01-146">Este recurso no existe en el host de Internet de la aplicación, por lo que se devuelve una respuesta *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="31d01-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="31d01-147">Dado que los exploradores solicitan páginas del lado cliente a hosts basados en Internet, los servidores web y los servicios de hospedaje deben reescribir todas las solicitudes de recursos que no estén físicamente en el servidor a la página `index.html`.</span><span class="sxs-lookup"><span data-stu-id="31d01-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="31d01-148">Cuando se devuelve `index.html`, el enrutador Blazor de la aplicación se hace cargo y responde con el recurso correcto.</span><span class="sxs-lookup"><span data-stu-id="31d01-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="31d01-149">Al implementar en un servidor IIS, puede usar el módulo URL Rewrite con el archivo `web.config` publicado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="31d01-150">Para más información, consulte la sección sobre [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="31d01-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="31d01-151">Implementación hospedada con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31d01-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="31d01-152">Una *implementación hospedada* se encarga de suministrar la aplicación Blazor WebAssembly a los exploradores desde una [aplicación ASP.NET Core](xref:index) que se ejecuta en un servidor web.</span><span class="sxs-lookup"><span data-stu-id="31d01-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="31d01-153">La aplicación cliente de Blazor WebAssembly se publica en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` de la aplicación de servidor, junto con cualquier otro recurso web estático de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="31d01-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="31d01-154">Las dos aplicaciones se implementan juntas.</span><span class="sxs-lookup"><span data-stu-id="31d01-154">The two apps are deployed together.</span></span> <span data-ttu-id="31d01-155">Se requiere un servidor web que pueda hospedar una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="31d01-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="31d01-156">En el caso de una implementación hospedada, Visual Studio incluye la plantilla de proyecto **Aplicación de Blazor WebAssembly** (plantilla `blazorwasm` si se usa el comando [`dotnet new`](/dotnet/core/tools/dotnet-new)) con la opción **`Hosted`** seleccionada (`-ho|--hosted` si se usa el comando `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="31d01-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="31d01-157">Para obtener más información sobre la implementación y el hospedaje de aplicaciones de ASP.NET Core, consulte <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="31d01-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="31d01-158">Para obtener información sobre cómo implementar en Azure App Service, vea <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="31d01-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="31d01-159">Implementación hospedada con varias aplicaciones Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="31d01-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="31d01-160">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="31d01-160">App configuration</span></span>

<span data-ttu-id="31d01-161">Para configurar una solución de Blazor hospedada para atender a varias aplicaciones Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="31d01-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="31d01-162">Use una solución de Blazor hospedada existente o cree una solución a partir de la plantilla de proyecto hospedada de Blazor.</span><span class="sxs-lookup"><span data-stu-id="31d01-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="31d01-163">En el archivo de proyecto de la aplicación cliente, agregue una propiedad `<StaticWebAssetBasePath>` a `<PropertyGroup>` con un valor de `FirstApp` para establecer la ruta de acceso base para los recursos estáticos del proyecto:</span><span class="sxs-lookup"><span data-stu-id="31d01-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="31d01-164">Agregue una segunda aplicación cliente a la solución:</span><span class="sxs-lookup"><span data-stu-id="31d01-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="31d01-165">Agregue una carpeta denominada `SecondClient` a la carpeta de la solución.</span><span class="sxs-lookup"><span data-stu-id="31d01-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="31d01-166">Cree una aplicación Blazor WebAssembly denominada `SecondBlazorApp.Client` en la carpeta `SecondClient` de la plantilla de proyecto de Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="31d01-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="31d01-167">En el archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="31d01-167">In the app's project file:</span></span>

    * <span data-ttu-id="31d01-168">Agregue una propiedad `<StaticWebAssetBasePath>` a `<PropertyGroup>` con un valor de `SecondApp`:</span><span class="sxs-lookup"><span data-stu-id="31d01-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="31d01-169">Agregue una referencia de proyecto al proyecto `Shared`:</span><span class="sxs-lookup"><span data-stu-id="31d01-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="31d01-170">El marcador de posición `{SOLUTION NAME}` es el nombre de la solución.</span><span class="sxs-lookup"><span data-stu-id="31d01-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="31d01-171">En el archivo del proyecto de la aplicación de servidor, cree una referencia de proyecto para la aplicación cliente agregada:</span><span class="sxs-lookup"><span data-stu-id="31d01-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="31d01-172">En el archivo `Properties/launchSettings.json` de la aplicación de servidor, configure el elemento `applicationUrl` del perfil de Kestrel (`{SOLUTION NAME}.Server`) para acceder a las aplicaciones cliente en los puertos 5001 y 5002:</span><span class="sxs-lookup"><span data-stu-id="31d01-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="31d01-173">En el método `Startup.Configure` de la aplicación de servidor (`Startup.cs`), quite las líneas siguientes, que aparecen después de la llamada a <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span><span class="sxs-lookup"><span data-stu-id="31d01-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="31d01-174">Agregue middleware que asigna solicitudes a las aplicaciones cliente.</span><span class="sxs-lookup"><span data-stu-id="31d01-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="31d01-175">En el ejemplo siguiente se configura el middleware para que se ejecute cuando:</span><span class="sxs-lookup"><span data-stu-id="31d01-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="31d01-176">El puerto de solicitud es 5001 para la aplicación cliente original o 5002 para la aplicación cliente agregada.</span><span class="sxs-lookup"><span data-stu-id="31d01-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="31d01-177">El host de solicitud es `firstapp.com` para la aplicación cliente original o `secondapp.com` para la aplicación cliente agregada.</span><span class="sxs-lookup"><span data-stu-id="31d01-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="31d01-178">El ejemplo que se muestra en esta sección requiere una configuración adicional para:</span><span class="sxs-lookup"><span data-stu-id="31d01-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="31d01-179">Acceder a las aplicaciones en los dominios de host de ejemplo, `firstapp.com` y `secondapp.com`.</span><span class="sxs-lookup"><span data-stu-id="31d01-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="31d01-180">Certificados que permitan a las aplicaciones cliente habilitar la seguridad TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="31d01-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="31d01-181">La configuración necesaria queda fuera del ámbito de este artículo y depende de cómo se hospede la solución.</span><span class="sxs-lookup"><span data-stu-id="31d01-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="31d01-182">Para más información, vea los [artículos sobre hospedaje e implementación](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="31d01-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="31d01-183">Coloque el código siguiente donde se quitaron las líneas anteriormente:</span><span class="sxs-lookup"><span data-stu-id="31d01-183">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="31d01-184">En el controlador de pronóstico meteorológico (`Controllers/WeatherForecastController.cs`) de la aplicación de servidor, reemplace la ruta existente (`[Route("[controller]")]`) a `WeatherForecastController` por las rutas siguientes:</span><span class="sxs-lookup"><span data-stu-id="31d01-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="31d01-185">El middleware agregado al método `Startup.Configure` de la aplicación de servidor anteriormente modifica las solicitudes entrantes a `/WeatherForecast` por `/FirstApp/WeatherForecast` o `/SecondApp/WeatherForecast` en función del puerto (5001/5002) o el dominio (`firstapp.com`/`secondapp.com`).</span><span class="sxs-lookup"><span data-stu-id="31d01-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="31d01-186">Las rutas de controlador anteriores son necesarias para devolver datos meteorológicos de la aplicación de servidor a las aplicaciones cliente.</span><span class="sxs-lookup"><span data-stu-id="31d01-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="31d01-187">Recursos estáticos y bibliotecas de clases</span><span class="sxs-lookup"><span data-stu-id="31d01-187">Static assets and class libraries</span></span>

<span data-ttu-id="31d01-188">Use los métodos siguientes para los recursos estáticos:</span><span class="sxs-lookup"><span data-stu-id="31d01-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="31d01-189">Cuando el recurso se encuentra en la carpeta `wwwroot` de la aplicación cliente, proporcione sus rutas de acceso de la forma habitual:</span><span class="sxs-lookup"><span data-stu-id="31d01-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="31d01-190">Cuando el recurso se encuentra en la carpeta `wwwroot` de una [biblioteca de clases de Razor (RCL)](xref:blazor/components/class-libraries), haga referencia al recurso estático de la aplicación cliente según las instrucciones del [artículo de RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="31d01-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="31d01-191">Se hace referencia de la forma habitual a los componentes que una biblioteca de clases proporciona a una aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="31d01-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="31d01-192">Si algún componente requiere hojas de estilo o archivos JavaScript, el archivo `wwwroot/index.html` de la aplicación cliente debe incluir los vínculos de recursos estáticos correctos.</span><span class="sxs-lookup"><span data-stu-id="31d01-192">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="31d01-193">Estos enfoques se demuestran en los ejemplos siguientes.</span><span class="sxs-lookup"><span data-stu-id="31d01-193">These approaches are demonstrated in the following examples.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="31d01-194">Agregue el siguiente componente `Jeep` a una de las aplicaciones cliente.</span><span class="sxs-lookup"><span data-stu-id="31d01-194">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="31d01-195">El componente `Jeep` usa:</span><span class="sxs-lookup"><span data-stu-id="31d01-195">The `Jeep` component uses:</span></span>

* <span data-ttu-id="31d01-196">Una imagen de la carpeta `wwwroot` de la aplicación cliente (`jeep-cj.png`).</span><span class="sxs-lookup"><span data-stu-id="31d01-196">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="31d01-197">Una imagen de una carpeta `wwwroot` de la [biblioteca de componentes de Razor agregada](xref:blazor/components/class-libraries) (`JeepImage`) (`jeep-yj.png`).</span><span class="sxs-lookup"><span data-stu-id="31d01-197">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="31d01-198">La plantilla del proyecto de RCL crea automáticamente el componente de ejemplo (`Component1`) cuando se agrega la biblioteca `JeepImage` a la solución.</span><span class="sxs-lookup"><span data-stu-id="31d01-198">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="31d01-199">**No** publique imágenes de vehículos a menos que sean de su titularidad.</span><span class="sxs-lookup"><span data-stu-id="31d01-199">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="31d01-200">De lo contrario, corre el riesgo de infringir los derechos de autor.</span><span class="sxs-lookup"><span data-stu-id="31d01-200">Otherwise, you risk copyright infringement.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="31d01-201">También se puede agregar la imagen `jeep-yj.png` de la biblioteca al componente `Component1` de la biblioteca (`Component1.razor`):</span><span class="sxs-lookup"><span data-stu-id="31d01-201">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="31d01-202">El archivo `wwwroot/index.html` de la aplicación cliente solicita la hoja de estilo de la biblioteca con la siguiente etiqueta `<link>` agregada:</span><span class="sxs-lookup"><span data-stu-id="31d01-202">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="31d01-203">Agregue la navegación al componente `Jeep` en el componente `NavMenu` de la aplicación cliente (`Shared/NavMenu.razor`):</span><span class="sxs-lookup"><span data-stu-id="31d01-203">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="31d01-204">Para obtener más información sobre RCL, vea:</span><span class="sxs-lookup"><span data-stu-id="31d01-204">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="31d01-205">Implementación independiente</span><span class="sxs-lookup"><span data-stu-id="31d01-205">Standalone deployment</span></span>

<span data-ttu-id="31d01-206">Una *implementación independiente* suministra la aplicación Blazor WebAssembly como un conjunto de archivos estáticos que los clientes solicitan directamente.</span><span class="sxs-lookup"><span data-stu-id="31d01-206">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="31d01-207">Cualquier servidor de archivos estático es capaz de suministrar la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="31d01-207">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="31d01-208">Los recursos de implementación independientes se publican en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="31d01-208">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="31d01-209">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="31d01-209">Azure App Service</span></span>

<span data-ttu-id="31d01-210">Las aplicaciones Blazor WebAssembly se pueden implementar en Azure App Services en Windows, que hospeda la aplicación en [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="31d01-210">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="31d01-211">Actualmente no se admite la implementación de una aplicación Blazor WebAssembly independiente en Azure App Service para Linux.</span><span class="sxs-lookup"><span data-stu-id="31d01-211">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="31d01-212">En este momento no hay disponible una imagen de servidor de Linux para hospedar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-212">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="31d01-213">Se está trabajando para habilitar este escenario.</span><span class="sxs-lookup"><span data-stu-id="31d01-213">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="31d01-214">IIS</span><span class="sxs-lookup"><span data-stu-id="31d01-214">IIS</span></span>

<span data-ttu-id="31d01-215">IIS es un servidor de archivos estáticos compatible con las aplicaciones Blazor.</span><span class="sxs-lookup"><span data-stu-id="31d01-215">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="31d01-216">Para configurar IIS para hospedar Blazor, consulte [Compilación de un sitio web estático en IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="31d01-216">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="31d01-217">Los recursos publicados se crean en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish`.</span><span class="sxs-lookup"><span data-stu-id="31d01-217">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="31d01-218">Hospede el contenido de la carpeta `publish` en el servidor web o el servicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="31d01-218">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="31d01-219">web.config</span><span class="sxs-lookup"><span data-stu-id="31d01-219">web.config</span></span>

<span data-ttu-id="31d01-220">Cuando se publica un proyecto de Blazor, se crea un archivo `web.config` con la siguiente configuración de IIS:</span><span class="sxs-lookup"><span data-stu-id="31d01-220">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="31d01-221">Se establecen los tipos MIME de las siguientes extensiones de archivo:</span><span class="sxs-lookup"><span data-stu-id="31d01-221">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="31d01-222">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="31d01-222">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="31d01-223">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="31d01-223">`.json`: `application/json`</span></span>
  * <span data-ttu-id="31d01-224">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="31d01-224">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="31d01-225">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="31d01-225">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="31d01-226">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="31d01-226">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="31d01-227">Se habilita la compresión HTTP de los siguientes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="31d01-227">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="31d01-228">Se establecen reglas del módulo URL Rewrite:</span><span class="sxs-lookup"><span data-stu-id="31d01-228">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="31d01-229">Proporcione el subdirectorio donde residen los recursos estáticos de la aplicación (`wwwroot/{PATH REQUESTED}`).</span><span class="sxs-lookup"><span data-stu-id="31d01-229">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="31d01-230">Cree el enrutamiento de reserva de SPA para que las solicitudes de recursos que no sean archivos se redirijan al documento predeterminado de la aplicación en su carpeta de recursos estáticos (`wwwroot/index.html`).</span><span class="sxs-lookup"><span data-stu-id="31d01-230">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="31d01-231">Uso de un archivo web.config personalizado</span><span class="sxs-lookup"><span data-stu-id="31d01-231">Use a custom web.config</span></span>

<span data-ttu-id="31d01-232">Para usar un archivo `web.config` personalizado, coloque el archivo `web.config` personalizado en la raíz de la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="31d01-232">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="31d01-233">Configure el proyecto para publicar recursos específicos de IIS mediante `PublishIISAssets` en el archivo de proyecto de la aplicación y publique el proyecto:</span><span class="sxs-lookup"><span data-stu-id="31d01-233">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="31d01-234">Instalación del módulo URL Rewrite</span><span class="sxs-lookup"><span data-stu-id="31d01-234">Install the URL Rewrite Module</span></span>

<span data-ttu-id="31d01-235">El [módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) es necesario para reescribir las URL.</span><span class="sxs-lookup"><span data-stu-id="31d01-235">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="31d01-236">El módulo no se instala de forma predeterminada y no está disponible para instalar como una característica de servicio de rol del servidor web (IIS).</span><span class="sxs-lookup"><span data-stu-id="31d01-236">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="31d01-237">El módulo se debe descargar desde el sitio web de IIS.</span><span class="sxs-lookup"><span data-stu-id="31d01-237">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="31d01-238">Use el instalador de plataforma web para instalar el módulo:</span><span class="sxs-lookup"><span data-stu-id="31d01-238">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="31d01-239">De forma local, vaya a la [página de descargas del módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="31d01-239">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="31d01-240">En el caso de la versión en inglés, seleccione **WebPI** para descargar el instalador de WebPI.</span><span class="sxs-lookup"><span data-stu-id="31d01-240">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="31d01-241">En el caso de otros idiomas, seleccione la arquitectura adecuada del servidor (x86/x64) para descargar el instalador.</span><span class="sxs-lookup"><span data-stu-id="31d01-241">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="31d01-242">Copie el instalador en el servidor.</span><span class="sxs-lookup"><span data-stu-id="31d01-242">Copy the installer to the server.</span></span> <span data-ttu-id="31d01-243">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="31d01-243">Run the installer.</span></span> <span data-ttu-id="31d01-244">Haga clic en el botón **Instalar** y acepte los términos de licencia.</span><span class="sxs-lookup"><span data-stu-id="31d01-244">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="31d01-245">No es necesario reiniciar el servidor al finalizar la instalación.</span><span class="sxs-lookup"><span data-stu-id="31d01-245">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="31d01-246">Configuración del sitio web</span><span class="sxs-lookup"><span data-stu-id="31d01-246">Configure the website</span></span>

<span data-ttu-id="31d01-247">Configure la **ruta de acceso física** del sitio web a la carpeta de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-247">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="31d01-248">La carpeta contiene:</span><span class="sxs-lookup"><span data-stu-id="31d01-248">The folder contains:</span></span>

* <span data-ttu-id="31d01-249">El archivo `web.config` que usa IIS para configurar el sitio web, incluidas las reglas de redireccionamiento y los tipos de contenido de archivo necesarios.</span><span class="sxs-lookup"><span data-stu-id="31d01-249">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="31d01-250">La carpeta de recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-250">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="31d01-251">Hospedaje como subaplicación de IIS</span><span class="sxs-lookup"><span data-stu-id="31d01-251">Host as an IIS sub-app</span></span>

<span data-ttu-id="31d01-252">Si una aplicación independiente se hospeda como una subaplicación de IIS, realice una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="31d01-252">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="31d01-253">Deshabilite el controlador del módulo de ASP.NET Core heredado.</span><span class="sxs-lookup"><span data-stu-id="31d01-253">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="31d01-254">Para quitar el controlador del archivo `web.config` publicado de la aplicación Blazor, agregue una sección `<handlers>` al archivo:</span><span class="sxs-lookup"><span data-stu-id="31d01-254">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="31d01-255">Deshabilite la herencia de la sección `<system.webServer>` de la aplicación raíz (principal) mediante un elemento `<location>` con `inheritInChildApplications` establecido en `false`:</span><span class="sxs-lookup"><span data-stu-id="31d01-255">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="31d01-256">Además de [configurarse la ruta de acceso base de la aplicación](xref:blazor/host-and-deploy/index#app-base-path), se quita el controlador o se deshabilita la herencia.</span><span class="sxs-lookup"><span data-stu-id="31d01-256">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="31d01-257">En el archivo `index.html` de la aplicación, establezca la ruta de acceso base de la aplicación en el alias de IIS que ha usado al configurar la subaplicación en IIS.</span><span class="sxs-lookup"><span data-stu-id="31d01-257">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="31d01-258">Compresión Brotli y Gzip</span><span class="sxs-lookup"><span data-stu-id="31d01-258">Brotli and Gzip compression</span></span>

<span data-ttu-id="31d01-259">IIS se puede configurar a través de `web.config` para dar servicio a recursos de Blazor comprimidos con Brotli o Gzip.</span><span class="sxs-lookup"><span data-stu-id="31d01-259">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="31d01-260">Para ver una configuración de ejemplo, consulte [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="31d01-260">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="31d01-261">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="31d01-261">Troubleshooting</span></span>

<span data-ttu-id="31d01-262">Si se recibe un error *500 Error interno del servidor* y el administrador de IIS produce errores al intentar acceder a la configuración del sitio web, confirme que el módulo URL Rewrite está instalado.</span><span class="sxs-lookup"><span data-stu-id="31d01-262">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="31d01-263">Si no lo está, IIS no puede analizar el archivo `web.config`.</span><span class="sxs-lookup"><span data-stu-id="31d01-263">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="31d01-264">Esto impide que el Administrador de IIS cargue la configuración del sitio web y que el sitio web suministre los archivos estáticos de Blazor.</span><span class="sxs-lookup"><span data-stu-id="31d01-264">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="31d01-265">Para obtener más información sobre cómo solucionar problemas de las implementaciones en IIS, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="31d01-265">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="31d01-266">Almacenamiento de Azure</span><span class="sxs-lookup"><span data-stu-id="31d01-266">Azure Storage</span></span>

<span data-ttu-id="31d01-267">El hospedaje de archivos estáticos de [Azure Storage](/azure/storage/) permite el hospedaje de aplicaciones Blazor sin servidor.</span><span class="sxs-lookup"><span data-stu-id="31d01-267">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="31d01-268">Se admiten nombres de dominio personalizados, Azure Content Delivery Network (CDN) y HTTPS.</span><span class="sxs-lookup"><span data-stu-id="31d01-268">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="31d01-269">Cuando el servicio de blob está habilitado para el hospedaje de sitios web estáticos en una cuenta de almacenamiento:</span><span class="sxs-lookup"><span data-stu-id="31d01-269">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="31d01-270">Establece el **nombre de documento de índice** en `index.html`.</span><span class="sxs-lookup"><span data-stu-id="31d01-270">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="31d01-271">Establece la **ruta de acceso del documento de error** en `index.html`.</span><span class="sxs-lookup"><span data-stu-id="31d01-271">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="31d01-272">Los componentes Razor y otros puntos de conexión que no son de archivo no residen en las rutas de acceso físicas del contenido estático almacenado por el servicio de blob.</span><span class="sxs-lookup"><span data-stu-id="31d01-272">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="31d01-273">Cuando se recibe una solicitud de uno de estos recursos que debe controlar el enrutador de Blazor, el error *404 - No encontrado* generado por el servicio de blob enruta la solicitud a la **ruta de acceso del documento de error**.</span><span class="sxs-lookup"><span data-stu-id="31d01-273">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="31d01-274">Se devuelve el blob `index.html`, y el enrutador de Blazor carga y procesa la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="31d01-274">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="31d01-275">Si los archivos no se cargan en tiempo de ejecución debido a tipos MIME inadecuados en los encabezados `Content-Type` de los archivos, haga algunas de las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="31d01-275">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="31d01-276">Configure las herramientas para establecer los tipos MIME correctos (encabezados `Content-Type`) cuando se implementen los archivos.</span><span class="sxs-lookup"><span data-stu-id="31d01-276">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="31d01-277">Cambie los tipos MIME (encabezados `Content-Type`) de los archivos una vez que se implementa la aplicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-277">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="31d01-278">En cada archivo del Explorador de Storage (Azure Portal) haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="31d01-278">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="31d01-279">Haga clic con el botón derecho en el archivo y seleccione **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="31d01-279">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="31d01-280">Establezca el valor de **ContentType** y seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="31d01-280">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="31d01-281">Para más información, consulte [Hospedaje de sitios web estáticos en Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="31d01-281">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="31d01-282">Nginx</span><span class="sxs-lookup"><span data-stu-id="31d01-282">Nginx</span></span>

<span data-ttu-id="31d01-283">El siguiente archivo `nginx.conf` se ha simplificado para mostrar cómo hay que configurar Nginx para enviar el archivo `index.html` siempre que no pueda encontrar un archivo correspondiente en el disco.</span><span class="sxs-lookup"><span data-stu-id="31d01-283">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="31d01-284">Al establecer el [límite de velocidad de ráfaga de NGINX](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) con [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), las aplicaciones de Blazor WebAssembly pueden requerir un valor de parámetro `burst` grande para acomodar el número relativamente elevado de solicitudes realizadas por una aplicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-284">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="31d01-285">Inicialmente, establezca el valor en al menos 60:</span><span class="sxs-lookup"><span data-stu-id="31d01-285">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="31d01-286">Aumente el valor si las herramientas de desarrollo del explorador o la herramienta de tráfico de red indican que las solicitudes reciben un código de estado *503: Servicio no disponible*.</span><span class="sxs-lookup"><span data-stu-id="31d01-286">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="31d01-287">Para obtener más información sobre la configuración del servidor web de producción de Nginx, consulte [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creación de archivos de configuración de NGINX y NGINX Plus).</span><span class="sxs-lookup"><span data-stu-id="31d01-287">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="31d01-288">Nginx en Docker</span><span class="sxs-lookup"><span data-stu-id="31d01-288">Nginx in Docker</span></span>

<span data-ttu-id="31d01-289">Para hospedar Blazor en Docker mediante Nginx, configure Dockerfile para usar la imagen de Nginx basada en Alpine.</span><span class="sxs-lookup"><span data-stu-id="31d01-289">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="31d01-290">Actualice el Dockerfile para copiar el archivo `nginx.config` en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="31d01-290">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="31d01-291">Agregue una línea al Dockerfile, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="31d01-291">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="31d01-292">Apache</span><span class="sxs-lookup"><span data-stu-id="31d01-292">Apache</span></span>

<span data-ttu-id="31d01-293">Para implementar una aplicación Blazor WebAssembly en CentOS 7 o posterior:</span><span class="sxs-lookup"><span data-stu-id="31d01-293">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="31d01-294">Cree el archivo de configuración de Apache.</span><span class="sxs-lookup"><span data-stu-id="31d01-294">Create the Apache configuration file.</span></span> <span data-ttu-id="31d01-295">El siguiente ejemplo es un archivo de configuración simplificado (`blazorapp.config`):</span><span class="sxs-lookup"><span data-stu-id="31d01-295">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="31d01-296">Coloque el archivo de configuración de Apache en el directorio `/etc/httpd/conf.d/`, que es el directorio de configuración de Apache predeterminado en CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="31d01-296">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="31d01-297">Coloque los archivos de la aplicación en el directorio `/var/www/blazorapp` (la ubicación especificada para `DocumentRoot` en el archivo de configuración).</span><span class="sxs-lookup"><span data-stu-id="31d01-297">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="31d01-298">Reinicie el servicio de Apache.</span><span class="sxs-lookup"><span data-stu-id="31d01-298">Restart the Apache service.</span></span>

<span data-ttu-id="31d01-299">Para más información, vea [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) y [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="31d01-299">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="31d01-300">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="31d01-300">GitHub Pages</span></span>

<span data-ttu-id="31d01-301">Para controlar las reescrituras de URL, agregue un archivo `wwwroot/404.html` con un script que controle el redireccionamiento de la solicitud a la página `index.html`.</span><span class="sxs-lookup"><span data-stu-id="31d01-301">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="31d01-302">Para obtener un ejemplo, vea el [repositorio de GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="31d01-302">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="31d01-303">[Sitio activo](https://stevesandersonms.github.io/BlazorOnGitHubPages/)</span><span class="sxs-lookup"><span data-stu-id="31d01-303">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="31d01-304">Si usa un sitio de proyecto en lugar de un sitio de la organización, actualice la etiqueta `<base>` en `wwwroot/index.html`.</span><span class="sxs-lookup"><span data-stu-id="31d01-304">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="31d01-305">Defina el valor del atributo `href` con el nombre del repositorio de GitHub con una barra diagonal final (por ejemplo, `/my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="31d01-305">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="31d01-306">En el [repositorio de GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), el elemento `href` base se actualiza cuando el [archivo de configuración `.github/workflows/main.yml`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml) realiza la publicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-306">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="31d01-307">El [repositorio de GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) no es propiedad de .NET Foundation o Microsoft, ni tampoco se encargan de su mantenimiento ni es compatible con ellos.</span><span class="sxs-lookup"><span data-stu-id="31d01-307">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="31d01-308">Valores de configuración de host</span><span class="sxs-lookup"><span data-stu-id="31d01-308">Host configuration values</span></span>

<span data-ttu-id="31d01-309">Las [aplicaciones Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) pueden aceptar los siguientes valores de configuración de host como argumentos de línea de comandos en tiempo de ejecución en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="31d01-309">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="31d01-310">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="31d01-310">Content root</span></span>

<span data-ttu-id="31d01-311">El argumento `--contentroot` establece la ruta de acceso absoluta al directorio que incluye los archivos de contenido de la aplicación ([raíz del contenido](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="31d01-311">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="31d01-312">En los ejemplos siguientes, `/content-root-path` es la ruta de acceso raíz del contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-312">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="31d01-313">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="31d01-313">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="31d01-314">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="31d01-314">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="31d01-315">Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="31d01-315">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="31d01-316">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="31d01-316">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="31d01-317">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="31d01-317">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="31d01-318">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="31d01-318">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="31d01-319">Ruta de acceso base</span><span class="sxs-lookup"><span data-stu-id="31d01-319">Path base</span></span>

<span data-ttu-id="31d01-320">El argumento `--pathbase` establece la ruta de acceso base de la aplicación para una aplicación que se ejecuta localmente con una ruta de acceso de URL relativa que no es raíz (el valor `href` de la etiqueta `<base>` se establece en una ruta de acceso que no sea `/` para ensayo y producción).</span><span class="sxs-lookup"><span data-stu-id="31d01-320">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="31d01-321">En los ejemplos siguientes, `/relative-URL-path` es la ruta de acceso base de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-321">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="31d01-322">Para obtener más información, vea [Ruta de acceso base de la aplicación](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="31d01-322">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="31d01-323">A diferencia de la ruta de acceso proporcionada al valor `href` de la etiqueta `<base>`, no incluya una barra diagonal final (`/`) al pasar el valor del argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="31d01-323">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="31d01-324">Si se proporciona la ruta de acceso base de la aplicación en la etiqueta `<base>` como `<base href="/CoolApp/">` (se incluye una barra diagonal final), se pasa el valor del argumento de línea de comandos como `--pathbase=/CoolApp` (sin barra diagonal final).</span><span class="sxs-lookup"><span data-stu-id="31d01-324">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="31d01-325">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="31d01-325">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="31d01-326">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="31d01-326">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="31d01-327">Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="31d01-327">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="31d01-328">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="31d01-328">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="31d01-329">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="31d01-329">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="31d01-330">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="31d01-330">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="31d01-331">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="31d01-331">URLs</span></span>

<span data-ttu-id="31d01-332">El argumento `--urls` establece las direcciones IP o las direcciones de host con los puertos y protocolos en los que escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="31d01-332">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="31d01-333">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="31d01-333">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="31d01-334">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="31d01-334">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="31d01-335">Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="31d01-335">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="31d01-336">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="31d01-336">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="31d01-337">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="31d01-337">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="31d01-338">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="31d01-338">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="31d01-339">Configurar el recortador</span><span class="sxs-lookup"><span data-stu-id="31d01-339">Configure the Trimmer</span></span>

<span data-ttu-id="31d01-340">Blazor realiza el recorte de lenguaje intermedio (IL) en cada compilación de lanzamiento para quitar el IL innecesario de los ensamblados de salida.</span><span class="sxs-lookup"><span data-stu-id="31d01-340">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="31d01-341">Para obtener más información, vea <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="31d01-341">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="31d01-342">Configurar el enlazador</span><span class="sxs-lookup"><span data-stu-id="31d01-342">Configure the Linker</span></span>

<span data-ttu-id="31d01-343">Blazor realiza la vinculación de lenguaje intermedio (IL) en cada compilación de lanzamiento para quitar el IL innecesario de los ensamblados de salida.</span><span class="sxs-lookup"><span data-stu-id="31d01-343">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="31d01-344">Para obtener más información, vea <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="31d01-344">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="31d01-345">Carga de recursos de arranque personalizados</span><span class="sxs-lookup"><span data-stu-id="31d01-345">Custom boot resource loading</span></span>

<span data-ttu-id="31d01-346">Una aplicación Blazor WebAssembly se puede inicializar con la función `loadBootResource` para invalidar el mecanismo de carga de recursos de arranque integrado.</span><span class="sxs-lookup"><span data-stu-id="31d01-346">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="31d01-347">Use `loadBootResource` en los siguientes escenarios:</span><span class="sxs-lookup"><span data-stu-id="31d01-347">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="31d01-348">Para permitir a los usuarios cargar recursos estáticos, como datos de zona horaria o `dotnet.wasm`, desde una red CDN.</span><span class="sxs-lookup"><span data-stu-id="31d01-348">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="31d01-349">Para cargar los ensamblados comprimidos mediante una solicitud HTTP y descomprimirlos en el cliente para aquellos hosts que no admiten la captura de contenido comprimido del servidor.</span><span class="sxs-lookup"><span data-stu-id="31d01-349">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="31d01-350">Para asignar otro alias a los recursos mediante el redireccionamiento de cada solicitud `fetch` a un nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="31d01-350">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="31d01-351">Los parámetros `loadBootResource` aparecen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="31d01-351">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="31d01-352">Parámetro</span><span class="sxs-lookup"><span data-stu-id="31d01-352">Parameter</span></span>    | <span data-ttu-id="31d01-353">Descripción</span><span class="sxs-lookup"><span data-stu-id="31d01-353">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="31d01-354">Tipo de recurso.</span><span class="sxs-lookup"><span data-stu-id="31d01-354">The type of the resource.</span></span> <span data-ttu-id="31d01-355">Tipos permitidos: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="31d01-355">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="31d01-356">Nombre del recurso.</span><span class="sxs-lookup"><span data-stu-id="31d01-356">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="31d01-357">URI relativo o absoluto del recurso.</span><span class="sxs-lookup"><span data-stu-id="31d01-357">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="31d01-358">Cadena de integridad que representa el contenido esperado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="31d01-358">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="31d01-359">`loadBootResource` devuelve cualquiera de los siguientes elementos para invalidar el proceso de carga:</span><span class="sxs-lookup"><span data-stu-id="31d01-359">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="31d01-360">Cadena URI.</span><span class="sxs-lookup"><span data-stu-id="31d01-360">URI string.</span></span> <span data-ttu-id="31d01-361">En el ejemplo siguiente (`wwwroot/index.html`), los siguientes archivos se sirven desde una red CDN en `https://my-awesome-cdn.com/`:</span><span class="sxs-lookup"><span data-stu-id="31d01-361">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="31d01-362">Datos de zona horaria</span><span class="sxs-lookup"><span data-stu-id="31d01-362">Timezone data</span></span>

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

* <span data-ttu-id="31d01-363">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="31d01-363">`Promise<Response>`.</span></span> <span data-ttu-id="31d01-364">Pase el parámetro `integrity` en un encabezado para conservar el comportamiento de comprobación de integridad predeterminado.</span><span class="sxs-lookup"><span data-stu-id="31d01-364">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="31d01-365">En el ejemplo siguiente (`wwwroot/index.html`), se agrega un encabezado HTTP personalizado a las solicitudes salientes y se pasa el parámetro `integrity` a través de la llamada a `fetch`:</span><span class="sxs-lookup"><span data-stu-id="31d01-365">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="31d01-366">`null`/`undefined`, que da lugar al comportamiento de carga predeterminado.</span><span class="sxs-lookup"><span data-stu-id="31d01-366">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="31d01-367">Los orígenes externos deben devolver los encabezados CORS necesarios para que los exploradores permitan la carga de recursos entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="31d01-367">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="31d01-368">Normalmente, las redes CDN proporcionan los encabezados necesarios de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="31d01-368">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="31d01-369">Solo tiene que especificar tipos para los comportamientos personalizados.</span><span class="sxs-lookup"><span data-stu-id="31d01-369">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="31d01-370">Los tipos no especificados en `loadBootResource` se cargan mediante el marco de trabajo según sus comportamientos de carga predeterminados.</span><span class="sxs-lookup"><span data-stu-id="31d01-370">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="31d01-371">Cambio de la extensión de nombre de archivo de los archivos DLL</span><span class="sxs-lookup"><span data-stu-id="31d01-371">Change the filename extension of DLL files</span></span>

<span data-ttu-id="31d01-372">En caso de que necesite cambiar las extensiones de nombre de los archivos `.dll` publicados de la aplicación, siga las instrucciones de esta sección.</span><span class="sxs-lookup"><span data-stu-id="31d01-372">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="31d01-373">Después de publicar la aplicación, use un script de shell o una canalización de compilación de DevOps para cambiar el nombre de los archivos `.dll` a fin de usar otra extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="31d01-373">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="31d01-374">Establezca como destino los archivos `.dll` que están en el directorio `wwwroot` de la salida publicada de la aplicación (por ejemplo, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span><span class="sxs-lookup"><span data-stu-id="31d01-374">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="31d01-375">En los ejemplos siguientes se cambia el nombre de los archivos `.dll` para que usen la extensión de archivo `.bin`.</span><span class="sxs-lookup"><span data-stu-id="31d01-375">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="31d01-376">En Windows:</span><span class="sxs-lookup"><span data-stu-id="31d01-376">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="31d01-377">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="31d01-377">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="31d01-378">En Linux o macOS:</span><span class="sxs-lookup"><span data-stu-id="31d01-378">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="31d01-379">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="31d01-379">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="31d01-380">Para usar una extensión de archivo diferente a `.bin`, reemplace `.bin` en los comandos anteriores.</span><span class="sxs-lookup"><span data-stu-id="31d01-380">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="31d01-381">Para dirigirse a los archivos comprimidos `blazor.boot.json.gz` y `blazor.boot.json.br`, adopte uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="31d01-381">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="31d01-382">Quite los archivos comprimidos `blazor.boot.json.gz` y `blazor.boot.json.br`.</span><span class="sxs-lookup"><span data-stu-id="31d01-382">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="31d01-383">Con este enfoque, la compresión está deshabilitada.</span><span class="sxs-lookup"><span data-stu-id="31d01-383">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="31d01-384">Vuelva a comprimir el archivo `blazor.boot.json` actualizado.</span><span class="sxs-lookup"><span data-stu-id="31d01-384">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="31d01-385">Las instrucciones anteriores también se aplican cuando se usan recursos de trabajo de servicio.</span><span class="sxs-lookup"><span data-stu-id="31d01-385">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="31d01-386">Quite o vuelva a comprimir `wwwroot/service-worker-assets.js.br` y `wwwroot/service-worker-assets.js.gz`.</span><span class="sxs-lookup"><span data-stu-id="31d01-386">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="31d01-387">De lo contrario, las comprobaciones de integridad de los archivos producirán errores en el explorador.</span><span class="sxs-lookup"><span data-stu-id="31d01-387">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="31d01-388">En el siguiente ejemplo de Windows se usa un script de PowerShell colocado en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="31d01-388">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="31d01-389">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="31d01-389">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="31d01-390">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="31d01-390">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="31d01-391">En el archivo del proyecto, el script se ejecuta después de publicar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="31d01-391">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="31d01-392">Al cambiar el nombre y la carga diferida de los mismos ensamblados, vea la guía de <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span><span class="sxs-lookup"><span data-stu-id="31d01-392">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="31d01-393">Resolución de errores de comprobación de integridad</span><span class="sxs-lookup"><span data-stu-id="31d01-393">Resolve integrity check failures</span></span>

<span data-ttu-id="31d01-394">Cuando Blazor WebAssembly descarga los archivos de inicio de una aplicación, le indica al navegador que realice comprobaciones de integridad en las respuestas.</span><span class="sxs-lookup"><span data-stu-id="31d01-394">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="31d01-395">Usa la información del archivo `blazor.boot.json` para especificar los valores hash de SHA-256 esperados para `.dll`, `.wasm` y otros archivos.</span><span class="sxs-lookup"><span data-stu-id="31d01-395">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="31d01-396">Esto es beneficioso por los siguientes motivos:</span><span class="sxs-lookup"><span data-stu-id="31d01-396">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="31d01-397">Garantiza que no se corre el riesgo de cargar un conjunto de archivos incoherentes, por ejemplo, si se aplica una nueva implementación al servidor web mientras el usuario se encuentra en el proceso de descarga de los archivos de aplicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-397">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="31d01-398">Los archivos incoherentes pueden provocar un comportamiento indefinido.</span><span class="sxs-lookup"><span data-stu-id="31d01-398">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="31d01-399">Garantiza que el explorador del usuario nunca almacene en caché las respuestas incoherentes o no válidas, lo que podría impedir que se inicie la aplicación aunque actualice manualmente la página.</span><span class="sxs-lookup"><span data-stu-id="31d01-399">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="31d01-400">Hace que sea seguro almacenar en caché las respuestas y ni siquiera comprobar los cambios en el servidor hasta que cambien los algoritmos hash SHA-256 esperados, por lo que las cargas de páginas posteriores implican menos solicitudes y se completan mucho más rápido.</span><span class="sxs-lookup"><span data-stu-id="31d01-400">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="31d01-401">Si el servidor web devuelve respuestas que no coinciden con los algoritmos hash SHA-256 esperados, verá un error similar al siguiente en la consola del desarrollador del explorador:</span><span class="sxs-lookup"><span data-stu-id="31d01-401">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

<span data-ttu-id="31d01-402">En la mayoría de los casos, esto *no* supone un problema con la propia comprobación de la integridad.</span><span class="sxs-lookup"><span data-stu-id="31d01-402">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="31d01-403">Significa en realidad que hay algún otro problema del cual le está advirtiendo la comprobación de la integridad.</span><span class="sxs-lookup"><span data-stu-id="31d01-403">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="31d01-404">Diagnóstico de problemas de integridad</span><span class="sxs-lookup"><span data-stu-id="31d01-404">Diagnosing integrity problems</span></span>

<span data-ttu-id="31d01-405">Cuando se compila una aplicación, el manifiesto de `blazor.boot.json` generado describe los algoritmos hash SHA-256 de los recursos de arranque (por ejemplo, `.dll`, `.wasm` y otros archivos) en el momento en que se genera el resultado de la compilación.</span><span class="sxs-lookup"><span data-stu-id="31d01-405">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="31d01-406">La comprobación de integridad se supera siempre que los algoritmos hash SHA-256 de `blazor.boot.json` coincidan con los archivos entregados al explorador.</span><span class="sxs-lookup"><span data-stu-id="31d01-406">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="31d01-407">Los motivos comunes por los que se produce un error son:</span><span class="sxs-lookup"><span data-stu-id="31d01-407">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="31d01-408">La respuesta del servidor web es un error (por ejemplo, *404 No encontrado* o *500 Error interno del servidor* ) en lugar del archivo solicitado por el explorador.</span><span class="sxs-lookup"><span data-stu-id="31d01-408">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error* ) instead of the file the browser requested.</span></span> <span data-ttu-id="31d01-409">El explorador lo detecta como un error de comprobación de integridad y no como un error de respuesta.</span><span class="sxs-lookup"><span data-stu-id="31d01-409">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="31d01-410">Algo ha cambiado el contenido de los archivos entre la compilación y la entrega de los archivos al explorador.</span><span class="sxs-lookup"><span data-stu-id="31d01-410">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="31d01-411">Esto puede ocurrir:</span><span class="sxs-lookup"><span data-stu-id="31d01-411">This might happen:</span></span>
   * <span data-ttu-id="31d01-412">Si el usuario o las herramientas de compilación modifican manualmente la salida de compilación.</span><span class="sxs-lookup"><span data-stu-id="31d01-412">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="31d01-413">Si algún aspecto del proceso de implementación ha modificado los archivos.</span><span class="sxs-lookup"><span data-stu-id="31d01-413">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="31d01-414">Por ejemplo, si usa un mecanismo de implementación basado en Git, tenga en cuenta que Git convierte de forma transparente los finales de línea de estilo Windows en finales de línea de estilo Unix si confirma archivos en Windows y los comprueba en Linux.</span><span class="sxs-lookup"><span data-stu-id="31d01-414">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="31d01-415">El cambio de los finales de línea de archivo cambia los algoritmos hash SHA-256.</span><span class="sxs-lookup"><span data-stu-id="31d01-415">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="31d01-416">Para evitar este problema, considere la posibilidad de [usar `.gitattributes` para tratar los artefactos de compilación como archivos `binary`](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span><span class="sxs-lookup"><span data-stu-id="31d01-416">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="31d01-417">El servidor web modifica el contenido del archivo como parte del servicio.</span><span class="sxs-lookup"><span data-stu-id="31d01-417">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="31d01-418">Por ejemplo, algunas redes de distribución de contenido (CDN) intentan automáticamente [minimizar](xref:client-side/bundling-and-minification#minification) HTML, con lo que se modifica.</span><span class="sxs-lookup"><span data-stu-id="31d01-418">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="31d01-419">Es posible que tenga que deshabilitar estas características.</span><span class="sxs-lookup"><span data-stu-id="31d01-419">You may need to disable such features.</span></span>

<span data-ttu-id="31d01-420">Para diagnosticar cuál de ellas se aplica en su caso:</span><span class="sxs-lookup"><span data-stu-id="31d01-420">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="31d01-421">Lea el mensaje de error para darse cuenta de qué archivo está desencadenando el error.</span><span class="sxs-lookup"><span data-stu-id="31d01-421">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="31d01-422">Abra las herramientas de desarrollo del explorador y mire en la pestaña *Red*. Si es necesario, vuelva a cargar la página para ver la lista de solicitudes y respuestas.</span><span class="sxs-lookup"><span data-stu-id="31d01-422">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="31d01-423">Busque el archivo que desencadena el error en esa lista.</span><span class="sxs-lookup"><span data-stu-id="31d01-423">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="31d01-424">Compruebe el código de estado HTTP en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="31d01-424">Check the HTTP status code in the response.</span></span> <span data-ttu-id="31d01-425">Si el servidor devuelve un valor distinto de *200 - Correcto* (u otro código de estado 2XX), tiene un problema de servidor por diagnosticar.</span><span class="sxs-lookup"><span data-stu-id="31d01-425">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="31d01-426">Por ejemplo, el código de estado 403 significa que hay un problema de autorización, mientras que el código de estado 500 significa que el servidor está dando error de una manera no especificada.</span><span class="sxs-lookup"><span data-stu-id="31d01-426">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="31d01-427">Consulte los registros del servidor para diagnosticar y corregir la aplicación.</span><span class="sxs-lookup"><span data-stu-id="31d01-427">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="31d01-428">Si el código de estado es *200 - Correcto* para el recurso, examine el contenido de la respuesta en las herramientas de desarrollo del explorador y compruebe que el contenido coincida con los datos esperados.</span><span class="sxs-lookup"><span data-stu-id="31d01-428">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matchs up with the data expected.</span></span> <span data-ttu-id="31d01-429">Por ejemplo, un problema común es configurar erróneamente el enrutamiento de modo que las solicitudes devuelvan los datos de `index.html` incluso para otros archivos.</span><span class="sxs-lookup"><span data-stu-id="31d01-429">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="31d01-430">Asegúrese de que las respuestas a las solicitudes de `.wasm` son archivos binarios de WebAssembly y que las respuestas a las solicitudes de `.dll` son archivos binarios de ensamblado de .NET.</span><span class="sxs-lookup"><span data-stu-id="31d01-430">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="31d01-431">Si no es así, tiene un problema de enrutamiento del lado servidor por diagnosticar.</span><span class="sxs-lookup"><span data-stu-id="31d01-431">If not, you have a server-side routing problem to diagnose.</span></span>

<span data-ttu-id="31d01-432">Si confirma que el servidor está devolviendo datos plausiblemente correctos, debe haber algo más que modifique el contenido entre la compilación y la entrega del archivo.</span><span class="sxs-lookup"><span data-stu-id="31d01-432">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="31d01-433">Para investigarlo:</span><span class="sxs-lookup"><span data-stu-id="31d01-433">To investigate this:</span></span>

 * <span data-ttu-id="31d01-434">Examine la cadena de herramientas de compilación y el mecanismo de implementación en caso de que estén modificando archivos después de compilarlos.</span><span class="sxs-lookup"><span data-stu-id="31d01-434">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="31d01-435">Un ejemplo de esto es cuando GIT transforma los finales de línea de los archivos, tal y como se ha descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="31d01-435">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="31d01-436">Examine el servidor web o la configuración de CDN en caso de que estén configurados para modificar las respuestas de forma dinámica (por ejemplo, intentando minimizar HTML).</span><span class="sxs-lookup"><span data-stu-id="31d01-436">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="31d01-437">Está adecuado que el servidor web implemente la compresión HTTP (por ejemplo, devolviendo `content-encoding: br` o `content-encoding: gzip`), ya que esto no afecta al resultado después de la descompresión.</span><span class="sxs-lookup"><span data-stu-id="31d01-437">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="31d01-438">Sin embargo, *no* es adecuado que el servidor web modifique los datos sin comprimir.</span><span class="sxs-lookup"><span data-stu-id="31d01-438">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="31d01-439">Deshabilitación de la comprobación de integridad para aplicaciones que no son de PWA</span><span class="sxs-lookup"><span data-stu-id="31d01-439">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="31d01-440">En la mayoría de los casos, no deshabilite la comprobación de integridad.</span><span class="sxs-lookup"><span data-stu-id="31d01-440">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="31d01-441">Al deshabilitar la comprobación de integridad, no se soluciona el problema subyacente que ha causado las respuestas inesperadas y se pierden las ventajas mencionadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="31d01-441">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="31d01-442">Puede haber casos en los que no se pueda confiar en el servidor web para que devuelva respuestas coherentes, y solo le queda la opción de deshabilitar las comprobaciones de integridad.</span><span class="sxs-lookup"><span data-stu-id="31d01-442">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="31d01-443">Para deshabilitar las comprobaciones de integridad, agregue lo siguiente a un grupo de propiedades en el archivo de `.csproj` del proyecto de Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="31d01-443">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="31d01-444">`BlazorCacheBootResources` también deshabilita el comportamiento predeterminado de Blazorde almacenar en caché `.dll`, `.wasm` y otros archivos según sus algoritmos hash SHA-256, ya que la propiedad indica que no se puede confiar en la exactitud de los algoritmos hash SHA-256.</span><span class="sxs-lookup"><span data-stu-id="31d01-444">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="31d01-445">Incluso con esta configuración, es posible que la memoria caché HTTP normal del explorador siga almacenando en caché esos archivos, pero que esto suceda o no depende de la configuración del servidor web y de los encabezados de `cache-control` a los que sirve.</span><span class="sxs-lookup"><span data-stu-id="31d01-445">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="31d01-446">La propiedad `BlazorCacheBootResources` no deshabilita las comprobaciones de integridad de las [aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app).</span><span class="sxs-lookup"><span data-stu-id="31d01-446">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="31d01-447">Para obtener instrucciones relativas a las PWA, consulte la sección [Deshabilitación de la comprobación de integridad para aplicaciones PWA](#disable-integrity-checking-for-pwas).</span><span class="sxs-lookup"><span data-stu-id="31d01-447">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="31d01-448">Deshabilitación de la comprobación de integridad para aplicaciones PWA</span><span class="sxs-lookup"><span data-stu-id="31d01-448">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="31d01-449">La plantilla de aplicación web progresiva (PWA) de Blazor contiene un archivo `service-worker.published.js` sugerido que es responsable de capturar y almacenar archivos de la aplicación para su uso sin conexión.</span><span class="sxs-lookup"><span data-stu-id="31d01-449">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="31d01-450">Se trata de un proceso independiente del mecanismo de inicio de la aplicación normal y tiene su propia lógica de comprobación de integridad independiente.</span><span class="sxs-lookup"><span data-stu-id="31d01-450">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="31d01-451">Dentro del archivo `service-worker.published.js`, está presente la línea siguiente:</span><span class="sxs-lookup"><span data-stu-id="31d01-451">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="31d01-452">Para deshabilitar la comprobación de la integridad, quite el parámetro `integrity` cambiando la línea por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="31d01-452">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="31d01-453">De nuevo, deshabilitar la comprobación de la integridad significa que se pierden las garantías de seguridad que ofrece este servicio.</span><span class="sxs-lookup"><span data-stu-id="31d01-453">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="31d01-454">Por ejemplo, existe el riesgo de que si el explorador del usuario almacena en caché la aplicación en el momento exacto en que implementa una nueva versión, podría almacenar en caché algunos archivos de la implementación anterior y otros de la implementación nueva.</span><span class="sxs-lookup"><span data-stu-id="31d01-454">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="31d01-455">Si eso sucede, la aplicación se bloquea en un estado interrumpido hasta que implemente una actualización adicional.</span><span class="sxs-lookup"><span data-stu-id="31d01-455">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
