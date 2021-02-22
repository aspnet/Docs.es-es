---
title: Archivos estáticos en ASP.NET Core
author: rick-anderson
description: Aprenda a proporcionar y proteger los archivos estáticos y a configurar los comportamientos de middleware de hospedaje de archivos estáticos en una aplicación web de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: fad569ab06735600299d8051a258651e329db8ce
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107186"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="61c59-103">Archivos estáticos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61c59-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="61c59-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="61c59-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="61c59-105">Los archivos estáticos, como HTML, CSS, imágenes y JavaScript, son activos que una aplicación de ASP.NET Core proporciona directamente a los clientes de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="61c59-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="61c59-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="61c59-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="61c59-107">Proporcionar archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="61c59-107">Serve static files</span></span>

<span data-ttu-id="61c59-108">Los archivos estáticos se almacenan en el directorio [raíz web](xref:fundamentals/index#web-root) del proyecto.</span><span class="sxs-lookup"><span data-stu-id="61c59-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="61c59-109">El directorio predeterminado es `{content root}/wwwroot`, pero se puede cambiar con el método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>.</span><span class="sxs-lookup"><span data-stu-id="61c59-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="61c59-110">Para más información, vea [Raíz del contenido](xref:fundamentals/index#content-root) y [Raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="61c59-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="61c59-111">El método <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> establece la raíz de contenido en el directorio actual:</span><span class="sxs-lookup"><span data-stu-id="61c59-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="61c59-112">El código anterior se creó con la plantilla de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="61c59-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="61c59-113">Se puede acceder a los archivos estáticos a través de una ruta de acceso relativa a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="61c59-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="61c59-114">Por ejemplo, las plantillas de proyecto **Aplicación web** contienen varias carpetas dentro de la carpeta `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="61c59-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="61c59-115">Considere la posibilidad de crear la carpeta *wwwroot/images* y agregar el archivo *wwwroot/images/MyImage.jpg*.</span><span class="sxs-lookup"><span data-stu-id="61c59-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="61c59-116">El formato del URI para acceder a un archivo en la carpeta `images` es `https://<hostname>/images/<image_file_name>`.</span><span class="sxs-lookup"><span data-stu-id="61c59-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="61c59-117">Por ejemplo, `https://localhost:5001/images/MyImage.jpg`.</span><span class="sxs-lookup"><span data-stu-id="61c59-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="61c59-118">Suministro de archivos en la raíz web</span><span class="sxs-lookup"><span data-stu-id="61c59-118">Serve files in web root</span></span>

<span data-ttu-id="61c59-119">Las plantillas de aplicación web predeterminadas llaman al método <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure`, que permite proporcionar archivos estáticos:</span><span class="sxs-lookup"><span data-stu-id="61c59-119">The default web app templates call the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="61c59-120">La sobrecarga del método `UseStaticFiles` sin parámetros marca los archivos en la [raíz web](xref:fundamentals/index#web-root) como que se pueden proporcionar.</span><span class="sxs-lookup"><span data-stu-id="61c59-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="61c59-121">El siguiente marcado hace referencia a *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="61c59-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="61c59-122">En el código anterior, el carácter de tilde de la ñ `~/` apunta a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="61c59-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="61c59-123">Proporcionar archivos fuera de la raíz web</span><span class="sxs-lookup"><span data-stu-id="61c59-123">Serve files outside of web root</span></span>

<span data-ttu-id="61c59-124">Considere una jerarquía de directorios en la que residen fuera de la [raíz web](xref:fundamentals/index#web-root) los archivos estáticos que se van a proporcionar:</span><span class="sxs-lookup"><span data-stu-id="61c59-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="61c59-125">Una solicitud puede acceder al archivo `red-rose.jpg` configurando el middleware de archivos estáticos como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="61c59-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="61c59-126">En el código anterior, la jerarquía del directorio *MyStaticFiles* se expone públicamente a través del segmento de URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="61c59-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="61c59-127">Una solicitud a `https://<hostname>/StaticFiles/images/red-rose.jpg` sirve al archivo *red-rose.jpg*.</span><span class="sxs-lookup"><span data-stu-id="61c59-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="61c59-128">El siguiente marcado hace referencia a *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="61c59-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="61c59-129">Establecer encabezados de respuesta HTTP</span><span class="sxs-lookup"><span data-stu-id="61c59-129">Set HTTP response headers</span></span>

<span data-ttu-id="61c59-130">Se puede usar un objeto <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> para establecer encabezados de respuesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="61c59-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="61c59-131">Además de configurar el servicio de archivos estáticos desde la [raíz web](xref:fundamentals/index#web-root), el código siguiente establece el encabezado `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="61c59-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="61c59-132">Los archivos estáticos se almacenan en caché públicamente durante 600 segundos:</span><span class="sxs-lookup"><span data-stu-id="61c59-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Se han agregado encabezados de respuesta que muestran el encabezado Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="61c59-134">Autorización de archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="61c59-134">Static file authorization</span></span>

<span data-ttu-id="61c59-135">Las plantillas de ASP.NET Core llaman a <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> antes de llamar a <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>.</span><span class="sxs-lookup"><span data-stu-id="61c59-135">The ASP.NET Core templates call <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> before calling <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>.</span></span> <span data-ttu-id="61c59-136">La mayoría de las aplicaciones siguen este patrón.</span><span class="sxs-lookup"><span data-stu-id="61c59-136">Most apps follow this pattern.</span></span> <span data-ttu-id="61c59-137">Cuando se llama al middleware de archivos estáticos antes de al middleware de autorización:</span><span class="sxs-lookup"><span data-stu-id="61c59-137">When the Static File Middleware is called before the authorization middleware:</span></span>

  * <span data-ttu-id="61c59-138">No se realizan comprobaciones de autorización en los archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="61c59-138">No authorization checks are performed on the static files.</span></span>
  * <span data-ttu-id="61c59-139">Los archivos estáticos que atiende el middleware de archivos estáticos, como los que están en `wwwroot`, son accesibles públicamente.</span><span class="sxs-lookup"><span data-stu-id="61c59-139">Static files served by the Static File Middleware, such as those those under `wwwroot`, are publicly accessible.</span></span>
  
<span data-ttu-id="61c59-140">Para proporcionar archivos estáticos según su autorización:</span><span class="sxs-lookup"><span data-stu-id="61c59-140">To serve static files based on authorization:</span></span>

  * <span data-ttu-id="61c59-141">Almacénelos fuera de `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="61c59-141">Store them outside of `wwwroot`.</span></span>
  * <span data-ttu-id="61c59-142">Llame a `UseStaticFiles`, especificando una ruta de acceso, después de llamar a `UseAuthorization`.</span><span class="sxs-lookup"><span data-stu-id="61c59-142">Call `UseStaticFiles`, specifying a path, after calling `UseAuthorization`.</span></span>
  * <span data-ttu-id="61c59-143">Establezca la [directiva de autorización de reserva](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="61c59-143">Set the [fallback authorization policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2&highlight=24-29)]
  
  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-25)]

  <span data-ttu-id="61c59-144">En el código anterior, la directiva de autorización de reserva requiere que se autentiquen ***todos*** los usuarios.</span><span class="sxs-lookup"><span data-stu-id="61c59-144">In the preceding code, the fallback authorization policy requires ***all*** users to be authenticated.</span></span> <span data-ttu-id="61c59-145">Los puntos de conexión como los controladores, Razor Pages, etc. que especifican sus propios requisitos de autorización no usan la directiva de autorización de reserva.</span><span class="sxs-lookup"><span data-stu-id="61c59-145">Endpoints such as controllers, Razor Pages, etc that specify their own authorization requirements don't use the fallback authorization policy.</span></span> <span data-ttu-id="61c59-146">Por ejemplo, Razor Pages, controladores o métodos de acción con `[AllowAnonymous]` o `[Authorize(PolicyName="MyPolicy")]` usan el atributo de autorización aplicado en lugar de la directiva de autorización de reserva.</span><span class="sxs-lookup"><span data-stu-id="61c59-146">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authorization attribute rather than the fallback authorization policy.</span></span>

  <span data-ttu-id="61c59-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> agrega <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> a la instancia actual, lo que exige que el usuario actual se autentique.</span><span class="sxs-lookup"><span data-stu-id="61c59-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

  <span data-ttu-id="61c59-148">Los recursos estáticos en `wwwroot` son accesibles públicamente porque se llama al middleware de archivos estáticos predeterminado (`app.UseStaticFiles();`) antes de a `UseAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="61c59-148">Static assets under `wwwroot` are publicly accessible because the default Static File Middleware (`app.UseStaticFiles();`) is called before `UseAuthentication`.</span></span> <span data-ttu-id="61c59-149">Los recursos estáticos de la carpeta *MyStaticFiles* requieren autenticación.</span><span class="sxs-lookup"><span data-stu-id="61c59-149">Static assets in the *MyStaticFiles* folder require authentication.</span></span> <span data-ttu-id="61c59-150">En el [código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) se muestra esto.</span><span class="sxs-lookup"><span data-stu-id="61c59-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) demonstrates this.</span></span>

<span data-ttu-id="61c59-151">Un enfoque alternativo a proporcionar archivos según la autorización es:</span><span class="sxs-lookup"><span data-stu-id="61c59-151">An alternative approach to serve files based on authorization is to:</span></span>

  * <span data-ttu-id="61c59-152">Almacénelos fuera de `wwwroot` y cualquier directorio al que el middleware de archivos estáticos tenga acceso.</span><span class="sxs-lookup"><span data-stu-id="61c59-152">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
  * <span data-ttu-id="61c59-153">Proporciónelos a través de un método de acción al que se aplica la autorización y devuelva un objeto <xref:Microsoft.AspNetCore.Mvc.FileResult>:</span><span class="sxs-lookup"><span data-stu-id="61c59-153">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="61c59-154">Examen de directorios</span><span class="sxs-lookup"><span data-stu-id="61c59-154">Directory browsing</span></span>

<span data-ttu-id="61c59-155">El examen de directorios permite enumerar directorios dentro de los directorios especificados.</span><span class="sxs-lookup"><span data-stu-id="61c59-155">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="61c59-156">Por motivos de seguridad, el examen de directorios está deshabilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="61c59-156">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="61c59-157">Para más información, consulte [Consideraciones](#considerations).</span><span class="sxs-lookup"><span data-stu-id="61c59-157">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="61c59-158">Habilite el examen de directorios con:</span><span class="sxs-lookup"><span data-stu-id="61c59-158">Enable directory browsing with:</span></span>

* <span data-ttu-id="61c59-159"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="61c59-159"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="61c59-160"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="61c59-160"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="61c59-161">El código anterior permite el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL `https://<hostname>/MyImages`, con vínculos a cada archivo y carpeta:</span><span class="sxs-lookup"><span data-stu-id="61c59-161">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![examen de directorios](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="61c59-163">Suministro de documentos predeterminados</span><span class="sxs-lookup"><span data-stu-id="61c59-163">Serve default documents</span></span>

<span data-ttu-id="61c59-164">El establecimiento de una página predeterminada proporciona a los visitantes un punto inicial en un sitio.</span><span class="sxs-lookup"><span data-stu-id="61c59-164">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="61c59-165">Para proporcionar una página predeterminada desde `wwwroot` sin un URI completo, llame al método <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A>:</span><span class="sxs-lookup"><span data-stu-id="61c59-165">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="61c59-166">Debe llamarse a `UseDefaultFiles` antes de a `UseStaticFiles` para proporcionar el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="61c59-166">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="61c59-167">`UseDefaultFiles` es un sistema de reescritura de direcciones URL que no proporciona el archivo.</span><span class="sxs-lookup"><span data-stu-id="61c59-167">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="61c59-168">Con `UseDefaultFiles`, las solicitudes a una carpeta en `wwwroot` buscan:</span><span class="sxs-lookup"><span data-stu-id="61c59-168">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="61c59-169">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="61c59-169">*default.htm*</span></span>
* <span data-ttu-id="61c59-170">*default.html*</span><span class="sxs-lookup"><span data-stu-id="61c59-170">*default.html*</span></span>
* <span data-ttu-id="61c59-171">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="61c59-171">*index.htm*</span></span>
* <span data-ttu-id="61c59-172">*index.html*</span><span class="sxs-lookup"><span data-stu-id="61c59-172">*index.html*</span></span>

<span data-ttu-id="61c59-173">El primer archivo que se encuentra en la lista se proporciona como si la solicitud fuera el URI completo.</span><span class="sxs-lookup"><span data-stu-id="61c59-173">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="61c59-174">La dirección URL del explorador sigue reflejando el URI solicitado.</span><span class="sxs-lookup"><span data-stu-id="61c59-174">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="61c59-175">El código siguiente cambia el nombre de archivo predeterminado a *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="61c59-175">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="61c59-176">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="61c59-176">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="61c59-177">UseFileServer para documentos predeterminados</span><span class="sxs-lookup"><span data-stu-id="61c59-177">UseFileServer for default documents</span></span>

<span data-ttu-id="61c59-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina la funcionalidad de `UseStaticFiles` y `UseDefaultFiles` y, opcionalmente, la de `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="61c59-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="61c59-179">Llame a `app.UseFileServer` para poder proporcionar archivos estáticos y el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="61c59-179">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="61c59-180">El examen de directorios no está habilitado.</span><span class="sxs-lookup"><span data-stu-id="61c59-180">Directory browsing isn't enabled.</span></span> <span data-ttu-id="61c59-181">El código siguiente muestra `Startup.Configure` con `UseFileServer`:</span><span class="sxs-lookup"><span data-stu-id="61c59-181">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="61c59-182">El código siguiente permite proporcionar archivos estáticos, el archivo predeterminado y el examen de directorios:</span><span class="sxs-lookup"><span data-stu-id="61c59-182">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="61c59-183">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="61c59-183">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="61c59-184">Tenga en cuenta la siguiente jerarquía de directorios:</span><span class="sxs-lookup"><span data-stu-id="61c59-184">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="61c59-185">El código siguiente permite proporcionar archivos estáticos, el archivo predeterminado y el examen de directorios de `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="61c59-185">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="61c59-186">Se debe llamar a <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> cuando el valor de la propiedad `EnableDirectoryBrowsing` es `true`.</span><span class="sxs-lookup"><span data-stu-id="61c59-186"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="61c59-187">Al usar la jerarquía de archivos y el código anterior, las direcciones URL se resuelven como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="61c59-187">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="61c59-188">Identificador URI</span><span class="sxs-lookup"><span data-stu-id="61c59-188">URI</span></span>            |      <span data-ttu-id="61c59-189">Respuesta</span><span class="sxs-lookup"><span data-stu-id="61c59-189">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="61c59-190">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="61c59-190">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="61c59-191">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="61c59-191">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="61c59-192">Si no existe ningún archivo con el nombre predeterminado en el directorio *MyStaticFiles*, `https://<hostname>/StaticFiles` devuelve la lista de directorios con vínculos activos:</span><span class="sxs-lookup"><span data-stu-id="61c59-192">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Lista de archivos estáticos](static-files/_static/db2.png)

<span data-ttu-id="61c59-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> y <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> realizan un redireccionamiento del lado cliente desde el URI de destino sin una `/` final hasta el URI de destino una `/` final.</span><span class="sxs-lookup"><span data-stu-id="61c59-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="61c59-195">Por ejemplo, desde `https://<hostname>/StaticFiles` hasta `https://<hostname>/StaticFiles/`.</span><span class="sxs-lookup"><span data-stu-id="61c59-195">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="61c59-196">Las direcciones URL relativas dentro del directorio *StaticFiles* no son válidas sin una barra diagonal final (`/`).</span><span class="sxs-lookup"><span data-stu-id="61c59-196">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="61c59-197">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="61c59-197">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="61c59-198">La clase <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> contiene una propiedad `Mappings` que actúa como una asignación de extensiones de archivo para tipos de contenido MIME.</span><span class="sxs-lookup"><span data-stu-id="61c59-198">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="61c59-199">En el ejemplo siguiente, se asignan varias extensiones de archivo a los tipos MIME conocidos.</span><span class="sxs-lookup"><span data-stu-id="61c59-199">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="61c59-200">Se reemplaza la extensión *.rtf* y se quita *.mp4*:</span><span class="sxs-lookup"><span data-stu-id="61c59-200">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="61c59-201">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="61c59-201">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="61c59-202">Vea [Tipos de contenido MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="61c59-202">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="61c59-203">Tipos de contenido no estándar</span><span class="sxs-lookup"><span data-stu-id="61c59-203">Non-standard content types</span></span>

<span data-ttu-id="61c59-204">El middleware de archivos estáticos entiende casi 400 tipos de contenido de archivo conocidos.</span><span class="sxs-lookup"><span data-stu-id="61c59-204">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="61c59-205">Si el usuario solicita un archivo con un tipo de archivo desconocido, el middleware de archivos estáticos pasa la solicitud al siguiente middleware de la canalización.</span><span class="sxs-lookup"><span data-stu-id="61c59-205">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="61c59-206">Si ningún middleware se ocupa de la solicitud, se devuelve una respuesta *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="61c59-206">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="61c59-207">Si se habilita la exploración de directorios, se muestra un vínculo al archivo en una lista de directorios.</span><span class="sxs-lookup"><span data-stu-id="61c59-207">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="61c59-208">El código siguiente permite proporcionar tipos desconocidos y procesa el archivo desconocido como una imagen:</span><span class="sxs-lookup"><span data-stu-id="61c59-208">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="61c59-209">El código siguiente muestra `Startup.Configure` con el código anterior:</span><span class="sxs-lookup"><span data-stu-id="61c59-209">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="61c59-210">Con el código anterior, una solicitud para un archivo con un tipo de contenido desconocido se devuelve como una imagen.</span><span class="sxs-lookup"><span data-stu-id="61c59-210">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="61c59-211">Habilitar <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> supone un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="61c59-211">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="61c59-212">Está deshabilitado de forma predeterminada y no se recomienda su uso.</span><span class="sxs-lookup"><span data-stu-id="61c59-212">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="61c59-213">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) proporciona una alternativa más segura a ofrecer archivos con extensiones no estándar.</span><span class="sxs-lookup"><span data-stu-id="61c59-213">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="61c59-214">Entrega de archivos desde varias ubicaciones</span><span class="sxs-lookup"><span data-stu-id="61c59-214">Serve files from multiple locations</span></span>

<span data-ttu-id="61c59-215">`UseStaticFiles` y `UseFileServer` tienen como valor predeterminado el proveedor de archivos que apunta a `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="61c59-215">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="61c59-216">Se pueden proporcionar instancias adicionales de `UseStaticFiles` y `UseFileServer` con otros proveedores de archivos para proporcionar archivos desde otras ubicaciones.</span><span class="sxs-lookup"><span data-stu-id="61c59-216">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="61c59-217">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="61c59-217">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="61c59-218">Consideraciones de seguridad para archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="61c59-218">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="61c59-219">`UseDirectoryBrowser` y `UseStaticFiles` pueden producir pérdidas de información confidencial.</span><span class="sxs-lookup"><span data-stu-id="61c59-219">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="61c59-220">Se recomienda deshabilitar el examen de directorios en producción.</span><span class="sxs-lookup"><span data-stu-id="61c59-220">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="61c59-221">Revise cuidadosamente los directorios que se habilitan mediante `UseStaticFiles` o `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="61c59-221">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="61c59-222">Todo el directorio y sus subdirectorios pasan a ser accesibles públicamente.</span><span class="sxs-lookup"><span data-stu-id="61c59-222">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="61c59-223">Almacene los archivos adecuados para proporcionarlos al público en un directorio dedicado, como `<content_root>/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="61c59-223">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="61c59-224">Separe estos archivos de las vistas MVC, de Razor Pages, de los archivos de configuración, etc.</span><span class="sxs-lookup"><span data-stu-id="61c59-224">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="61c59-225">Las direcciones URL para el contenido que se expone a través de `UseDirectoryBrowser` y `UseStaticFiles` están sujetas a la distinción entre mayúsculas y minúsculas, y a restricciones de caracteres del sistema de archivos subyacente.</span><span class="sxs-lookup"><span data-stu-id="61c59-225">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="61c59-226">Por ejemplo, Windows no distingue entre mayúsculas y minúsculas, pero macOS y Linux sí.</span><span class="sxs-lookup"><span data-stu-id="61c59-226">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="61c59-227">Las aplicaciones de ASP.NET Core hospedadas en IIS usan el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para reenviar todas las solicitudes a la aplicación, incluidas las solicitudes de archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="61c59-227">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="61c59-228">No se utiliza el controlador de archivos estáticos de IIS y no tiene la posibilidad de controlar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="61c59-228">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="61c59-229">Complete los pasos siguientes en el Administrador de IIS para quitar el controlador de archivos estáticos de IIS en el nivel de servidor o de sitio web:</span><span class="sxs-lookup"><span data-stu-id="61c59-229">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="61c59-230">Navegue hasta la característica **Módulos**.</span><span class="sxs-lookup"><span data-stu-id="61c59-230">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="61c59-231">En la lista, seleccione **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="61c59-231">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="61c59-232">Haga clic en **Quitar** en la barra lateral **Acciones**.</span><span class="sxs-lookup"><span data-stu-id="61c59-232">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="61c59-233">Si el controlador de archivos estáticos de IIS está habilitado **y** el módulo de ASP.NET Core no está configurado correctamente, se proporcionan archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="61c59-233">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="61c59-234">Esto sucede, por ejemplo, si el archivo *web.config* no está implementado.</span><span class="sxs-lookup"><span data-stu-id="61c59-234">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="61c59-235">Coloque los archivos de código (incluidos *.cs* y *.cshtml*) fuera de la [raíz web](xref:fundamentals/index#web-root) del proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="61c59-235">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="61c59-236">Por lo tanto, se crea una separación lógica entre el contenido del lado cliente de la aplicación y el código basado en servidor.</span><span class="sxs-lookup"><span data-stu-id="61c59-236">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="61c59-237">Esto impide que se filtre el código del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="61c59-237">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61c59-238">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="61c59-238">Additional resources</span></span>

* [<span data-ttu-id="61c59-239">Middleware</span><span class="sxs-lookup"><span data-stu-id="61c59-239">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="61c59-240">Introducción a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61c59-240">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="61c59-241">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="61c59-241">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="61c59-242">Los archivos estáticos, como HTML, CSS, imágenes y JavaScript, son activos que una aplicación de ASP.NET Core proporciona directamente a los clientes.</span><span class="sxs-lookup"><span data-stu-id="61c59-242">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="61c59-243">Se necesita alguna configuración para habilitar el servicio de estos archivos.</span><span class="sxs-lookup"><span data-stu-id="61c59-243">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="61c59-244">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="61c59-244">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="61c59-245">Proporcionar archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="61c59-245">Serve static files</span></span>

<span data-ttu-id="61c59-246">Los archivos estáticos se almacenan en el directorio [raíz web](xref:fundamentals/index#web-root) del proyecto.</span><span class="sxs-lookup"><span data-stu-id="61c59-246">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="61c59-247">El directorio predeterminado es *{raíz del contenido}/wwwroot*, pero se puede cambiar con el método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>.</span><span class="sxs-lookup"><span data-stu-id="61c59-247">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="61c59-248">Vea [Raíz del contenido](xref:fundamentals/index#content-root) y [Raíz web](xref:fundamentals/index#web-root) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="61c59-248">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="61c59-249">El host de web de la aplicación debe tener conocimiento del directorio raíz del contenido.</span><span class="sxs-lookup"><span data-stu-id="61c59-249">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="61c59-250">El método `WebHost.CreateDefaultBuilder` establece la raíz de contenido en el directorio actual:</span><span class="sxs-lookup"><span data-stu-id="61c59-250">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="61c59-251">Se puede acceder a los archivos estáticos a través de una ruta de acceso relativa a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="61c59-251">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="61c59-252">Por ejemplo, la plantilla de proyecto **Aplicación web** contiene varias carpetas dentro de la carpeta `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="61c59-252">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="61c59-253">El formato de URI para acceder a un archivo en la subcarpeta *images* es *http://\<server_address>/images/\<image_file_name>* .</span><span class="sxs-lookup"><span data-stu-id="61c59-253">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="61c59-254">Por ejemplo, *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="61c59-254">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="61c59-255">Si el destino es .NET Framework, agregue el paquete [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="61c59-255">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="61c59-256">Si el destino es .NET Core, el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluye este paquete.</span><span class="sxs-lookup"><span data-stu-id="61c59-256">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="61c59-257">Configure el [middleware](xref:fundamentals/middleware/index), que permite proporcionar archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="61c59-257">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="61c59-258">Proporcionar archivos dentro de la raíz web</span><span class="sxs-lookup"><span data-stu-id="61c59-258">Serve files inside of web root</span></span>

<span data-ttu-id="61c59-259">Invoque al método <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="61c59-259">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="61c59-260">La sobrecarga del método `UseStaticFiles` sin parámetros marca los archivos en la [raíz web](xref:fundamentals/index#web-root) como que se pueden proporcionar.</span><span class="sxs-lookup"><span data-stu-id="61c59-260">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="61c59-261">El siguiente marcado hace referencia a *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="61c59-261">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="61c59-262">En el código anterior, el carácter de tilde de la ñ `~/` apunta a la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="61c59-262">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="61c59-263">Proporcionar archivos fuera de la raíz web</span><span class="sxs-lookup"><span data-stu-id="61c59-263">Serve files outside of web root</span></span>

<span data-ttu-id="61c59-264">Considere una jerarquía de directorios en la que residen fuera de la [raíz web](xref:fundamentals/index#web-root) los archivos estáticos que se van a proporcionar:</span><span class="sxs-lookup"><span data-stu-id="61c59-264">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="61c59-265">Una solicitud puede acceder al archivo *banner1.svg* configurando el middleware de archivos estáticos como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="61c59-265">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="61c59-266">En el código anterior, la jerarquía del directorio *MyStaticFiles* se expone públicamente a través del segmento de URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="61c59-266">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="61c59-267">Una solicitud a *http://\<server_address>/StaticFiles/images/banner1.svg* proporciona el archivo *banner1.svg*.</span><span class="sxs-lookup"><span data-stu-id="61c59-267">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="61c59-268">El siguiente marcado hace referencia a *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="61c59-268">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="61c59-269">Establecer encabezados de respuesta HTTP</span><span class="sxs-lookup"><span data-stu-id="61c59-269">Set HTTP response headers</span></span>

<span data-ttu-id="61c59-270">Se puede usar un objeto <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> para establecer encabezados de respuesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="61c59-270">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="61c59-271">Además de configurar el servicio de archivos estáticos desde la [raíz web](xref:fundamentals/index#web-root), el código siguiente establece el encabezado `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="61c59-271">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="61c59-272">El método <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> existe en el paquete [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="61c59-272">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="61c59-273">Los archivos se han hecho públicamente almacenables en caché durante 10 minutos (600 segundos) en el entorno de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="61c59-273">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Se han agregado encabezados de respuesta que muestran el encabezado Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="61c59-275">Autorización de archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="61c59-275">Static file authorization</span></span>

<span data-ttu-id="61c59-276">El middleware de archivos estáticos no proporciona comprobaciones de autorización.</span><span class="sxs-lookup"><span data-stu-id="61c59-276">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="61c59-277">Los archivos que proporciona, incluidos los de *wwwroot*, están accesibles de forma pública.</span><span class="sxs-lookup"><span data-stu-id="61c59-277">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="61c59-278">Para proporcionar archivos según su autorización:</span><span class="sxs-lookup"><span data-stu-id="61c59-278">To serve files based on authorization:</span></span>

* <span data-ttu-id="61c59-279">Almacénelos fuera de *wwwroot* y cualquier directorio al que el middleware de archivos estáticos tenga acceso.</span><span class="sxs-lookup"><span data-stu-id="61c59-279">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="61c59-280">Proporciónelos a través de un método de acción al que se aplica la autorización.</span><span class="sxs-lookup"><span data-stu-id="61c59-280">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="61c59-281">Devuelva un objeto <xref:Microsoft.AspNetCore.Mvc.FileResult>:</span><span class="sxs-lookup"><span data-stu-id="61c59-281">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="61c59-282">Habilite el examen de directorios</span><span class="sxs-lookup"><span data-stu-id="61c59-282">Enable directory browsing</span></span>

<span data-ttu-id="61c59-283">El examen de directorios permite a los usuarios de su aplicación web ver una lista de directorios y archivos contenidos en un directorio especificado.</span><span class="sxs-lookup"><span data-stu-id="61c59-283">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="61c59-284">Por motivos de seguridad, el examen de directorios está deshabilitado de forma predeterminada (consulte [Consideraciones](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="61c59-284">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="61c59-285">Habilite el examen de directorios invocando el método <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="61c59-285">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="61c59-286">Agregar servicios requeridos invocando el método <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> desde `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="61c59-286">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="61c59-287">El código anterior permite el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL *http://\<server_address>/MyImages*, con vínculos a cada archivo y carpeta:</span><span class="sxs-lookup"><span data-stu-id="61c59-287">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![examen de directorios](static-files/_static/dir-browse.png)

<span data-ttu-id="61c59-289">Vea [consideraciones](#considerations) sobre los riesgos de seguridad al habilitar el examen.</span><span class="sxs-lookup"><span data-stu-id="61c59-289">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="61c59-290">Tenga en cuenta las dos llamadas a `UseStaticFiles` en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="61c59-290">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="61c59-291">La primera llamada permite proporcionar archivos estáticos en la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="61c59-291">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="61c59-292">La segunda llamada habilita el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL *http://\<server_address>/MyImages*:</span><span class="sxs-lookup"><span data-stu-id="61c59-292">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="61c59-293">Proporcionar un documento predeterminado</span><span class="sxs-lookup"><span data-stu-id="61c59-293">Serve a default document</span></span>

<span data-ttu-id="61c59-294">Establecer una página principal predeterminada proporciona a los visitantes un punto de partida lógico cuando visitan su sitio.</span><span class="sxs-lookup"><span data-stu-id="61c59-294">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="61c59-295">Para proporcionar una página predeterminada sin que el usuario cumpla por completo los requisitos del URI, llame al método <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> desde `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="61c59-295">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="61c59-296">Debe llamarse a `UseDefaultFiles` antes de a `UseStaticFiles` para proporcionar el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="61c59-296">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="61c59-297">`UseDefaultFiles` es un sistema de reescritura de direcciones URL que no proporciona realmente el archivo.</span><span class="sxs-lookup"><span data-stu-id="61c59-297">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="61c59-298">Habilite el middleware de archivos estáticos a través de `UseStaticFiles` para proporcionar el archivo.</span><span class="sxs-lookup"><span data-stu-id="61c59-298">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="61c59-299">Con `UseDefaultFiles`, las solicitudes a una carpeta buscan:</span><span class="sxs-lookup"><span data-stu-id="61c59-299">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="61c59-300">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="61c59-300">*default.htm*</span></span>
* <span data-ttu-id="61c59-301">*default.html*</span><span class="sxs-lookup"><span data-stu-id="61c59-301">*default.html*</span></span>
* <span data-ttu-id="61c59-302">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="61c59-302">*index.htm*</span></span>
* <span data-ttu-id="61c59-303">*index.html*</span><span class="sxs-lookup"><span data-stu-id="61c59-303">*index.html*</span></span>

<span data-ttu-id="61c59-304">El primer archivo que se encuentra en la lista se proporciona como si la solicitud fuera el URI completo.</span><span class="sxs-lookup"><span data-stu-id="61c59-304">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="61c59-305">La dirección URL del explorador sigue reflejando el URI solicitado.</span><span class="sxs-lookup"><span data-stu-id="61c59-305">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="61c59-306">El código siguiente cambia el nombre de archivo predeterminado a *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="61c59-306">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="61c59-307">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="61c59-307">UseFileServer</span></span>

<span data-ttu-id="61c59-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina la funcionalidad de `UseStaticFiles` y `UseDefaultFiles` y, opcionalmente, la de `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="61c59-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="61c59-309">El código siguiente permite proporcionar archivos estáticos y el archivo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="61c59-309">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="61c59-310">El examen de directorios no está habilitado.</span><span class="sxs-lookup"><span data-stu-id="61c59-310">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="61c59-311">El código siguiente refuerza la sobrecarga sin parámetros habilitando el examen de directorios:</span><span class="sxs-lookup"><span data-stu-id="61c59-311">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="61c59-312">Tenga en cuenta la siguiente jerarquía de directorios:</span><span class="sxs-lookup"><span data-stu-id="61c59-312">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="61c59-313">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="61c59-313">**wwwroot**</span></span>
  * <span data-ttu-id="61c59-314">**css**</span><span class="sxs-lookup"><span data-stu-id="61c59-314">**css**</span></span>
  * <span data-ttu-id="61c59-315">**images**</span><span class="sxs-lookup"><span data-stu-id="61c59-315">**images**</span></span>
  * <span data-ttu-id="61c59-316">**js**</span><span class="sxs-lookup"><span data-stu-id="61c59-316">**js**</span></span>
* <span data-ttu-id="61c59-317">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="61c59-317">**MyStaticFiles**</span></span>
  * <span data-ttu-id="61c59-318">**images**</span><span class="sxs-lookup"><span data-stu-id="61c59-318">**images**</span></span>
    * <span data-ttu-id="61c59-319">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="61c59-319">*banner1.svg*</span></span>
  * <span data-ttu-id="61c59-320">*default.html*</span><span class="sxs-lookup"><span data-stu-id="61c59-320">*default.html*</span></span>

<span data-ttu-id="61c59-321">El código siguiente permite los archivos estáticos, los archivos predeterminados y el examen de directorios de `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="61c59-321">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="61c59-322">Se debe llamar a `AddDirectoryBrowser` cuando el valor de la propiedad `EnableDirectoryBrowsing` es `true`:</span><span class="sxs-lookup"><span data-stu-id="61c59-322">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="61c59-323">Al usar la jerarquía de archivos y el código anterior, las direcciones URL se resuelven como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="61c59-323">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="61c59-324">Identificador URI</span><span class="sxs-lookup"><span data-stu-id="61c59-324">URI</span></span>            |                             <span data-ttu-id="61c59-325">Respuesta</span><span class="sxs-lookup"><span data-stu-id="61c59-325">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="61c59-326">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="61c59-326">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="61c59-327">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="61c59-327">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="61c59-328">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="61c59-328">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="61c59-329">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="61c59-329">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="61c59-330">Si no existe ningún archivo con el nombre predeterminado en el directorio *MyStaticFiles*, *http://\<server_address>/StaticFiles* devuelve la lista de directorios con vínculos activos:</span><span class="sxs-lookup"><span data-stu-id="61c59-330">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Lista de archivos estáticos](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="61c59-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> y <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> realizan un redireccionamiento del lado cliente desde `http://{SERVER ADDRESS}/StaticFiles` (sin una barra diagonal final) hasta `http://{SERVER ADDRESS}/StaticFiles/` (con una barra diagonal final).</span><span class="sxs-lookup"><span data-stu-id="61c59-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="61c59-333">Las direcciones URL relativas dentro del directorio *StaticFiles* no son válidas sin una barra diagonal final.</span><span class="sxs-lookup"><span data-stu-id="61c59-333">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="61c59-334">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="61c59-334">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="61c59-335">La clase <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> contiene una propiedad `Mappings` que actúa como una asignación de extensiones de archivo para tipos de contenido MIME.</span><span class="sxs-lookup"><span data-stu-id="61c59-335">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="61c59-336">En el ejemplo siguiente, se registran varias extensiones de archivo a los tipos MIME conocidos.</span><span class="sxs-lookup"><span data-stu-id="61c59-336">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="61c59-337">Se reemplaza la extensión *.rtf* y se quita *.mp4*.</span><span class="sxs-lookup"><span data-stu-id="61c59-337">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="61c59-338">Vea [Tipos de contenido MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="61c59-338">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="61c59-339">Para obtener información sobre el uso de un objeto <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> personalizado o para configurar otras instancias de <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> en aplicaciones Blazor Server, vea <xref:blazor/fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="61c59-339">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="61c59-340">Tipos de contenido no estándar</span><span class="sxs-lookup"><span data-stu-id="61c59-340">Non-standard content types</span></span>

<span data-ttu-id="61c59-341">El middleware de archivos estáticos entiende casi 400 tipos de contenido de archivo conocidos.</span><span class="sxs-lookup"><span data-stu-id="61c59-341">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="61c59-342">Si el usuario solicita un archivo con un tipo de archivo desconocido, el middleware de archivos estáticos pasa la solicitud al siguiente middleware de la canalización.</span><span class="sxs-lookup"><span data-stu-id="61c59-342">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="61c59-343">Si ningún middleware se ocupa de la solicitud, se devuelve una respuesta *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="61c59-343">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="61c59-344">Si se habilita la exploración de directorios, se muestra un vínculo al archivo en una lista de directorios.</span><span class="sxs-lookup"><span data-stu-id="61c59-344">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="61c59-345">El código siguiente permite proporcionar tipos desconocidos y procesa el archivo desconocido como una imagen:</span><span class="sxs-lookup"><span data-stu-id="61c59-345">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="61c59-346">Con el código anterior, una solicitud para un archivo con un tipo de contenido desconocido se devuelve como una imagen.</span><span class="sxs-lookup"><span data-stu-id="61c59-346">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="61c59-347">Habilitar <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> supone un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="61c59-347">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="61c59-348">Está deshabilitado de forma predeterminada y no se recomienda su uso.</span><span class="sxs-lookup"><span data-stu-id="61c59-348">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="61c59-349">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) proporciona una alternativa más segura a ofrecer archivos con extensiones no estándar.</span><span class="sxs-lookup"><span data-stu-id="61c59-349">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="61c59-350">Entrega de archivos desde varias ubicaciones</span><span class="sxs-lookup"><span data-stu-id="61c59-350">Serve files from multiple locations</span></span>

<span data-ttu-id="61c59-351">`UseStaticFiles` y `UseFileServer` tienen como valor predeterminado el proveedor de archivos que apunta a *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="61c59-351">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="61c59-352">Puede proporcionar instancias adicionales de `UseStaticFiles` y `UseFileServer` con otros proveedores de archivos para proporcionar archivos desde otras ubicaciones.</span><span class="sxs-lookup"><span data-stu-id="61c59-352">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="61c59-353">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="61c59-353">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="61c59-354">Consideraciones</span><span class="sxs-lookup"><span data-stu-id="61c59-354">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="61c59-355">`UseDirectoryBrowser` y `UseStaticFiles` pueden producir pérdidas de información confidencial.</span><span class="sxs-lookup"><span data-stu-id="61c59-355">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="61c59-356">Se recomienda deshabilitar el examen de directorios en producción.</span><span class="sxs-lookup"><span data-stu-id="61c59-356">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="61c59-357">Revise cuidadosamente los directorios que se habilitan mediante `UseStaticFiles` o `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="61c59-357">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="61c59-358">Todo el directorio y sus subdirectorios pasan a ser accesibles públicamente.</span><span class="sxs-lookup"><span data-stu-id="61c59-358">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="61c59-359">Almacene los archivos adecuados para proporcionarlos al público en un directorio dedicado, como *\<content_root>/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="61c59-359">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="61c59-360">Separe estos archivos de las vistas MVC, las instancias de Razor Pages (solo 2.x), los archivos de configuración, etc.</span><span class="sxs-lookup"><span data-stu-id="61c59-360">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="61c59-361">Las direcciones URL para el contenido que se expone a través de `UseDirectoryBrowser` y `UseStaticFiles` están sujetas a la distinción entre mayúsculas y minúsculas, y a restricciones de caracteres del sistema de archivos subyacente.</span><span class="sxs-lookup"><span data-stu-id="61c59-361">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="61c59-362">Por ejemplo, Windows no distingue entre mayúsculas y minúsculas, pero macOS y Linux sí.</span><span class="sxs-lookup"><span data-stu-id="61c59-362">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="61c59-363">Las aplicaciones de ASP.NET Core hospedadas en IIS usan el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para reenviar todas las solicitudes a la aplicación, incluidas las solicitudes de archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="61c59-363">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="61c59-364">No se usa el controlador de archivos estáticos de IIS.</span><span class="sxs-lookup"><span data-stu-id="61c59-364">The IIS static file handler isn't used.</span></span> <span data-ttu-id="61c59-365">No tiene ninguna posibilidad de controlar las solicitudes antes de que las controle el módulo.</span><span class="sxs-lookup"><span data-stu-id="61c59-365">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="61c59-366">Complete los pasos siguientes en el Administrador de IIS para quitar el controlador de archivos estáticos de IIS en el nivel de servidor o de sitio web:</span><span class="sxs-lookup"><span data-stu-id="61c59-366">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="61c59-367">Navegue hasta la característica **Módulos**.</span><span class="sxs-lookup"><span data-stu-id="61c59-367">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="61c59-368">En la lista, seleccione **StaticFileModule**.</span><span class="sxs-lookup"><span data-stu-id="61c59-368">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="61c59-369">Haga clic en **Quitar** en la barra lateral **Acciones**.</span><span class="sxs-lookup"><span data-stu-id="61c59-369">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="61c59-370">Si el controlador de archivos estáticos de IIS está habilitado **y** el módulo de ASP.NET Core no está configurado correctamente, se proporcionan archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="61c59-370">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="61c59-371">Esto sucede, por ejemplo, si el archivo *web.config* no está implementado.</span><span class="sxs-lookup"><span data-stu-id="61c59-371">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="61c59-372">Coloque los archivos de código (incluidos *.cs* y *.cshtml*) fuera de la [raíz web](xref:fundamentals/index#web-root) del proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="61c59-372">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="61c59-373">Por lo tanto, se crea una separación lógica entre el contenido del lado cliente de la aplicación y el código basado en servidor.</span><span class="sxs-lookup"><span data-stu-id="61c59-373">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="61c59-374">Esto impide que se filtre el código del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="61c59-374">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61c59-375">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="61c59-375">Additional resources</span></span>

* [<span data-ttu-id="61c59-376">Middleware</span><span class="sxs-lookup"><span data-stu-id="61c59-376">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="61c59-377">Introducción a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61c59-377">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
