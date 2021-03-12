---
title: Introducción a Swashbuckle y ASP.NET Core
author: zuckerthoben
description: Obtenga información sobre cómo agregar Swashbuckle a un proyecto de ASP.NET Core Web API para integrar la interfaz de usuario de Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
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
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: fdec03422f4a4517950ff6de2a0400df5307b40f
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588541"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="678c3-103">Introducción a Swashbuckle y ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="678c3-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="678c3-104">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="678c3-104">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="678c3-105">Hay tres componentes principales de Swashbuckle:</span><span class="sxs-lookup"><span data-stu-id="678c3-105">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="678c3-106">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): modelo de objetos de Swagger y middleware para exponer objetos `SwaggerDocument` como puntos de conexión JSON.</span><span class="sxs-lookup"><span data-stu-id="678c3-106">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="678c3-107">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): generador de Swagger que genera objetos `SwaggerDocument` directamente desde las rutas, controladores y modelos.</span><span class="sxs-lookup"><span data-stu-id="678c3-107">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="678c3-108">Se suele combinar con el middleware de punto de conexión de Swagger para exponer automáticamente el JSON de Swagger.</span><span class="sxs-lookup"><span data-stu-id="678c3-108">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="678c3-109">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): versión insertada de la herramienta de interfaz de usuario de Swagger.</span><span class="sxs-lookup"><span data-stu-id="678c3-109">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="678c3-110">Interpreta el JSON de Swagger para crear una experiencia enriquecida y personalizable para describir la funcionalidad de la API web.</span><span class="sxs-lookup"><span data-stu-id="678c3-110">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="678c3-111">Incluye herramientas de ejecución de pruebas integradas para los métodos públicos.</span><span class="sxs-lookup"><span data-stu-id="678c3-111">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="678c3-112">Instalación del paquete</span><span class="sxs-lookup"><span data-stu-id="678c3-112">Package installation</span></span>

<span data-ttu-id="678c3-113">Se puede agregar Swashbuckle con los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="678c3-113">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="678c3-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="678c3-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="678c3-115">En la ventana **Consola del Administrador de paquetes**:</span><span class="sxs-lookup"><span data-stu-id="678c3-115">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="678c3-116">Vaya a **Vista** > **Otras ventanas** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="678c3-116">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="678c3-117">Vaya al directorio en el que está *TodoApi.csproj*.</span><span class="sxs-lookup"><span data-stu-id="678c3-117">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="678c3-118">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="678c3-118">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.6.3
    ```

* <span data-ttu-id="678c3-119">En el cuadro de diálogo **Administrar paquetes NuGet**:</span><span class="sxs-lookup"><span data-stu-id="678c3-119">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="678c3-120">Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** > **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="678c3-120">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="678c3-121">Establezca el **origen del paquete** en "nuget.org".</span><span class="sxs-lookup"><span data-stu-id="678c3-121">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="678c3-122">Asegúrese de que la opción "Incluir versión preliminar" está habilitada.</span><span class="sxs-lookup"><span data-stu-id="678c3-122">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="678c3-123">Escriba "Swashbuckle.AspNetCore" en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="678c3-123">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="678c3-124">Seleccione el paquete "Swashbuckle.AspNetCore" más reciente en la pestaña **Examinar** y haga clic en **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="678c3-124">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="678c3-125">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="678c3-125">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="678c3-126">Haga clic con el botón derecho en la carpeta *Paquetes* en **Panel de solución** > **Agregar paquetes...**</span><span class="sxs-lookup"><span data-stu-id="678c3-126">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="678c3-127">Establezca el menú desplegable **Origen** de la ventana **Agregar paquetes** en "nuget.org".</span><span class="sxs-lookup"><span data-stu-id="678c3-127">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="678c3-128">Asegúrese de que la opción "Show pre-release packages" (Mostrar paquetes de versión preliminar) está habilitada.</span><span class="sxs-lookup"><span data-stu-id="678c3-128">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="678c3-129">Escriba "Swashbuckle.AspNetCore" en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="678c3-129">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="678c3-130">Seleccione el paquete "Swashbuckle.AspNetCore" más reciente en el panel de resultados y haga clic en **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="678c3-130">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="678c3-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="678c3-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="678c3-132">Ejecute el siguiente comando en el **terminal integrado**:</span><span class="sxs-lookup"><span data-stu-id="678c3-132">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

### <a name="net-core-cli"></a>[<span data-ttu-id="678c3-133">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="678c3-133">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="678c3-134">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="678c3-134">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="678c3-135">Agregar y configurar el middleware de Swagger</span><span class="sxs-lookup"><span data-stu-id="678c3-135">Add and configure Swagger middleware</span></span>

<span data-ttu-id="678c3-136">Agregue el generador de Swagger a la colección de servicios en el método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="678c3-136">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

<span data-ttu-id="678c3-137">En el método `Startup.Configure`, habilite el middleware para servir el documento JSON generado y la interfaz de usuario de Swagger:</span><span class="sxs-lookup"><span data-stu-id="678c3-137">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="678c3-138">Swashbuckle se basa en <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> de MVC para detectar las rutas y los puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="678c3-138">Swashbuckle relies on MVC's <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> to discover the routes and endpoints.</span></span> <span data-ttu-id="678c3-139">Si el proyecto llama a <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>, las rutas y los puntos de conexión se detectan automáticamente.</span><span class="sxs-lookup"><span data-stu-id="678c3-139">If the project calls <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>, routes and endpoints are discovered automatically.</span></span> <span data-ttu-id="678c3-140">Al llamar a <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A>, se debe llamar explícitamente al método <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A>.</span><span class="sxs-lookup"><span data-stu-id="678c3-140">When calling <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A>, the <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> method must be explicitly called.</span></span> <span data-ttu-id="678c3-141">Para más información, consulte [Swashbuckle, ApiExplorer y enrutamiento](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span><span class="sxs-lookup"><span data-stu-id="678c3-141">For more information, see [Swashbuckle, ApiExplorer, and Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span></span>

<span data-ttu-id="678c3-142">La llamada de método `UseSwaggerUI` anterior habilita el [middleware de archivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="678c3-142">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="678c3-143">Si el destino es .NET Framework o .NET Core 1.x, agregue el paquete NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="678c3-143">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="678c3-144">Inicie la aplicación y vaya a `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="678c3-144">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="678c3-145">El documento generado en el que se describen los puntos de conexión aparecerá según se muestra en la [especificación de OpenAPI (openapi.json)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson).</span><span class="sxs-lookup"><span data-stu-id="678c3-145">The generated document describing the endpoints appears as shown in [OpenAPI specification (openapi.json)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson).</span></span>

<span data-ttu-id="678c3-146">La interfaz de usuario de Swagger se encuentra en `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="678c3-146">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="678c3-147">Explore la API a través de la interfaz de usuario de Swagger e incorpórela a otros programas.</span><span class="sxs-lookup"><span data-stu-id="678c3-147">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="678c3-148">Para servir la interfaz de usuario de Swagger en la raíz de la aplicación (`http://localhost:<port>/`), establezca la propiedad `RoutePrefix` en una cadena vacía:</span><span class="sxs-lookup"><span data-stu-id="678c3-148">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="678c3-149">Si usa directorios con IIS o un proxy inverso, establezca el punto de conexión de Swagger en una ruta de acceso relativa mediante el prefijo `./`.</span><span class="sxs-lookup"><span data-stu-id="678c3-149">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="678c3-150">Por ejemplo: `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="678c3-150">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="678c3-151">El uso de `/swagger/v1/swagger.json` indica a la aplicación que debe buscar el archivo JSON en la verdadera raíz de la dirección URL (junto con el prefijo de ruta, si se usa).</span><span class="sxs-lookup"><span data-stu-id="678c3-151">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="678c3-152">Por ejemplo, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` en lugar de `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="678c3-152">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

> [!NOTE]
> <span data-ttu-id="678c3-153">De forma predeterminada, Swashbuckle genera y expone JSON de Swagger en la versión 3.0 de la especificación (denominada oficialmente la especificación OpenAPI).</span><span class="sxs-lookup"><span data-stu-id="678c3-153">By default, Swashbuckle generates and exposes Swagger JSON in version 3.0 of the specification&mdash;officially called the OpenAPI Specification.</span></span> <span data-ttu-id="678c3-154">Para admitir la compatibilidad con versiones anteriores, puede optar por exponer JSON en el formato 2.0 en su lugar.</span><span class="sxs-lookup"><span data-stu-id="678c3-154">To support backwards compatibility, you can opt into exposing JSON in the 2.0 format instead.</span></span> <span data-ttu-id="678c3-155">Este formato 2.0 es importante para integraciones como Microsoft Power Apps y Microsoft Flow, que actualmente admiten la versión 2.0 de OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="678c3-155">This 2.0 format is important for integrations such as Microsoft Power Apps and Microsoft Flow that currently support OpenAPI version 2.0.</span></span> <span data-ttu-id="678c3-156">Para optar por el formato 2.0, establezca la propiedad `SerializeAsV2` en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="678c3-156">To opt into the 2.0 format, set the `SerializeAsV2` property in `Startup.Configure`:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a><span data-ttu-id="678c3-157">Personalizar y ampliar</span><span class="sxs-lookup"><span data-stu-id="678c3-157">Customize and extend</span></span>

<span data-ttu-id="678c3-158">Swagger proporciona opciones para documentar el modelo de objetos y personalizar la interfaz de usuario para que coincida con el tema.</span><span class="sxs-lookup"><span data-stu-id="678c3-158">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="678c3-159">En la clase `Startup`, agregue los siguientes espacios de nombres:</span><span class="sxs-lookup"><span data-stu-id="678c3-159">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="678c3-160">Información y descripción de la API</span><span class="sxs-lookup"><span data-stu-id="678c3-160">API info and description</span></span>

<span data-ttu-id="678c3-161">La acción de configuración que se pasa al método `AddSwaggerGen` agrega información, como el autor, la licencia y la descripción:</span><span class="sxs-lookup"><span data-stu-id="678c3-161">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

<span data-ttu-id="678c3-162">En la clase `Startup`, importe el siguiente espacio de nombres para que use la clase `OpenApiInfo`:</span><span class="sxs-lookup"><span data-stu-id="678c3-162">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="678c3-163">Con la clase `OpenApiInfo`, modifique la información que se muestra en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="678c3-163">Using the `OpenApiInfo` class, modify the information displayed in the UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="678c3-164">La interfaz de usuario de Swagger muestra información de la versión:</span><span class="sxs-lookup"><span data-stu-id="678c3-164">The Swagger UI displays the version's information:</span></span>

![Interfaz de usuario de Swagger con información de la versión: descripción, autor y el vínculo See more (Ver más)](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="678c3-166">comentarios XML</span><span class="sxs-lookup"><span data-stu-id="678c3-166">XML comments</span></span>

<span data-ttu-id="678c3-167">Los comentarios XML se pueden habilitar con los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="678c3-167">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="678c3-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="678c3-168">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="678c3-169">Haga clic con el botón derecho en el **Explorador de soluciones** y seleccione **Editar <nombre_de_proyecto>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="678c3-169">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="678c3-170">Agregue manualmente las líneas resaltadas al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="678c3-170">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="678c3-171">Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="678c3-171">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="678c3-172">Active la casilla **Archivo de documentación XML** en la sección **Salida** de la pestaña **Compilar**.</span><span class="sxs-lookup"><span data-stu-id="678c3-172">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="678c3-173">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="678c3-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="678c3-174">Desde el *Panel de solución*, presione **Control** y haga clic en el nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="678c3-174">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="678c3-175">Vaya a **Herramientas** > **Editar archivo**.</span><span class="sxs-lookup"><span data-stu-id="678c3-175">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="678c3-176">Agregue manualmente las líneas resaltadas al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="678c3-176">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="678c3-177">Abra el cuadro de diálogo **Opciones del proyecto** > **Compilar** > **Compilador**.</span><span class="sxs-lookup"><span data-stu-id="678c3-177">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="678c3-178">Active la casilla **Generar documentación XML** en la sección **Opciones generales**.</span><span class="sxs-lookup"><span data-stu-id="678c3-178">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="678c3-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="678c3-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="678c3-180">Agregue manualmente las líneas resaltadas al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="678c3-180">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="678c3-181">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="678c3-181">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="678c3-182">Agregue manualmente las líneas resaltadas al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="678c3-182">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="678c3-183">Puede habilitar los comentarios XML para proporcionar información de depuración para miembros y tipos públicos sin documentación.</span><span class="sxs-lookup"><span data-stu-id="678c3-183">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="678c3-184">Los miembros y tipos que no estén documentados se indican por medio del mensaje de advertencia.</span><span class="sxs-lookup"><span data-stu-id="678c3-184">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="678c3-185">Por ejemplo, el siguiente mensaje señala una infracción con el código de advertencia 1591:</span><span class="sxs-lookup"><span data-stu-id="678c3-185">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="678c3-186">Para eliminar las advertencias a nivel de proyecto, defina una lista delimitada por punto y coma de códigos de advertencia que se deban omitir en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="678c3-186">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="678c3-187">Al anexar los códigos de advertencia a `$(NoWarn);`, se aplican también los [valores predeterminados de C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).</span><span class="sxs-lookup"><span data-stu-id="678c3-187">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="678c3-188">Para suprimir las advertencias solo para miembros específicos, incluya el código en directivas de preprocesador de [advertencias #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning).</span><span class="sxs-lookup"><span data-stu-id="678c3-188">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="678c3-189">Este enfoque es útil para el código que no debe exponerse mediante los documentos de API. En el ejemplo siguiente, se omite el código de advertencia CS1591 para toda la clase `Program`.</span><span class="sxs-lookup"><span data-stu-id="678c3-189">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="678c3-190">La ejecución del código de advertencia se restaura al cerrar la definición de clase.</span><span class="sxs-lookup"><span data-stu-id="678c3-190">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="678c3-191">Especifique varios códigos de advertencia con una lista delimitada por comas.</span><span class="sxs-lookup"><span data-stu-id="678c3-191">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="678c3-192">Configure Swagger para usar el archivo XML que se generó con las instrucciones anteriores.</span><span class="sxs-lookup"><span data-stu-id="678c3-192">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="678c3-193">En Linux o sistemas operativos que no sean Windows, las rutas de acceso y los nombres de archivo pueden distinguir entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="678c3-193">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="678c3-194">Por ejemplo, un archivo *TodoApi.XML* es válido en Windows, pero no en CentOS.</span><span class="sxs-lookup"><span data-stu-id="678c3-194">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="678c3-195">En el código anterior, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) se usa para crear un nombre de archivo XML que coincida con el proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="678c3-195">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="678c3-196">La propiedad [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) sirve para crear una ruta de acceso al archivo XML.</span><span class="sxs-lookup"><span data-stu-id="678c3-196">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="678c3-197">Algunas características de Swagger (por ejemplo, esquemas de parámetros de entrada o métodos HTTP y códigos de respuesta de los atributos respectivos) funcionan sin el uso de un archivo de documentación XML.</span><span class="sxs-lookup"><span data-stu-id="678c3-197">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="678c3-198">Para la mayoría de las características, es decir, los resúmenes de los métodos y las descripciones de los parámetros y los códigos de respuesta, el uso de un archivo XML es obligatorio.</span><span class="sxs-lookup"><span data-stu-id="678c3-198">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="678c3-199">Agregar comentarios con la triple barra diagonal a una acción mejora la interfaz de usuario de Swagger en tanto agrega una descripción de la acción al encabezado de la sección.</span><span class="sxs-lookup"><span data-stu-id="678c3-199">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="678c3-200">Agregue un elemento [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) antes de la acción `Delete`:</span><span class="sxs-lookup"><span data-stu-id="678c3-200">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="678c3-201">La interfaz de usuario de Swagger muestra el texto interno del elemento `<summary>` del código anterior:</span><span class="sxs-lookup"><span data-stu-id="678c3-201">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Interfaz de usuario de Swagger en la que se muestra el comentario XML "Deletes a specific TodoItem" (Elimina una tarea pendiente específica)](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="678c3-204">La interfaz de usuario se controla por medio del esquema JSON generado:</span><span class="sxs-lookup"><span data-stu-id="678c3-204">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="678c3-205">Agregue un elemento [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) a la documentación del método de acción `Create`.</span><span class="sxs-lookup"><span data-stu-id="678c3-205">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="678c3-206">Este elemento complementa la información especificada en el elemento `<summary>` y proporciona una interfaz de usuario de Swagger más sólida.</span><span class="sxs-lookup"><span data-stu-id="678c3-206">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="678c3-207">El contenido del elemento `<remarks>` puede estar formado por texto, JSON o XML.</span><span class="sxs-lookup"><span data-stu-id="678c3-207">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="678c3-208">Fíjese en las mejoras de la interfaz de usuario con estos comentarios extra:</span><span class="sxs-lookup"><span data-stu-id="678c3-208">Notice the UI enhancements with these additional comments:</span></span>

![Interfaz de usuario de Swagger con comentarios adicionales](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="678c3-210">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="678c3-210">Data annotations</span></span>

<span data-ttu-id="678c3-211">Marque el modelo con atributos que se encuentren en el espacio de nombres [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) para controlar los componentes de la interfaz de usuario de Swagger.</span><span class="sxs-lookup"><span data-stu-id="678c3-211">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="678c3-212">Agregue el atributo `[Required]` a la propiedad `Name` de la clase `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="678c3-212">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="678c3-213">La presencia de este atributo cambia el comportamiento de la interfaz de usuario y modifica el esquema JSON subyacente:</span><span class="sxs-lookup"><span data-stu-id="678c3-213">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="678c3-214">Agregue el atributo `[Produces("application/json")]` al controlador de API.</span><span class="sxs-lookup"><span data-stu-id="678c3-214">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="678c3-215">Su propósito consiste en declarar que las acciones del controlador admiten un contenido de respuesta de tipo *application/json*:</span><span class="sxs-lookup"><span data-stu-id="678c3-215">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="678c3-216">En el menú desplegable **Response Content Type** (Tipo de contenido de respuesta) se selecciona este tipo de contenido como el valor predeterminado para las acciones GET del controlador:</span><span class="sxs-lookup"><span data-stu-id="678c3-216">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Interfaz de usuario de Swagger con el tipo de contenido de respuesta predeterminado](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="678c3-218">A medida que aumenta el uso de anotaciones de datos en la API web, la interfaz de usuario y las páginas de ayuda de la API pasan a ser más descriptivas y útiles.</span><span class="sxs-lookup"><span data-stu-id="678c3-218">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="678c3-219">Describir los tipos de respuesta</span><span class="sxs-lookup"><span data-stu-id="678c3-219">Describe response types</span></span>

<span data-ttu-id="678c3-220">Lo que más preocupa a los desarrolladores que consumen una API web es lo que se devuelve; sobre todo, los tipos de respuesta y los códigos de error (si no son los habituales).</span><span class="sxs-lookup"><span data-stu-id="678c3-220">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="678c3-221">Los tipos de respuesta y los códigos de error se indican en las anotaciones de datos y los comentarios XML.</span><span class="sxs-lookup"><span data-stu-id="678c3-221">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="678c3-222">La acción `Create` devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="678c3-222">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="678c3-223">Cuando el cuerpo de solicitud enviado es null, se devuelve un código de estado HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="678c3-223">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="678c3-224">Sin la documentación correcta en la interfaz de usuario de Swagger, el consumidor no dispone de la información necesaria de estos resultados esperados.</span><span class="sxs-lookup"><span data-stu-id="678c3-224">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="678c3-225">Corrija este problema agregando las líneas resaltadas en el siguiente ejemplo:</span><span class="sxs-lookup"><span data-stu-id="678c3-225">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="678c3-226">Ahora, la interfaz de usuario de Swagger documenta de forma clara los códigos de respuesta HTTP esperados:</span><span class="sxs-lookup"><span data-stu-id="678c3-226">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Interfaz de usuario de Swagger, donde se muestra la descripción de la clase de respuesta POST "Returns the newly created item" (Devuelve el elemento recién creado) y "400 - If the item is null" (400 - Si el elemento es nulo) para el código de estado y el motivo en Response Messages (Mensajes de respuesta)](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="678c3-228">En ASP.NET Core 2.2 o versiones posteriores, las convenciones se pueden usar como alternativa a la representación explícita de acciones individuales con `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="678c3-228">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="678c3-229">Para obtener más información, vea <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="678c3-229">For more information, see <xref:web-api/advanced/conventions>.</span></span>

<span data-ttu-id="678c3-230">Para admitir la decoración `[ProducesResponseType]`, el paquete [Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) ofrece extensiones para habilitar y enriquecer los metadatos de respuesta, esquema y parámetro.</span><span class="sxs-lookup"><span data-stu-id="678c3-230">To support the `[ProducesResponseType]` decoration, the [Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) package offers extensions to enable and enrich the response, schema, and parameter metadata.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="678c3-231">Personalizar la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="678c3-231">Customize the UI</span></span>

<span data-ttu-id="678c3-232">La interfaz de usuario predeterminada es funcional y tiene un aspecto adecuado.</span><span class="sxs-lookup"><span data-stu-id="678c3-232">The default UI is both functional and presentable.</span></span> <span data-ttu-id="678c3-233">Pero las páginas de documentación de la API deben ostentar su marca o tema.</span><span class="sxs-lookup"><span data-stu-id="678c3-233">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="678c3-234">Para incluir la personalización de marca en los componentes de Swashbuckle, se deben agregar los recursos para servir archivos estáticos y generar la estructura de carpetas que hospedará estos archivos.</span><span class="sxs-lookup"><span data-stu-id="678c3-234">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="678c3-235">Si el destino es .NET Framework o .NET Core 1.x, agregue el paquete NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) al proyecto:</span><span class="sxs-lookup"><span data-stu-id="678c3-235">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="678c3-236">El paquete NuGet anterior ya estará instalado si el destino es .NET Core 2.x y se usa el [metapaquete](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="678c3-236">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="678c3-237">Habilitar middleware de archivos estáticos:</span><span class="sxs-lookup"><span data-stu-id="678c3-237">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="678c3-238">Para insertar hojas de estilos CSS adicionales, agréguelas a la carpeta *wwwroot* del proyecto y especifique la ruta de acceso relativa en las opciones de middleware:</span><span class="sxs-lookup"><span data-stu-id="678c3-238">To inject additional CSS stylesheets, add them to the project's *wwwroot* folder and specify the relative path in the middleware options:</span></span>

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```

## <a name="additional-resources"></a><span data-ttu-id="678c3-239">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="678c3-239">Additional resources</span></span>

* [<span data-ttu-id="678c3-240">Microsoft Learn: Mejorar la experiencia del desarrollador de una API con documentación de Swagger</span><span class="sxs-lookup"><span data-stu-id="678c3-240">Microsoft Learn: Improve the developer experience of an API with Swagger documentation</span></span>](/learn/modules/improve-api-developer-experience-with-swagger/)
