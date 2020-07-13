---
title: Archivos estáticos en ASP.NET Core
author: rick-anderson
description: Aprenda a proporcionar y proteger los archivos estáticos y a configurar los comportamientos de middleware de hospedaje de archivos estáticos en una aplicación web de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 3f4fc6f7d9d44d76d0504d9666df41571fd0b12c
ms.sourcegitcommit: d306407dc5bfe6fdfbac482214b3f59371b582bc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2020
ms.locfileid: "85951948"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="d4404-103">Archivos estáticos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4404-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4404-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="d4404-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="d4404-105">Los archivos estáticos, como HTML, CSS, imágenes y JavaScript, son activos que una aplicación de ASP.NET Core proporciona directamente a los clientes de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d4404-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="d4404-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d4404-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="d4404-107">Proporcionar archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="d4404-107">Serve static files</span></span>

<span data-ttu-id="d4404-108">Los archivos estáticos se almacenan en el directorio [raíz web](xref:fundamentals/index#web-root) del proyecto.</span><span class="sxs-lookup"><span data-stu-id="d4404-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="d4404-109">El directorio predeterminado es `{content root}/wwwroot`, pero se puede cambiar con el método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>.</span><span class="sxs-lookup"><span data-stu-id="d4404-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="d4404-110">Para más información, vea [Raíz del contenido](xref:fundamentals/index#content-root) y [Raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d4404-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="d4404-111">El método <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> establece la raíz de contenido en el directorio actual:</span><span class="sxs-lookup"><span data-stu-id="d4404-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="d4404-112">El código anterior se creó con la plantilla de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="d4404-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="d4404-113">Se puede acceder a los archivos estáticos a través de una ruta de acceso relativa a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d4404-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="d4404-114">Por ejemplo, las plantillas de proyecto **Aplicación web** contienen varias carpetas dentro de la carpeta `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="d4404-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="d4404-115">Considere la posibilidad de crear la carpeta *wwwroot/images* y agregar el archivo *wwwroot/images/MyImage.jpg*.</span><span class="sxs-lookup"><span data-stu-id="d4404-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="d4404-116">El formato del URI para acceder a un archivo en la carpeta `images` es `https://<hostname>/images/<image_file_name>`.</span><span class="sxs-lookup"><span data-stu-id="d4404-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="d4404-117">Por ejemplo, `https://localhost:5001/images/MyImage.jpg`.</span><span class="sxs-lookup"><span data-stu-id="d4404-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="d4404-118">Suministro de archivos en la raíz web</span><span class="sxs-lookup"><span data-stu-id="d4404-118">Serve files in web root</span></span>

<span data-ttu-id="d4404-119">Las plantillas de aplicación web predeterminadas llaman al método <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure`, que permite proporcionar archivos estáticos:</span><span class="sxs-lookup"><span data-stu-id="d4404-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="d4404-120">La sobrecarga del método `UseStaticFiles` sin parámetros marca los archivos en la [raíz web](xref:fundamentals/index#web-root) como que se pueden proporcionar.</span><span class="sxs-lookup"><span data-stu-id="d4404-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="d4404-121">El siguiente marcado hace referencia a *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="d4404-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="d4404-122">En el código anterior, el carácter de tilde de la ñ `~/` apunta a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d4404-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="d4404-123">Proporcionar archivos fuera de la raíz web</span><span class="sxs-lookup"><span data-stu-id="d4404-123">Serve files outside of web root</span></span>

<span data-ttu-id="d4404-124">Considere una jerarquía de directorios en la que residen fuera de la [raíz web](xref:fundamentals/index#web-root) los archivos estáticos que se van a proporcionar:</span><span class="sxs-lookup"><span data-stu-id="d4404-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="d4404-125">Una solicitud puede acceder al archivo `red-rose.jpg` configurando el middleware de archivos estáticos como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="d4404-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="d4404-126">En el código anterior, la jerarquía del directorio *MyStaticFiles* se expone públicamente a través del segmento de URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="d4404-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="d4404-127">Una solicitud a `https://<hostname>/StaticFiles/images/red-rose.jpg` sirve al archivo *red-rose.jpg*.</span><span class="sxs-lookup"><span data-stu-id="d4404-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="d4404-128">El siguiente marcado hace referencia a *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="d4404-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="d4404-129">Establecer encabezados de respuesta HTTP</span><span class="sxs-lookup"><span data-stu-id="d4404-129">Set HTTP response headers</span></span>

<span data-ttu-id="d4404-130">Se puede usar un objeto <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> para establecer encabezados de respuesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d4404-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="d4404-131">Además de configurar el servicio de archivos estáticos desde la [raíz web](xref:fundamentals/index#web-root), el código siguiente establece el encabezado `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="d4404-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="d4404-132">Los archivos estáticos se almacenan en caché públicamente durante 600 segundos:</span><span class="sxs-lookup"><span data-stu-id="d4404-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Se han agregado encabezados de respuesta que muestran el encabezado Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="d4404-134">Autorización de archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="d4404-134">Static file authorization</span></span>

<span data-ttu-id="d4404-135">El middleware de archivos estáticos no proporciona comprobaciones de autorización.</span><span class="sxs-lookup"><span data-stu-id="d4404-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="d4404-136">Los archivos que proporciona, incluidos los de `wwwroot`, están accesibles de forma pública.</span><span class="sxs-lookup"><span data-stu-id="d4404-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="d4404-137">Para proporcionar archivos según su autorización:</span><span class="sxs-lookup"><span data-stu-id="d4404-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="d4404-138">Almacénelos fuera de `wwwroot` y cualquier directorio al que el middleware de archivos estáticos tenga acceso.</span><span class="sxs-lookup"><span data-stu-id="d4404-138">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="d4404-139">Proporciónelos a través de un método de acción al que se aplica la autorización y devuelva un objeto <xref:Microsoft.AspNetCore.Mvc.FileResult>:</span><span class="sxs-lookup"><span data-stu-id="d4404-139">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="d4404-140">Examen de directorios</span><span class="sxs-lookup"><span data-stu-id="d4404-140">Directory browsing</span></span>

<span data-ttu-id="d4404-141">El examen de directorios permite enumerar directorios dentro de los directorios especificados.</span><span class="sxs-lookup"><span data-stu-id="d4404-141">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="d4404-142">Por motivos de seguridad, el examen de directorios está deshabilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d4404-142">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="d4404-143">Para más información, consulte [Consideraciones](#sc).</span><span class="sxs-lookup"><span data-stu-id="d4404-143">For more information, see [Considerations](#sc).</span></span>

<span data-ttu-id="d4404-144">Habilite el examen de directorios con:</span><span class="sxs-lookup"><span data-stu-id="d4404-144">Enable directory browsing with:</span></span>

* <span data-ttu-id="d4404-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d4404-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="d4404-146"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d4404-146"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="d4404-147">El código anterior permite el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL `https://<hostname>/MyImages`, con vínculos a cada archivo y carpeta:</span><span class="sxs-lookup"><span data-stu-id="d4404-147">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![examen de directorios](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="d4404-149">Suministro de documentos predeterminados</span><span class="sxs-lookup"><span data-stu-id="d4404-149">Serve default documents</span></span>

<span data-ttu-id="d4404-150">El establecimiento de una página predeterminada proporciona a los visitantes un punto inicial en un sitio.</span><span class="sxs-lookup"><span data-stu-id="d4404-150">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="d4404-151">Para proporcionar una página predeterminada desde `wwwroot` sin un URI completo, llame al método <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A>:</span><span class="sxs-lookup"><span data-stu-id="d4404-151">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="d4404-152">Debe llamarse a `UseDefaultFiles` antes de a `UseStaticFiles` para proporcionar el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d4404-152">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="d4404-153">`UseDefaultFiles` es un sistema de reescritura de direcciones URL que no proporciona el archivo.</span><span class="sxs-lookup"><span data-stu-id="d4404-153">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="d4404-154">Con `UseDefaultFiles`, las solicitudes a una carpeta en `wwwroot` buscan:</span><span class="sxs-lookup"><span data-stu-id="d4404-154">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="d4404-155">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="d4404-155">*default.htm*</span></span>
* <span data-ttu-id="d4404-156">*default.html*</span><span class="sxs-lookup"><span data-stu-id="d4404-156">*default.html*</span></span>
* <span data-ttu-id="d4404-157">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="d4404-157">*index.htm*</span></span>
* <span data-ttu-id="d4404-158">*index.html*</span><span class="sxs-lookup"><span data-stu-id="d4404-158">*index.html*</span></span>

<span data-ttu-id="d4404-159">El primer archivo que se encuentra en la lista se proporciona como si la solicitud fuera el URI completo.</span><span class="sxs-lookup"><span data-stu-id="d4404-159">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="d4404-160">La dirección URL del explorador sigue reflejando el URI solicitado.</span><span class="sxs-lookup"><span data-stu-id="d4404-160">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="d4404-161">El código siguiente cambia el nombre de archivo predeterminado a *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="d4404-161">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="d4404-162">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="d4404-162">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="d4404-163">UseFileServer para documentos predeterminados</span><span class="sxs-lookup"><span data-stu-id="d4404-163">UseFileServer for default documents</span></span>

<span data-ttu-id="d4404-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina la funcionalidad de `UseStaticFiles` y `UseDefaultFiles` y, opcionalmente, la de `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="d4404-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="d4404-165">Llame a `app.UseFileServer` para poder proporcionar archivos estáticos y el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d4404-165">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="d4404-166">El examen de directorios no está habilitado.</span><span class="sxs-lookup"><span data-stu-id="d4404-166">Directory browsing isn't enabled.</span></span> <span data-ttu-id="d4404-167">El código siguiente muestra `Startup.Configure` con `UseFileServer`:</span><span class="sxs-lookup"><span data-stu-id="d4404-167">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="d4404-168">El código siguiente permite proporcionar archivos estáticos, el archivo predeterminado y el examen de directorios:</span><span class="sxs-lookup"><span data-stu-id="d4404-168">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="d4404-169">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="d4404-169">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="d4404-170">Tenga en cuenta la siguiente jerarquía de directorios:</span><span class="sxs-lookup"><span data-stu-id="d4404-170">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="d4404-171">El código siguiente permite proporcionar archivos estáticos, el archivo predeterminado y el examen de directorios de `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="d4404-171">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="d4404-172">Se debe llamar a <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> cuando el valor de la propiedad `EnableDirectoryBrowsing` es `true`.</span><span class="sxs-lookup"><span data-stu-id="d4404-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="d4404-173">Al usar la jerarquía de archivos y el código anterior, las direcciones URL se resuelven como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="d4404-173">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="d4404-174">Identificador URI</span><span class="sxs-lookup"><span data-stu-id="d4404-174">URI</span></span>            |      <span data-ttu-id="d4404-175">Respuesta</span><span class="sxs-lookup"><span data-stu-id="d4404-175">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="d4404-176">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="d4404-176">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="d4404-177">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="d4404-177">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="d4404-178">Si no existe ningún archivo con el nombre predeterminado en el directorio *MyStaticFiles*, `https://<hostname>/StaticFiles` devuelve la lista de directorios con vínculos activos:</span><span class="sxs-lookup"><span data-stu-id="d4404-178">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Lista de archivos estáticos](static-files/_static/db2.png)

<span data-ttu-id="d4404-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> y <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> realizan un redireccionamiento del lado cliente desde el URI de destino sin una `/` final hasta el URI de destino una `/` final.</span><span class="sxs-lookup"><span data-stu-id="d4404-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="d4404-181">Por ejemplo, desde `https://<hostname>/StaticFiles` hasta `https://<hostname>/StaticFiles/`.</span><span class="sxs-lookup"><span data-stu-id="d4404-181">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="d4404-182">Las direcciones URL relativas dentro del directorio *StaticFiles* no son válidas sin una barra diagonal final (`/`).</span><span class="sxs-lookup"><span data-stu-id="d4404-182">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="d4404-183">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="d4404-183">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="d4404-184">La clase <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> contiene una propiedad `Mappings` que actúa como una asignación de extensiones de archivo para tipos de contenido MIME.</span><span class="sxs-lookup"><span data-stu-id="d4404-184">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="d4404-185">En el ejemplo siguiente, se asignan varias extensiones de archivo a los tipos MIME conocidos.</span><span class="sxs-lookup"><span data-stu-id="d4404-185">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="d4404-186">Se reemplaza la extensión *.rtf* y se quita *.mp4*:</span><span class="sxs-lookup"><span data-stu-id="d4404-186">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="d4404-187">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="d4404-187">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="d4404-188">Vea [Tipos de contenido MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="d4404-188">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="d4404-189">Tipos de contenido no estándar</span><span class="sxs-lookup"><span data-stu-id="d4404-189">Non-standard content types</span></span>

<span data-ttu-id="d4404-190">El middleware de archivos estáticos entiende casi 400 tipos de contenido de archivo conocidos.</span><span class="sxs-lookup"><span data-stu-id="d4404-190">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="d4404-191">Si el usuario solicita un archivo con un tipo de archivo desconocido, el middleware de archivos estáticos pasa la solicitud al siguiente middleware de la canalización.</span><span class="sxs-lookup"><span data-stu-id="d4404-191">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="d4404-192">Si ningún middleware se ocupa de la solicitud, se devuelve una respuesta *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="d4404-192">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="d4404-193">Si se habilita la exploración de directorios, se muestra un vínculo al archivo en una lista de directorios.</span><span class="sxs-lookup"><span data-stu-id="d4404-193">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="d4404-194">El código siguiente permite proporcionar tipos desconocidos y procesa el archivo desconocido como una imagen:</span><span class="sxs-lookup"><span data-stu-id="d4404-194">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="d4404-195">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="d4404-195">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="d4404-196">Con el código anterior, una solicitud para un archivo con un tipo de contenido desconocido se devuelve como una imagen.</span><span class="sxs-lookup"><span data-stu-id="d4404-196">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="d4404-197">Habilitar <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> supone un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="d4404-197">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="d4404-198">Está deshabilitado de forma predeterminada y no se recomienda su uso.</span><span class="sxs-lookup"><span data-stu-id="d4404-198">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="d4404-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) proporciona una alternativa más segura a ofrecer archivos con extensiones no estándar.</span><span class="sxs-lookup"><span data-stu-id="d4404-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="d4404-200">Entrega de archivos desde varias ubicaciones</span><span class="sxs-lookup"><span data-stu-id="d4404-200">Serve files from multiple locations</span></span>

<span data-ttu-id="d4404-201">`UseStaticFiles` y `UseFileServer` tienen como valor predeterminado el proveedor de archivos que apunta a `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="d4404-201">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="d4404-202">Se pueden proporcionar instancias adicionales de `UseStaticFiles` y `UseFileServer` con otros proveedores de archivos para proporcionar archivos desde otras ubicaciones.</span><span class="sxs-lookup"><span data-stu-id="d4404-202">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="d4404-203">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="d4404-203">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="d4404-204">Consideraciones de seguridad para archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="d4404-204">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="d4404-205">`UseDirectoryBrowser` y `UseStaticFiles` pueden producir pérdidas de información confidencial.</span><span class="sxs-lookup"><span data-stu-id="d4404-205">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="d4404-206">Se recomienda deshabilitar el examen de directorios en producción.</span><span class="sxs-lookup"><span data-stu-id="d4404-206">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="d4404-207">Revise cuidadosamente los directorios que se habilitan mediante `UseStaticFiles` o `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="d4404-207">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="d4404-208">Todo el directorio y sus subdirectorios pasan a ser accesibles públicamente.</span><span class="sxs-lookup"><span data-stu-id="d4404-208">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="d4404-209">Almacene los archivos adecuados para proporcionarlos al público en un directorio dedicado, como `<content_root>/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="d4404-209">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="d4404-210">Separe estos archivos de las vistas MVC, de Razor Pages, de los archivos de configuración, etc.</span><span class="sxs-lookup"><span data-stu-id="d4404-210">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="d4404-211">Las direcciones URL para el contenido que se expone a través de `UseDirectoryBrowser` y `UseStaticFiles` están sujetas a la distinción entre mayúsculas y minúsculas, y a restricciones de caracteres del sistema de archivos subyacente.</span><span class="sxs-lookup"><span data-stu-id="d4404-211">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="d4404-212">Por ejemplo, Windows no distingue entre mayúsculas y minúsculas, pero macOS y Linux sí.</span><span class="sxs-lookup"><span data-stu-id="d4404-212">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="d4404-213">Las aplicaciones de ASP.NET Core hospedadas en IIS usan el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para reenviar todas las solicitudes a la aplicación, incluidas las solicitudes de archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="d4404-213">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="d4404-214">No se utiliza el controlador de archivos estáticos de IIS y no tiene la posibilidad de controlar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d4404-214">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="d4404-215">Complete los pasos siguientes en el Administrador de IIS para quitar el controlador de archivos estáticos de IIS en el nivel de servidor o de sitio web:</span><span class="sxs-lookup"><span data-stu-id="d4404-215">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="d4404-216">Navegue hasta la característica **Módulos**.</span><span class="sxs-lookup"><span data-stu-id="d4404-216">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="d4404-217">En la lista, seleccione **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="d4404-217">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="d4404-218">Haga clic en **Quitar** en la barra lateral **Acciones**.</span><span class="sxs-lookup"><span data-stu-id="d4404-218">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="d4404-219">Si el controlador de archivos estáticos de IIS está habilitado **y** el módulo de ASP.NET Core no está configurado correctamente, se proporcionan archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="d4404-219">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="d4404-220">Esto sucede, por ejemplo, si el archivo *web.config* no está implementado.</span><span class="sxs-lookup"><span data-stu-id="d4404-220">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="d4404-221">Coloque los archivos de código (incluidos *.cs* y *.cshtml*) fuera de la [raíz web](xref:fundamentals/index#web-root) del proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d4404-221">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="d4404-222">Por lo tanto, se crea una separación lógica entre el contenido del lado cliente de la aplicación y el código basado en servidor.</span><span class="sxs-lookup"><span data-stu-id="d4404-222">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="d4404-223">Esto impide que se filtre el código del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="d4404-223">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4404-224">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d4404-224">Additional resources</span></span>

* [<span data-ttu-id="d4404-225">Middleware</span><span class="sxs-lookup"><span data-stu-id="d4404-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="d4404-226">Introducción a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4404-226">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d4404-227">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d4404-227">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d4404-228">Los archivos estáticos, como HTML, CSS, imágenes y JavaScript, son activos que una aplicación de ASP.NET Core proporciona directamente a los clientes.</span><span class="sxs-lookup"><span data-stu-id="d4404-228">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="d4404-229">Se necesita alguna configuración para habilitar el servicio de estos archivos.</span><span class="sxs-lookup"><span data-stu-id="d4404-229">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="d4404-230">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d4404-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="d4404-231">Proporcionar archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="d4404-231">Serve static files</span></span>

<span data-ttu-id="d4404-232">Los archivos estáticos se almacenan en el directorio [raíz web](xref:fundamentals/index#web-root) del proyecto.</span><span class="sxs-lookup"><span data-stu-id="d4404-232">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="d4404-233">El directorio predeterminado es *{raíz del contenido}/wwwroot*, pero se puede cambiar con el método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>.</span><span class="sxs-lookup"><span data-stu-id="d4404-233">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="d4404-234">Vea [Raíz del contenido](xref:fundamentals/index#content-root) y [Raíz web](xref:fundamentals/index#web-root) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="d4404-234">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="d4404-235">El host de web de la aplicación debe tener conocimiento del directorio raíz del contenido.</span><span class="sxs-lookup"><span data-stu-id="d4404-235">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="d4404-236">El método `WebHost.CreateDefaultBuilder` establece la raíz de contenido en el directorio actual:</span><span class="sxs-lookup"><span data-stu-id="d4404-236">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="d4404-237">Se puede acceder a los archivos estáticos a través de una ruta de acceso relativa a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d4404-237">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="d4404-238">Por ejemplo, la plantilla de proyecto **Aplicación web** contiene varias carpetas dentro de la carpeta `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="d4404-238">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="d4404-239">El formato de URI para acceder a un archivo en la subcarpeta *images* es *http://\<server_address>/images/\<image_file_name>* .</span><span class="sxs-lookup"><span data-stu-id="d4404-239">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="d4404-240">Por ejemplo, *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="d4404-240">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="d4404-241">Si el destino es .NET Framework, agregue el paquete [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="d4404-241">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="d4404-242">Si el destino es .NET Core, el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluye este paquete.</span><span class="sxs-lookup"><span data-stu-id="d4404-242">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="d4404-243">Configure el [middleware](xref:fundamentals/middleware/index), que permite proporcionar archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="d4404-243">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="d4404-244">Proporcionar archivos dentro de la raíz web</span><span class="sxs-lookup"><span data-stu-id="d4404-244">Serve files inside of web root</span></span>

<span data-ttu-id="d4404-245">Invoque al método <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d4404-245">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="d4404-246">La sobrecarga del método `UseStaticFiles` sin parámetros marca los archivos en la [raíz web](xref:fundamentals/index#web-root) como que se pueden proporcionar.</span><span class="sxs-lookup"><span data-stu-id="d4404-246">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="d4404-247">El siguiente marcado hace referencia a *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="d4404-247">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="d4404-248">En el código anterior, el carácter de tilde de la ñ `~/` apunta a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d4404-248">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="d4404-249">Proporcionar archivos fuera de la raíz web</span><span class="sxs-lookup"><span data-stu-id="d4404-249">Serve files outside of web root</span></span>

<span data-ttu-id="d4404-250">Considere una jerarquía de directorios en la que residen fuera de la [raíz web](xref:fundamentals/index#web-root) los archivos estáticos que se van a proporcionar:</span><span class="sxs-lookup"><span data-stu-id="d4404-250">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="d4404-251">Una solicitud puede acceder al archivo *banner1.svg* configurando el middleware de archivos estáticos como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="d4404-251">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="d4404-252">En el código anterior, la jerarquía del directorio *MyStaticFiles* se expone públicamente a través del segmento de URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="d4404-252">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="d4404-253">Una solicitud a *http://\<server_address>/StaticFiles/images/banner1.svg* proporciona el archivo *banner1.svg*.</span><span class="sxs-lookup"><span data-stu-id="d4404-253">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="d4404-254">El siguiente marcado hace referencia a *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="d4404-254">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="d4404-255">Establecer encabezados de respuesta HTTP</span><span class="sxs-lookup"><span data-stu-id="d4404-255">Set HTTP response headers</span></span>

<span data-ttu-id="d4404-256">Se puede usar un objeto <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> para establecer encabezados de respuesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d4404-256">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="d4404-257">Además de configurar el servicio de archivos estáticos desde la [raíz web](xref:fundamentals/index#web-root), el código siguiente establece el encabezado `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="d4404-257">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="d4404-258">El método <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> existe en el paquete [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="d4404-258">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="d4404-259">Los archivos se han hecho públicamente almacenables en caché durante 10 minutos (600 segundos) en el entorno de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="d4404-259">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Se han agregado encabezados de respuesta que muestran el encabezado Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="d4404-261">Autorización de archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="d4404-261">Static file authorization</span></span>

<span data-ttu-id="d4404-262">El middleware de archivos estáticos no proporciona comprobaciones de autorización.</span><span class="sxs-lookup"><span data-stu-id="d4404-262">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="d4404-263">Los archivos que proporciona, incluidos los de *wwwroot*, están accesibles de forma pública.</span><span class="sxs-lookup"><span data-stu-id="d4404-263">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="d4404-264">Para proporcionar archivos según su autorización:</span><span class="sxs-lookup"><span data-stu-id="d4404-264">To serve files based on authorization:</span></span>

* <span data-ttu-id="d4404-265">Almacénelos fuera de *wwwroot* y cualquier directorio al que el middleware de archivos estáticos tenga acceso.</span><span class="sxs-lookup"><span data-stu-id="d4404-265">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="d4404-266">Proporciónelos a través de un método de acción al que se aplica la autorización.</span><span class="sxs-lookup"><span data-stu-id="d4404-266">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="d4404-267">Devuelva un objeto <xref:Microsoft.AspNetCore.Mvc.FileResult>:</span><span class="sxs-lookup"><span data-stu-id="d4404-267">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="d4404-268">Habilite el examen de directorios</span><span class="sxs-lookup"><span data-stu-id="d4404-268">Enable directory browsing</span></span>

<span data-ttu-id="d4404-269">El examen de directorios permite a los usuarios de su aplicación web ver una lista de directorios y archivos contenidos en un directorio especificado.</span><span class="sxs-lookup"><span data-stu-id="d4404-269">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="d4404-270">Por motivos de seguridad, el examen de directorios está deshabilitado de forma predeterminada (consulte [Consideraciones](#sc)).</span><span class="sxs-lookup"><span data-stu-id="d4404-270">Directory browsing is disabled by default for security reasons (see [Considerations](#sc)).</span></span> <span data-ttu-id="d4404-271">Habilite el examen de directorios invocando el método <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d4404-271">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="d4404-272">Agregar servicios requeridos invocando el método <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> desde `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d4404-272">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="d4404-273">El código anterior permite el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL *http://\<server_address>/MyImages*, con vínculos a cada archivo y carpeta:</span><span class="sxs-lookup"><span data-stu-id="d4404-273">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![examen de directorios](static-files/_static/dir-browse.png)

<span data-ttu-id="d4404-275">Vea [consideraciones](#considerations) sobre los riesgos de seguridad al habilitar el examen.</span><span class="sxs-lookup"><span data-stu-id="d4404-275">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="d4404-276">Tenga en cuenta las dos llamadas a `UseStaticFiles` en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="d4404-276">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="d4404-277">La primera llamada permite proporcionar archivos estáticos en la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="d4404-277">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="d4404-278">La segunda llamada habilita el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL *http://\<server_address>/MyImages*:</span><span class="sxs-lookup"><span data-stu-id="d4404-278">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="d4404-279">Proporcionar un documento predeterminado</span><span class="sxs-lookup"><span data-stu-id="d4404-279">Serve a default document</span></span>

<span data-ttu-id="d4404-280">Establecer una página principal predeterminada proporciona a los visitantes un punto de partida lógico cuando visitan su sitio.</span><span class="sxs-lookup"><span data-stu-id="d4404-280">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="d4404-281">Para proporcionar una página predeterminada sin que el usuario cumpla por completo los requisitos del URI, llame al método <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> desde `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d4404-281">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="d4404-282">Debe llamarse a `UseDefaultFiles` antes de a `UseStaticFiles` para proporcionar el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d4404-282">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="d4404-283">`UseDefaultFiles` es un sistema de reescritura de direcciones URL que no proporciona realmente el archivo.</span><span class="sxs-lookup"><span data-stu-id="d4404-283">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="d4404-284">Habilite el middleware de archivos estáticos a través de `UseStaticFiles` para proporcionar el archivo.</span><span class="sxs-lookup"><span data-stu-id="d4404-284">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="d4404-285">Con `UseDefaultFiles`, las solicitudes a una carpeta buscan:</span><span class="sxs-lookup"><span data-stu-id="d4404-285">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="d4404-286">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="d4404-286">*default.htm*</span></span>
* <span data-ttu-id="d4404-287">*default.html*</span><span class="sxs-lookup"><span data-stu-id="d4404-287">*default.html*</span></span>
* <span data-ttu-id="d4404-288">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="d4404-288">*index.htm*</span></span>
* <span data-ttu-id="d4404-289">*index.html*</span><span class="sxs-lookup"><span data-stu-id="d4404-289">*index.html*</span></span>

<span data-ttu-id="d4404-290">El primer archivo que se encuentra en la lista se proporciona como si la solicitud fuera el URI completo.</span><span class="sxs-lookup"><span data-stu-id="d4404-290">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="d4404-291">La dirección URL del explorador sigue reflejando el URI solicitado.</span><span class="sxs-lookup"><span data-stu-id="d4404-291">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="d4404-292">El código siguiente cambia el nombre de archivo predeterminado a *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="d4404-292">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="d4404-293">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="d4404-293">UseFileServer</span></span>

<span data-ttu-id="d4404-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina la funcionalidad de `UseStaticFiles` y `UseDefaultFiles` y, opcionalmente, la de `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="d4404-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="d4404-295">El código siguiente permite proporcionar archivos estáticos y el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d4404-295">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="d4404-296">El examen de directorios no está habilitado.</span><span class="sxs-lookup"><span data-stu-id="d4404-296">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="d4404-297">El código siguiente refuerza la sobrecarga sin parámetros habilitando el examen de directorios:</span><span class="sxs-lookup"><span data-stu-id="d4404-297">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="d4404-298">Tenga en cuenta la siguiente jerarquía de directorios:</span><span class="sxs-lookup"><span data-stu-id="d4404-298">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="d4404-299">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="d4404-299">**wwwroot**</span></span>
  * <span data-ttu-id="d4404-300">**css**</span><span class="sxs-lookup"><span data-stu-id="d4404-300">**css**</span></span>
  * <span data-ttu-id="d4404-301">**images**</span><span class="sxs-lookup"><span data-stu-id="d4404-301">**images**</span></span>
  * <span data-ttu-id="d4404-302">**js**</span><span class="sxs-lookup"><span data-stu-id="d4404-302">**js**</span></span>
* <span data-ttu-id="d4404-303">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="d4404-303">**MyStaticFiles**</span></span>
  * <span data-ttu-id="d4404-304">**images**</span><span class="sxs-lookup"><span data-stu-id="d4404-304">**images**</span></span>
    * <span data-ttu-id="d4404-305">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="d4404-305">*banner1.svg*</span></span>
  * <span data-ttu-id="d4404-306">*default.html*</span><span class="sxs-lookup"><span data-stu-id="d4404-306">*default.html*</span></span>

<span data-ttu-id="d4404-307">El código siguiente permite los archivos estáticos, los archivos predeterminados y el examen de directorios de `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="d4404-307">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="d4404-308">Se debe llamar a `AddDirectoryBrowser` cuando el valor de la propiedad `EnableDirectoryBrowsing` es `true`:</span><span class="sxs-lookup"><span data-stu-id="d4404-308">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="d4404-309">Al usar la jerarquía de archivos y el código anterior, las direcciones URL se resuelven como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="d4404-309">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="d4404-310">Identificador URI</span><span class="sxs-lookup"><span data-stu-id="d4404-310">URI</span></span>            |                             <span data-ttu-id="d4404-311">Respuesta</span><span class="sxs-lookup"><span data-stu-id="d4404-311">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="d4404-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="d4404-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="d4404-313">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="d4404-313">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="d4404-314">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="d4404-314">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="d4404-315">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="d4404-315">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="d4404-316">Si no existe ningún archivo con el nombre predeterminado en el directorio *MyStaticFiles*, *http://\<server_address>/StaticFiles* devuelve la lista de directorios con vínculos activos:</span><span class="sxs-lookup"><span data-stu-id="d4404-316">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Lista de archivos estáticos](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="d4404-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> y <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> realizan un redireccionamiento del lado cliente desde `http://{SERVER ADDRESS}/StaticFiles` (sin una barra diagonal final) hasta `http://{SERVER ADDRESS}/StaticFiles/` (con una barra diagonal final).</span><span class="sxs-lookup"><span data-stu-id="d4404-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="d4404-319">Las direcciones URL relativas dentro del directorio *StaticFiles* no son válidas sin una barra diagonal final.</span><span class="sxs-lookup"><span data-stu-id="d4404-319">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="d4404-320">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="d4404-320">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="d4404-321">La clase <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> contiene una propiedad `Mappings` que actúa como una asignación de extensiones de archivo para tipos de contenido MIME.</span><span class="sxs-lookup"><span data-stu-id="d4404-321">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="d4404-322">En el ejemplo siguiente, se registran varias extensiones de archivo a los tipos MIME conocidos.</span><span class="sxs-lookup"><span data-stu-id="d4404-322">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="d4404-323">Se reemplaza la extensión *.rtf* y se quita *.mp4*.</span><span class="sxs-lookup"><span data-stu-id="d4404-323">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="d4404-324">Vea [Tipos de contenido MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="d4404-324">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="d4404-325">Tipos de contenido no estándar</span><span class="sxs-lookup"><span data-stu-id="d4404-325">Non-standard content types</span></span>

<span data-ttu-id="d4404-326">El middleware de archivos estáticos entiende casi 400 tipos de contenido de archivo conocidos.</span><span class="sxs-lookup"><span data-stu-id="d4404-326">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="d4404-327">Si el usuario solicita un archivo con un tipo de archivo desconocido, el middleware de archivos estáticos pasa la solicitud al siguiente middleware de la canalización.</span><span class="sxs-lookup"><span data-stu-id="d4404-327">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="d4404-328">Si ningún middleware se ocupa de la solicitud, se devuelve una respuesta *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="d4404-328">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="d4404-329">Si se habilita la exploración de directorios, se muestra un vínculo al archivo en una lista de directorios.</span><span class="sxs-lookup"><span data-stu-id="d4404-329">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="d4404-330">El código siguiente permite proporcionar tipos desconocidos y procesa el archivo desconocido como una imagen:</span><span class="sxs-lookup"><span data-stu-id="d4404-330">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="d4404-331">Con el código anterior, una solicitud para un archivo con un tipo de contenido desconocido se devuelve como una imagen.</span><span class="sxs-lookup"><span data-stu-id="d4404-331">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="d4404-332">Habilitar <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> supone un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="d4404-332">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="d4404-333">Está deshabilitado de forma predeterminada y no se recomienda su uso.</span><span class="sxs-lookup"><span data-stu-id="d4404-333">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="d4404-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) proporciona una alternativa más segura a ofrecer archivos con extensiones no estándar.</span><span class="sxs-lookup"><span data-stu-id="d4404-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="d4404-335">Entrega de archivos desde varias ubicaciones</span><span class="sxs-lookup"><span data-stu-id="d4404-335">Serve files from multiple locations</span></span>

<span data-ttu-id="d4404-336">`UseStaticFiles` y `UseFileServer` tienen como valor predeterminado el proveedor de archivos que apunta a *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="d4404-336">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="d4404-337">Puede proporcionar instancias adicionales de `UseStaticFiles` y `UseFileServer` con otros proveedores de archivos para proporcionar archivos desde otras ubicaciones.</span><span class="sxs-lookup"><span data-stu-id="d4404-337">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="d4404-338">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="d4404-338">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="d4404-339">Consideraciones</span><span class="sxs-lookup"><span data-stu-id="d4404-339">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="d4404-340">`UseDirectoryBrowser` y `UseStaticFiles` pueden producir pérdidas de información confidencial.</span><span class="sxs-lookup"><span data-stu-id="d4404-340">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="d4404-341">Se recomienda deshabilitar el examen de directorios en producción.</span><span class="sxs-lookup"><span data-stu-id="d4404-341">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="d4404-342">Revise cuidadosamente los directorios que se habilitan mediante `UseStaticFiles` o `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="d4404-342">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="d4404-343">Todo el directorio y sus subdirectorios pasan a ser accesibles públicamente.</span><span class="sxs-lookup"><span data-stu-id="d4404-343">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="d4404-344">Almacene los archivos adecuados para proporcionarlos al público en un directorio dedicado, como *\<content_root>/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="d4404-344">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="d4404-345">Separe estos archivos de las vistas MVC, las instancias de Razor Pages (solo 2.x), los archivos de configuración, etc.</span><span class="sxs-lookup"><span data-stu-id="d4404-345">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="d4404-346">Las direcciones URL para el contenido que se expone a través de `UseDirectoryBrowser` y `UseStaticFiles` están sujetas a la distinción entre mayúsculas y minúsculas, y a restricciones de caracteres del sistema de archivos subyacente.</span><span class="sxs-lookup"><span data-stu-id="d4404-346">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="d4404-347">Por ejemplo, Windows no distingue entre mayúsculas y minúsculas, pero macOS y Linux sí.</span><span class="sxs-lookup"><span data-stu-id="d4404-347">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="d4404-348">Las aplicaciones de ASP.NET Core hospedadas en IIS usan el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para reenviar todas las solicitudes a la aplicación, incluidas las solicitudes de archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="d4404-348">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="d4404-349">No se usa el controlador de archivos estáticos de IIS.</span><span class="sxs-lookup"><span data-stu-id="d4404-349">The IIS static file handler isn't used.</span></span> <span data-ttu-id="d4404-350">No tiene ninguna posibilidad de controlar las solicitudes antes de que las controle el módulo.</span><span class="sxs-lookup"><span data-stu-id="d4404-350">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="d4404-351">Complete los pasos siguientes en el Administrador de IIS para quitar el controlador de archivos estáticos de IIS en el nivel de servidor o de sitio web:</span><span class="sxs-lookup"><span data-stu-id="d4404-351">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="d4404-352">Navegue hasta la característica **Módulos**.</span><span class="sxs-lookup"><span data-stu-id="d4404-352">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="d4404-353">En la lista, seleccione **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="d4404-353">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="d4404-354">Haga clic en **Quitar** en la barra lateral **Acciones**.</span><span class="sxs-lookup"><span data-stu-id="d4404-354">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="d4404-355">Si el controlador de archivos estáticos de IIS está habilitado **y** el módulo de ASP.NET Core no está configurado correctamente, se proporcionan archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="d4404-355">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="d4404-356">Esto sucede, por ejemplo, si el archivo *web.config* no está implementado.</span><span class="sxs-lookup"><span data-stu-id="d4404-356">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="d4404-357">Coloque los archivos de código (incluidos *.cs* y *.cshtml*) fuera de la [raíz web](xref:fundamentals/index#web-root) del proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d4404-357">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="d4404-358">Por lo tanto, se crea una separación lógica entre el contenido del lado cliente de la aplicación y el código basado en servidor.</span><span class="sxs-lookup"><span data-stu-id="d4404-358">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="d4404-359">Esto impide que se filtre el código del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="d4404-359">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4404-360">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d4404-360">Additional resources</span></span>

* [<span data-ttu-id="d4404-361">Middleware</span><span class="sxs-lookup"><span data-stu-id="d4404-361">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="d4404-362">Introducción a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4404-362">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
