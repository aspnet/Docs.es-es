---
title: Archivos estáticos en ASP.NET Core
author: rick-anderson
description: Aprenda a proporcionar y proteger los archivos estáticos y a configurar los comportamientos de middleware de hospedaje de archivos estáticos en una aplicación web de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 24fda96dbe48945d172eb36e8a91af31f168ff7f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627668"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="2d9be-103">Archivos estáticos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2d9be-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2d9be-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="2d9be-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="2d9be-105">Los archivos estáticos, como HTML, CSS, imágenes y JavaScript, son activos que una aplicación de ASP.NET Core proporciona directamente a los clientes de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2d9be-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="2d9be-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2d9be-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="2d9be-107">Proporcionar archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="2d9be-107">Serve static files</span></span>

<span data-ttu-id="2d9be-108">Los archivos estáticos se almacenan en el directorio [raíz web](xref:fundamentals/index#web-root) del proyecto.</span><span class="sxs-lookup"><span data-stu-id="2d9be-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="2d9be-109">El directorio predeterminado es `{content root}/wwwroot`, pero se puede cambiar con el método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>.</span><span class="sxs-lookup"><span data-stu-id="2d9be-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="2d9be-110">Para más información, vea [Raíz del contenido](xref:fundamentals/index#content-root) y [Raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="2d9be-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="2d9be-111">El método <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> establece la raíz de contenido en el directorio actual:</span><span class="sxs-lookup"><span data-stu-id="2d9be-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="2d9be-112">El código anterior se creó con la plantilla de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="2d9be-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="2d9be-113">Se puede acceder a los archivos estáticos a través de una ruta de acceso relativa a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="2d9be-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="2d9be-114">Por ejemplo, las plantillas de proyecto **Aplicación web** contienen varias carpetas dentro de la carpeta `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="2d9be-115">Considere la posibilidad de crear la carpeta *wwwroot/images* y agregar el archivo *wwwroot/images/MyImage.jpg*.</span><span class="sxs-lookup"><span data-stu-id="2d9be-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="2d9be-116">El formato del URI para acceder a un archivo en la carpeta `images` es `https://<hostname>/images/<image_file_name>`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="2d9be-117">Por ejemplo, `https://localhost:5001/images/MyImage.jpg`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="2d9be-118">Suministro de archivos en la raíz web</span><span class="sxs-lookup"><span data-stu-id="2d9be-118">Serve files in web root</span></span>

<span data-ttu-id="2d9be-119">Las plantillas de aplicación web predeterminadas llaman al método <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure`, que permite proporcionar archivos estáticos:</span><span class="sxs-lookup"><span data-stu-id="2d9be-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="2d9be-120">La sobrecarga del método `UseStaticFiles` sin parámetros marca los archivos en la [raíz web](xref:fundamentals/index#web-root) como que se pueden proporcionar.</span><span class="sxs-lookup"><span data-stu-id="2d9be-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="2d9be-121">El siguiente marcado hace referencia a *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="2d9be-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="2d9be-122">En el código anterior, el carácter de tilde de la ñ `~/` apunta a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="2d9be-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="2d9be-123">Proporcionar archivos fuera de la raíz web</span><span class="sxs-lookup"><span data-stu-id="2d9be-123">Serve files outside of web root</span></span>

<span data-ttu-id="2d9be-124">Considere una jerarquía de directorios en la que residen fuera de la [raíz web](xref:fundamentals/index#web-root) los archivos estáticos que se van a proporcionar:</span><span class="sxs-lookup"><span data-stu-id="2d9be-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="2d9be-125">Una solicitud puede acceder al archivo `red-rose.jpg` configurando el middleware de archivos estáticos como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="2d9be-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="2d9be-126">En el código anterior, la jerarquía del directorio *MyStaticFiles* se expone públicamente a través del segmento de URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="2d9be-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="2d9be-127">Una solicitud a `https://<hostname>/StaticFiles/images/red-rose.jpg` sirve al archivo *red-rose.jpg*.</span><span class="sxs-lookup"><span data-stu-id="2d9be-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="2d9be-128">El siguiente marcado hace referencia a *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="2d9be-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="2d9be-129">Establecer encabezados de respuesta HTTP</span><span class="sxs-lookup"><span data-stu-id="2d9be-129">Set HTTP response headers</span></span>

<span data-ttu-id="2d9be-130">Se puede usar un objeto <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> para establecer encabezados de respuesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2d9be-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="2d9be-131">Además de configurar el servicio de archivos estáticos desde la [raíz web](xref:fundamentals/index#web-root), el código siguiente establece el encabezado `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="2d9be-132">Los archivos estáticos se almacenan en caché públicamente durante 600 segundos:</span><span class="sxs-lookup"><span data-stu-id="2d9be-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Se han agregado encabezados de respuesta que muestran el encabezado Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="2d9be-134">Autorización de archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="2d9be-134">Static file authorization</span></span>

<span data-ttu-id="2d9be-135">El middleware de archivos estáticos no proporciona comprobaciones de autorización.</span><span class="sxs-lookup"><span data-stu-id="2d9be-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="2d9be-136">Los archivos que proporciona, incluidos los de `wwwroot`, están accesibles de forma pública.</span><span class="sxs-lookup"><span data-stu-id="2d9be-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="2d9be-137">Para proporcionar archivos según su autorización:</span><span class="sxs-lookup"><span data-stu-id="2d9be-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="2d9be-138">Almacénelos fuera de `wwwroot` y cualquier directorio al que el middleware de archivos estáticos predeterminado tenga acceso.</span><span class="sxs-lookup"><span data-stu-id="2d9be-138">Store them outside of `wwwroot` and any directory accessible to the default Static File Middleware.</span></span>
* <span data-ttu-id="2d9be-139">Llame a `UseStaticFiles` después de `UseAuthorization` y especifique la ruta de acceso:</span><span class="sxs-lookup"><span data-stu-id="2d9be-139">Call `UseStaticFiles` after `UseAuthorization` and specify the path:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  <span data-ttu-id="2d9be-140">Para el enfoque anterior se necesita la autenticación de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="2d9be-140">The preceding approach requires users to be authenticated:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="2d9be-141">Un enfoque alternativo consiste en proporcionar archivos según la autorización:</span><span class="sxs-lookup"><span data-stu-id="2d9be-141">An alternative approach to serve files based on authorization:</span></span>

* <span data-ttu-id="2d9be-142">Almacénelos fuera de `wwwroot` y cualquier directorio al que el middleware de archivos estáticos tenga acceso.</span><span class="sxs-lookup"><span data-stu-id="2d9be-142">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="2d9be-143">Proporciónelos a través de un método de acción al que se aplica la autorización y devuelva un objeto <xref:Microsoft.AspNetCore.Mvc.FileResult>:</span><span class="sxs-lookup"><span data-stu-id="2d9be-143">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="2d9be-144">Examen de directorios</span><span class="sxs-lookup"><span data-stu-id="2d9be-144">Directory browsing</span></span>

<span data-ttu-id="2d9be-145">El examen de directorios permite enumerar directorios dentro de los directorios especificados.</span><span class="sxs-lookup"><span data-stu-id="2d9be-145">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="2d9be-146">Por motivos de seguridad, el examen de directorios está deshabilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2d9be-146">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="2d9be-147">Para más información, consulte [Consideraciones](#sc).</span><span class="sxs-lookup"><span data-stu-id="2d9be-147">For more information, see [Considerations](#sc).</span></span>

<span data-ttu-id="2d9be-148">Habilite el examen de directorios con:</span><span class="sxs-lookup"><span data-stu-id="2d9be-148">Enable directory browsing with:</span></span>

* <span data-ttu-id="2d9be-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="2d9be-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="2d9be-151">El código anterior permite el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL `https://<hostname>/MyImages`, con vínculos a cada archivo y carpeta:</span><span class="sxs-lookup"><span data-stu-id="2d9be-151">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![examen de directorios](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="2d9be-153">Suministro de documentos predeterminados</span><span class="sxs-lookup"><span data-stu-id="2d9be-153">Serve default documents</span></span>

<span data-ttu-id="2d9be-154">El establecimiento de una página predeterminada proporciona a los visitantes un punto inicial en un sitio.</span><span class="sxs-lookup"><span data-stu-id="2d9be-154">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="2d9be-155">Para proporcionar una página predeterminada desde `wwwroot` sin un URI completo, llame al método <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A>:</span><span class="sxs-lookup"><span data-stu-id="2d9be-155">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="2d9be-156">Debe llamarse a `UseDefaultFiles` antes de a `UseStaticFiles` para proporcionar el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="2d9be-156">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="2d9be-157">`UseDefaultFiles` es un sistema de reescritura de direcciones URL que no proporciona el archivo.</span><span class="sxs-lookup"><span data-stu-id="2d9be-157">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="2d9be-158">Con `UseDefaultFiles`, las solicitudes a una carpeta en `wwwroot` buscan:</span><span class="sxs-lookup"><span data-stu-id="2d9be-158">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="2d9be-159">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="2d9be-159">*default.htm*</span></span>
* <span data-ttu-id="2d9be-160">*default.html*</span><span class="sxs-lookup"><span data-stu-id="2d9be-160">*default.html*</span></span>
* <span data-ttu-id="2d9be-161">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="2d9be-161">*index.htm*</span></span>
* <span data-ttu-id="2d9be-162">*index.html*</span><span class="sxs-lookup"><span data-stu-id="2d9be-162">*index.html*</span></span>

<span data-ttu-id="2d9be-163">El primer archivo que se encuentra en la lista se proporciona como si la solicitud fuera el URI completo.</span><span class="sxs-lookup"><span data-stu-id="2d9be-163">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="2d9be-164">La dirección URL del explorador sigue reflejando el URI solicitado.</span><span class="sxs-lookup"><span data-stu-id="2d9be-164">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="2d9be-165">El código siguiente cambia el nombre de archivo predeterminado a *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="2d9be-165">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="2d9be-166">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="2d9be-166">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="2d9be-167">UseFileServer para documentos predeterminados</span><span class="sxs-lookup"><span data-stu-id="2d9be-167">UseFileServer for default documents</span></span>

<span data-ttu-id="2d9be-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina la funcionalidad de `UseStaticFiles` y `UseDefaultFiles` y, opcionalmente, la de `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="2d9be-169">Llame a `app.UseFileServer` para poder proporcionar archivos estáticos y el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="2d9be-169">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="2d9be-170">El examen de directorios no está habilitado.</span><span class="sxs-lookup"><span data-stu-id="2d9be-170">Directory browsing isn't enabled.</span></span> <span data-ttu-id="2d9be-171">El código siguiente muestra `Startup.Configure` con `UseFileServer`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-171">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="2d9be-172">El código siguiente permite proporcionar archivos estáticos, el archivo predeterminado y el examen de directorios:</span><span class="sxs-lookup"><span data-stu-id="2d9be-172">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="2d9be-173">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="2d9be-173">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="2d9be-174">Tenga en cuenta la siguiente jerarquía de directorios:</span><span class="sxs-lookup"><span data-stu-id="2d9be-174">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="2d9be-175">El código siguiente permite proporcionar archivos estáticos, el archivo predeterminado y el examen de directorios de `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-175">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="2d9be-176">Se debe llamar a <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> cuando el valor de la propiedad `EnableDirectoryBrowsing` es `true`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="2d9be-177">Al usar la jerarquía de archivos y el código anterior, las direcciones URL se resuelven como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="2d9be-177">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="2d9be-178">Identificador URI</span><span class="sxs-lookup"><span data-stu-id="2d9be-178">URI</span></span>            |      <span data-ttu-id="2d9be-179">Respuesta</span><span class="sxs-lookup"><span data-stu-id="2d9be-179">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="2d9be-180">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="2d9be-180">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="2d9be-181">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="2d9be-181">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="2d9be-182">Si no existe ningún archivo con el nombre predeterminado en el directorio *MyStaticFiles*, `https://<hostname>/StaticFiles` devuelve la lista de directorios con vínculos activos:</span><span class="sxs-lookup"><span data-stu-id="2d9be-182">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Lista de archivos estáticos](static-files/_static/db2.png)

<span data-ttu-id="2d9be-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> y <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> realizan un redireccionamiento del lado cliente desde el URI de destino sin una `/` final hasta el URI de destino una `/` final.</span><span class="sxs-lookup"><span data-stu-id="2d9be-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="2d9be-185">Por ejemplo, desde `https://<hostname>/StaticFiles` hasta `https://<hostname>/StaticFiles/`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-185">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="2d9be-186">Las direcciones URL relativas dentro del directorio *StaticFiles* no son válidas sin una barra diagonal final (`/`).</span><span class="sxs-lookup"><span data-stu-id="2d9be-186">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="2d9be-187">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="2d9be-187">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="2d9be-188">La clase <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> contiene una propiedad `Mappings` que actúa como una asignación de extensiones de archivo para tipos de contenido MIME.</span><span class="sxs-lookup"><span data-stu-id="2d9be-188">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="2d9be-189">En el ejemplo siguiente, se asignan varias extensiones de archivo a los tipos MIME conocidos.</span><span class="sxs-lookup"><span data-stu-id="2d9be-189">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="2d9be-190">Se reemplaza la extensión *.rtf* y se quita *.mp4*:</span><span class="sxs-lookup"><span data-stu-id="2d9be-190">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="2d9be-191">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="2d9be-191">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="2d9be-192">Vea [Tipos de contenido MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="2d9be-192">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="2d9be-193">Tipos de contenido no estándar</span><span class="sxs-lookup"><span data-stu-id="2d9be-193">Non-standard content types</span></span>

<span data-ttu-id="2d9be-194">El middleware de archivos estáticos entiende casi 400 tipos de contenido de archivo conocidos.</span><span class="sxs-lookup"><span data-stu-id="2d9be-194">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="2d9be-195">Si el usuario solicita un archivo con un tipo de archivo desconocido, el middleware de archivos estáticos pasa la solicitud al siguiente middleware de la canalización.</span><span class="sxs-lookup"><span data-stu-id="2d9be-195">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="2d9be-196">Si ningún middleware se ocupa de la solicitud, se devuelve una respuesta *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="2d9be-196">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="2d9be-197">Si se habilita la exploración de directorios, se muestra un vínculo al archivo en una lista de directorios.</span><span class="sxs-lookup"><span data-stu-id="2d9be-197">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="2d9be-198">El código siguiente permite proporcionar tipos desconocidos y procesa el archivo desconocido como una imagen:</span><span class="sxs-lookup"><span data-stu-id="2d9be-198">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="2d9be-199">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="2d9be-199">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="2d9be-200">Con el código anterior, una solicitud para un archivo con un tipo de contenido desconocido se devuelve como una imagen.</span><span class="sxs-lookup"><span data-stu-id="2d9be-200">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="2d9be-201">Habilitar <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> supone un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="2d9be-201">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="2d9be-202">Está deshabilitado de forma predeterminada y no se recomienda su uso.</span><span class="sxs-lookup"><span data-stu-id="2d9be-202">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="2d9be-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) proporciona una alternativa más segura a ofrecer archivos con extensiones no estándar.</span><span class="sxs-lookup"><span data-stu-id="2d9be-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="2d9be-204">Entrega de archivos desde varias ubicaciones</span><span class="sxs-lookup"><span data-stu-id="2d9be-204">Serve files from multiple locations</span></span>

<span data-ttu-id="2d9be-205">`UseStaticFiles` y `UseFileServer` tienen como valor predeterminado el proveedor de archivos que apunta a `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-205">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="2d9be-206">Se pueden proporcionar instancias adicionales de `UseStaticFiles` y `UseFileServer` con otros proveedores de archivos para proporcionar archivos desde otras ubicaciones.</span><span class="sxs-lookup"><span data-stu-id="2d9be-206">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="2d9be-207">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="2d9be-207">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="2d9be-208">Consideraciones de seguridad para archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="2d9be-208">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="2d9be-209">`UseDirectoryBrowser` y `UseStaticFiles` pueden producir pérdidas de información confidencial.</span><span class="sxs-lookup"><span data-stu-id="2d9be-209">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="2d9be-210">Se recomienda deshabilitar el examen de directorios en producción.</span><span class="sxs-lookup"><span data-stu-id="2d9be-210">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="2d9be-211">Revise cuidadosamente los directorios que se habilitan mediante `UseStaticFiles` o `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-211">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="2d9be-212">Todo el directorio y sus subdirectorios pasan a ser accesibles públicamente.</span><span class="sxs-lookup"><span data-stu-id="2d9be-212">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="2d9be-213">Almacene los archivos adecuados para proporcionarlos al público en un directorio dedicado, como `<content_root>/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-213">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="2d9be-214">Separe estos archivos de las vistas MVC, de Razor Pages, de los archivos de configuración, etc.</span><span class="sxs-lookup"><span data-stu-id="2d9be-214">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="2d9be-215">Las direcciones URL para el contenido que se expone a través de `UseDirectoryBrowser` y `UseStaticFiles` están sujetas a la distinción entre mayúsculas y minúsculas, y a restricciones de caracteres del sistema de archivos subyacente.</span><span class="sxs-lookup"><span data-stu-id="2d9be-215">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="2d9be-216">Por ejemplo, Windows no distingue entre mayúsculas y minúsculas, pero macOS y Linux sí.</span><span class="sxs-lookup"><span data-stu-id="2d9be-216">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="2d9be-217">Las aplicaciones de ASP.NET Core hospedadas en IIS usan el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para reenviar todas las solicitudes a la aplicación, incluidas las solicitudes de archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="2d9be-217">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="2d9be-218">No se utiliza el controlador de archivos estáticos de IIS y no tiene la posibilidad de controlar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="2d9be-218">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="2d9be-219">Complete los pasos siguientes en el Administrador de IIS para quitar el controlador de archivos estáticos de IIS en el nivel de servidor o de sitio web:</span><span class="sxs-lookup"><span data-stu-id="2d9be-219">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="2d9be-220">Navegue hasta la característica **Módulos**.</span><span class="sxs-lookup"><span data-stu-id="2d9be-220">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="2d9be-221">En la lista, seleccione **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="2d9be-221">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="2d9be-222">Haga clic en **Quitar** en la barra lateral **Acciones**.</span><span class="sxs-lookup"><span data-stu-id="2d9be-222">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="2d9be-223">Si el controlador de archivos estáticos de IIS está habilitado **y** el módulo de ASP.NET Core no está configurado correctamente, se proporcionan archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="2d9be-223">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="2d9be-224">Esto sucede, por ejemplo, si el archivo *web.config* no está implementado.</span><span class="sxs-lookup"><span data-stu-id="2d9be-224">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="2d9be-225">Coloque los archivos de código (incluidos *.cs* y *.cshtml*) fuera de la [raíz web](xref:fundamentals/index#web-root) del proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2d9be-225">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="2d9be-226">Por lo tanto, se crea una separación lógica entre el contenido del lado cliente de la aplicación y el código basado en servidor.</span><span class="sxs-lookup"><span data-stu-id="2d9be-226">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="2d9be-227">Esto impide que se filtre el código del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="2d9be-227">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d9be-228">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="2d9be-228">Additional resources</span></span>

* [<span data-ttu-id="2d9be-229">Middleware</span><span class="sxs-lookup"><span data-stu-id="2d9be-229">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="2d9be-230">Introducción a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2d9be-230">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2d9be-231">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="2d9be-231">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="2d9be-232">Los archivos estáticos, como HTML, CSS, imágenes y JavaScript, son activos que una aplicación de ASP.NET Core proporciona directamente a los clientes.</span><span class="sxs-lookup"><span data-stu-id="2d9be-232">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="2d9be-233">Se necesita alguna configuración para habilitar el servicio de estos archivos.</span><span class="sxs-lookup"><span data-stu-id="2d9be-233">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="2d9be-234">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2d9be-234">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="2d9be-235">Proporcionar archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="2d9be-235">Serve static files</span></span>

<span data-ttu-id="2d9be-236">Los archivos estáticos se almacenan en el directorio [raíz web](xref:fundamentals/index#web-root) del proyecto.</span><span class="sxs-lookup"><span data-stu-id="2d9be-236">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="2d9be-237">El directorio predeterminado es *{raíz del contenido}/wwwroot*, pero se puede cambiar con el método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>.</span><span class="sxs-lookup"><span data-stu-id="2d9be-237">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="2d9be-238">Vea [Raíz del contenido](xref:fundamentals/index#content-root) y [Raíz web](xref:fundamentals/index#web-root) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="2d9be-238">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="2d9be-239">El host de web de la aplicación debe tener conocimiento del directorio raíz del contenido.</span><span class="sxs-lookup"><span data-stu-id="2d9be-239">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="2d9be-240">El método `WebHost.CreateDefaultBuilder` establece la raíz de contenido en el directorio actual:</span><span class="sxs-lookup"><span data-stu-id="2d9be-240">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="2d9be-241">Se puede acceder a los archivos estáticos a través de una ruta de acceso relativa a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="2d9be-241">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="2d9be-242">Por ejemplo, la plantilla de proyecto **Aplicación web** contiene varias carpetas dentro de la carpeta `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-242">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="2d9be-243">El formato de URI para acceder a un archivo en la subcarpeta *images* es *http://\<server_address>/images/\<image_file_name>* .</span><span class="sxs-lookup"><span data-stu-id="2d9be-243">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="2d9be-244">Por ejemplo, *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="2d9be-244">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="2d9be-245">Si el destino es .NET Framework, agregue el paquete [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="2d9be-245">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="2d9be-246">Si el destino es .NET Core, el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluye este paquete.</span><span class="sxs-lookup"><span data-stu-id="2d9be-246">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="2d9be-247">Configure el [middleware](xref:fundamentals/middleware/index), que permite proporcionar archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="2d9be-247">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="2d9be-248">Proporcionar archivos dentro de la raíz web</span><span class="sxs-lookup"><span data-stu-id="2d9be-248">Serve files inside of web root</span></span>

<span data-ttu-id="2d9be-249">Invoque al método <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-249">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="2d9be-250">La sobrecarga del método `UseStaticFiles` sin parámetros marca los archivos en la [raíz web](xref:fundamentals/index#web-root) como que se pueden proporcionar.</span><span class="sxs-lookup"><span data-stu-id="2d9be-250">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="2d9be-251">El siguiente marcado hace referencia a *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="2d9be-251">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="2d9be-252">En el código anterior, el carácter de tilde de la ñ `~/` apunta a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="2d9be-252">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="2d9be-253">Proporcionar archivos fuera de la raíz web</span><span class="sxs-lookup"><span data-stu-id="2d9be-253">Serve files outside of web root</span></span>

<span data-ttu-id="2d9be-254">Considere una jerarquía de directorios en la que residen fuera de la [raíz web](xref:fundamentals/index#web-root) los archivos estáticos que se van a proporcionar:</span><span class="sxs-lookup"><span data-stu-id="2d9be-254">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="2d9be-255">Una solicitud puede acceder al archivo *banner1.svg* configurando el middleware de archivos estáticos como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="2d9be-255">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="2d9be-256">En el código anterior, la jerarquía del directorio *MyStaticFiles* se expone públicamente a través del segmento de URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="2d9be-256">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="2d9be-257">Una solicitud a *http://\<server_address>/StaticFiles/images/banner1.svg* proporciona el archivo *banner1.svg*.</span><span class="sxs-lookup"><span data-stu-id="2d9be-257">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="2d9be-258">El siguiente marcado hace referencia a *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="2d9be-258">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="2d9be-259">Establecer encabezados de respuesta HTTP</span><span class="sxs-lookup"><span data-stu-id="2d9be-259">Set HTTP response headers</span></span>

<span data-ttu-id="2d9be-260">Se puede usar un objeto <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> para establecer encabezados de respuesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2d9be-260">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="2d9be-261">Además de configurar el servicio de archivos estáticos desde la [raíz web](xref:fundamentals/index#web-root), el código siguiente establece el encabezado `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-261">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="2d9be-262">El método <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> existe en el paquete [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="2d9be-262">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="2d9be-263">Los archivos se han hecho públicamente almacenables en caché durante 10 minutos (600 segundos) en el entorno de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="2d9be-263">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Se han agregado encabezados de respuesta que muestran el encabezado Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="2d9be-265">Autorización de archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="2d9be-265">Static file authorization</span></span>

<span data-ttu-id="2d9be-266">El middleware de archivos estáticos no proporciona comprobaciones de autorización.</span><span class="sxs-lookup"><span data-stu-id="2d9be-266">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="2d9be-267">Los archivos que proporciona, incluidos los de *wwwroot*, están accesibles de forma pública.</span><span class="sxs-lookup"><span data-stu-id="2d9be-267">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="2d9be-268">Para proporcionar archivos según su autorización:</span><span class="sxs-lookup"><span data-stu-id="2d9be-268">To serve files based on authorization:</span></span>

* <span data-ttu-id="2d9be-269">Almacénelos fuera de *wwwroot* y cualquier directorio al que el middleware de archivos estáticos tenga acceso.</span><span class="sxs-lookup"><span data-stu-id="2d9be-269">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="2d9be-270">Proporciónelos a través de un método de acción al que se aplica la autorización.</span><span class="sxs-lookup"><span data-stu-id="2d9be-270">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="2d9be-271">Devuelva un objeto <xref:Microsoft.AspNetCore.Mvc.FileResult>:</span><span class="sxs-lookup"><span data-stu-id="2d9be-271">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="2d9be-272">Habilite el examen de directorios</span><span class="sxs-lookup"><span data-stu-id="2d9be-272">Enable directory browsing</span></span>

<span data-ttu-id="2d9be-273">El examen de directorios permite a los usuarios de su aplicación web ver una lista de directorios y archivos contenidos en un directorio especificado.</span><span class="sxs-lookup"><span data-stu-id="2d9be-273">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="2d9be-274">Por motivos de seguridad, el examen de directorios está deshabilitado de forma predeterminada (consulte [Consideraciones](#sc)).</span><span class="sxs-lookup"><span data-stu-id="2d9be-274">Directory browsing is disabled by default for security reasons (see [Considerations](#sc)).</span></span> <span data-ttu-id="2d9be-275">Habilite el examen de directorios invocando el método <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-275">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="2d9be-276">Agregar servicios requeridos invocando el método <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> desde `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-276">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="2d9be-277">El código anterior permite el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL *http://\<server_address>/MyImages*, con vínculos a cada archivo y carpeta:</span><span class="sxs-lookup"><span data-stu-id="2d9be-277">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![examen de directorios](static-files/_static/dir-browse.png)

<span data-ttu-id="2d9be-279">Vea [consideraciones](#considerations) sobre los riesgos de seguridad al habilitar el examen.</span><span class="sxs-lookup"><span data-stu-id="2d9be-279">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="2d9be-280">Tenga en cuenta las dos llamadas a `UseStaticFiles` en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="2d9be-280">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="2d9be-281">La primera llamada permite proporcionar archivos estáticos en la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="2d9be-281">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="2d9be-282">La segunda llamada habilita el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL *http://\<server_address>/MyImages*:</span><span class="sxs-lookup"><span data-stu-id="2d9be-282">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="2d9be-283">Proporcionar un documento predeterminado</span><span class="sxs-lookup"><span data-stu-id="2d9be-283">Serve a default document</span></span>

<span data-ttu-id="2d9be-284">Establecer una página principal predeterminada proporciona a los visitantes un punto de partida lógico cuando visitan su sitio.</span><span class="sxs-lookup"><span data-stu-id="2d9be-284">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="2d9be-285">Para proporcionar una página predeterminada sin que el usuario cumpla por completo los requisitos del URI, llame al método <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> desde `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-285">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="2d9be-286">Debe llamarse a `UseDefaultFiles` antes de a `UseStaticFiles` para proporcionar el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="2d9be-286">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="2d9be-287">`UseDefaultFiles` es un sistema de reescritura de direcciones URL que no proporciona realmente el archivo.</span><span class="sxs-lookup"><span data-stu-id="2d9be-287">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="2d9be-288">Habilite el middleware de archivos estáticos a través de `UseStaticFiles` para proporcionar el archivo.</span><span class="sxs-lookup"><span data-stu-id="2d9be-288">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="2d9be-289">Con `UseDefaultFiles`, las solicitudes a una carpeta buscan:</span><span class="sxs-lookup"><span data-stu-id="2d9be-289">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="2d9be-290">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="2d9be-290">*default.htm*</span></span>
* <span data-ttu-id="2d9be-291">*default.html*</span><span class="sxs-lookup"><span data-stu-id="2d9be-291">*default.html*</span></span>
* <span data-ttu-id="2d9be-292">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="2d9be-292">*index.htm*</span></span>
* <span data-ttu-id="2d9be-293">*index.html*</span><span class="sxs-lookup"><span data-stu-id="2d9be-293">*index.html*</span></span>

<span data-ttu-id="2d9be-294">El primer archivo que se encuentra en la lista se proporciona como si la solicitud fuera el URI completo.</span><span class="sxs-lookup"><span data-stu-id="2d9be-294">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="2d9be-295">La dirección URL del explorador sigue reflejando el URI solicitado.</span><span class="sxs-lookup"><span data-stu-id="2d9be-295">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="2d9be-296">El código siguiente cambia el nombre de archivo predeterminado a *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="2d9be-296">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="2d9be-297">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="2d9be-297">UseFileServer</span></span>

<span data-ttu-id="2d9be-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina la funcionalidad de `UseStaticFiles` y `UseDefaultFiles` y, opcionalmente, la de `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="2d9be-299">El código siguiente permite proporcionar archivos estáticos y el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="2d9be-299">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="2d9be-300">El examen de directorios no está habilitado.</span><span class="sxs-lookup"><span data-stu-id="2d9be-300">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="2d9be-301">El código siguiente refuerza la sobrecarga sin parámetros habilitando el examen de directorios:</span><span class="sxs-lookup"><span data-stu-id="2d9be-301">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="2d9be-302">Tenga en cuenta la siguiente jerarquía de directorios:</span><span class="sxs-lookup"><span data-stu-id="2d9be-302">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="2d9be-303">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="2d9be-303">**wwwroot**</span></span>
  * <span data-ttu-id="2d9be-304">**css**</span><span class="sxs-lookup"><span data-stu-id="2d9be-304">**css**</span></span>
  * <span data-ttu-id="2d9be-305">**images**</span><span class="sxs-lookup"><span data-stu-id="2d9be-305">**images**</span></span>
  * <span data-ttu-id="2d9be-306">**js**</span><span class="sxs-lookup"><span data-stu-id="2d9be-306">**js**</span></span>
* <span data-ttu-id="2d9be-307">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="2d9be-307">**MyStaticFiles**</span></span>
  * <span data-ttu-id="2d9be-308">**images**</span><span class="sxs-lookup"><span data-stu-id="2d9be-308">**images**</span></span>
    * <span data-ttu-id="2d9be-309">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="2d9be-309">*banner1.svg*</span></span>
  * <span data-ttu-id="2d9be-310">*default.html*</span><span class="sxs-lookup"><span data-stu-id="2d9be-310">*default.html*</span></span>

<span data-ttu-id="2d9be-311">El código siguiente permite los archivos estáticos, los archivos predeterminados y el examen de directorios de `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-311">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="2d9be-312">Se debe llamar a `AddDirectoryBrowser` cuando el valor de la propiedad `EnableDirectoryBrowsing` es `true`:</span><span class="sxs-lookup"><span data-stu-id="2d9be-312">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="2d9be-313">Al usar la jerarquía de archivos y el código anterior, las direcciones URL se resuelven como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="2d9be-313">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="2d9be-314">Identificador URI</span><span class="sxs-lookup"><span data-stu-id="2d9be-314">URI</span></span>            |                             <span data-ttu-id="2d9be-315">Respuesta</span><span class="sxs-lookup"><span data-stu-id="2d9be-315">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="2d9be-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="2d9be-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="2d9be-317">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="2d9be-317">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="2d9be-318">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="2d9be-318">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="2d9be-319">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="2d9be-319">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="2d9be-320">Si no existe ningún archivo con el nombre predeterminado en el directorio *MyStaticFiles*, *http://\<server_address>/StaticFiles* devuelve la lista de directorios con vínculos activos:</span><span class="sxs-lookup"><span data-stu-id="2d9be-320">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Lista de archivos estáticos](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="2d9be-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> y <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> realizan un redireccionamiento del lado cliente desde `http://{SERVER ADDRESS}/StaticFiles` (sin una barra diagonal final) hasta `http://{SERVER ADDRESS}/StaticFiles/` (con una barra diagonal final).</span><span class="sxs-lookup"><span data-stu-id="2d9be-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="2d9be-323">Las direcciones URL relativas dentro del directorio *StaticFiles* no son válidas sin una barra diagonal final.</span><span class="sxs-lookup"><span data-stu-id="2d9be-323">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="2d9be-324">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="2d9be-324">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="2d9be-325">La clase <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> contiene una propiedad `Mappings` que actúa como una asignación de extensiones de archivo para tipos de contenido MIME.</span><span class="sxs-lookup"><span data-stu-id="2d9be-325">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="2d9be-326">En el ejemplo siguiente, se registran varias extensiones de archivo a los tipos MIME conocidos.</span><span class="sxs-lookup"><span data-stu-id="2d9be-326">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="2d9be-327">Se reemplaza la extensión *.rtf* y se quita *.mp4*.</span><span class="sxs-lookup"><span data-stu-id="2d9be-327">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="2d9be-328">Vea [Tipos de contenido MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="2d9be-328">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="2d9be-329">Para obtener información sobre el uso de un objeto <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> personalizado o para configurar otras instancias de <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> en aplicaciones Blazor Server, vea <xref:blazor/fundamentals/additional-scenarios#static-files>.</span><span class="sxs-lookup"><span data-stu-id="2d9be-329">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/additional-scenarios#static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="2d9be-330">Tipos de contenido no estándar</span><span class="sxs-lookup"><span data-stu-id="2d9be-330">Non-standard content types</span></span>

<span data-ttu-id="2d9be-331">El middleware de archivos estáticos entiende casi 400 tipos de contenido de archivo conocidos.</span><span class="sxs-lookup"><span data-stu-id="2d9be-331">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="2d9be-332">Si el usuario solicita un archivo con un tipo de archivo desconocido, el middleware de archivos estáticos pasa la solicitud al siguiente middleware de la canalización.</span><span class="sxs-lookup"><span data-stu-id="2d9be-332">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="2d9be-333">Si ningún middleware se ocupa de la solicitud, se devuelve una respuesta *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="2d9be-333">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="2d9be-334">Si se habilita la exploración de directorios, se muestra un vínculo al archivo en una lista de directorios.</span><span class="sxs-lookup"><span data-stu-id="2d9be-334">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="2d9be-335">El código siguiente permite proporcionar tipos desconocidos y procesa el archivo desconocido como una imagen:</span><span class="sxs-lookup"><span data-stu-id="2d9be-335">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="2d9be-336">Con el código anterior, una solicitud para un archivo con un tipo de contenido desconocido se devuelve como una imagen.</span><span class="sxs-lookup"><span data-stu-id="2d9be-336">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="2d9be-337">Habilitar <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> supone un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="2d9be-337">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="2d9be-338">Está deshabilitado de forma predeterminada y no se recomienda su uso.</span><span class="sxs-lookup"><span data-stu-id="2d9be-338">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="2d9be-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) proporciona una alternativa más segura a ofrecer archivos con extensiones no estándar.</span><span class="sxs-lookup"><span data-stu-id="2d9be-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="2d9be-340">Entrega de archivos desde varias ubicaciones</span><span class="sxs-lookup"><span data-stu-id="2d9be-340">Serve files from multiple locations</span></span>

<span data-ttu-id="2d9be-341">`UseStaticFiles` y `UseFileServer` tienen como valor predeterminado el proveedor de archivos que apunta a *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="2d9be-341">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="2d9be-342">Puede proporcionar instancias adicionales de `UseStaticFiles` y `UseFileServer` con otros proveedores de archivos para proporcionar archivos desde otras ubicaciones.</span><span class="sxs-lookup"><span data-stu-id="2d9be-342">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="2d9be-343">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="2d9be-343">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="2d9be-344">Consideraciones</span><span class="sxs-lookup"><span data-stu-id="2d9be-344">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="2d9be-345">`UseDirectoryBrowser` y `UseStaticFiles` pueden producir pérdidas de información confidencial.</span><span class="sxs-lookup"><span data-stu-id="2d9be-345">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="2d9be-346">Se recomienda deshabilitar el examen de directorios en producción.</span><span class="sxs-lookup"><span data-stu-id="2d9be-346">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="2d9be-347">Revise cuidadosamente los directorios que se habilitan mediante `UseStaticFiles` o `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="2d9be-347">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="2d9be-348">Todo el directorio y sus subdirectorios pasan a ser accesibles públicamente.</span><span class="sxs-lookup"><span data-stu-id="2d9be-348">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="2d9be-349">Almacene los archivos adecuados para proporcionarlos al público en un directorio dedicado, como *\<content_root>/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="2d9be-349">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="2d9be-350">Separe estos archivos de las vistas MVC, las instancias de Razor Pages (solo 2.x), los archivos de configuración, etc.</span><span class="sxs-lookup"><span data-stu-id="2d9be-350">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="2d9be-351">Las direcciones URL para el contenido que se expone a través de `UseDirectoryBrowser` y `UseStaticFiles` están sujetas a la distinción entre mayúsculas y minúsculas, y a restricciones de caracteres del sistema de archivos subyacente.</span><span class="sxs-lookup"><span data-stu-id="2d9be-351">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="2d9be-352">Por ejemplo, Windows no distingue entre mayúsculas y minúsculas, pero macOS y Linux sí.</span><span class="sxs-lookup"><span data-stu-id="2d9be-352">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="2d9be-353">Las aplicaciones de ASP.NET Core hospedadas en IIS usan el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para reenviar todas las solicitudes a la aplicación, incluidas las solicitudes de archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="2d9be-353">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="2d9be-354">No se usa el controlador de archivos estáticos de IIS.</span><span class="sxs-lookup"><span data-stu-id="2d9be-354">The IIS static file handler isn't used.</span></span> <span data-ttu-id="2d9be-355">No tiene ninguna posibilidad de controlar las solicitudes antes de que las controle el módulo.</span><span class="sxs-lookup"><span data-stu-id="2d9be-355">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="2d9be-356">Complete los pasos siguientes en el Administrador de IIS para quitar el controlador de archivos estáticos de IIS en el nivel de servidor o de sitio web:</span><span class="sxs-lookup"><span data-stu-id="2d9be-356">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="2d9be-357">Navegue hasta la característica **Módulos**.</span><span class="sxs-lookup"><span data-stu-id="2d9be-357">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="2d9be-358">En la lista, seleccione **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="2d9be-358">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="2d9be-359">Haga clic en **Quitar** en la barra lateral **Acciones**.</span><span class="sxs-lookup"><span data-stu-id="2d9be-359">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="2d9be-360">Si el controlador de archivos estáticos de IIS está habilitado **y** el módulo de ASP.NET Core no está configurado correctamente, se proporcionan archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="2d9be-360">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="2d9be-361">Esto sucede, por ejemplo, si el archivo *web.config* no está implementado.</span><span class="sxs-lookup"><span data-stu-id="2d9be-361">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="2d9be-362">Coloque los archivos de código (incluidos *.cs* y *.cshtml*) fuera de la [raíz web](xref:fundamentals/index#web-root) del proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2d9be-362">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="2d9be-363">Por lo tanto, se crea una separación lógica entre el contenido del lado cliente de la aplicación y el código basado en servidor.</span><span class="sxs-lookup"><span data-stu-id="2d9be-363">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="2d9be-364">Esto impide que se filtre el código del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="2d9be-364">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d9be-365">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="2d9be-365">Additional resources</span></span>

* [<span data-ttu-id="2d9be-366">Middleware</span><span class="sxs-lookup"><span data-stu-id="2d9be-366">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="2d9be-367">Introducción a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2d9be-367">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
