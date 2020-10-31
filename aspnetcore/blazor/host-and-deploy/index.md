---
title: 'Hospedaje e implementación de ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: Descubra cómo hospedar e implementar aplicaciones de :::no-loc(Blazor):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 082072d2b70abfe60da8e2cd40daa8b93ebcc9ac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055820"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor"></a><span data-ttu-id="546d9-103">Hospedaje e implementación de ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="546d9-103">Host and deploy ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="546d9-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="546d9-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="546d9-105">Publicar la aplicación</span><span class="sxs-lookup"><span data-stu-id="546d9-105">Publish the app</span></span>

<span data-ttu-id="546d9-106">Las aplicaciones se publican para implementación en la configuración de versión.</span><span class="sxs-lookup"><span data-stu-id="546d9-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="546d9-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="546d9-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="546d9-108">Seleccione **Compilar** > **Publicar {aplicación}** en la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="546d9-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="546d9-109">Seleccione el *destino de publicación* .</span><span class="sxs-lookup"><span data-stu-id="546d9-109">Select the *publish target* .</span></span> <span data-ttu-id="546d9-110">Para publicar localmente, seleccione **Carpeta** .</span><span class="sxs-lookup"><span data-stu-id="546d9-110">To publish locally, select **Folder** .</span></span>
1. <span data-ttu-id="546d9-111">Acepte la ubicación predeterminada del campo **Elegir una carpeta** o especifique una ubicación diferente.</span><span class="sxs-lookup"><span data-stu-id="546d9-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="546d9-112">Seleccione el botón **`Publish`** .</span><span class="sxs-lookup"><span data-stu-id="546d9-112">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="546d9-113">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="546d9-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="546d9-114">Seleccione **Compilar** > **Publicar en carpeta** .</span><span class="sxs-lookup"><span data-stu-id="546d9-114">Select **Build** > **Publish to Folder** .</span></span>
1. <span data-ttu-id="546d9-115">Confirme la carpeta para recibir los recursos publicados y seleccione **`Publish`** .</span><span class="sxs-lookup"><span data-stu-id="546d9-115">Confirm the folder to receive the published assets and select **`Publish`** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="546d9-116">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="546d9-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="546d9-117">Use el comando [`dotnet publish`](/dotnet/core/tools/dotnet-publish) para publicar la aplicación con una configuración de versión:</span><span class="sxs-lookup"><span data-stu-id="546d9-117">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="546d9-118">Al publicar la aplicación se desencadena una [restauración](/dotnet/core/tools/dotnet-restore) de las dependencias del proyecto y se [compila](/dotnet/core/tools/dotnet-build) el proyecto antes de crear los recursos para la implementación.</span><span class="sxs-lookup"><span data-stu-id="546d9-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="546d9-119">Como parte del proceso de compilación, se quitan los ensamblados y métodos que no se usan para reducir los tiempos de carga y el tamaño de descarga de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="546d9-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="546d9-120">Ubicaciones de publicación:</span><span class="sxs-lookup"><span data-stu-id="546d9-120">Publish locations:</span></span>

* :::no-loc(Blazor WebAssembly):::
  * <span data-ttu-id="546d9-121">Independiente: La aplicación se publica en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="546d9-121">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="546d9-122">Para implementar la aplicación como un sitio estático, copie el contenido de la carpeta `wwwroot` en el host del sitio estático.</span><span class="sxs-lookup"><span data-stu-id="546d9-122">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="546d9-123">Hospedada: La aplicación cliente de :::no-loc(Blazor WebAssembly)::: se publica en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` de la aplicación de servidor, junto con cualquier otro recurso web estático de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="546d9-123">Hosted: The client :::no-loc(Blazor WebAssembly)::: app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="546d9-124">Implemente el contenido de la carpeta `publish` en el host.</span><span class="sxs-lookup"><span data-stu-id="546d9-124">Deploy the contents of the `publish` folder to the host.</span></span>
* <span data-ttu-id="546d9-125">:::no-loc(Blazor Server):::: La aplicación se publica en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish`.</span><span class="sxs-lookup"><span data-stu-id="546d9-125">:::no-loc(Blazor Server):::: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="546d9-126">Implemente el contenido de la carpeta `publish` en el host.</span><span class="sxs-lookup"><span data-stu-id="546d9-126">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="546d9-127">Los recursos de la carpeta se implementan en el servidor web.</span><span class="sxs-lookup"><span data-stu-id="546d9-127">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="546d9-128">La implementación puede ser un proceso manual o automatizado, en función de las herramientas de desarrollo que se usen.</span><span class="sxs-lookup"><span data-stu-id="546d9-128">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="546d9-129">Ruta de acceso base de la aplicación</span><span class="sxs-lookup"><span data-stu-id="546d9-129">App base path</span></span>

<span data-ttu-id="546d9-130">La *ruta de acceso base de la aplicación* es la de la dirección URL raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="546d9-130">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="546d9-131">Tenga en cuenta la siguiente aplicación de ASP.NET Core y la subaplicación de :::no-loc(Blazor)::::</span><span class="sxs-lookup"><span data-stu-id="546d9-131">Consider the following ASP.NET Core app and :::no-loc(Blazor)::: sub-app:</span></span>

* <span data-ttu-id="546d9-132">La aplicación de ASP.NET Core se denomina `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="546d9-132">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="546d9-133">La aplicación reside físicamente en `d:/MyApp`.</span><span class="sxs-lookup"><span data-stu-id="546d9-133">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="546d9-134">Las solicitudes se reciben en `https://www.contoso.com/{MYAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="546d9-134">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="546d9-135">Una aplicación de :::no-loc(Blazor)::: denominada `CoolApp` es una subaplicación de `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="546d9-135">A :::no-loc(Blazor)::: app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="546d9-136">La aplicación reside físicamente en `d:/MyApp/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="546d9-136">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="546d9-137">Las solicitudes se reciben en `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="546d9-137">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="546d9-138">Sin especificar una configuración adicional para `CoolApp`, la subaplicación de este escenario desconoce dónde reside en el servidor.</span><span class="sxs-lookup"><span data-stu-id="546d9-138">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="546d9-139">Por ejemplo, la aplicación no puede construir URL relativas correctas para sus recursos sin saber que reside en la ruta de acceso URL relativa `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="546d9-139">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="546d9-140">Para proporcionar la configuración de la ruta de acceso base de la aplicación de :::no-loc(Blazor)::: de `https://www.contoso.com/CoolApp/`, el atributo `<base>` de la etiqueta `href` se establece en la ruta de acceso raíz relativa del archivo `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::) o del archivo `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span><span class="sxs-lookup"><span data-stu-id="546d9-140">To provide configuration for the :::no-loc(Blazor)::: app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::) or `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="546d9-141">Las aplicaciones de :::no-loc(Blazor Server)::: establecen además la ruta de acceso base del servidor mediante una llamada a <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> en la canalización de solicitudes de la aplicación de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="546d9-141">:::no-loc(Blazor Server)::: apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="546d9-142">Al proporcionar la ruta de acceso URL relativa, un componente que no se encuentre en el directorio raíz puede construir direcciones URL relativas a la ruta de acceso raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="546d9-142">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="546d9-143">Los componentes que se encuentran en diferentes niveles de la estructura de directorios pueden compilar vínculos a otros recursos en ubicaciones de toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="546d9-143">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="546d9-144">La ruta de acceso base de la aplicación también se usa para interceptar hipervínculos seleccionados en los que el destino `href` del vínculo está dentro del espacio de URI de la ruta de acceso base de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="546d9-144">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="546d9-145">El enrutador de :::no-loc(Blazor)::: controla la navegación interna.</span><span class="sxs-lookup"><span data-stu-id="546d9-145">The :::no-loc(Blazor)::: router handles the internal navigation.</span></span>

<span data-ttu-id="546d9-146">En muchos escenarios de hospedaje, la ruta de acceso URL relativa a la aplicación es la raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="546d9-146">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="546d9-147">En estos casos, la ruta de acceso base URL relativa de la aplicación es una barra diagonal (`<base href="/" />`), que es la configuración predeterminada para una aplicación de :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="546d9-147">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a :::no-loc(Blazor)::: app.</span></span> <span data-ttu-id="546d9-148">En otros escenarios de hospedaje, como las subaplicaciones de IIS y GitHub Pages, la ruta de acceso base de la aplicación debe establecerse en la ruta de acceso URL relativa del servidor de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="546d9-148">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="546d9-149">Para establecer la ruta de acceso base de la aplicación, actualice la etiqueta `<base>` dentro de los elementos de etiqueta `<head>` del archivo `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::) o del archivo `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::).</span><span class="sxs-lookup"><span data-stu-id="546d9-149">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::) or `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::).</span></span> <span data-ttu-id="546d9-150">Establezca el valor del atributo `href` en `/{RELATIVE URL PATH}/` (la barra diagonal final es necesaria), donde `{RELATIVE URL PATH}` es la ruta de acceso URL relativa completa de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="546d9-150">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="546d9-151">En el caso de una aplicación de :::no-loc(Blazor WebAssembly)::: con una ruta de acceso URL relativa que no sea raíz (por ejemplo, `<base href="/CoolApp/">`), la aplicación no puede encontrar sus recursos *cuando se ejecuta de forma local* .</span><span class="sxs-lookup"><span data-stu-id="546d9-151">For an :::no-loc(Blazor WebAssembly)::: app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally* .</span></span> <span data-ttu-id="546d9-152">Para solucionar este problema durante la fase de desarrollo y pruebas local, puede proporcionar un argumento de *ruta de acceso base* que coincida con el valor `href` de la etiqueta `<base>` en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="546d9-152">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="546d9-153">No incluya una barra diagonal final.</span><span class="sxs-lookup"><span data-stu-id="546d9-153">Don't include a trailing slash.</span></span> <span data-ttu-id="546d9-154">Para pasar el argumento de ruta de acceso base al ejecutar la aplicación de forma local, ejecute el comando `dotnet run` desde el directorio de la aplicación con la opción `--pathbase`:</span><span class="sxs-lookup"><span data-stu-id="546d9-154">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="546d9-155">En el caso de una aplicación de :::no-loc(Blazor WebAssembly)::: con una ruta de acceso a una URL relativa de `/CoolApp/` (`<base href="/CoolApp/">`), el comando es el siguiente:</span><span class="sxs-lookup"><span data-stu-id="546d9-155">For a :::no-loc(Blazor WebAssembly)::: app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="546d9-156">La aplicación de :::no-loc(Blazor WebAssembly)::: responde de forma local en `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="546d9-156">The :::no-loc(Blazor WebAssembly)::: app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="546d9-157">**Configuración `MapFallbackToPage` de :::no-loc(Blazor Server):::**</span><span class="sxs-lookup"><span data-stu-id="546d9-157">**:::no-loc(Blazor Server)::: `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="546d9-158">Pase la siguiente ruta de acceso a <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="546d9-158">Pass the following path to <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="546d9-159">El marcador de posición `{RELATIVE PATH}` es la ruta de acceso no raíz en el servidor.</span><span class="sxs-lookup"><span data-stu-id="546d9-159">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="546d9-160">Por ejemplo, `CoolApp` es el segmento del marcador de posición si la dirección URL no raíz a la aplicación es `https://{HOST}:{PORT}/CoolApp/`:</span><span class="sxs-lookup"><span data-stu-id="546d9-160">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

<span data-ttu-id="546d9-161">**Hospedaje de varias aplicaciones de :::no-loc(Blazor WebAssembly):::**</span><span class="sxs-lookup"><span data-stu-id="546d9-161">**Host multiple :::no-loc(Blazor WebAssembly)::: apps**</span></span>

<span data-ttu-id="546d9-162">Para obtener más información sobre cómo hospedar varias aplicaciones de :::no-loc(Blazor WebAssembly)::: en una solución de :::no-loc(Blazor)::: hospedada, vea <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span><span class="sxs-lookup"><span data-stu-id="546d9-162">For more information on hosting multiple :::no-loc(Blazor WebAssembly)::: apps in a hosted :::no-loc(Blazor)::: solution, see <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span></span>

## <a name="deployment"></a><span data-ttu-id="546d9-163">Implementación</span><span class="sxs-lookup"><span data-stu-id="546d9-163">Deployment</span></span>

<span data-ttu-id="546d9-164">Para una guía sobre la implementación, consulte los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="546d9-164">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
