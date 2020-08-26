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
# <a name="static-files-in-aspnet-core"></a>Archivos estáticos en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)

Los archivos estáticos, como HTML, CSS, imágenes y JavaScript, son activos que una aplicación de ASP.NET Core proporciona directamente a los clientes de forma predeterminada.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Proporcionar archivos estáticos

Los archivos estáticos se almacenan en el directorio [raíz web](xref:fundamentals/index#web-root) del proyecto. El directorio predeterminado es `{content root}/wwwroot`, pero se puede cambiar con el método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>. Para más información, vea [Raíz del contenido](xref:fundamentals/index#content-root) y [Raíz web](xref:fundamentals/index#web-root).

El método <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> establece la raíz de contenido en el directorio actual:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

El código anterior se creó con la plantilla de la aplicación web.

Se puede acceder a los archivos estáticos a través de una ruta de acceso relativa a la [raíz web](xref:fundamentals/index#web-root). Por ejemplo, las plantillas de proyecto **Aplicación web** contienen varias carpetas dentro de la carpeta `wwwroot`:

* `wwwroot`
  * `css`
  * `js`
  * `lib`

Considere la posibilidad de crear la carpeta *wwwroot/images* y agregar el archivo *wwwroot/images/MyImage.jpg*. El formato del URI para acceder a un archivo en la carpeta `images` es `https://<hostname>/images/<image_file_name>`. Por ejemplo, `https://localhost:5001/images/MyImage.jpg`.

### <a name="serve-files-in-web-root"></a>Suministro de archivos en la raíz web

Las plantillas de aplicación web predeterminadas llaman al método <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure`, que permite proporcionar archivos estáticos:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

La sobrecarga del método `UseStaticFiles` sin parámetros marca los archivos en la [raíz web](xref:fundamentals/index#web-root) como que se pueden proporcionar. El siguiente marcado hace referencia a *wwwroot/images/MyImage.jpg*:

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

En el código anterior, el carácter de tilde de la ñ `~/` apunta a la [raíz web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Proporcionar archivos fuera de la raíz web

Considere una jerarquía de directorios en la que residen fuera de la [raíz web](xref:fundamentals/index#web-root) los archivos estáticos que se van a proporcionar:

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

Una solicitud puede acceder al archivo `red-rose.jpg` configurando el middleware de archivos estáticos como se muestra a continuación:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

En el código anterior, la jerarquía del directorio *MyStaticFiles* se expone públicamente a través del segmento de URI *StaticFiles*. Una solicitud a `https://<hostname>/StaticFiles/images/red-rose.jpg` sirve al archivo *red-rose.jpg*.

El siguiente marcado hace referencia a *MyStaticFiles/images/red-rose.jpg*:

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a>Establecer encabezados de respuesta HTTP

Se puede usar un objeto <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> para establecer encabezados de respuesta HTTP. Además de configurar el servicio de archivos estáticos desde la [raíz web](xref:fundamentals/index#web-root), el código siguiente establece el encabezado `Cache-Control`:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

Los archivos estáticos se almacenan en caché públicamente durante 600 segundos:

![Se han agregado encabezados de respuesta que muestran el encabezado Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorización de archivos estáticos

El middleware de archivos estáticos no proporciona comprobaciones de autorización. Los archivos que proporciona, incluidos los de `wwwroot`, están accesibles de forma pública. Para proporcionar archivos según su autorización:

* Almacénelos fuera de `wwwroot` y cualquier directorio al que el middleware de archivos estáticos predeterminado tenga acceso.
* Llame a `UseStaticFiles` después de `UseAuthorization` y especifique la ruta de acceso:

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  Para el enfoque anterior se necesita la autenticación de los usuarios:

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

Un enfoque alternativo consiste en proporcionar archivos según la autorización:

* Almacénelos fuera de `wwwroot` y cualquier directorio al que el middleware de archivos estáticos tenga acceso.
* Proporciónelos a través de un método de acción al que se aplica la autorización y devuelva un objeto <xref:Microsoft.AspNetCore.Mvc.FileResult>:

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a>Examen de directorios

El examen de directorios permite enumerar directorios dentro de los directorios especificados.

Por motivos de seguridad, el examen de directorios está deshabilitado de forma predeterminada. Para más información, consulte [Consideraciones](#sc).

Habilite el examen de directorios con:

* <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> en `Startup.ConfigureServices`.
* <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> en `Startup.Configure`.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

El código anterior permite el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL `https://<hostname>/MyImages`, con vínculos a cada archivo y carpeta:

![examen de directorios](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a>Suministro de documentos predeterminados

El establecimiento de una página predeterminada proporciona a los visitantes un punto inicial en un sitio. Para proporcionar una página predeterminada desde `wwwroot` sin un URI completo, llame al método <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A>:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

Debe llamarse a `UseDefaultFiles` antes de a `UseStaticFiles` para proporcionar el archivo predeterminado. `UseDefaultFiles` es un sistema de reescritura de direcciones URL que no proporciona el archivo.

Con `UseDefaultFiles`, las solicitudes a una carpeta en `wwwroot` buscan:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

El primer archivo que se encuentra en la lista se proporciona como si la solicitud fuera el URI completo. La dirección URL del explorador sigue reflejando el URI solicitado.

El código siguiente cambia el nombre de archivo predeterminado a *mydefault.html*:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

El código siguiente muestra `Startup.Configure` con el código anterior:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a>UseFileServer para documentos predeterminados

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina la funcionalidad de `UseStaticFiles` y `UseDefaultFiles` y, opcionalmente, la de `UseDirectoryBrowser`.

Llame a `app.UseFileServer` para poder proporcionar archivos estáticos y el archivo predeterminado. El examen de directorios no está habilitado. El código siguiente muestra `Startup.Configure` con `UseFileServer`:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

El código siguiente permite proporcionar archivos estáticos, el archivo predeterminado y el examen de directorios:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

El código siguiente muestra `Startup.Configure` con el código anterior:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

Tenga en cuenta la siguiente jerarquía de directorios:

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

El código siguiente permite proporcionar archivos estáticos, el archivo predeterminado y el examen de directorios de `MyStaticFiles`:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

Se debe llamar a <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> cuando el valor de la propiedad `EnableDirectoryBrowsing` es `true`.

Al usar la jerarquía de archivos y el código anterior, las direcciones URL se resuelven como se indica a continuación:

| Identificador URI            |      Respuesta  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | *MyStaticFiles/images/MyImage.jpg* |
| `https://<hostname>/StaticFiles` | *MyStaticFiles/default.html* |

Si no existe ningún archivo con el nombre predeterminado en el directorio *MyStaticFiles*, `https://<hostname>/StaticFiles` devuelve la lista de directorios con vínculos activos:

![Lista de archivos estáticos](static-files/_static/db2.png)

<xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> y <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> realizan un redireccionamiento del lado cliente desde el URI de destino sin una `/` final hasta el URI de destino una `/` final. Por ejemplo, desde `https://<hostname>/StaticFiles` hasta `https://<hostname>/StaticFiles/`. Las direcciones URL relativas dentro del directorio *StaticFiles* no son válidas sin una barra diagonal final (`/`).

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

La clase <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> contiene una propiedad `Mappings` que actúa como una asignación de extensiones de archivo para tipos de contenido MIME. En el ejemplo siguiente, se asignan varias extensiones de archivo a los tipos MIME conocidos. Se reemplaza la extensión *.rtf* y se quita *.mp4*:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

El código siguiente muestra `Startup.Configure` con el código anterior:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

Vea [Tipos de contenido MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Tipos de contenido no estándar

El middleware de archivos estáticos entiende casi 400 tipos de contenido de archivo conocidos. Si el usuario solicita un archivo con un tipo de archivo desconocido, el middleware de archivos estáticos pasa la solicitud al siguiente middleware de la canalización. Si ningún middleware se ocupa de la solicitud, se devuelve una respuesta *404 No encontrado*. Si se habilita la exploración de directorios, se muestra un vínculo al archivo en una lista de directorios.

El código siguiente permite proporcionar tipos desconocidos y procesa el archivo desconocido como una imagen:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

El código siguiente muestra `Startup.Configure` con el código anterior:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

Con el código anterior, una solicitud para un archivo con un tipo de contenido desconocido se devuelve como una imagen.

> [!WARNING]
> Habilitar <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> supone un riesgo para la seguridad. Está deshabilitado de forma predeterminada y no se recomienda su uso. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) proporciona una alternativa más segura a ofrecer archivos con extensiones no estándar.

## <a name="serve-files-from-multiple-locations"></a>Entrega de archivos desde varias ubicaciones

`UseStaticFiles` y `UseFileServer` tienen como valor predeterminado el proveedor de archivos que apunta a `wwwroot`. Se pueden proporcionar instancias adicionales de `UseStaticFiles` y `UseFileServer` con otros proveedores de archivos para proporcionar archivos desde otras ubicaciones. Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a>Consideraciones de seguridad para archivos estáticos

> [!WARNING]
> `UseDirectoryBrowser` y `UseStaticFiles` pueden producir pérdidas de información confidencial. Se recomienda deshabilitar el examen de directorios en producción. Revise cuidadosamente los directorios que se habilitan mediante `UseStaticFiles` o `UseDirectoryBrowser`. Todo el directorio y sus subdirectorios pasan a ser accesibles públicamente. Almacene los archivos adecuados para proporcionarlos al público en un directorio dedicado, como `<content_root>/wwwroot`. Separe estos archivos de las vistas MVC, de Razor Pages, de los archivos de configuración, etc.

* Las direcciones URL para el contenido que se expone a través de `UseDirectoryBrowser` y `UseStaticFiles` están sujetas a la distinción entre mayúsculas y minúsculas, y a restricciones de caracteres del sistema de archivos subyacente. Por ejemplo, Windows no distingue entre mayúsculas y minúsculas, pero macOS y Linux sí.

* Las aplicaciones de ASP.NET Core hospedadas en IIS usan el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para reenviar todas las solicitudes a la aplicación, incluidas las solicitudes de archivos estáticos. No se utiliza el controlador de archivos estáticos de IIS y no tiene la posibilidad de controlar las solicitudes.

* Complete los pasos siguientes en el Administrador de IIS para quitar el controlador de archivos estáticos de IIS en el nivel de servidor o de sitio web:
    1. Navegue hasta la característica **Módulos**.
    1. En la lista, seleccione **StaticFileModule**.
    1. Haga clic en **Quitar** en la barra lateral **Acciones**.

> [!WARNING]
> Si el controlador de archivos estáticos de IIS está habilitado **y** el módulo de ASP.NET Core no está configurado correctamente, se proporcionan archivos estáticos. Esto sucede, por ejemplo, si el archivo *web.config* no está implementado.

* Coloque los archivos de código (incluidos *.cs* y *.cshtml*) fuera de la [raíz web](xref:fundamentals/index#web-root) del proyecto de la aplicación. Por lo tanto, se crea una separación lógica entre el contenido del lado cliente de la aplicación y el código basado en servidor. Esto impide que se filtre el código del lado servidor.

## <a name="additional-resources"></a>Recursos adicionales

* [Middleware](xref:fundamentals/middleware/index)
* [Introducción a ASP.NET Core](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Scott Addie](https://twitter.com/Scott_Addie)

Los archivos estáticos, como HTML, CSS, imágenes y JavaScript, son activos que una aplicación de ASP.NET Core proporciona directamente a los clientes. Se necesita alguna configuración para habilitar el servicio de estos archivos.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Proporcionar archivos estáticos

Los archivos estáticos se almacenan en el directorio [raíz web](xref:fundamentals/index#web-root) del proyecto. El directorio predeterminado es *{raíz del contenido}/wwwroot*, pero se puede cambiar con el método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A>. Vea [Raíz del contenido](xref:fundamentals/index#content-root) y [Raíz web](xref:fundamentals/index#web-root) para obtener más información.

El host de web de la aplicación debe tener conocimiento del directorio raíz del contenido.

El método `WebHost.CreateDefaultBuilder` establece la raíz de contenido en el directorio actual:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

Se puede acceder a los archivos estáticos a través de una ruta de acceso relativa a la [raíz web](xref:fundamentals/index#web-root). Por ejemplo, la plantilla de proyecto **Aplicación web** contiene varias carpetas dentro de la carpeta `wwwroot`:

* `wwwroot`
  * `css`
  * `images`
  * `js`

El formato de URI para acceder a un archivo en la subcarpeta *images* es *http://\<server_address>/images/\<image_file_name>* . Por ejemplo, *http://localhost:9189/images/banner3.svg* .

Si el destino es .NET Framework, agregue el paquete [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al proyecto. Si el destino es .NET Core, el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluye este paquete.

Configure el [middleware](xref:fundamentals/middleware/index), que permite proporcionar archivos estáticos.

### <a name="serve-files-inside-of-web-root"></a>Proporcionar archivos dentro de la raíz web

Invoque al método <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

La sobrecarga del método `UseStaticFiles` sin parámetros marca los archivos en la [raíz web](xref:fundamentals/index#web-root) como que se pueden proporcionar. El siguiente marcado hace referencia a *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

En el código anterior, el carácter de tilde de la ñ `~/` apunta a la [raíz web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Proporcionar archivos fuera de la raíz web

Considere una jerarquía de directorios en la que residen fuera de la [raíz web](xref:fundamentals/index#web-root) los archivos estáticos que se van a proporcionar:

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

Una solicitud puede acceder al archivo *banner1.svg* configurando el middleware de archivos estáticos como se muestra a continuación:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

En el código anterior, la jerarquía del directorio *MyStaticFiles* se expone públicamente a través del segmento de URI *StaticFiles*. Una solicitud a *http://\<server_address>/StaticFiles/images/banner1.svg* proporciona el archivo *banner1.svg*.

El siguiente marcado hace referencia a *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Establecer encabezados de respuesta HTTP

Se puede usar un objeto <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> para establecer encabezados de respuesta HTTP. Además de configurar el servicio de archivos estáticos desde la [raíz web](xref:fundamentals/index#web-root), el código siguiente establece el encabezado `Cache-Control`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

El método <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> existe en el paquete [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).

Los archivos se han hecho públicamente almacenables en caché durante 10 minutos (600 segundos) en el entorno de desarrollo:

![Se han agregado encabezados de respuesta que muestran el encabezado Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorización de archivos estáticos

El middleware de archivos estáticos no proporciona comprobaciones de autorización. Los archivos que proporciona, incluidos los de *wwwroot*, están accesibles de forma pública. Para proporcionar archivos según su autorización:

* Almacénelos fuera de *wwwroot* y cualquier directorio al que el middleware de archivos estáticos tenga acceso.
* Proporciónelos a través de un método de acción al que se aplica la autorización. Devuelva un objeto <xref:Microsoft.AspNetCore.Mvc.FileResult>:

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Habilite el examen de directorios

El examen de directorios permite a los usuarios de su aplicación web ver una lista de directorios y archivos contenidos en un directorio especificado. Por motivos de seguridad, el examen de directorios está deshabilitado de forma predeterminada (consulte [Consideraciones](#sc)). Habilite el examen de directorios invocando el método <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> en `Startup.Configure`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Agregar servicios requeridos invocando el método <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> desde `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

El código anterior permite el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL *http://\<server_address>/MyImages*, con vínculos a cada archivo y carpeta:

![examen de directorios](static-files/_static/dir-browse.png)

Vea [consideraciones](#considerations) sobre los riesgos de seguridad al habilitar el examen.

Tenga en cuenta las dos llamadas a `UseStaticFiles` en el ejemplo siguiente. La primera llamada permite proporcionar archivos estáticos en la carpeta *wwwroot*. La segunda llamada habilita el examen de directorios de la carpeta *wwwroot/images* usando la dirección URL *http://\<server_address>/MyImages*:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Proporcionar un documento predeterminado

Establecer una página principal predeterminada proporciona a los visitantes un punto de partida lógico cuando visitan su sitio. Para proporcionar una página predeterminada sin que el usuario cumpla por completo los requisitos del URI, llame al método <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> desde `Startup.Configure`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> Debe llamarse a `UseDefaultFiles` antes de a `UseStaticFiles` para proporcionar el archivo predeterminado. `UseDefaultFiles` es un sistema de reescritura de direcciones URL que no proporciona realmente el archivo. Habilite el middleware de archivos estáticos a través de `UseStaticFiles` para proporcionar el archivo.

Con `UseDefaultFiles`, las solicitudes a una carpeta buscan:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

El primer archivo que se encuentra en la lista se proporciona como si la solicitud fuera el URI completo. La dirección URL del explorador sigue reflejando el URI solicitado.

El código siguiente cambia el nombre de archivo predeterminado a *mydefault.html*:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina la funcionalidad de `UseStaticFiles` y `UseDefaultFiles` y, opcionalmente, la de `UseDirectoryBrowser`.

El código siguiente permite proporcionar archivos estáticos y el archivo predeterminado. El examen de directorios no está habilitado.

```csharp
app.UseFileServer();
```

El código siguiente refuerza la sobrecarga sin parámetros habilitando el examen de directorios:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Tenga en cuenta la siguiente jerarquía de directorios:

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

El código siguiente permite los archivos estáticos, los archivos predeterminados y el examen de directorios de `MyStaticFiles`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

Se debe llamar a `AddDirectoryBrowser` cuando el valor de la propiedad `EnableDirectoryBrowsing` es `true`:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Al usar la jerarquía de archivos y el código anterior, las direcciones URL se resuelven como se indica a continuación:

| Identificador URI            |                             Respuesta  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

Si no existe ningún archivo con el nombre predeterminado en el directorio *MyStaticFiles*, *http://\<server_address>/StaticFiles* devuelve la lista de directorios con vínculos activos:

![Lista de archivos estáticos](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> y <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> realizan un redireccionamiento del lado cliente desde `http://{SERVER ADDRESS}/StaticFiles` (sin una barra diagonal final) hasta `http://{SERVER ADDRESS}/StaticFiles/` (con una barra diagonal final). Las direcciones URL relativas dentro del directorio *StaticFiles* no son válidas sin una barra diagonal final.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

La clase <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> contiene una propiedad `Mappings` que actúa como una asignación de extensiones de archivo para tipos de contenido MIME. En el ejemplo siguiente, se registran varias extensiones de archivo a los tipos MIME conocidos. Se reemplaza la extensión *.rtf* y se quita *.mp4*.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Vea [Tipos de contenido MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

Para obtener información sobre el uso de un objeto <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> personalizado o para configurar otras instancias de <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> en aplicaciones Blazor Server, vea <xref:blazor/fundamentals/additional-scenarios#static-files>.

## <a name="non-standard-content-types"></a>Tipos de contenido no estándar

El middleware de archivos estáticos entiende casi 400 tipos de contenido de archivo conocidos. Si el usuario solicita un archivo con un tipo de archivo desconocido, el middleware de archivos estáticos pasa la solicitud al siguiente middleware de la canalización. Si ningún middleware se ocupa de la solicitud, se devuelve una respuesta *404 No encontrado*. Si se habilita la exploración de directorios, se muestra un vínculo al archivo en una lista de directorios.

El código siguiente permite proporcionar tipos desconocidos y procesa el archivo desconocido como una imagen:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Con el código anterior, una solicitud para un archivo con un tipo de contenido desconocido se devuelve como una imagen.

> [!WARNING]
> Habilitar <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> supone un riesgo para la seguridad. Está deshabilitado de forma predeterminada y no se recomienda su uso. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) proporciona una alternativa más segura a ofrecer archivos con extensiones no estándar.

## <a name="serve-files-from-multiple-locations"></a>Entrega de archivos desde varias ubicaciones

`UseStaticFiles` y `UseFileServer` tienen como valor predeterminado el proveedor de archivos que apunta a *wwwroot*. Puede proporcionar instancias adicionales de `UseStaticFiles` y `UseFileServer` con otros proveedores de archivos para proporcionar archivos desde otras ubicaciones. Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Consideraciones

> [!WARNING]
> `UseDirectoryBrowser` y `UseStaticFiles` pueden producir pérdidas de información confidencial. Se recomienda deshabilitar el examen de directorios en producción. Revise cuidadosamente los directorios que se habilitan mediante `UseStaticFiles` o `UseDirectoryBrowser`. Todo el directorio y sus subdirectorios pasan a ser accesibles públicamente. Almacene los archivos adecuados para proporcionarlos al público en un directorio dedicado, como *\<content_root>/wwwroot*. Separe estos archivos de las vistas MVC, las instancias de Razor Pages (solo 2.x), los archivos de configuración, etc.

* Las direcciones URL para el contenido que se expone a través de `UseDirectoryBrowser` y `UseStaticFiles` están sujetas a la distinción entre mayúsculas y minúsculas, y a restricciones de caracteres del sistema de archivos subyacente. Por ejemplo, Windows no distingue entre mayúsculas y minúsculas, pero macOS y Linux sí.

* Las aplicaciones de ASP.NET Core hospedadas en IIS usan el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para reenviar todas las solicitudes a la aplicación, incluidas las solicitudes de archivos estáticos. No se usa el controlador de archivos estáticos de IIS. No tiene ninguna posibilidad de controlar las solicitudes antes de que las controle el módulo.

* Complete los pasos siguientes en el Administrador de IIS para quitar el controlador de archivos estáticos de IIS en el nivel de servidor o de sitio web:
    1. Navegue hasta la característica **Módulos**.
    1. En la lista, seleccione **StaticFileModule**.
    1. Haga clic en **Quitar** en la barra lateral **Acciones**.

> [!WARNING]
> Si el controlador de archivos estáticos de IIS está habilitado **y** el módulo de ASP.NET Core no está configurado correctamente, se proporcionan archivos estáticos. Esto sucede, por ejemplo, si el archivo *web.config* no está implementado.

* Coloque los archivos de código (incluidos *.cs* y *.cshtml*) fuera de la [raíz web](xref:fundamentals/index#web-root) del proyecto de la aplicación. Por lo tanto, se crea una separación lógica entre el contenido del lado cliente de la aplicación y el código basado en servidor. Esto impide que se filtre el código del lado servidor.

## <a name="additional-resources"></a>Recursos adicionales

* [Middleware](xref:fundamentals/middleware/index)
* [Introducción a ASP.NET Core](xref:index)

::: moniker-end
