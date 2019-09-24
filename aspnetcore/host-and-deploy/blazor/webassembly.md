---
title: Hospedaje e implementación de Blazor WebAssembly con ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo hospedar e implementar una aplicación de Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y GitHub Pages.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f6c143cc17d08e0cada67ec831e090661c4fed56
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081442"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="674cb-103">Hospedaje e implementación de Blazor WebAssembly con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="674cb-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="674cb-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="674cb-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="674cb-105">Con el [modelo de hospedaje de Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="674cb-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="674cb-106">La aplicación Blazor, sus dependencias y el entorno de ejecución de .NET se descargan en el explorador.</span><span class="sxs-lookup"><span data-stu-id="674cb-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="674cb-107">La aplicación se ejecuta directamente en el subproceso de interfaz de usuario del explorador.</span><span class="sxs-lookup"><span data-stu-id="674cb-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="674cb-108">Se admiten las estrategias de implementación siguientes:</span><span class="sxs-lookup"><span data-stu-id="674cb-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="674cb-109">Una aplicación ASP.NET Core proporciona la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="674cb-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="674cb-110">Esta estrategia se trata en la sección [Implementación hospedada con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="674cb-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="674cb-111">La aplicación Blazor se coloca en un servicio o servidor web de hospedaje estático, donde no se usa .NET para proporcionar la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="674cb-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="674cb-112">Esta estrategia se trata en la sección sobre [implementación independiente](#standalone-deployment), que incluye información sobre cómo hospedar una aplicación Blazor WebAssembly como una subaplicación de IIS.</span><span class="sxs-lookup"><span data-stu-id="674cb-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="674cb-113">Reescritura de las URL para conseguir un enrutamiento correcto</span><span class="sxs-lookup"><span data-stu-id="674cb-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="674cb-114">Enrutar las solicitudes para los componentes de la página en una aplicación Blazor WebAssembly no es tan sencillo como enrutar las solicitudes en una aplicación Blazor Server hospedada.</span><span class="sxs-lookup"><span data-stu-id="674cb-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="674cb-115">Le recomendamos usar una aplicación Blazor WebAssembly más ligera con dos componentes:</span><span class="sxs-lookup"><span data-stu-id="674cb-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="674cb-116">*Main.razor* &ndash; se carga en la raíz de la aplicación y contiene un vínculo al componente `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="674cb-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="674cb-117">*About.Razor* &ndash; componente `About`.</span><span class="sxs-lookup"><span data-stu-id="674cb-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="674cb-118">Cuando se solicita el documento predeterminado de la aplicación mediante la barra de direcciones del explorador (por ejemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="674cb-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="674cb-119">El explorador realiza una solicitud.</span><span class="sxs-lookup"><span data-stu-id="674cb-119">The browser makes a request.</span></span>
1. <span data-ttu-id="674cb-120">Se devuelve la página predeterminada, que suele ser *index.html*.</span><span class="sxs-lookup"><span data-stu-id="674cb-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="674cb-121">*index.html* arranca la aplicación.</span><span class="sxs-lookup"><span data-stu-id="674cb-121">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="674cb-122">Se carga el enrutador de Blazor y se representa el componente `Main` de Razor.</span><span class="sxs-lookup"><span data-stu-id="674cb-122">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="674cb-123">En la página principal, seleccionar el vínculo al componente `About` funciona en el cliente porque el enrutador de Blazor impide que el explorador haga una solicitud en Internet a `www.contoso.com` sobre `About` y presenta el propio componente `About` representado.</span><span class="sxs-lookup"><span data-stu-id="674cb-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="674cb-124">Todas las solicitudes de puntos de conexión internos *dentro de la aplicación Blazor WebAssembly* funcionan del mismo modo: Las solicitudes no desencadenan solicitudes basadas en el explorador a recursos hospedados en el servidor en Internet.</span><span class="sxs-lookup"><span data-stu-id="674cb-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="674cb-125">El enrutador controla las solicitudes de forma interna.</span><span class="sxs-lookup"><span data-stu-id="674cb-125">The router handles the requests internally.</span></span>

<span data-ttu-id="674cb-126">Si se realiza una solicitud mediante la barra de direcciones del explorador para `www.contoso.com/About`, se produce un error.</span><span class="sxs-lookup"><span data-stu-id="674cb-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="674cb-127">Este recurso no existe en el host de Internet de la aplicación, por lo que se devuelve una respuesta *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="674cb-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="674cb-128">Dado que los exploradores realizan solicitudes a hosts basados en Internet para las páginas del lado cliente, los servidores web y los servicios de hospedaje deben reescribir todas las solicitudes de recursos que no estén físicamente en el servidor a la página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="674cb-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="674cb-129">Cuando se devuelve *index.html*, el enrutador Blazor de la aplicación se hace cargo y responde con el recurso correcto.</span><span class="sxs-lookup"><span data-stu-id="674cb-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="674cb-130">Al implementar en un servidor IIS, puede usar el módulo URL Rewrite con el archivo *web.config* publicado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="674cb-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="674cb-131">Para más información, consulte la sección sobre [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="674cb-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="674cb-132">Implementación hospedada con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="674cb-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="674cb-133">Una *implementación hospedada* se encarga de proporcionar la aplicación Blazor WebAssembly a los exploradores desde una [aplicación ASP.NET Core](xref:index) que se ejecuta en un servidor web.</span><span class="sxs-lookup"><span data-stu-id="674cb-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="674cb-134">La aplicación Blazor se incluye con la aplicación ASP.NET Core en la salida publicada para que ambas se implementen juntas.</span><span class="sxs-lookup"><span data-stu-id="674cb-134">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="674cb-135">Se requiere un servidor web que pueda hospedar una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="674cb-135">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="674cb-136">En el caso de una implementación hospedada, Visual Studio incluye la plantilla de proyecto **Blazor WebAssembly App** (la plantilla `blazorwasm` al usar el comando [dotnet new](/dotnet/core/tools/dotnet-new)) con la opción **Hosted** (Hospedada) seleccionada.</span><span class="sxs-lookup"><span data-stu-id="674cb-136">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected.</span></span>

<span data-ttu-id="674cb-137">Para obtener más información sobre la implementación y el hospedaje de aplicaciones de ASP.NET Core, consulte <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="674cb-137">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="674cb-138">Para obtener información sobre cómo implementar en Azure App Service, vea <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="674cb-138">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="674cb-139">Implementación independiente</span><span class="sxs-lookup"><span data-stu-id="674cb-139">Standalone deployment</span></span>

<span data-ttu-id="674cb-140">Una *implementación independiente* proporciona la aplicación Blazor WebAssembly como un conjunto de archivos estáticos que los clientes solicitan directamente.</span><span class="sxs-lookup"><span data-stu-id="674cb-140">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="674cb-141">Cualquier servidor de archivos estático es capaz de servir a la aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="674cb-141">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="674cb-142">Los activos de implementación independientes se publican en la carpeta */bin/Release/{RED DE DESTINO}/publish/{NOMBRE DE ENSAMBLADO}/dist*.</span><span class="sxs-lookup"><span data-stu-id="674cb-142">Standalone deployment assets are published to the *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="674cb-143">IIS</span><span class="sxs-lookup"><span data-stu-id="674cb-143">IIS</span></span>

<span data-ttu-id="674cb-144">IIS es un servidor de archivos estáticos compatible con las aplicaciones de Blazor.</span><span class="sxs-lookup"><span data-stu-id="674cb-144">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="674cb-145">Para configurar IIS para hospedar Blazor, vea [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Compilación de un sitio web estático en IIS).</span><span class="sxs-lookup"><span data-stu-id="674cb-145">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="674cb-146">Los recursos publicados se crean en la carpeta */bin/Release/{TARGET FRAMEWORK}/publish*.</span><span class="sxs-lookup"><span data-stu-id="674cb-146">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="674cb-147">Hospede el contenido de la carpeta *publish* en el servidor web o el servicio de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="674cb-147">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="674cb-148">web.config</span><span class="sxs-lookup"><span data-stu-id="674cb-148">web.config</span></span>

<span data-ttu-id="674cb-149">Cuando se publica un proyecto de Blazor, se crea un archivo *web.config* con la siguiente configuración de IIS:</span><span class="sxs-lookup"><span data-stu-id="674cb-149">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="674cb-150">Se establecen los tipos MIME de las siguientes extensiones de archivo:</span><span class="sxs-lookup"><span data-stu-id="674cb-150">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="674cb-151">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="674cb-151">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="674cb-152">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="674cb-152">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="674cb-153">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="674cb-153">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="674cb-154">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="674cb-154">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="674cb-155">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="674cb-155">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="674cb-156">Se habilita la compresión HTTP de los siguientes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="674cb-156">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="674cb-157">Se establecen reglas del módulo URL Rewrite:</span><span class="sxs-lookup"><span data-stu-id="674cb-157">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="674cb-158">Proporcionar el subdirectorio donde residen los recursos estáticos de la aplicación ( *{ASSEMBLY NAME}/dist/{PATH REQUESTED}* ).</span><span class="sxs-lookup"><span data-stu-id="674cb-158">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="674cb-159">Crear el enrutamiento de reserva de SPA para que las solicitudes de recursos que no sean archivos se redirijan al documento predeterminado de la aplicación en su carpeta de recursos estáticos ( *{ASSEMBLY NAME}/dist/index.html*).</span><span class="sxs-lookup"><span data-stu-id="674cb-159">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="674cb-160">Instalación del módulo URL Rewrite</span><span class="sxs-lookup"><span data-stu-id="674cb-160">Install the URL Rewrite Module</span></span>

<span data-ttu-id="674cb-161">El [módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) es necesario para reescribir las URL.</span><span class="sxs-lookup"><span data-stu-id="674cb-161">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="674cb-162">El módulo no se instala de forma predeterminada y no está disponible para instalar como una característica de servicio de rol del servidor web (IIS).</span><span class="sxs-lookup"><span data-stu-id="674cb-162">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="674cb-163">El módulo se debe descargar desde el sitio web de IIS.</span><span class="sxs-lookup"><span data-stu-id="674cb-163">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="674cb-164">Use el instalador de plataforma web para instalar el módulo:</span><span class="sxs-lookup"><span data-stu-id="674cb-164">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="674cb-165">De forma local, vaya a la [página de descargas del módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="674cb-165">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="674cb-166">En el caso de la versión en inglés, seleccione **WebPI** para descargar el instalador de WebPI.</span><span class="sxs-lookup"><span data-stu-id="674cb-166">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="674cb-167">En el caso de otros idiomas, seleccione la arquitectura adecuada del servidor (x86/x64) para descargar el instalador.</span><span class="sxs-lookup"><span data-stu-id="674cb-167">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="674cb-168">Copie el instalador en el servidor.</span><span class="sxs-lookup"><span data-stu-id="674cb-168">Copy the installer to the server.</span></span> <span data-ttu-id="674cb-169">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="674cb-169">Run the installer.</span></span> <span data-ttu-id="674cb-170">Haga clic en el botón **Instalar** y acepte los términos de licencia.</span><span class="sxs-lookup"><span data-stu-id="674cb-170">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="674cb-171">No es necesario reiniciar el servidor al finalizar la instalación.</span><span class="sxs-lookup"><span data-stu-id="674cb-171">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="674cb-172">Configuración del sitio web</span><span class="sxs-lookup"><span data-stu-id="674cb-172">Configure the website</span></span>

<span data-ttu-id="674cb-173">Configure la **ruta de acceso física** del sitio web a la carpeta de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="674cb-173">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="674cb-174">La carpeta contiene:</span><span class="sxs-lookup"><span data-stu-id="674cb-174">The folder contains:</span></span>

* <span data-ttu-id="674cb-175">El archivo *web.config* que usa IIS para configurar el sitio web, incluidas las reglas de redireccionamiento y los tipos de contenido de archivos necesarios.</span><span class="sxs-lookup"><span data-stu-id="674cb-175">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="674cb-176">La carpeta de recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="674cb-176">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="674cb-177">Hospedaje como subaplicación de IIS</span><span class="sxs-lookup"><span data-stu-id="674cb-177">Host as an IIS sub-app</span></span>

<span data-ttu-id="674cb-178">Si una aplicación independiente se hospeda como una subaplicación de IIS, realice una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="674cb-178">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="674cb-179">Deshabilite el controlador del módulo de ASP.NET Core heredado.</span><span class="sxs-lookup"><span data-stu-id="674cb-179">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="674cb-180">Para quitar el controlador del archivo *web.config* publicado de la aplicación de Blazor, agregue una sección `<handlers>` al archivo:</span><span class="sxs-lookup"><span data-stu-id="674cb-180">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="674cb-181">Deshabilite la herencia de la sección `<system.webServer>` de la aplicación raíz (principal) mediante un elemento `<location>` con `inheritInChildApplications` establecido en `false`:</span><span class="sxs-lookup"><span data-stu-id="674cb-181">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="674cb-182">Además de [configurarse la ruta de acceso base de la aplicación](xref:host-and-deploy/blazor/index#app-base-path), se quita el controlador o se deshabilita la herencia.</span><span class="sxs-lookup"><span data-stu-id="674cb-182">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="674cb-183">Establezca la ruta de acceso base de la aplicación en el archivo *index.html* de la aplicación en el alias de IIS que ha usado al configurar la subaplicación en IIS.</span><span class="sxs-lookup"><span data-stu-id="674cb-183">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="674cb-184">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="674cb-184">Troubleshooting</span></span>

<span data-ttu-id="674cb-185">Si se recibe un error *500 Error interno del servidor* y el administrador de IIS produce errores al intentar acceder a la configuración del sitio web, confirme que el módulo URL Rewrite está instalado.</span><span class="sxs-lookup"><span data-stu-id="674cb-185">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="674cb-186">Si no lo está, IIS no puede analizar el archivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="674cb-186">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="674cb-187">Esto impide que el Administrador de IIS cargue la configuración del sitio web y que el sitio web proporcione los archivos estáticos de Blazor.</span><span class="sxs-lookup"><span data-stu-id="674cb-187">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="674cb-188">Para obtener más información sobre cómo solucionar problemas de las implementaciones en IIS, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="674cb-188">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="674cb-189">Almacenamiento de Azure</span><span class="sxs-lookup"><span data-stu-id="674cb-189">Azure Storage</span></span>

<span data-ttu-id="674cb-190">El hospedaje de archivos estáticos de [Azure Storage](/azure/storage/) permite el hospedaje de aplicaciones Blazor sin servidor.</span><span class="sxs-lookup"><span data-stu-id="674cb-190">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="674cb-191">Se admiten nombres de dominio personalizados, Azure Content Delivery Network (CDN) y HTTPS.</span><span class="sxs-lookup"><span data-stu-id="674cb-191">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="674cb-192">Cuando el servicio de blob está habilitado para el hospedaje de sitios web estáticos en una cuenta de almacenamiento:</span><span class="sxs-lookup"><span data-stu-id="674cb-192">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="674cb-193">Establece el **nombre de documento de índice** en `index.html`.</span><span class="sxs-lookup"><span data-stu-id="674cb-193">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="674cb-194">Establece la **ruta de acceso del documento de error** en `index.html`.</span><span class="sxs-lookup"><span data-stu-id="674cb-194">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="674cb-195">Los componentes Razor y otros puntos de conexión que no son de archivo no residen en las rutas de acceso físicas del contenido estático almacenado por el servicio de blob.</span><span class="sxs-lookup"><span data-stu-id="674cb-195">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="674cb-196">Cuando se recibe una solicitud de uno de estos recursos que debe controlar el enrutador de Blazor, el error *404: no encontrado* generado por el servicio de blob enruta la solicitud a la **ruta de acceso del documento de error**.</span><span class="sxs-lookup"><span data-stu-id="674cb-196">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="674cb-197">Se devuelve el blob *index.html* y el enrutador de Blazor carga y procesa la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="674cb-197">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="674cb-198">Para más información, consulte [Hospedaje de sitios web estáticos en Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="674cb-198">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="674cb-199">Nginx</span><span class="sxs-lookup"><span data-stu-id="674cb-199">Nginx</span></span>

<span data-ttu-id="674cb-200">El siguiente archivo *nginx.conf* se ha simplificado para mostrar cómo configurar Nginx para enviar el archivo *index.html* siempre que no pueda encontrar un archivo correspondiente en el disco.</span><span class="sxs-lookup"><span data-stu-id="674cb-200">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="674cb-201">Para obtener más información sobre la configuración del servidor web de producción de Nginx, consulte [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creación de archivos de configuración de NGINX y NGINX Plus).</span><span class="sxs-lookup"><span data-stu-id="674cb-201">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="674cb-202">Nginx en Docker</span><span class="sxs-lookup"><span data-stu-id="674cb-202">Nginx in Docker</span></span>

<span data-ttu-id="674cb-203">Para hospedar Blazor en Docker mediante Nginx, configure el Dockerfile para usar la imagen de Nginx basada en Alpine.</span><span class="sxs-lookup"><span data-stu-id="674cb-203">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="674cb-204">Actualice el Dockerfile para copiar el archivo *nginx.config* en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="674cb-204">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="674cb-205">Agregue una línea al Dockerfile, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="674cb-205">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="674cb-206">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="674cb-206">GitHub Pages</span></span>

<span data-ttu-id="674cb-207">Para controlar las reescrituras de URL, agregue un archivo *404.html* con un script que controle el redireccionamiento de la solicitud a la página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="674cb-207">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="674cb-208">Para consultar una implementación de ejemplo que ha proporcionado la comunidad, vea [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) (Aplicaciones de página única para GitHub Pages [rafrex/spa-github-pages en GitHub]).</span><span class="sxs-lookup"><span data-stu-id="674cb-208">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="674cb-209">Se puede ver un ejemplo del enfoque de la comunidad en [blazor-demo/blazor-demo.github.io en GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sitio activo](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="674cb-209">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="674cb-210">Al usar un sitio de proyecto en lugar de un sitio de la organización, agregue o actualice la etiqueta `<base>` en *index.html*.</span><span class="sxs-lookup"><span data-stu-id="674cb-210">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="674cb-211">Defina el valor del atributo `href` con el nombre del repositorio de GitHub con una barra diagonal final (por ejemplo, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="674cb-211">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="674cb-212">Valores de configuración de host</span><span class="sxs-lookup"><span data-stu-id="674cb-212">Host configuration values</span></span>

<span data-ttu-id="674cb-213">Las [aplicaciones Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) pueden aceptar los siguientes valores de configuración de host como argumentos de línea de comandos en tiempo de ejecución en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="674cb-213">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="674cb-214">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="674cb-214">Content Root</span></span>

<span data-ttu-id="674cb-215">El argumento `--contentroot` establece la ruta de acceso absoluta al directorio que incluye los archivos de contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="674cb-215">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="674cb-216">En los ejemplos siguientes, `/content-root-path` es la ruta de acceso raíz del contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="674cb-216">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="674cb-217">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="674cb-217">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="674cb-218">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="674cb-218">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="674cb-219">Agregue una entrada al archivo *launchSettings.json* de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="674cb-219">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="674cb-220">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="674cb-220">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="674cb-221">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="674cb-221">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="674cb-222">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="674cb-222">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="674cb-223">Ruta de acceso base</span><span class="sxs-lookup"><span data-stu-id="674cb-223">Path base</span></span>

<span data-ttu-id="674cb-224">El argumento `--pathbase` establece la ruta de acceso base de la aplicación para una aplicación que se ejecuta localmente con una ruta de acceso de URL relativa que no es raíz (el valor `href` de la etiqueta `<base>` se establece en una ruta de acceso que no sea `/` para ensayo y producción).</span><span class="sxs-lookup"><span data-stu-id="674cb-224">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="674cb-225">En los ejemplos siguientes, `/relative-URL-path` es la ruta de acceso base de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="674cb-225">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="674cb-226">Para obtener más información, vea [Ruta de acceso base de la aplicación](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="674cb-226">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="674cb-227">A diferencia de la ruta de acceso proporcionada al valor `href` de la etiqueta `<base>`, no incluya una barra diagonal final (`/`) al pasar el valor del argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="674cb-227">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="674cb-228">Si se proporciona la ruta de acceso base de la aplicación en la etiqueta `<base>` como `<base href="/CoolApp/">` (se incluye una barra diagonal final), se pasa el valor del argumento de línea de comandos como `--pathbase=/CoolApp` (sin barra diagonal final).</span><span class="sxs-lookup"><span data-stu-id="674cb-228">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="674cb-229">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="674cb-229">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="674cb-230">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="674cb-230">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="674cb-231">Agregue una entrada al archivo *launchSettings.json* de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="674cb-231">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="674cb-232">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="674cb-232">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="674cb-233">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="674cb-233">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="674cb-234">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="674cb-234">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="674cb-235">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="674cb-235">URLs</span></span>

<span data-ttu-id="674cb-236">El argumento `--urls` establece las direcciones IP o las direcciones de host con los puertos y protocolos en los que escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="674cb-236">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="674cb-237">Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="674cb-237">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="674cb-238">En el directorio de la aplicación, ejecute lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="674cb-238">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="674cb-239">Agregue una entrada al archivo *launchSettings.json* de la aplicación en el perfil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="674cb-239">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="674cb-240">Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="674cb-240">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="674cb-241">En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**.</span><span class="sxs-lookup"><span data-stu-id="674cb-241">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="674cb-242">Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="674cb-242">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="674cb-243">Configurar el enlazador</span><span class="sxs-lookup"><span data-stu-id="674cb-243">Configure the Linker</span></span>

<span data-ttu-id="674cb-244">Blazor realiza la vinculación de lenguaje intermedio (IL) en cada compilación para quitar el IL innecesario de los ensamblados de salida.</span><span class="sxs-lookup"><span data-stu-id="674cb-244">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="674cb-245">La vinculación de ensamblados puede controlarse en la compilación.</span><span class="sxs-lookup"><span data-stu-id="674cb-245">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="674cb-246">Para más información, consulte <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="674cb-246">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>