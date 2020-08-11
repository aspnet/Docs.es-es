---
title: Hospedaje e implementación de ASP.NET Core Blazor WebAssembly
author: guardrex
description: Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 9d596e38a1d8350cd4a27f2fec4b262a0edf1015
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818851"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="b6091-103">Hospedaje e implementación de ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b6091-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="b6091-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) y [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="b6091-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="b6091-105">Con el [modelo de hospedaje de Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="b6091-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="b6091-106">La aplicación Blazor, sus dependencias y el entorno de ejecución de .NET se descargan en el explorador en paralelo.</span><span class="sxs-lookup"><span data-stu-id="b6091-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="b6091-107">La aplicación se ejecuta directamente en el subproceso de interfaz de usuario del explorador.</span><span class="sxs-lookup"><span data-stu-id="b6091-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="b6091-108">Se admiten las estrategias de implementación siguientes:</span><span class="sxs-lookup"><span data-stu-id="b6091-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="b6091-109">Una aplicación ASP.NET Core suministra la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="b6091-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="b6091-110">Esta estrategia se trata en la sección [Implementación hospedada con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="b6091-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="b6091-111">La aplicación Blazor se coloca en un servicio o servidor web de hospedaje estático, donde no se usa .NET para suministrar la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="b6091-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="b6091-112">Esta estrategia se trata en la sección [Implementación independiente](#standalone-deployment), que incluye información sobre cómo hospedar una aplicación Blazor WebAssembly como una subaplicación de IIS.</span><span class="sxs-lookup"><span data-stu-id="b6091-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="b6091-113">Compresión</span><span class="sxs-lookup"><span data-stu-id="b6091-113">Compression</span></span>

<span data-ttu-id="b6091-114">Cuando se publica una aplicación Blazor WebAssembly, la salida se comprime estáticamente durante la publicación para reducir el tamaño de la aplicación y acabar con la necesidad de compresión en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="b6091-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="b6091-115">Se usan los algoritmos de compresión siguientes:</span><span class="sxs-lookup"><span data-stu-id="b6091-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="b6091-116">[Brotli](https://tools.ietf.org/html/rfc7932) (el nivel más alto)</span><span class="sxs-lookup"><span data-stu-id="b6091-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="b6091-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="b6091-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="b6091-118">Blazor se basa en el host para proporcionar los archivos comprimidos adecuados.</span><span class="sxs-lookup"><span data-stu-id="b6091-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="b6091-119">Cuando se usa un proyecto hospedado de ASP.NET Core, el proyecto host tiene capacidad para realizar la negociación de contenido y proporcionar los archivos comprimidos estáticamente.</span><span class="sxs-lookup"><span data-stu-id="b6091-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="b6091-120">Al hospedar una aplicación independiente Blazor WebAssembly, puede que sea necesario realizar trabajo adicional para garantizar que se proporcionan los archivos comprimidos estáticamente:</span><span class="sxs-lookup"><span data-stu-id="b6091-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="b6091-121">Para ver la configuración de compresión de `web.config` de IIS, vea la sección [IIS: compresión Brotli y Gzip](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="b6091-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="b6091-122">Al hospedar en soluciones de hospedaje estáticas que no admiten la negociación de contenido de archivos comprimidos estáticamente (como es el caso de las páginas de GitHub), considere la posibilidad de configurar la aplicación para capturar y descodificar archivos comprimidos Brotli:</span><span class="sxs-lookup"><span data-stu-id="b6091-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="b6091-123">Obtenga el descodificador Brotli de JavaScript del [repositorio de GitHub google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="b6091-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="b6091-124">A partir de julio de 2020, el archivo del descodificador se llama `decode.min.js` y se encuentra en la [carpeta `js`](https://github.com/google/brotli/tree/master/js) del repositorio.</span><span class="sxs-lookup"><span data-stu-id="b6091-124">As of July 2020, the decoder file is named `decode.min.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  * <span data-ttu-id="b6091-125">Actualice la aplicación para que use el descodificador.</span><span class="sxs-lookup"><span data-stu-id="b6091-125">Update the app to use the decoder.</span></span> <span data-ttu-id="b6091-126">Cambie el marcado de la etiqueta `<body>` de cierre en `wwwroot/index.html` por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b6091-126">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.min.js"></script>
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
 
<span data-ttu-id="b6091-127">Para deshabilitar la compresión, agregue la propiedad `BlazorEnableCompression` de MSBuild al archivo del proyecto de la aplicación y establezca el valor en `false`:</span><span class="sxs-lookup"><span data-stu-id="b6091-127">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="b6091-128">Reescritura de las URL para conseguir un enrutamiento correcto</span><span class="sxs-lookup"><span data-stu-id="b6091-128">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="b6091-129">Enrutar las solicitudes de los componentes de página de una aplicación Blazor WebAssembly no es tan sencillo como enrutar las solicitudes de una aplicación Blazor Server hospedada.</span><span class="sxs-lookup"><span data-stu-id="b6091-129">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="b6091-130">Imagine que tiene una aplicación Blazor WebAssembly con dos componentes:</span><span class="sxs-lookup"><span data-stu-id="b6091-130">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="b6091-131">`Main.razor`: se carga en la raíz de la aplicación y contiene un vínculo al componente `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="b6091-131">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="b6091-132">`About.razor`: componente `About`.</span><span class="sxs-lookup"><span data-stu-id="b6091-132">`About.razor`: `About` component.</span></span>

<span data-ttu-id="b6091-133">Cuando se solicita el documento predeterminado de la aplicación mediante la barra de direcciones del explorador (por ejemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="b6091-133">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="b6091-134">El explorador realiza una solicitud.</span><span class="sxs-lookup"><span data-stu-id="b6091-134">The browser makes a request.</span></span>
1. <span data-ttu-id="b6091-135">Se devuelve la página predeterminada, que suele ser `index.html`.</span><span class="sxs-lookup"><span data-stu-id="b6091-135">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="b6091-136">`index.html` arranca la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b6091-136">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="b6091-137">Se carga el enrutador de Blazor y se representa el componente `Main` de Razor.</span><span class="sxs-lookup"><span data-stu-id="b6091-137">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="b6091-138">En la página principal, la selección del vínculo al componente `About` funciona en el cliente porque el enrutador de Blazor impide que el explorador haga una solicitud en Internet a `www.contoso.com` sobre `About` y presenta el propio componente `About` representado.</span><span class="sxs-lookup"><span data-stu-id="b6091-138">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="b6091-139">Todas las solicitudes de puntos de conexión internos *dentro de la aplicación Blazor WebAssembly* funcionan del mismo modo: Las solicitudes no desencadenan solicitudes basadas en el explorador a recursos hospedados en el servidor en Internet.</span><span class="sxs-lookup"><span data-stu-id="b6091-139">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="b6091-140">El enrutador controla las solicitudes de forma interna.</span><span class="sxs-lookup"><span data-stu-id="b6091-140">The router handles the requests internally.</span></span>

<span data-ttu-id="b6091-141">Si se realiza una solicitud mediante la barra de direcciones del explorador para `www.contoso.com/About`, se produce un error.</span><span class="sxs-lookup"><span data-stu-id="b6091-141">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="b6091-142">Este recurso no existe en el host de Internet de la aplicación, por lo que se devuelve una respuesta *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="b6091-142">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="b6091-143">Dado que los exploradores solicitan páginas del lado cliente a hosts basados en Internet, los servidores web y los servicios de hospedaje deben reescribir todas las solicitudes de recursos que no estén físicamente en el servidor a la página `index.html`.</span><span class="sxs-lookup"><span data-stu-id="b6091-143">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="b6091-144">Cuando se devuelve `index.html`, el enrutador Blazor de la aplicación se hace cargo y responde con el recurso correcto.</span><span class="sxs-lookup"><span data-stu-id="b6091-144">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="b6091-145">Al implementar en un servidor IIS, puede usar el módulo URL Rewrite con el archivo `web.config` publicado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b6091-145">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="b6091-146">Para más información, consulte la sección sobre [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="b6091-146">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="b6091-147">Implementación hospedada con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6091-147">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="b6091-148">Una *implementación hospedada* se encarga de suministrar la aplicación Blazor WebAssembly a los exploradores desde una [aplicación ASP.NET Core](xref:index) que se ejecuta en un servidor web.</span><span class="sxs-lookup"><span data-stu-id="b6091-148">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="b6091-149">La aplicación cliente de Blazor WebAssembly se publica en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` de la aplicación de servidor, junto con cualquier otro recurso web estático de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="b6091-149">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="b6091-150">Las dos aplicaciones se implementan juntas.</span><span class="sxs-lookup"><span data-stu-id="b6091-150">The two apps are deployed together.</span></span> <span data-ttu-id="b6091-151">Se requiere un servidor web que pueda hospedar una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6091-151">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="b6091-152">En el caso de una implementación hospedada, Visual Studio incluye la plantilla de proyecto **Aplicación de Blazor WebAssembly** (plantilla `blazorwasm` si se usa el comando [`dotnet new`](/dotnet/core/tools/dotnet-new)) con la opción **`Hosted`** seleccionada (`-ho|--hosted` si se usa el comando `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="b6091-152">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="b6091-153">Para obtener más información sobre la implementación y el hospedaje de aplicaciones de ASP.NET Core, consulte <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="b6091-153">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="b6091-154">Para obtener información sobre cómo implementar en Azure App Service, vea <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="b6091-154">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="b6091-155">Implementación hospedada con varias aplicaciones Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b6091-155">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="b6091-156">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="b6091-156">App configuration</span></span>

<span data-ttu-id="b6091-157">Para configurar una solución de Blazor hospedada para atender a varias aplicaciones Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="b6091-157">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="b6091-158">Use una solución de Blazor hospedada existente o cree una solución a partir de la plantilla de proyecto hospedada de Blazor.</span><span class="sxs-lookup"><span data-stu-id="b6091-158">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="b6091-159">En el archivo de proyecto de la aplicación cliente, agregue una propiedad `<StaticWebAssetBasePath>` a `<PropertyGroup>` con un valor de `FirstApp` para establecer la ruta de acceso base para los recursos estáticos del proyecto:</span><span class="sxs-lookup"><span data-stu-id="b6091-159">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="b6091-160">Agregue una segunda aplicación cliente a la solución:</span><span class="sxs-lookup"><span data-stu-id="b6091-160">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="b6091-161">Agregue una carpeta denominada `SecondClient` a la carpeta de la solución.</span><span class="sxs-lookup"><span data-stu-id="b6091-161">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="b6091-162">Cree una aplicación Blazor WebAssembly denominada `SecondBlazorApp.Client` en la carpeta `SecondClient` de la plantilla de proyecto de Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b6091-162">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="b6091-163">En el archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="b6091-163">In the app's project file:</span></span>

    * <span data-ttu-id="b6091-164">Agregue una propiedad `<StaticWebAssetBasePath>` a `<PropertyGroup>` con un valor de `SecondApp`:</span><span class="sxs-lookup"><span data-stu-id="b6091-164">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="b6091-165">Agregue una referencia de proyecto al proyecto `Shared`:</span><span class="sxs-lookup"><span data-stu-id="b6091-165">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="b6091-166">El marcador de posición `{SOLUTION NAME}` es el nombre de la solución.</span><span class="sxs-lookup"><span data-stu-id="b6091-166">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="b6091-167">En el archivo del proyecto de la aplicación de servidor, cree una referencia de proyecto para la aplicación cliente agregada:</span><span class="sxs-lookup"><span data-stu-id="b6091-167">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="b6091-168">En el archivo `Properties/launchSettings.json` de la aplicación de servidor, configure el elemento `applicationUrl` del perfil de Kestrel (`{SOLUTION NAME}.Server`) para acceder a las aplicaciones cliente en los puertos 5001 y 5002:</span><span class="sxs-lookup"><span data-stu-id="b6091-168">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="b6091-169">En el método `Startup.Configure` de la aplicación de servidor (`Startup.cs`), quite las líneas siguientes, que aparecen después de la llamada a <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span><span class="sxs-lookup"><span data-stu-id="b6091-169">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="b6091-170">Agregue middleware que asigna solicitudes a las aplicaciones cliente.</span><span class="sxs-lookup"><span data-stu-id="b6091-170">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="b6091-171">En el ejemplo siguiente se configura el middleware para que se ejecute cuando:</span><span class="sxs-lookup"><span data-stu-id="b6091-171">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="b6091-172">El puerto de solicitud es 5001 para la aplicación cliente original o 5002 para la aplicación cliente agregada.</span><span class="sxs-lookup"><span data-stu-id="b6091-172">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="b6091-173">El host de solicitud es `firstapp.com` para la aplicación cliente original o `secondapp.com` para la aplicación cliente agregada.</span><span class="sxs-lookup"><span data-stu-id="b6091-173">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="b6091-174">El ejemplo que se muestra en esta sección requiere una configuración adicional para:</span><span class="sxs-lookup"><span data-stu-id="b6091-174">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="b6091-175">Acceder a las aplicaciones en los dominios de host de ejemplo, `firstapp.com` y `secondapp.com`.</span><span class="sxs-lookup"><span data-stu-id="b6091-175">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="b6091-176">Certificados que permitan a las aplicaciones cliente habilitar la seguridad TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="b6091-176">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="b6091-177">La configuración necesaria queda fuera del ámbito de este artículo y depende de cómo se hospede la solución.</span><span class="sxs-lookup"><span data-stu-id="b6091-177">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="b6091-178">Para más información, vea los [artículos sobre hospedaje e implementación](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="b6091-178">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="b6091-179">Coloque el código siguiente donde se quitaron las líneas anteriormente:</span><span class="sxs-lookup"><span data-stu-id="b6091-179">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="b6091-180">En el controlador de pronóstico meteorológico (`Controllers/WeatherForecastController.cs`) de la aplicación de servidor, reemplace la ruta existente (`[Route("[controller]")]`) a `WeatherForecastController` por las rutas siguientes:</span><span class="sxs-lookup"><span data-stu-id="b6091-180">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="b6091-181">El middleware agregado al método `Startup.Configure` de la aplicación de servidor anteriormente modifica las solicitudes entrantes a `/WeatherForecast` por `/FirstApp/WeatherForecast` o `/SecondApp/WeatherForecast` en función del puerto (5001/5002) o el dominio (`firstapp.com`/`secondapp.com`).</span><span class="sxs-lookup"><span data-stu-id="b6091-181">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="b6091-182">Las rutas de controlador anteriores son necesarias para devolver datos meteorológicos de la aplicación de servidor a las aplicaciones cliente.</span><span class="sxs-lookup"><span data-stu-id="b6091-182">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="b6091-183">Recursos estáticos y bibliotecas de clases</span><span class="sxs-lookup"><span data-stu-id="b6091-183">Static assets and class libraries</span></span>

<span data-ttu-id="b6091-184">Use los métodos siguientes para los recursos estáticos:</span><span class="sxs-lookup"><span data-stu-id="b6091-184">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="b6091-185">Cuando el recurso se encuentra en la carpeta `wwwroot` de la aplicación cliente, proporcione sus rutas de acceso de la forma habitual:</span><span class="sxs-lookup"><span data-stu-id="b6091-185">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="b6091-186">Cuando el recurso se encuentra en la carpeta `wwwroot` de una [biblioteca de clases de Razor (RCL)](xref:blazor/components/class-libraries), haga referencia al recurso estático de la aplicación cliente según las instrucciones del [artículo de RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="b6091-186">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b6091-187">Se hace referencia de la forma habitual a los componentes que una biblioteca de clases proporciona a una aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="b6091-187">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="b6091-188">Si algún componente requiere hojas de estilo o archivos JavaScript, use cualquiera de los métodos siguientes para obtener los recursos estáticos:</span><span class="sxs-lookup"><span data-stu-id="b6091-188">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="b6091-189">El archivo `wwwroot/index.html` de la aplicación cliente se puede vincular (`<link>`) con los recursos estáticos.</span><span class="sxs-lookup"><span data-stu-id="b6091-189">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="b6091-190">El componente puede usar el [componente `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) del marco de trabajo para obtener los recursos estáticos.</span><span class="sxs-lookup"><span data-stu-id="b6091-190">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="b6091-191">Los enfoques anteriores se demuestran en los ejemplos siguientes.</span><span class="sxs-lookup"><span data-stu-id="b6091-191">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b6091-192">Se hace referencia de la forma habitual a los componentes que una biblioteca de clases proporciona a una aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="b6091-192">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="b6091-193">Si algún componente requiere hojas de estilo o archivos JavaScript, el archivo `wwwroot/index.html` de la aplicación cliente debe incluir los vínculos de recursos estáticos correctos.</span><span class="sxs-lookup"><span data-stu-id="b6091-193">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="b6091-194">Estos enfoques se demuestran en los ejemplos siguientes.</span><span class="sxs-lookup"><span data-stu-id="b6091-194">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="b6091-195">Agregue el siguiente componente `Jeep` a una de las aplicaciones cliente.</span><span class="sxs-lookup"><span data-stu-id="b6091-195">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="b6091-196">El componente `Jeep` usa:</span><span class="sxs-lookup"><span data-stu-id="b6091-196">The `Jeep` component uses:</span></span>

* <span data-ttu-id="b6091-197">Una imagen de la carpeta `wwwroot` de la aplicación cliente (`jeep-cj.png`).</span><span class="sxs-lookup"><span data-stu-id="b6091-197">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="b6091-198">Una imagen de una carpeta `wwwroot` de la [biblioteca de componentes de Razor agregada](xref:blazor/components/class-libraries) (`JeepImage`) (`jeep-yj.png`).</span><span class="sxs-lookup"><span data-stu-id="b6091-198">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="b6091-199">La plantilla del proyecto de RCL crea automáticamente el componente de ejemplo (`Component1`) cuando se agrega la biblioteca `JeepImage` a la solución.</span><span class="sxs-lookup"><span data-stu-id="b6091-199">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="b6091-200">**No** publique imágenes de vehículos a menos que sean de su titularidad.</span><span class="sxs-lookup"><span data-stu-id="b6091-200">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="b6091-201">De lo contrario, corre el riesgo de infringir los derechos de autor.</span><span class="sxs-lookup"><span data-stu-id="b6091-201">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b6091-202">También se puede agregar la imagen `jeep-yj.png` de la biblioteca al componente `Component1` de la biblioteca (`Component1.razor`).</span><span class="sxs-lookup"><span data-stu-id="b6091-202">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="b6091-203">Para proporcionar la clase CSS `my-component` a la página de la aplicación cliente, establezca un vínculo con la hoja de estilo de la biblioteca mediante el [componente `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) del marco de trabajo:</span><span class="sxs-lookup"><span data-stu-id="b6091-203">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

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

<span data-ttu-id="b6091-204">Una alternativa al uso del [componente `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) es cargar la hoja de estilo del archivo `wwwroot/index.html` de la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="b6091-204">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="b6091-205">Este enfoque hace que la hoja de estilo esté disponible para todos los componentes de la aplicación cliente:</span><span class="sxs-lookup"><span data-stu-id="b6091-205">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b6091-206">También se puede agregar la imagen `jeep-yj.png` de la biblioteca al componente `Component1` de la biblioteca (`Component1.razor`):</span><span class="sxs-lookup"><span data-stu-id="b6091-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="b6091-207">El archivo `wwwroot/index.html` de la aplicación cliente solicita la hoja de estilo de la biblioteca con la siguiente etiqueta `<link>` agregada:</span><span class="sxs-lookup"><span data-stu-id="b6091-207">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="b6091-208">Agregue la navegación al componente `Jeep` en el componente `NavMenu` de la aplicación cliente (`Shared/NavMenu.razor`):</span><span class="sxs-lookup"><span data-stu-id="b6091-208">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="b6091-209">Para obtener más información sobre RCL, vea:</span><span class="sxs-lookup"><span data-stu-id="b6091-209">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="b6091-210">Implementación independiente</span><span class="sxs-lookup"><span data-stu-id="b6091-210">Standalone deployment</span></span>

<span data-ttu-id="b6091-211">Una *implementación independiente* suministra la aplicación Blazor WebAssembly como un conjunto de archivos estáticos que los clientes solicitan directamente.</span><span class="sxs-lookup"><span data-stu-id="b6091-211">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="b6091-212">Cualquier servidor de archivos estático es capaz de suministrar la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="b6091-212">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="b6091-213">Los recursos de implementación independientes se publican en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="b6091-213">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="b6091-214">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b6091-214">Azure App Service</span></span>

<span data-ttu-id="b6091-215">Las aplicaciones Blazor WebAssembly se pueden implementar en Azure App Services en Windows, que hospeda la aplicación en [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="b6091-215">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="b6091-216">Actualmente no se admite la implementación de una aplicación Blazor WebAssembly independiente en Azure App Service para Linux.</span><span class="sxs-lookup"><span data-stu-id="b6091-216">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="b6091-217">En este momento no hay disponible una imagen de servidor de Linux para hospedar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b6091-217">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="b6091-218">Se está trabajando para habilitar este escenario.</span><span class="sxs-lookup"><span data-stu-id="b6091-218">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="b6091-219">IIS</span><span class="sxs-lookup"><span data-stu-id="b6091-219">IIS</span></span>

<span data-ttu-id="b6091-220">IIS es un servidor de archivos estáticos compatible con las aplicaciones Blazor.</span><span class="sxs-lookup"><span data-stu-id="b6091-220">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="b6091-221">Para configurar IIS para hospedar Blazor, consulte [Compilación de un sitio web estático en IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="b6091-221">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="b6091-222">Los recursos publicados se crean en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish`.</span><span class="sxs-lookup"><span data-stu-id="b6091-222">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="b6091-223">Hospede el contenido de la carpeta `publish` en el servidor web o el servicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="b6091-223">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="b6091-224">web.config</span><span class="sxs-lookup"><span data-stu-id="b6091-224">web.config</span></span>

<span data-ttu-id="b6091-225">Cuando se publica un proyecto de Blazor, se crea un archivo `web.config` con la siguiente configuración de IIS:</span><span class="sxs-lookup"><span data-stu-id="b6091-225">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="b6091-226">Se establecen los tipos MIME de las siguientes extensiones de archivo:</span><span class="sxs-lookup"><span data-stu-id="b6091-226">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="b6091-227">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="b6091-227">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="b6091-228">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="b6091-228">`.json`: `application/json`</span></span>
  * <span data-ttu-id="b6091-229">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="b6091-229">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="b6091-230">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="b6091-230">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="b6091-231">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="b6091-231">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="b6091-232">Se habilita la compresión HTTP de los siguientes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="b6091-232">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="b6091-233">Se establecen reglas del módulo URL Rewrite:</span><span class="sxs-lookup"><span data-stu-id="b6091-233">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="b6091-234">Proporcione el subdirectorio donde residen los recursos estáticos de la aplicación (`wwwroot/{PATH REQUESTED}`).</span><span class="sxs-lookup"><span data-stu-id="b6091-234">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="b6091-235">Cree el enrutamiento de reserva de SPA para que las solicitudes de recursos que no sean archivos se redirijan al documento predeterminado de la aplicación en su carpeta de recursos estáticos (`wwwroot/index.html`).</span><span class="sxs-lookup"><span data-stu-id="b6091-235">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="b6091-236">Uso de un archivo web.config personalizado</span><span class="sxs-lookup"><span data-stu-id="b6091-236">Use a custom web.config</span></span>

<span data-ttu-id="b6091-237">Para usar un archivo `web.config` personalizado, coloque el archivo `web.config` personalizado en la raíz de la carpeta del proyecto y publique el proyecto.</span><span class="sxs-lookup"><span data-stu-id="b6091-237">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="b6091-238">Instalación del módulo URL Rewrite</span><span class="sxs-lookup"><span data-stu-id="b6091-238">Install the URL Rewrite Module</span></span>

<span data-ttu-id="b6091-239">El [módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) es necesario para reescribir las URL.</span><span class="sxs-lookup"><span data-stu-id="b6091-239">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="b6091-240">El módulo no se instala de forma predeterminada y no está disponible para instalar como una característica de servicio de rol del servidor web (IIS).</span><span class="sxs-lookup"><span data-stu-id="b6091-240">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="b6091-241">El módulo se debe descargar desde el sitio web de IIS.</span><span class="sxs-lookup"><span data-stu-id="b6091-241">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="b6091-242">Use el instalador de plataforma web para instalar el módulo:</span><span class="sxs-lookup"><span data-stu-id="b6091-242">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="b6091-243">De forma local, vaya a la [página de descargas del módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="b6091-243">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="b6091-244">En el caso de la versión en inglés, seleccione **WebPI** para descargar el instalador de WebPI.</span><span class="sxs-lookup"><span data-stu-id="b6091-244">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="b6091-245">En el caso de otros idiomas, seleccione la arquitectura adecuada del servidor (x86/x64) para descargar el instalador.</span><span class="sxs-lookup"><span data-stu-id="b6091-245">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="b6091-246">Copie el instalador en el servidor.</span><span class="sxs-lookup"><span data-stu-id="b6091-246">Copy the installer to the server.</span></span> <span data-ttu-id="b6091-247">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="b6091-247">Run the installer.</span></span> <span data-ttu-id="b6091-248">Haga clic en el botón **Instalar** y acepte los términos de licencia.</span><span class="sxs-lookup"><span data-stu-id="b6091-248">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="b6091-249">No es necesario reiniciar el servidor al finalizar la instalación.</span><span class="sxs-lookup"><span data-stu-id="b6091-249">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="b6091-250">Configuración del sitio web</span><span class="sxs-lookup"><span data-stu-id="b6091-250">Configure the website</span></span>

<span data-ttu-id="b6091-251">Configure la **ruta de acceso física** del sitio web a la carpeta de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b6091-251">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="b6091-252">La carpeta contiene:</span><span class="sxs-lookup"><span data-stu-id="b6091-252">The folder contains:</span></span>

* <span data-ttu-id="b6091-253">El archivo `web.config` que usa IIS para configurar el sitio web, incluidas las reglas de redireccionamiento y los tipos de contenido de archivo necesarios.</span><span class="sxs-lookup"><span data-stu-id="b6091-253">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="b6091-254">La carpeta de recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b6091-254">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="b6091-255">Hospedaje como subaplicación de IIS</span><span class="sxs-lookup"><span data-stu-id="b6091-255">Host as an IIS sub-app</span></span>

<span data-ttu-id="b6091-256">Si una aplicación independiente se hospeda como una subaplicación de IIS, realice una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="b6091-256">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="b6091-257">Deshabilite el controlador del módulo de ASP.NET Core heredado.</span><span class="sxs-lookup"><span data-stu-id="b6091-257">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="b6091-258">Para quitar el controlador del archivo `web.config` publicado de la aplicación Blazor, agregue una sección `<handlers>` al archivo:</span><span class="sxs-lookup"><span data-stu-id="b6091-258">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="b6091-259">Deshabilite la herencia de la sección `<system.webServer>` de la aplicación raíz (principal) mediante un elemento `<location>` con `inheritInChildApplications` establecido en `false`:</span><span class="sxs-lookup"><span data-stu-id="b6091-259">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="b6091-260">Además de [configurarse la ruta de acceso base de la aplicación](xref:blazor/host-and-deploy/index#app-base-path), se quita el controlador o se deshabilita la herencia.</span><span class="sxs-lookup"><span data-stu-id="b6091-260">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="b6091-261">En el archivo `index.html` de la aplicación, establezca la ruta de acceso base de la aplicación en el alias de IIS que ha usado al configurar la subaplicación en IIS.</span><span class="sxs-lookup"><span data-stu-id="b6091-261">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="b6091-262">Compresión Brotli y Gzip</span><span class="sxs-lookup"><span data-stu-id="b6091-262">Brotli and Gzip compression</span></span>

<span data-ttu-id="b6091-263">IIS se puede configurar a través de `web.config` para dar servicio a recursos de Blazor comprimidos con Brotli o Gzip.</span><span class="sxs-lookup"><span data-stu-id="b6091-263">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="b6091-264">Para ver una configuración de ejemplo, consulte [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="b6091-264">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="b6091-265">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="b6091-265">Troubleshooting</span></span>

<span data-ttu-id="b6091-266">Si se recibe un error *500 Error interno del servidor* y el administrador de IIS produce errores al intentar acceder a la configuración del sitio web, confirme que el módulo URL Rewrite está instalado.</span><span class="sxs-lookup"><span data-stu-id="b6091-266">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="b6091-267">Si no lo está, IIS no puede analizar el archivo `web.config`.</span><span class="sxs-lookup"><span data-stu-id="b6091-267">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="b6091-268">Esto impide que el Administrador de IIS cargue la configuración del sitio web y que el sitio web suministre los archivos estáticos de Blazor.</span><span class="sxs-lookup"><span data-stu-id="b6091-268">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="b6091-269">Para obtener más información sobre cómo solucionar problemas de las implementaciones en IIS, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b6091-269">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="b6091-270">Almacenamiento de Azure</span><span class="sxs-lookup"><span data-stu-id="b6091-270">Azure Storage</span></span>

<span data-ttu-id="b6091-271">El hospedaje de archivos estáticos de [Azure Storage](/azure/storage/) permite el hospedaje de aplicaciones Blazor sin servidor.</span><span class="sxs-lookup"><span data-stu-id="b6091-271">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="b6091-272">Se admiten nombres de dominio personalizados, Azure Content Delivery Network (CDN) y HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b6091-272">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="b6091-273">Cuando el servicio de blob está habilitado para el hospedaje de sitios web estáticos en una cuenta de almacenamiento:</span><span class="sxs-lookup"><span data-stu-id="b6091-273">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="b6091-274">Establece el **nombre de documento de índice** en `index.html`.</span><span class="sxs-lookup"><span data-stu-id="b6091-274">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="b6091-275">Establece la **ruta de acceso del documento de error** en `index.html`.</span><span class="sxs-lookup"><span data-stu-id="b6091-275">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="b6091-276">Los componentes Razor y otros puntos de conexión que no son de archivo no residen en las rutas de acceso físicas del contenido estático almacenado por el servicio de blob.</span><span class="sxs-lookup"><span data-stu-id="b6091-276">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="b6091-277">Cuando se recibe una solicitud de uno de estos recursos que debe controlar el enrutador de Blazor, el error *404 - No encontrado* generado por el servicio de blob enruta la solicitud a la **ruta de acceso del documento de error**.</span><span class="sxs-lookup"><span data-stu-id="b6091-277">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="b6091-278">Se devuelve el blob `index.html`, y el enrutador de Blazor carga y procesa la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="b6091-278">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="b6091-279">Si los archivos no se cargan en tiempo de ejecución debido a tipos MIME inadecuados en los encabezados `Content-Type` de los archivos, haga algunas de las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b6091-279">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="b6091-280">Configure las herramientas para establecer los tipos MIME correctos (encabezados `Content-Type`) cuando se implementen los archivos.</span><span class="sxs-lookup"><span data-stu-id="b6091-280">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="b6091-281">Cambie los tipos MIME (encabezados `Content-Type`) de los archivos una vez que se implementa la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b6091-281">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="b6091-282">En cada archivo del Explorador de Storage (Azure Portal) haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b6091-282">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="b6091-283">Haga clic con el botón derecho en el archivo y seleccione **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="b6091-283">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="b6091-284">Establezca el valor de **ContentType** y seleccione el botón **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="b6091-284">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="b6091-285">Para más información, consulte [Hospedaje de sitios web estáticos en Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="b6091-285">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="b6091-286">Nginx</span><span class="sxs-lookup"><span data-stu-id="b6091-286">Nginx</span></span>

<span data-ttu-id="b6091-287">El siguiente archivo `nginx.conf` se ha simplificado para mostrar cómo hay que configurar Nginx para enviar el archivo `index.html` siempre que no pueda encontrar un archivo correspondiente en el disco.</span><span class="sxs-lookup"><span data-stu-id="b6091-287">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="b6091-288">Al establecer el [límite de velocidad de ráfaga de NGINX](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) con [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), las aplicaciones de Blazor WebAssembly pueden requerir un valor de parámetro `burst` grande para acomodar el número relativamente elevado de solicitudes realizadas por una aplicación.</span><span class="sxs-lookup"><span data-stu-id="b6091-288">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="b6091-289">Inicialmente, establezca el valor en al menos 60:</span><span class="sxs-lookup"><span data-stu-id="b6091-289">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="b6091-290">Aumente el valor si las herramientas de desarrollo del explorador o la herramienta de tráfico de red indican que las solicitudes reciben un código de estado *503: Servicio no disponible*.</span><span class="sxs-lookup"><span data-stu-id="b6091-290">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="b6091-291">Para obtener más información sobre la configuración del servidor web de producción de Nginx, consulte [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creación de archivos de configuración de NGINX y NGINX Plus).</span><span class="sxs-lookup"><span data-stu-id="b6091-291">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="b6091-292">Nginx en Docker</span><span class="sxs-lookup"><span data-stu-id="b6091-292">Nginx in Docker</span></span>

<span data-ttu-id="b6091-293">Para hospedar Blazor en Docker mediante Nginx, configure Dockerfile para usar la imagen de Nginx basada en Alpine.</span><span class="sxs-lookup"><span data-stu-id="b6091-293">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="b6091-294">Actualice el Dockerfile para copiar el archivo `nginx.config` en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="b6091-294">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="b6091-295">Agregue una línea al Dockerfile, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b6091-295">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="b6091-296">Apache</span><span class="sxs-lookup"><span data-stu-id="b6091-296">Apache</span></span>

<span data-ttu-id="b6091-297">Para implementar una aplicación Blazor WebAssembly en CentOS 7 o posterior:</span><span class="sxs-lookup"><span data-stu-id="b6091-297">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="b6091-298">Cree el archivo de configuración de Apache.</span><span class="sxs-lookup"><span data-stu-id="b6091-298">Create the Apache configuration file.</span></span> <span data-ttu-id="b6091-299">El siguiente ejemplo es un archivo de configuración simplificado (`blazorapp.config`):</span><span class="sxs-lookup"><span data-stu-id="b6091-299">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="b6091-300">Coloque el archivo de configuración de Apache en el directorio `/etc/httpd/conf.d/`, que es el directorio de configuración de Apache predeterminado en CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="b6091-300">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="b6091-301">Coloque los archivos de la aplicación en el directorio `/var/www/blazorapp` (la ubicación especificada para `DocumentRoot` en el archivo de configuración).</span><span class="sxs-lookup"><span data-stu-id="b6091-301">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="b6091-302">Reinicie el servicio de Apache.</span><span class="sxs-lookup"><span data-stu-id="b6091-302">Restart the Apache service.</span></span>

<span data-ttu-id="b6091-303">Para más información, vea [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) y [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="b6091-303">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="b6091-304">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="b6091-304">GitHub Pages</span></span>

<span data-ttu-id="b6091-305">Para controlar las reescrituras de URL, agregue un archivo `wwwroot/404.html` con un script que controle el redireccionamiento de la solicitud a la página `index.html`.</span><span class="sxs-lookup"><span data-stu-id="b6091-305">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="b6091-306">Para obtener un ejemplo, vea el [repositorio de GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="b6091-306">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="b6091-307">[Sitio activo](https://stevesandersonms.github.io/BlazorOnGitHubPages/)</span><span class="sxs-lookup"><span data-stu-id="b6091-307">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="b6091-308">Si usa un sitio de proyecto en lugar de un sitio de la organización, actualice la etiqueta `<base>` en `wwwroot/index.html`.</span><span class="sxs-lookup"><span data-stu-id="b6091-308">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="b6091-309">Defina el valor del atributo `href` con el nombre del repositorio de GitHub con una barra diagonal final (por ejemplo, `/my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="b6091-309">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="b6091-310">En el [repositorio de GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), el elemento `href` base se actualiza cuando el [archivo de configuración `.github/workflows/main.yml`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml) realiza la publicación.</span><span class="sxs-lookup"><span data-stu-id="b6091-310">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="b6091-311">El [repositorio de GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) no es propiedad de .NET Foundation o Microsoft, ni tampoco se encargan de su mantenimiento ni es compatible con ellos.</span><span class="sxs-lookup"><span data-stu-id="b6091-311">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="b6091-312">Valores de configuración de host</span><span class="sxs-lookup"><span data-stu-id="b6091-312">Host configuration values</span></span>

<span data-ttu-id="b6091-313">Las [aplicaciones Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) pueden aceptar los siguientes valores de configuración de host como argumentos de línea de comandos en tiempo de ejecución en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="b6091-313">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="b6091-314">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="b6091-314">Content root</span></span>

<span data-ttu-id="b6091-315">El argumento `--contentroot` establece la ruta de acceso absoluta al directorio que incluye los archivos de contenido de la aplicación ([raíz del contenido](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="b6091-315">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="b6091-316">En los ejemplos siguientes, `/content-root-path` es la ruta de acceso raíz del contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b6091-316">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="b6091-317">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="b6091-317">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b6091-318">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b6091-318">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="b6091-319">Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="b6091-319">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="b6091-320">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b6091-320">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="b6091-321">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="b6091-321">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b6091-322">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="b6091-322">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="b6091-323">Ruta de acceso base</span><span class="sxs-lookup"><span data-stu-id="b6091-323">Path base</span></span>

<span data-ttu-id="b6091-324">El argumento `--pathbase` establece la ruta de acceso base de la aplicación para una aplicación que se ejecuta localmente con una ruta de acceso de URL relativa que no es raíz (el valor `href` de la etiqueta `<base>` se establece en una ruta de acceso que no sea `/` para ensayo y producción).</span><span class="sxs-lookup"><span data-stu-id="b6091-324">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="b6091-325">En los ejemplos siguientes, `/relative-URL-path` es la ruta de acceso base de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b6091-325">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="b6091-326">Para obtener más información, vea [Ruta de acceso base de la aplicación](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="b6091-326">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b6091-327">A diferencia de la ruta de acceso proporcionada al valor `href` de la etiqueta `<base>`, no incluya una barra diagonal final (`/`) al pasar el valor del argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="b6091-327">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="b6091-328">Si se proporciona la ruta de acceso base de la aplicación en la etiqueta `<base>` como `<base href="/CoolApp/">` (se incluye una barra diagonal final), se pasa el valor del argumento de línea de comandos como `--pathbase=/CoolApp` (sin barra diagonal final).</span><span class="sxs-lookup"><span data-stu-id="b6091-328">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="b6091-329">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="b6091-329">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b6091-330">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b6091-330">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="b6091-331">Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="b6091-331">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="b6091-332">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b6091-332">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="b6091-333">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="b6091-333">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b6091-334">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="b6091-334">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="b6091-335">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="b6091-335">URLs</span></span>

<span data-ttu-id="b6091-336">El argumento `--urls` establece las direcciones IP o las direcciones de host con los puertos y protocolos en los que escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="b6091-336">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="b6091-337">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="b6091-337">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b6091-338">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b6091-338">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="b6091-339">Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="b6091-339">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="b6091-340">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b6091-340">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="b6091-341">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="b6091-341">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b6091-342">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="b6091-342">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="b6091-343">Configurar el enlazador</span><span class="sxs-lookup"><span data-stu-id="b6091-343">Configure the Linker</span></span>

<span data-ttu-id="b6091-344">Blazor realiza la vinculación de lenguaje intermedio (IL) en cada compilación de lanzamiento para quitar el IL innecesario de los ensamblados de salida.</span><span class="sxs-lookup"><span data-stu-id="b6091-344">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="b6091-345">Para obtener más información, vea <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="b6091-345">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="b6091-346">Carga de recursos de arranque personalizados</span><span class="sxs-lookup"><span data-stu-id="b6091-346">Custom boot resource loading</span></span>

<span data-ttu-id="b6091-347">Una aplicación Blazor WebAssembly se puede inicializar con la función `loadBootResource` para invalidar el mecanismo de carga de recursos de arranque integrado.</span><span class="sxs-lookup"><span data-stu-id="b6091-347">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="b6091-348">Use `loadBootResource` en los siguientes escenarios:</span><span class="sxs-lookup"><span data-stu-id="b6091-348">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="b6091-349">Para permitir a los usuarios cargar recursos estáticos, como datos de zona horaria o `dotnet.wasm`, desde una red CDN.</span><span class="sxs-lookup"><span data-stu-id="b6091-349">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="b6091-350">Para cargar los ensamblados comprimidos mediante una solicitud HTTP y descomprimirlos en el cliente para aquellos hosts que no admiten la captura de contenido comprimido del servidor.</span><span class="sxs-lookup"><span data-stu-id="b6091-350">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="b6091-351">Para asignar otro alias a los recursos mediante el redireccionamiento de cada solicitud `fetch` a un nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="b6091-351">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="b6091-352">Los parámetros `loadBootResource` aparecen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="b6091-352">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="b6091-353">Parámetro</span><span class="sxs-lookup"><span data-stu-id="b6091-353">Parameter</span></span>    | <span data-ttu-id="b6091-354">Descripción</span><span class="sxs-lookup"><span data-stu-id="b6091-354">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="b6091-355">Tipo de recurso.</span><span class="sxs-lookup"><span data-stu-id="b6091-355">The type of the resource.</span></span> <span data-ttu-id="b6091-356">Tipos permitidos: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="b6091-356">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="b6091-357">Nombre del recurso.</span><span class="sxs-lookup"><span data-stu-id="b6091-357">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="b6091-358">URI relativo o absoluto del recurso.</span><span class="sxs-lookup"><span data-stu-id="b6091-358">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="b6091-359">Cadena de integridad que representa el contenido esperado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="b6091-359">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="b6091-360">`loadBootResource` devuelve cualquiera de los siguientes elementos para invalidar el proceso de carga:</span><span class="sxs-lookup"><span data-stu-id="b6091-360">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="b6091-361">Cadena URI.</span><span class="sxs-lookup"><span data-stu-id="b6091-361">URI string.</span></span> <span data-ttu-id="b6091-362">En el ejemplo siguiente (`wwwroot/index.html`), los siguientes archivos se sirven desde una red CDN en `https://my-awesome-cdn.com/`:</span><span class="sxs-lookup"><span data-stu-id="b6091-362">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="b6091-363">Datos de zona horaria</span><span class="sxs-lookup"><span data-stu-id="b6091-363">Timezone data</span></span>

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

* <span data-ttu-id="b6091-364">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="b6091-364">`Promise<Response>`.</span></span> <span data-ttu-id="b6091-365">Pase el parámetro `integrity` en un encabezado para conservar el comportamiento de comprobación de integridad predeterminado.</span><span class="sxs-lookup"><span data-stu-id="b6091-365">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="b6091-366">En el ejemplo siguiente (`wwwroot/index.html`), se agrega un encabezado HTTP personalizado a las solicitudes salientes y se pasa el parámetro `integrity` a través de la llamada a `fetch`:</span><span class="sxs-lookup"><span data-stu-id="b6091-366">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="b6091-367">`null`/`undefined`, que da lugar al comportamiento de carga predeterminado.</span><span class="sxs-lookup"><span data-stu-id="b6091-367">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="b6091-368">Los orígenes externos deben devolver los encabezados CORS necesarios para que los exploradores permitan la carga de recursos entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="b6091-368">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="b6091-369">Normalmente, las redes CDN proporcionan los encabezados necesarios de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b6091-369">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="b6091-370">Solo tiene que especificar tipos para los comportamientos personalizados.</span><span class="sxs-lookup"><span data-stu-id="b6091-370">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="b6091-371">Los tipos no especificados en `loadBootResource` se cargan mediante el marco de trabajo según sus comportamientos de carga predeterminados.</span><span class="sxs-lookup"><span data-stu-id="b6091-371">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="b6091-372">Cambio de la extensión de nombre de archivo de los archivos DLL</span><span class="sxs-lookup"><span data-stu-id="b6091-372">Change the filename extension of DLL files</span></span>

<span data-ttu-id="b6091-373">En caso de que necesite cambiar las extensiones de nombre de los archivos `.dll` publicados de la aplicación, siga las instrucciones de esta sección.</span><span class="sxs-lookup"><span data-stu-id="b6091-373">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="b6091-374">Después de publicar la aplicación, use un script de shell o una canalización de compilación de DevOps para cambiar el nombre de los archivos `.dll` a fin de usar otra extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="b6091-374">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="b6091-375">Establezca como destino los archivos `.dll` que están en el directorio `wwwroot` de la salida publicada de la aplicación (por ejemplo, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span><span class="sxs-lookup"><span data-stu-id="b6091-375">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="b6091-376">En los ejemplos siguientes se cambia el nombre de los archivos `.dll` para que usen la extensión de archivo `.bin`.</span><span class="sxs-lookup"><span data-stu-id="b6091-376">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="b6091-377">En Windows:</span><span class="sxs-lookup"><span data-stu-id="b6091-377">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="b6091-378">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="b6091-378">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="b6091-379">En Linux o macOS:</span><span class="sxs-lookup"><span data-stu-id="b6091-379">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="b6091-380">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="b6091-380">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="b6091-381">Para usar una extensión de archivo diferente a `.bin`, reemplace `.bin` en los comandos anteriores.</span><span class="sxs-lookup"><span data-stu-id="b6091-381">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="b6091-382">Para dirigirse a los archivos comprimidos `blazor.boot.json.gz` y `blazor.boot.json.br`, adopte uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="b6091-382">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="b6091-383">Quite los archivos comprimidos `blazor.boot.json.gz` y `blazor.boot.json.br`.</span><span class="sxs-lookup"><span data-stu-id="b6091-383">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="b6091-384">Con este enfoque, la compresión está deshabilitada.</span><span class="sxs-lookup"><span data-stu-id="b6091-384">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="b6091-385">Vuelva a comprimir el archivo `blazor.boot.json` actualizado.</span><span class="sxs-lookup"><span data-stu-id="b6091-385">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="b6091-386">Las instrucciones anteriores también se aplican cuando se usan recursos de trabajo de servicio.</span><span class="sxs-lookup"><span data-stu-id="b6091-386">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="b6091-387">Quite o vuelva a comprimir `wwwroot/service-worker-assets.js.br` y `wwwroot/service-worker-assets.js.gz`.</span><span class="sxs-lookup"><span data-stu-id="b6091-387">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="b6091-388">De lo contrario, las comprobaciones de integridad de los archivos producirán errores en el explorador.</span><span class="sxs-lookup"><span data-stu-id="b6091-388">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="b6091-389">En el siguiente ejemplo de Windows se usa un script de PowerShell colocado en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="b6091-389">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="b6091-390">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="b6091-390">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="b6091-391">Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="b6091-391">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="b6091-392">En el archivo del proyecto, el script se ejecuta después de publicar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="b6091-392">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="b6091-393">Para proporcionar comentarios, visite [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="b6091-393">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
