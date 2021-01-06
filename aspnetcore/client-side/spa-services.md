---
title: Uso de servicios de JavaScript para crear aplicaciones de página única en ASP.NET Core
author: scottaddie
description: Obtenga información sobre las ventajas de usar los servicios de JavaScript para crear una aplicación de página única (SPA) respaldada por ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
no-loc:
- appsettings.json
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
uid: client-side/spa-services
ms.openlocfilehash: 1b9f5b4b4e066cdd3dd5fbfa666c7a087949979f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054650"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="86828-103">Uso de servicios de JavaScript para crear aplicaciones de página única en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="86828-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="86828-104">Por [Scott Addie](https://github.com/scottaddie) y [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="86828-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="86828-105">Una aplicación de página única (SPA) es un tipo popular de aplicación web debido a su inherente experiencia de usuario mejorada.</span><span class="sxs-lookup"><span data-stu-id="86828-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="86828-106">La integración de bibliotecas o marcos de SPA del lado cliente, como [Angular](https://angular.io/) o [React](https://facebook.github.io/react/), con marcos del lado servidor como ASP.NET Core puede ser difícil.</span><span class="sxs-lookup"><span data-stu-id="86828-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="86828-107">Los servicios de JavaScript se desarrollaron para reducir la fricción en el proceso de integración.</span><span class="sxs-lookup"><span data-stu-id="86828-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="86828-108">Permite que haya un funcionamiento sin problemas entre las distintas pilas de tecnología de cliente y servidor.</span><span class="sxs-lookup"><span data-stu-id="86828-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="86828-109">Las características descritas en este artículo están obsoletas a partir de ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="86828-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="86828-110">Un mecanismo de integración de marcos de SPA más sencillo está disponible en el paquete NuGet [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions).</span><span class="sxs-lookup"><span data-stu-id="86828-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="86828-111">Para obtener más información, vea [[Anuncio] Microsoft.AspNetCore.SpaServices y Microsoft.AspNetCore.NodeServices quedan obsoletos](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="86828-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="86828-112">Qué son los servicios de JavaScript</span><span class="sxs-lookup"><span data-stu-id="86828-112">What is JavaScript Services</span></span>

<span data-ttu-id="86828-113">Los servicios de JavaScript son una colección de tecnologías del lado cliente para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="86828-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="86828-114">Su objetivo es colocar ASP.NET Core como la plataforma del lado servidor preferida de los desarrolladores para compilar SPA.</span><span class="sxs-lookup"><span data-stu-id="86828-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="86828-115">Los servicios de JavaScript constan de dos paquetes NuGet distintos:</span><span class="sxs-lookup"><span data-stu-id="86828-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="86828-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="86828-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="86828-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="86828-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="86828-118">Estos paquetes son útiles en los escenarios siguientes:</span><span class="sxs-lookup"><span data-stu-id="86828-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="86828-119">Ejecutar JavaScript en el servidor</span><span class="sxs-lookup"><span data-stu-id="86828-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="86828-120">Usar una biblioteca o un marco de SPA</span><span class="sxs-lookup"><span data-stu-id="86828-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="86828-121">Compilar recursos del lado cliente con Webpack</span><span class="sxs-lookup"><span data-stu-id="86828-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="86828-122">Este artículo se centra sobre todo en usar el paquete SpaServices.</span><span class="sxs-lookup"><span data-stu-id="86828-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="86828-123">Qué es SpaServices</span><span class="sxs-lookup"><span data-stu-id="86828-123">What is SpaServices</span></span>

<span data-ttu-id="86828-124">SpaServices se ha creado para posicionar ASP.NET Core como la plataforma del lado servidor preferida de los desarrolladores para compilar SPA.</span><span class="sxs-lookup"><span data-stu-id="86828-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="86828-125">SpaServices no es necesario para desarrollar SPA con ASP.NET Core y no obliga a los desarrolladores a usar un marco de cliente determinado.</span><span class="sxs-lookup"><span data-stu-id="86828-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="86828-126">SpaServices proporciona una infraestructura útil como la siguiente:</span><span class="sxs-lookup"><span data-stu-id="86828-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="86828-127">Representación previa del lado servidor</span><span class="sxs-lookup"><span data-stu-id="86828-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="86828-128">Middleware de desarrollo de Webpack</span><span class="sxs-lookup"><span data-stu-id="86828-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="86828-129">Sustitución del módulo de acceso frecuente</span><span class="sxs-lookup"><span data-stu-id="86828-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="86828-130">Aplicaciones auxiliares de enrutamiento</span><span class="sxs-lookup"><span data-stu-id="86828-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="86828-131">En conjunto, estos componentes de infraestructura mejoran el flujo de trabajo de desarrollo y la experiencia en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="86828-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="86828-132">Los componentes se pueden adoptar individualmente.</span><span class="sxs-lookup"><span data-stu-id="86828-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="86828-133">Requisitos previos para usar SpaServices</span><span class="sxs-lookup"><span data-stu-id="86828-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="86828-134">Para trabajar con SpaServices, instale lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="86828-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="86828-135">[Node.js](https://nodejs.org/) (versión 6 o posterior) con npm</span><span class="sxs-lookup"><span data-stu-id="86828-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="86828-136">Para comprobar que estos componentes están instalados y se pueden encontrar, ejecute lo siguiente desde la línea de comandos:</span><span class="sxs-lookup"><span data-stu-id="86828-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="86828-137">Si se implementa en un sitio web de Azure, no se necesita ninguna acción, ya que Node.js está instalado y disponible en los entornos de servidor.</span><span class="sxs-lookup"><span data-stu-id="86828-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="86828-138">En Windows, al usar Visual Studio 2017, el SDK se instala al seleccionar la carga de trabajo **Desarrollo multiplataforma de .NET Core**.</span><span class="sxs-lookup"><span data-stu-id="86828-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="86828-139">Paquete NuGet [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)</span><span class="sxs-lookup"><span data-stu-id="86828-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="86828-140">Representación previa del lado servidor</span><span class="sxs-lookup"><span data-stu-id="86828-140">Server-side prerendering</span></span>

<span data-ttu-id="86828-141">Una aplicación universal (también conocida como isomorfa) es una aplicación JavaScript capaz de ejecutarse tanto en el servidor como en el cliente.</span><span class="sxs-lookup"><span data-stu-id="86828-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="86828-142">Angular, React y otros marcos populares proporcionan una plataforma universal para este estilo de desarrollo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="86828-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="86828-143">La idea es representar primero los componentes del marco en el servidor mediante Node.js y luego delegar la ejecución en el cliente.</span><span class="sxs-lookup"><span data-stu-id="86828-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="86828-144">Los [Asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) de ASP.NET Core proporcionados por SpaServices simplifican la implementación de la representación previa del lado servidor al invocar las funciones de JavaScript en el servidor.</span><span class="sxs-lookup"><span data-stu-id="86828-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="86828-145">Requisitos previos de la representación previa del lado servidor</span><span class="sxs-lookup"><span data-stu-id="86828-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="86828-146">Instale el paquete de npm [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering):</span><span class="sxs-lookup"><span data-stu-id="86828-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="86828-147">Configuración de la representación previa del lado servidor</span><span class="sxs-lookup"><span data-stu-id="86828-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="86828-148">Los asistentes de etiquetas se pueden descubrir mediante el registro del espacio de nombres en el archivo *_ViewImports.cshtml* del proyecto:</span><span class="sxs-lookup"><span data-stu-id="86828-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="86828-149">Estos asistentes de etiquetas abstraen las complejidades de la comunicación directa con las API de bajo nivel al aprovechar una sintaxis similar a HTML dentro de la vista de Razor:</span><span class="sxs-lookup"><span data-stu-id="86828-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="86828-150">Asistente de etiquetas asp-prerender-module</span><span class="sxs-lookup"><span data-stu-id="86828-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="86828-151">El asistente de etiquetas `asp-prerender-module`, que se usa en el ejemplo de código anterior, ejecuta *ClientApp/dist/main-server.js* en el servidor mediante Node.js.</span><span class="sxs-lookup"><span data-stu-id="86828-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="86828-152">Por motivos de claridad, el archivo *main-server.js* es un artefacto de la tarea de transpilación de TypeScript a JavaScript en el proceso de compilación de [Webpack](https://webpack.github.io/).</span><span class="sxs-lookup"><span data-stu-id="86828-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="86828-153">Webpack define un alias de punto de entrada de `main-server`; y el recorrido del gráfico de dependencias de este alias comienza en el archivo *ClientApp/boot-server.ts*:</span><span class="sxs-lookup"><span data-stu-id="86828-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="86828-154">En el siguiente ejemplo de Angular, el archivo *ClientApp/boot-server.ts* usa la función `createServerRenderer` y el tipo `RenderResult` del paquete `aspnet-prerendering` de npm para configurar la representación del servidor mediante Node.js.</span><span class="sxs-lookup"><span data-stu-id="86828-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="86828-155">El marcado HTML destinado a la representación del lado servidor se pasa a una llamada a la función resolve, que se encapsula en un objeto `Promise` de JavaScript fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="86828-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="86828-156">La importancia del objeto `Promise` es que proporciona de forma asincrónica el marcado HTML a la página para que se inserte en el elemento de marcador de posición del DOM.</span><span class="sxs-lookup"><span data-stu-id="86828-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="86828-157">Asistente de etiquetas asp-prerender-data</span><span class="sxs-lookup"><span data-stu-id="86828-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="86828-158">Cuando se combina con el asistente de etiquetas `asp-prerender-module`, el asistente de etiquetas `asp-prerender-data` se puede usar para pasar información contextual de la vista de Razor al código JavaScript del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="86828-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="86828-159">Por ejemplo, el marcado siguiente pasa los datos de usuario al módulo `main-server`:</span><span class="sxs-lookup"><span data-stu-id="86828-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="86828-160">El argumento `UserName` recibido se serializa mediante el serializador JSON integrado y se almacena en el objeto `params.data`.</span><span class="sxs-lookup"><span data-stu-id="86828-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="86828-161">En el siguiente ejemplo de Angular, los datos se usan para construir un saludo personalizado en un elemento `h1`:</span><span class="sxs-lookup"><span data-stu-id="86828-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="86828-162">Los nombres de propiedad pasados en los asistentes de etiquetas se representan con la notación **PascalCase**.</span><span class="sxs-lookup"><span data-stu-id="86828-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="86828-163">Compare esto con JavaScript, donde los mismos nombres de propiedad se representan con la notación **camelCase**.</span><span class="sxs-lookup"><span data-stu-id="86828-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="86828-164">La configuración de serialización de JSON predeterminada es responsable de esta diferencia.</span><span class="sxs-lookup"><span data-stu-id="86828-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="86828-165">Para ampliar el ejemplo de código anterior, los datos se pueden pasar del servidor a la vista mediante la hidratación de la propiedad `globals` proporcionada a la función `resolve`:</span><span class="sxs-lookup"><span data-stu-id="86828-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="86828-166">La matriz `postList` definida en el objeto `globals` se adjunta al objeto `window` global del explorador.</span><span class="sxs-lookup"><span data-stu-id="86828-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="86828-167">Esta variable que se eleva al ámbito global elimina la duplicación del esfuerzo, sobre todo en lo que respecta a la carga de los mismos datos una vez en el servidor y de nuevo en el cliente.</span><span class="sxs-lookup"><span data-stu-id="86828-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![Variable global postList adjuntada al objeto de ventana](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="86828-169">Middleware de desarrollo de Webpack</span><span class="sxs-lookup"><span data-stu-id="86828-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="86828-170">El [middleware de desarrollo de Webpack](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) incorpora un flujo de trabajo de desarrollo simplificado, mediante el que Webpack compila los recursos a petición.</span><span class="sxs-lookup"><span data-stu-id="86828-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="86828-171">El middleware compila y atiende automáticamente los recursos del lado cliente cuando se recarga una página en el explorador.</span><span class="sxs-lookup"><span data-stu-id="86828-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="86828-172">El enfoque alternativo consiste en invocar manualmente a Webpack mediante el script de compilación de npm del proyecto cuando cambia una dependencia de terceros o el código personalizado.</span><span class="sxs-lookup"><span data-stu-id="86828-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="86828-173">En el ejemplo siguiente se muestra un script de compilación de npm en el archivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="86828-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="86828-174">Requisitos previos del middleware de desarrollo de Webpack</span><span class="sxs-lookup"><span data-stu-id="86828-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="86828-175">Instale el paquete de npm [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack):</span><span class="sxs-lookup"><span data-stu-id="86828-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="86828-176">Configuración del middleware de desarrollo de Webpack</span><span class="sxs-lookup"><span data-stu-id="86828-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="86828-177">El middleware de desarrollo de Webpack se registra en la canalización de solicitudes HTTP mediante el siguiente código en el método `Configure` del archivo *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="86828-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="86828-178">Se debe llamar al método de extensión `UseWebpackDevMiddleware` antes de [registrar el hospedaje de archivos estáticos](xref:fundamentals/static-files) mediante el método de extensión `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="86828-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="86828-179">Por motivos de seguridad, registre el middleware solo cuando la aplicación se ejecute en modo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="86828-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="86828-180">La propiedad `output.publicPath` del archivo *webpack.config.js* indica al middleware que observe si se producen cambios en la carpeta `dist`:</span><span class="sxs-lookup"><span data-stu-id="86828-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="86828-181">Sustitución del módulo de acceso frecuente</span><span class="sxs-lookup"><span data-stu-id="86828-181">Hot Module Replacement</span></span>

<span data-ttu-id="86828-182">Piense en la característica de [sustitución del módulo de acceso frecuente](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) de Webpack como una evolución del [middleware de desarrollo de Webpack](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="86828-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="86828-183">HMR presenta las mismas ventajas, pero optimiza aún más el flujo de trabajo de desarrollo al actualizar automáticamente el contenido de la página después de compilar los cambios.</span><span class="sxs-lookup"><span data-stu-id="86828-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="86828-184">No lo confunda con una actualización del explorador, lo que interferiría con el estado en memoria actual y la sesión de depuración de la SPA.</span><span class="sxs-lookup"><span data-stu-id="86828-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="86828-185">Hay un vínculo activo entre el servicio de middleware de desarrollo de Webpack y el explorador, lo que significa que los cambios se insertan en el explorador.</span><span class="sxs-lookup"><span data-stu-id="86828-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="86828-186">Requisitos previos de la sustitución del módulo de acceso frecuente</span><span class="sxs-lookup"><span data-stu-id="86828-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="86828-187">Instale el paquete de npm [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware):</span><span class="sxs-lookup"><span data-stu-id="86828-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="86828-188">Configuración de la sustitución del módulo de acceso frecuente</span><span class="sxs-lookup"><span data-stu-id="86828-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="86828-189">El componente HMR debe estar registrado en la canalización de solicitudes HTTP de MVC en el método `Configure`:</span><span class="sxs-lookup"><span data-stu-id="86828-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="86828-190">Tal y como pasaba con el [middleware de desarrollo de Webpack](#webpack-dev-middleware), se debe llamar al método de extensión `UseWebpackDevMiddleware` antes del método de extensión `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="86828-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="86828-191">Por motivos de seguridad, registre el middleware solo cuando la aplicación se ejecute en modo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="86828-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="86828-192">El archivo *webpack.config.js* debe definir una matriz `plugins`, aunque se deje vacío:</span><span class="sxs-lookup"><span data-stu-id="86828-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="86828-193">Después de cargar la aplicación en el explorador, la pestaña de la consola de las herramientas de desarrollo proporciona la confirmación de la activación de HMR:</span><span class="sxs-lookup"><span data-stu-id="86828-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Mensaje conectado de la sustitución del módulo de acceso frecuente](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="86828-195">Aplicaciones auxiliares de enrutamiento</span><span class="sxs-lookup"><span data-stu-id="86828-195">Routing helpers</span></span>

<span data-ttu-id="86828-196">En la mayoría de las SPA basadas en ASP.NET Core, a menudo se prefiere el enrutamiento del lado cliente además del enrutamiento del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="86828-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="86828-197">Los sistemas de enrutamiento de SPA y MVC pueden funcionar de forma independiente sin interferencias.</span><span class="sxs-lookup"><span data-stu-id="86828-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="86828-198">Aun así, hay un caso perimetral que plantea desafíos: identificar las respuestas HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="86828-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="86828-199">Piense en el escenario en el que se usa una ruta sin extensión de `/some/page`.</span><span class="sxs-lookup"><span data-stu-id="86828-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="86828-200">Supongamos que la solicitud no coincide con el patrón de una ruta del lado servidor, pero su patrón coincide con una ruta del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="86828-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="86828-201">Ahora piense en una solicitud entrante de `/images/user-512.png`, que suele esperar encontrar un archivo de imagen en el servidor.</span><span class="sxs-lookup"><span data-stu-id="86828-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="86828-202">Si esa ruta de acceso a recursos solicitada no coincide con ninguna ruta o archivo estático del lado servidor, es poco probable que la aplicación del lado cliente la administre (como regla general, se prefiere devolver un código de estado HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="86828-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="86828-203">Requisitos previos de las aplicaciones auxiliares de enrutamiento</span><span class="sxs-lookup"><span data-stu-id="86828-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="86828-204">Instale el paquete de npm de enrutamiento del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="86828-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="86828-205">Se usa Angular como ejemplo:</span><span class="sxs-lookup"><span data-stu-id="86828-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="86828-206">Configuración de las aplicaciones auxiliares de enrutamiento</span><span class="sxs-lookup"><span data-stu-id="86828-206">Routing helpers configuration</span></span>

<span data-ttu-id="86828-207">En el método `Configure` se usa un método de extensión denominado `MapSpaFallbackRoute`:</span><span class="sxs-lookup"><span data-stu-id="86828-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="86828-208">Las rutas se evalúan en el orden en que están configuradas.</span><span class="sxs-lookup"><span data-stu-id="86828-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="86828-209">Por consiguiente, la ruta `default` del ejemplo de código anterior se usa primero en la coincidencia de patrones.</span><span class="sxs-lookup"><span data-stu-id="86828-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="86828-210">Crear un proyecto nuevo</span><span class="sxs-lookup"><span data-stu-id="86828-210">Create a new project</span></span>

<span data-ttu-id="86828-211">Los servicios de JavaScript proporcionan plantillas de aplicación preconfiguradas.</span><span class="sxs-lookup"><span data-stu-id="86828-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="86828-212">SpaServices se usa en estas plantillas con diferentes marcos y bibliotecas, como Angular, React y Redux.</span><span class="sxs-lookup"><span data-stu-id="86828-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="86828-213">Estas plantillas se pueden instalar mediante la CLI de .NET Core al ejecutar el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="86828-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="86828-214">Se muestra una lista de las plantillas de SPA disponibles:</span><span class="sxs-lookup"><span data-stu-id="86828-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="86828-215">Plantillas</span><span class="sxs-lookup"><span data-stu-id="86828-215">Templates</span></span>                                 | <span data-ttu-id="86828-216">Nombre corto</span><span class="sxs-lookup"><span data-stu-id="86828-216">Short Name</span></span> | <span data-ttu-id="86828-217">Lenguaje</span><span class="sxs-lookup"><span data-stu-id="86828-217">Language</span></span> | <span data-ttu-id="86828-218">Etiquetas</span><span class="sxs-lookup"><span data-stu-id="86828-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="86828-219">MVC ASP.NET Core con Angular</span><span class="sxs-lookup"><span data-stu-id="86828-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="86828-220">angular</span><span class="sxs-lookup"><span data-stu-id="86828-220">angular</span></span>    | <span data-ttu-id="86828-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="86828-221">[C#]</span></span>     | <span data-ttu-id="86828-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="86828-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="86828-223">MVC ASP.NET Core con React.js</span><span class="sxs-lookup"><span data-stu-id="86828-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="86828-224">react</span><span class="sxs-lookup"><span data-stu-id="86828-224">react</span></span>      | <span data-ttu-id="86828-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="86828-225">[C#]</span></span>     | <span data-ttu-id="86828-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="86828-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="86828-227">MVC ASP.NET Core con React.js y Redux</span><span class="sxs-lookup"><span data-stu-id="86828-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="86828-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="86828-228">reactredux</span></span> | <span data-ttu-id="86828-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="86828-229">[C#]</span></span>     | <span data-ttu-id="86828-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="86828-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="86828-231">Para crear un proyecto con una de las plantillas de SPA, incluya el **nombre corto** de la plantilla en el comando [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="86828-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="86828-232">El siguiente comando crea una aplicación Angular con el MVC de ASP.NET Core configurado en el lado servidor:</span><span class="sxs-lookup"><span data-stu-id="86828-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="86828-233">Establecimiento del modo de configuración de ejecución</span><span class="sxs-lookup"><span data-stu-id="86828-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="86828-234">Hay dos modos de configuración de ejecución principales:</span><span class="sxs-lookup"><span data-stu-id="86828-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="86828-235">**Desarrollo**:</span><span class="sxs-lookup"><span data-stu-id="86828-235">**Development**:</span></span>
  * <span data-ttu-id="86828-236">Incluye mapas de origen para facilitar la depuración.</span><span class="sxs-lookup"><span data-stu-id="86828-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="86828-237">No optimiza el código del lado cliente para el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="86828-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="86828-238">**Producción**:</span><span class="sxs-lookup"><span data-stu-id="86828-238">**Production**:</span></span>
  * <span data-ttu-id="86828-239">Excluye los mapas de origen.</span><span class="sxs-lookup"><span data-stu-id="86828-239">Excludes source maps.</span></span>
  * <span data-ttu-id="86828-240">Optimiza el código del lado cliente mediante la unión y la minificación.</span><span class="sxs-lookup"><span data-stu-id="86828-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="86828-241">ASP.NET Core usa una variable de entorno denominada `ASPNETCORE_ENVIRONMENT` para almacenar el modo de configuración.</span><span class="sxs-lookup"><span data-stu-id="86828-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="86828-242">Para obtener más información, consulte [Establecimiento del entorno](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="86828-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="86828-243">Ejecución con la CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="86828-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="86828-244">Restaure los paquetes NuGet y de npm necesarios mediante la ejecución del siguiente comando en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="86828-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="86828-245">Compile y ejecute la aplicación:</span><span class="sxs-lookup"><span data-stu-id="86828-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="86828-246">La aplicación se inicia en localhost según el [modo de configuración de ejecución](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="86828-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="86828-247">Al ir a `http://localhost:5000` en el explorador, se muestra la página de aterrizaje.</span><span class="sxs-lookup"><span data-stu-id="86828-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="86828-248">Ejecución con Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="86828-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="86828-249">Abra el archivo *.csproj* generado por el comando [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="86828-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="86828-250">Los paquetes NuGet y de npm necesarios se restauran automáticamente al abrir el proyecto.</span><span class="sxs-lookup"><span data-stu-id="86828-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="86828-251">Este proceso de restauración puede tardar unos minutos y, cuando se complete, la aplicación estará lista para ejecutarse.</span><span class="sxs-lookup"><span data-stu-id="86828-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="86828-252">Haga clic en el botón verde de ejecución o presione `Ctrl + F5` y el explorador se abrirá en la página de aterrizaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="86828-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="86828-253">La aplicación se ejecuta en localhost según el [modo de configuración de ejecución](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="86828-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="86828-254">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="86828-254">Test the app</span></span>

<span data-ttu-id="86828-255">Las plantillas de SpaServices están preconfiguradas para ejecutar pruebas del lado cliente mediante [Karma](https://karma-runner.github.io/1.0/index.html) y [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="86828-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="86828-256">Jasmine es un conocido marco de pruebas unitarias para JavaScript, mientras que Karma es un ejecutor de pruebas para llevar a cabo estas pruebas.</span><span class="sxs-lookup"><span data-stu-id="86828-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="86828-257">Karma está configurado para funcionar con el [middleware de desarrollo de Webpack](#webpack-dev-middleware) de modo que no es necesario que el desarrollador detenga y ejecute la prueba cada vez que se hagan cambios.</span><span class="sxs-lookup"><span data-stu-id="86828-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="86828-258">La prueba se ejecuta de forma automática, ya sea el código que se ejecuta en el caso de prueba o el caso de prueba en sí.</span><span class="sxs-lookup"><span data-stu-id="86828-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="86828-259">Si se usa la aplicación Angular como ejemplo, ya se proporcionan dos casos de prueba de Jasmine de `CounterComponent` en el archivo *counter.component.spec.ts*:</span><span class="sxs-lookup"><span data-stu-id="86828-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="86828-260">Abra el símbolo del sistema en el directorio *ClientApp*.</span><span class="sxs-lookup"><span data-stu-id="86828-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="86828-261">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="86828-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="86828-262">El script inicia el ejecutor de pruebas de Karma, que lee la configuración definida en el archivo *karma.conf.js*.</span><span class="sxs-lookup"><span data-stu-id="86828-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="86828-263">Entre otras opciones, el archivo *karma.conf.js* identifica los archivos de prueba que se van a ejecutar mediante su matriz `files`:</span><span class="sxs-lookup"><span data-stu-id="86828-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="86828-264">Publicar la aplicación</span><span class="sxs-lookup"><span data-stu-id="86828-264">Publish the app</span></span>

<span data-ttu-id="86828-265">Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474) para obtener más información sobre cómo publicar en Azure.</span><span class="sxs-lookup"><span data-stu-id="86828-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="86828-266">Combinar los recursos del lado cliente generados y los artefactos de ASP.NET Core publicados en un paquete listo para implementar puede resultar complicado.</span><span class="sxs-lookup"><span data-stu-id="86828-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="86828-267">Afortunadamente, SpaServices orquesta todo el proceso de publicación con un destino de MSBuild personalizado denominado `RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="86828-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="86828-268">El destino de MSBuild tiene las siguientes responsabilidades:</span><span class="sxs-lookup"><span data-stu-id="86828-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="86828-269">Restaurar los paquetes de npm</span><span class="sxs-lookup"><span data-stu-id="86828-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="86828-270">Crear una compilación de nivel de producción de los recursos del lado cliente de terceros</span><span class="sxs-lookup"><span data-stu-id="86828-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="86828-271">Crear una compilación de nivel de producción de los recursos del lado cliente personalizados</span><span class="sxs-lookup"><span data-stu-id="86828-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="86828-272">Copiar los recursos generados por Webpack en la carpeta de publicación</span><span class="sxs-lookup"><span data-stu-id="86828-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="86828-273">El destino de MSBuild se invoca cuando se ejecuta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="86828-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="86828-274">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="86828-274">Additional resources</span></span>

* [<span data-ttu-id="86828-275">Documentación de Angular</span><span class="sxs-lookup"><span data-stu-id="86828-275">Angular Docs</span></span>](https://angular.io/docs)
