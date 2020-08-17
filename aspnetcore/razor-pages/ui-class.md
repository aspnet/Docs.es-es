---
title: Interfaz de usuario reutilizable de Razor en bibliotecas de clases con ASP.NET Core
author: Rick-Anderson
description: Aquí se explica cómo crear una interfaz de usuario de Razor reutilizable mediante vistas parciales en una biblioteca de clases de ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
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
uid: razor-pages/ui-class
ms.openlocfilehash: 042ef8dcc7135e0c60baec972d405380d85a6a10
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019227"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a><span data-ttu-id="0be87-103">Creación de una interfaz de usuario reutilizable con el proyecto de biblioteca de clases de Razor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0be87-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="0be87-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0be87-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0be87-105">Las vistas, páginas, controladores y modelos de página de Razor, los [componentes de Razor](xref:blazor/components/class-libraries), los [componentes de vista](xref:mvc/views/view-components) y los modelos de datos se pueden integrar en una biblioteca de clases de Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="0be87-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="0be87-106">Las RCL se pueden empaquetar y reutilizar.</span><span class="sxs-lookup"><span data-stu-id="0be87-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="0be87-107">Las aplicaciones pueden incluir la RCL y reemplazar las vistas y páginas que contienen.</span><span class="sxs-lookup"><span data-stu-id="0be87-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="0be87-108">Si existe una vista,una vista parcial o una página de Razor tanto en la aplicación web como en la RCL, tiene prioridad el marcado de Razor (archivo *.cshtml*) de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="0be87-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="0be87-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0be87-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="0be87-110">Creación de una biblioteca de clases que contenga interfaz de usuario de Razor</span><span class="sxs-lookup"><span data-stu-id="0be87-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0be87-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0be87-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0be87-112">En Visual Studio, seleccione **Crear un proyecto nuevo**.</span><span class="sxs-lookup"><span data-stu-id="0be87-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="0be87-113">Seleccione **Biblioteca de clases de Razor** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0be87-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="0be87-114">Asigne un nombre a la biblioteca (por ejemplo, "RazorClassLib") > **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0be87-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="0be87-115">Para evitar un conflicto de nombres de archivo con la biblioteca de vistas generada, asegúrese de que el nombre de la biblioteca no acaba en `.Views`.</span><span class="sxs-lookup"><span data-stu-id="0be87-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="0be87-116">Seleccione **Admitir páginas y vistas** si necesita admitir vistas.</span><span class="sxs-lookup"><span data-stu-id="0be87-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="0be87-117">Solo se admite Razor Pages de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="0be87-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="0be87-118">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0be87-118">Select **Create**.</span></span>

<span data-ttu-id="0be87-119">De forma predeterminada, la plantilla de la biblioteca de clases de Razor (RCL) usa el desarrollo de componentes de Razor.</span><span class="sxs-lookup"><span data-stu-id="0be87-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="0be87-120">La opción **Admitir páginas y vistas** proporciona compatibilidad con páginas y vistas.</span><span class="sxs-lookup"><span data-stu-id="0be87-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0be87-121">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0be87-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0be87-122">Ejecute `dotnet new razorclasslib` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="0be87-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="0be87-123">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0be87-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="0be87-124">De forma predeterminada, la plantilla de la biblioteca de clases de Razor (RCL) usa el desarrollo de componentes de Razor.</span><span class="sxs-lookup"><span data-stu-id="0be87-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="0be87-125">Pase la opción `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`) para proporcionar compatibilidad con páginas y vistas.</span><span class="sxs-lookup"><span data-stu-id="0be87-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="0be87-126">Para más información, vea [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="0be87-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="0be87-127">Para evitar un conflicto de nombres de archivo con la biblioteca de vistas generada, asegúrese de que el nombre de la biblioteca no acaba en `.Views`.</span><span class="sxs-lookup"><span data-stu-id="0be87-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="0be87-128">Agregue archivos de Razor a la RCL.</span><span class="sxs-lookup"><span data-stu-id="0be87-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="0be87-129">Las plantillas de ASP.NET Core dan por sentado que el contenido de la RCL se encuentra en la carpeta *Áreas*.</span><span class="sxs-lookup"><span data-stu-id="0be87-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="0be87-130">Consulte la sección [Diseño de páginas de RCL](#rcl-pages-layout) para crear una RCL que exponga contenido en `~/Pages` y no en `~/Areas/Pages`.</span><span class="sxs-lookup"><span data-stu-id="0be87-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="0be87-131">Referencias al contenido de la RCL</span><span class="sxs-lookup"><span data-stu-id="0be87-131">Reference RCL content</span></span>

<span data-ttu-id="0be87-132">Los siguientes elementos pueden hacer referencia a la RCL:</span><span class="sxs-lookup"><span data-stu-id="0be87-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="0be87-133">Paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="0be87-133">NuGet package.</span></span> <span data-ttu-id="0be87-134">Vea [Creación de paquetes NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) y [Creación y publicación de un paquete NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="0be87-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="0be87-135">*{NombreDeProyecto}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0be87-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="0be87-136">Vea [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="0be87-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="0be87-137">Reemplazar vistas, vistas parciales y páginas</span><span class="sxs-lookup"><span data-stu-id="0be87-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="0be87-138">Si existe una vista,una vista parcial o una página de Razor tanto en la aplicación web como en la RCL, tiene prioridad el marcado de Razor (archivo *.cshtml*) de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="0be87-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="0be87-139">Por ejemplo, si agrega *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* a WebApp1, Page1 en WebApp1 prevalecerá sobre Page1 en la biblioteca de clases de Razor.</span><span class="sxs-lookup"><span data-stu-id="0be87-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="0be87-140">En la descarga de ejemplo, cambie el nombre *WebApp1/Areas/MyFeature2* por *WebApp1/Areas/MyFeature* para comprobar la prioridad.</span><span class="sxs-lookup"><span data-stu-id="0be87-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="0be87-141">Copie la vista parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* en *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0be87-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="0be87-142">Actualice el marcado para señalar la nueva ubicación.</span><span class="sxs-lookup"><span data-stu-id="0be87-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="0be87-143">Compile y ejecute la aplicación para comprobar si se está usando la versión de la vista parcial de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0be87-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="0be87-144">Diseño de páginas de RCL</span><span class="sxs-lookup"><span data-stu-id="0be87-144">RCL Pages layout</span></span>

<span data-ttu-id="0be87-145">Para hacer referencia al contenido de la RCL como si formara parte de la carpeta *Pages* de la aplicación web, cree el proyecto RCL con la siguiente estructura de archivos:</span><span class="sxs-lookup"><span data-stu-id="0be87-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="0be87-146">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="0be87-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="0be87-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="0be87-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="0be87-148">Imagine que *RazorUIClassLib/Pages/Shared* contiene dos archivos parciales: *_Header.cshtml* y *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0be87-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="0be87-149">En ese caso, se podrían agregar etiquetas `<partial>` al archivo *_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0be87-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="0be87-150">Creación de una RCL con recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="0be87-150">Create an RCL with static assets</span></span>

<span data-ttu-id="0be87-151">Una RCL puede requerir recursos estáticos complementarios a los que puede hacer referencia la RCL o la aplicación de consumo de la RCL.</span><span class="sxs-lookup"><span data-stu-id="0be87-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="0be87-152">ASP.NET Core permite crear bibliotecas de clases de Razor que incluyan recursos estáticos que estén disponibles para una aplicación de consumo.</span><span class="sxs-lookup"><span data-stu-id="0be87-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="0be87-153">Para incluir los recursos complementarios como parte de una RCL, cree una carpeta *wwwroot* en la biblioteca de clases e incluya en ella los archivos necesarios.</span><span class="sxs-lookup"><span data-stu-id="0be87-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="0be87-154">Al empaquetar una RCL, todos los recursos complementarios de la carpeta *wwwroot* se incluyen automáticamente en el paquete.</span><span class="sxs-lookup"><span data-stu-id="0be87-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="0be87-155">Use el comando `dotnet pack` en vez de la versión de NuGet.exe `nuget pack`.</span><span class="sxs-lookup"><span data-stu-id="0be87-155">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="0be87-156">Exclusión de recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="0be87-156">Exclude static assets</span></span>

<span data-ttu-id="0be87-157">Para excluir recursos estáticos, agregue la ruta de exclusión deseada al grupo de propiedades `$(DefaultItemExcludes)` en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="0be87-157">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="0be87-158">Separe las entradas con un punto y coma (`;`).</span><span class="sxs-lookup"><span data-stu-id="0be87-158">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="0be87-159">En el ejemplo siguiente, la hoja de estilos *lib.css* de la carpeta *wwwroot* no se considera un recurso estático y no se incluye en la RCL publicada:</span><span class="sxs-lookup"><span data-stu-id="0be87-159">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="0be87-160">Integración de TypeScript</span><span class="sxs-lookup"><span data-stu-id="0be87-160">Typescript integration</span></span>

<span data-ttu-id="0be87-161">Para incluir archivos TypeScript en una RCL:</span><span class="sxs-lookup"><span data-stu-id="0be87-161">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="0be87-162">Coloque los archivos TypeScript ( *.ts*) fuera de la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="0be87-162">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="0be87-163">Por ejemplo, coloque los archivos en una carpeta de *cliente*.</span><span class="sxs-lookup"><span data-stu-id="0be87-163">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="0be87-164">Configure la salida de la compilación de TypeScript para la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="0be87-164">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="0be87-165">Establezca la propiedad `TypescriptOutDir` dentro de un `PropertyGroup` en el archivo del proyecto:</span><span class="sxs-lookup"><span data-stu-id="0be87-165">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="0be87-166">Incluya el destino de TypeScript como una dependencia del destino `ResolveCurrentProjectStaticWebAssets`; para ello, agregue el siguiente destino dentro de un objeto `PropertyGroup` en el archivo del proyecto:</span><span class="sxs-lookup"><span data-stu-id="0be87-166">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="0be87-167">Consumo de contenido de una RCL a la que se hace referencia</span><span class="sxs-lookup"><span data-stu-id="0be87-167">Consume content from a referenced RCL</span></span>

<span data-ttu-id="0be87-168">Los archivos incluidos en la carpeta *wwwroot* de la RCL se exponen a la RCL o a la aplicación de consumo en el prefijo `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="0be87-168">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="0be87-169">Por ejemplo, una biblioteca denominada *Razor.Class.Lib* produce una ruta de acceso al contenido estático en `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="0be87-169">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="0be87-170">Al generar un paquete NuGet, si el nombre del ensamblado no es igual que el del identificador del paquete, use el identificador de paquete para `{LIBRARY NAME}`.</span><span class="sxs-lookup"><span data-stu-id="0be87-170">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="0be87-171">La aplicación de consumo hace referencia a los recursos estáticos proporcionados por la biblioteca con `<script>`, `<style>`, `<img>` y otras etiquetas HTML.</span><span class="sxs-lookup"><span data-stu-id="0be87-171">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="0be87-172">La aplicación de consumo debe tener habilitada la [compatibilidad con archivos estáticos](xref:fundamentals/static-files) en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0be87-172">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="0be87-173">Al ejecutar la aplicación de consumo desde la salida de la compilación (`dotnet run`), los activos web estáticos están habilitados de forma predeterminada en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="0be87-173">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="0be87-174">Para admitir recursos en otros entornos al ejecutar la aplicación desde la salida de la compilación, llame a `UseStaticWebAssets` en el generador de hosts en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0be87-174">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="0be87-175">No es necesario llamar a `UseStaticWebAssets` si se ejecuta una aplicación desde la salida publicada (`dotnet publish`).</span><span class="sxs-lookup"><span data-stu-id="0be87-175">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="0be87-176">Flujo de desarrollo de varios proyectos</span><span class="sxs-lookup"><span data-stu-id="0be87-176">Multi-project development flow</span></span>

<span data-ttu-id="0be87-177">Al ejecutar la aplicación de consumo:</span><span class="sxs-lookup"><span data-stu-id="0be87-177">When the consuming app runs:</span></span>

* <span data-ttu-id="0be87-178">Los recursos de la biblioteca de clases de Razor permanecen en sus carpetas originales.</span><span class="sxs-lookup"><span data-stu-id="0be87-178">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="0be87-179">Los recursos no se mueven a la aplicación de consumo.</span><span class="sxs-lookup"><span data-stu-id="0be87-179">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="0be87-180">Cualquier cambio dentro de la carpeta *wwwroot* de la RCL se refleja en la aplicación de consumo después de que se vuelva a compilar la RCL y sin tener que recompilar la aplicación de consumo.</span><span class="sxs-lookup"><span data-stu-id="0be87-180">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="0be87-181">Cuando se compila la biblioteca de clases de Razor, se genera un manifiesto que describe las ubicaciones de los recursos web estáticos.</span><span class="sxs-lookup"><span data-stu-id="0be87-181">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="0be87-182">La aplicación de consumo lee el manifiesto en tiempo de ejecución para consumir los recursos de los proyectos y paquetes a los que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="0be87-182">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="0be87-183">Cuando se agrega un nuevo recurso a una RCL, esta se debe recompilar para actualizar el manifiesto antes de que una aplicación de consumo pueda acceder al nuevo recurso.</span><span class="sxs-lookup"><span data-stu-id="0be87-183">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="0be87-184">Publicar</span><span class="sxs-lookup"><span data-stu-id="0be87-184">Publish</span></span>

<span data-ttu-id="0be87-185">Cuando se publica la aplicación, los recursos complementarios de todos los proyectos y paquetes a los que se hace referencia se copian en la carpeta *wwwroot* de la aplicación publicada en `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="0be87-185">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0be87-186">Las vistas, páginas, controladores y modelos de página de Razor, los [componentes de Razor](xref:blazor/components/class-libraries), los [componentes de vista](xref:mvc/views/view-components) y los modelos de datos se pueden integrar en una biblioteca de clases de Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="0be87-186">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="0be87-187">Las RCL se pueden empaquetar y reutilizar.</span><span class="sxs-lookup"><span data-stu-id="0be87-187">The RCL can be packaged and reused.</span></span> <span data-ttu-id="0be87-188">Las aplicaciones pueden incluir la RCL y reemplazar las vistas y páginas que contienen.</span><span class="sxs-lookup"><span data-stu-id="0be87-188">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="0be87-189">Si existe una vista,una vista parcial o una página de Razor tanto en la aplicación web como en la RCL, tiene prioridad el marcado de Razor (archivo *.cshtml*) de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="0be87-189">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="0be87-190">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0be87-190">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="0be87-191">Creación de una biblioteca de clases que contenga interfaz de usuario de Razor</span><span class="sxs-lookup"><span data-stu-id="0be87-191">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0be87-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0be87-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0be87-193">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="0be87-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0be87-194">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0be87-194">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0be87-195">Asigne un nombre a la biblioteca (por ejemplo, "RazorClassLib") > **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="0be87-195">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="0be87-196">Para evitar un conflicto de nombres de archivo con la biblioteca de vistas generada, asegúrese de que el nombre de la biblioteca no acaba en `.Views`.</span><span class="sxs-lookup"><span data-stu-id="0be87-196">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="0be87-197">Confirme que **ASP.NET Core 2.1** o una versión posterior está seleccionado.</span><span class="sxs-lookup"><span data-stu-id="0be87-197">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="0be87-198">Seleccione **Biblioteca de clases de Razor** > **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="0be87-198">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="0be87-199">Una RCL tiene el siguiente archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="0be87-199">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="0be87-200">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0be87-200">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0be87-201">Ejecute `dotnet new razorclasslib` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="0be87-201">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="0be87-202">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0be87-202">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="0be87-203">Para más información, vea [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="0be87-203">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="0be87-204">Para evitar un conflicto de nombres de archivo con la biblioteca de vistas generada, asegúrese de que el nombre de la biblioteca no acaba en `.Views`.</span><span class="sxs-lookup"><span data-stu-id="0be87-204">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="0be87-205">Agregue archivos de Razor a la RCL.</span><span class="sxs-lookup"><span data-stu-id="0be87-205">Add Razor files to the RCL.</span></span>

<span data-ttu-id="0be87-206">Las plantillas de ASP.NET Core dan por sentado que el contenido de la RCL se encuentra en la carpeta *Áreas*.</span><span class="sxs-lookup"><span data-stu-id="0be87-206">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="0be87-207">Consulte la sección [Diseño de páginas de RCL](#rcl-pages-layout) para crear una RCL que exponga contenido en `~/Pages` y no en `~/Areas/Pages`.</span><span class="sxs-lookup"><span data-stu-id="0be87-207">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="0be87-208">Referencias al contenido de la RCL</span><span class="sxs-lookup"><span data-stu-id="0be87-208">Reference RCL content</span></span>

<span data-ttu-id="0be87-209">Los siguientes elementos pueden hacer referencia a la RCL:</span><span class="sxs-lookup"><span data-stu-id="0be87-209">The RCL can be referenced by:</span></span>

* <span data-ttu-id="0be87-210">Paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="0be87-210">NuGet package.</span></span> <span data-ttu-id="0be87-211">Vea [Creación de paquetes NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) y [Creación y publicación de un paquete NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="0be87-211">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="0be87-212">*{NombreDeProyecto}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0be87-212">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="0be87-213">Vea [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="0be87-213">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a><span data-ttu-id="0be87-214">Tutorial: Creación y uso de un proyecto de RCL desde un proyecto de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0be87-214">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="0be87-215">En lugar de crearlo, puede descargar el [proyecto completo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) y comprobarlo.</span><span class="sxs-lookup"><span data-stu-id="0be87-215">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="0be87-216">La descarga de ejemplo contiene más código y vínculos que hacen que el proyecto sea fácil de comprobar.</span><span class="sxs-lookup"><span data-stu-id="0be87-216">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="0be87-217">Puede dejar sus comentarios en [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098) sobre las descargas de ejemplo en comparación con las instrucciones paso a paso.</span><span class="sxs-lookup"><span data-stu-id="0be87-217">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="0be87-218">Comprobar la aplicación de descarga</span><span class="sxs-lookup"><span data-stu-id="0be87-218">Test the download app</span></span>

<span data-ttu-id="0be87-219">Si no ha descargado la aplicación final y prefiere crear el proyecto de tutorial, omita este paso y vaya a la [siguiente sección](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="0be87-219">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0be87-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0be87-220">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0be87-221">Abra el archivo *.sln* en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0be87-221">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="0be87-222">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0be87-222">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0be87-223">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0be87-223">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0be87-224">Desde un símbolo del sistema en el directorio *cli*, cree la RCL y la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="0be87-224">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="0be87-225">Vaya al directorio *WebApp1* y ejecute la aplicación:</span><span class="sxs-lookup"><span data-stu-id="0be87-225">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="0be87-226">Siga las instrucciones de [Probar WebApp1](#test-webapp1).</span><span class="sxs-lookup"><span data-stu-id="0be87-226">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="0be87-227">Creación de una RCL</span><span class="sxs-lookup"><span data-stu-id="0be87-227">Create an RCL</span></span>

<span data-ttu-id="0be87-228">En esta sección, se creará una RCL</span><span class="sxs-lookup"><span data-stu-id="0be87-228">In this section, an RCL is created.</span></span> <span data-ttu-id="0be87-229">y se agregarán a ella archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="0be87-229">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0be87-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0be87-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0be87-231">Cree el proyecto de RCL:</span><span class="sxs-lookup"><span data-stu-id="0be87-231">Create the RCL project:</span></span>

* <span data-ttu-id="0be87-232">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="0be87-232">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0be87-233">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0be87-233">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0be87-234">Asigne a la aplicación el nombre **RazorUIClassLib** > **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="0be87-234">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="0be87-235">Confirme que **ASP.NET Core 2.1** o una versión posterior está seleccionado.</span><span class="sxs-lookup"><span data-stu-id="0be87-235">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="0be87-236">Seleccione **Biblioteca de clases de Razor** > **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="0be87-236">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="0be87-237">Agregue un archivo de vista parcial de Razor denominado *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0be87-237">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0be87-238">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0be87-238">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0be87-239">Ejecute lo siguiente desde la línea de comandos:</span><span class="sxs-lookup"><span data-stu-id="0be87-239">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="0be87-240">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="0be87-240">The preceding commands:</span></span>

* <span data-ttu-id="0be87-241">Crean la biblioteca de clases de Razor `RazorUIClassLib`.</span><span class="sxs-lookup"><span data-stu-id="0be87-241">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="0be87-242">Crean una página _Message de Razor y la agrega a la RCL.</span><span class="sxs-lookup"><span data-stu-id="0be87-242">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="0be87-243">El parámetro `-np` crea la página sin un `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="0be87-243">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="0be87-244">Crean un archivo [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) y lo agregan a la RCL.</span><span class="sxs-lookup"><span data-stu-id="0be87-244">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="0be87-245">El archivo *_ViewStart.cshtml* es necesario para poder usar el diseño del proyecto de Razor Pages (que agregaremos en la siguiente sección).</span><span class="sxs-lookup"><span data-stu-id="0be87-245">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a><span data-ttu-id="0be87-246">Adición de archivos y carpetas de Razor al proyecto</span><span class="sxs-lookup"><span data-stu-id="0be87-246">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="0be87-247">Reemplace el marcado de *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="0be87-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="0be87-248">Reemplace el marcado de *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="0be87-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="0be87-249">Se necesita `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` para usar la vista parcial (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="0be87-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="0be87-250">En lugar de incluir la directiva `@addTagHelper`, puede agregar un archivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0be87-250">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="0be87-251">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0be87-251">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="0be87-252">Para obtener más información sobre *_ViewImports.cshtml*, consulte la sección [Importar directivas compartidas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="0be87-252">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="0be87-253">Compile la biblioteca de clases para confirmar que no hay ningún error de compilador:</span><span class="sxs-lookup"><span data-stu-id="0be87-253">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="0be87-254">La salida de la compilación contiene *RazorUIClassLib. dll* y *RazorUIClassLib. views.dll*.</span><span class="sxs-lookup"><span data-stu-id="0be87-254">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="0be87-255">*RazorUIClassLib.Views.dll* incluye el contenido de Razor compilado.</span><span class="sxs-lookup"><span data-stu-id="0be87-255">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a><span data-ttu-id="0be87-256">Uso de la biblioteca de interfaz de usuario de Razor desde un proyecto de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0be87-256">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0be87-257">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0be87-257">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0be87-258">Cree la aplicación web de páginas de Razor:</span><span class="sxs-lookup"><span data-stu-id="0be87-258">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="0be87-259">En el **Explorador de soluciones**, haga clic con el botón derecho en la solución > **Agregar** > **Nuevo proyecto**.</span><span class="sxs-lookup"><span data-stu-id="0be87-259">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="0be87-260">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0be87-260">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0be87-261">Denomine la aplicación **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="0be87-261">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="0be87-262">Confirme que **ASP.NET Core 2.1** o una versión posterior está seleccionado.</span><span class="sxs-lookup"><span data-stu-id="0be87-262">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="0be87-263">Seleccione **Aplicación web** > **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="0be87-263">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="0be87-264">En el **Explorador de soluciones**, haga clic con el botón derecho en **WebApp1** y seleccione **Establecer como proyecto de inicio**.</span><span class="sxs-lookup"><span data-stu-id="0be87-264">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="0be87-265">En el **Explorador de soluciones**, haga clic con el botón derecho en **WebApp1** y seleccione **Dependencias de compilación** > **Dependencias del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="0be87-265">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="0be87-266">Marque **RazorUIClassLib** como dependencia de **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="0be87-266">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="0be87-267">En el **Explorador de soluciones**, haga clic con el botón derecho en **WebApp1** y seleccione **Agregar** > **Referencia**.</span><span class="sxs-lookup"><span data-stu-id="0be87-267">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="0be87-268">En el cuadro de diálogo **Administrador de referencias**, marque la casilla **RazorUIClassLib** > **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="0be87-268">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="0be87-269">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0be87-269">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0be87-270">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0be87-270">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0be87-271">Cree una aplicación web de Razor Pages y un archivo de solución que contenga dicha aplicación y la biblioteca de clases de Razor:</span><span class="sxs-lookup"><span data-stu-id="0be87-271">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="0be87-272">Compile y ejecute la aplicación web:</span><span class="sxs-lookup"><span data-stu-id="0be87-272">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="0be87-273">Probar WebApp1</span><span class="sxs-lookup"><span data-stu-id="0be87-273">Test WebApp1</span></span>

<span data-ttu-id="0be87-274">Vaya a `/MyFeature/Page1` para comprobar que la biblioteca de clases de la interfaz de usuario de Razor está en uso.</span><span class="sxs-lookup"><span data-stu-id="0be87-274">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="0be87-275">Reemplazar vistas, vistas parciales y páginas</span><span class="sxs-lookup"><span data-stu-id="0be87-275">Override views, partial views, and pages</span></span>

<span data-ttu-id="0be87-276">Si existe una vista,una vista parcial o una página de Razor tanto en la aplicación web como en la RCL, tiene prioridad el marcado de Razor (archivo *.cshtml*) de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="0be87-276">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="0be87-277">Por ejemplo, si agrega *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* a WebApp1, Page1 en WebApp1 prevalecerá sobre Page1 en la biblioteca de clases de Razor.</span><span class="sxs-lookup"><span data-stu-id="0be87-277">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="0be87-278">En la descarga de ejemplo, cambie el nombre *WebApp1/Areas/MyFeature2* por *WebApp1/Areas/MyFeature* para comprobar la prioridad.</span><span class="sxs-lookup"><span data-stu-id="0be87-278">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="0be87-279">Copie la vista parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* en *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0be87-279">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="0be87-280">Actualice el marcado para señalar la nueva ubicación.</span><span class="sxs-lookup"><span data-stu-id="0be87-280">Update the markup to indicate the new location.</span></span> <span data-ttu-id="0be87-281">Compile y ejecute la aplicación para comprobar si se está usando la versión de la vista parcial de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0be87-281">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="0be87-282">Diseño de páginas de RCL</span><span class="sxs-lookup"><span data-stu-id="0be87-282">RCL Pages layout</span></span>

<span data-ttu-id="0be87-283">Para hacer referencia al contenido de la RCL como si formara parte de la carpeta *Pages* de la aplicación web, cree el proyecto RCL con la siguiente estructura de archivos:</span><span class="sxs-lookup"><span data-stu-id="0be87-283">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="0be87-284">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="0be87-284">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="0be87-285">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="0be87-285">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="0be87-286">Imagine que *RazorUIClassLib/Pages/Shared* contiene dos archivos parciales: *_Header.cshtml* y *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0be87-286">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="0be87-287">En ese caso, se podrían agregar etiquetas `<partial>` al archivo *_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0be87-287">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0be87-288">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="0be87-288">Additional resources</span></span>

* <xref:blazor/components/class-libraries>
