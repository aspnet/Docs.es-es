---
title: Asistente de etiquetas delimitadoras en ASP.NET Core
author: pkellner
description: Descubra los atributos del asistente de etiquetas delimitadoras de ASP.NET Core y el papel que desempeña cada atributo al ampliar el comportamiento de la etiqueta delimitadora de código HTML.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
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
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 25f1716b5c80492edb72bc9555954c9c93c02a49
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018771"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a>Asistente de etiquetas delimitadoras en ASP.NET Core

De [Peter Kellner](https://peterkellner.net) y [Scott Addie](https://github.com/scottaddie)

El [asistente de etiquetas delimitadoras](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) mejora la etiqueta delimitadora de código HTML estándar (`<a ... ></a>`) agregando nuevos atributos. Por convención, los nombres de atributo tienen el prefijo `asp-`. El valor de atributo `href` del elemento delimitador representado se determina mediante los valores de los atributos `asp-`.

Para obtener información general sobre asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

En los ejemplos de todo este documento se usa *SpeakerController*:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a>Atributos del asistente de etiquetas delimitadoras

### <a name="asp-controller"></a>asp-controller

El atributo [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) asigna el controlador usado para generar la dirección URL. En el marcado siguiente se especifican todos los altavoces:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

El código HTML generado:

```html
<a href="/Speaker">All Speakers</a>
```

Si el atributo `asp-controller` está especificado y `asp-action` no lo está, el valor `asp-action` predeterminado es la acción del controlador asociada a la vista que se está ejecutando. Si se omite `asp-action` en el marcado anterior y se usa el asistente de etiquetas delimitadoras en la vista *Index* de *HomeController* (*/Home*), el código HTML generado es el siguiente:

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a>asp-action

El valor del atributo [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) representa el nombre de la acción del controlador incluido en el atributo `href` generado. El siguiente marcado establece el valor del atributo `href` generado en la página "Speaker Evaluations":

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

El código HTML generado:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

Si no hay ningún atributo `asp-controller` especificado, se usará el controlador predeterminado que llama a la vista que se está ejecutando.

Si el valor del atributo `asp-action` es `Index`, no se anexa ninguna acción a la dirección URL, lo que da lugar a la invocación de la acción `Index` predeterminada. La acción especificada (o su valor predeterminado), debe existir en el controlador al que se hace referencia en `asp-controller`.

### <a name="asp-route-value"></a>asp-route-{valor}

El atributo [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) permite indicar un prefijo de ruta comodín. Cualquier valor que ocupe el marcador de posición `{value}` se interpretará como un parámetro de ruta potencial. Si no se encuentra ninguna ruta predeterminada, este prefijo de ruta se anexará al atributo `href` generado como valor y parámetro de solicitud. En caso contrario, se sustituirá en la plantilla de ruta.

Observe la siguiente acción del controlador:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

Con una plantilla de ruta predeterminada definida en *Startup.Configure*:

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

La vista de MVC usa el modelo, proporcionado por la acción, como se indica a continuación:

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

Se hace coincidir el marcador de posición `{id?}` de la ruta predeterminada. El código HTML generado:

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

Supongamos que el prefijo de ruta no forma parte de la plantilla de enrutamiento coincidente, al igual que con la siguiente vista de MVC:

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

Se genera el siguiente código HTML porque no se encontró `speakerid` en la ruta coincidente:

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

Si no se especifica `asp-controller` o `asp-action`, se sigue el mismo proceso predeterminado que en el atributo `asp-route`.

### <a name="asp-route"></a>asp-route

El atributo [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) se usa para crear una dirección URL que se vincula directamente con una ruta con nombre. Mediante [atributos de enrutamiento](xref:mvc/controllers/routing#attribute-routing) puede asignarse un nombre a una ruta tal y como se muestra en `SpeakerController` y usarse en su acción `Evaluations`:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

En el siguiente marcado, el atributo `asp-route` hace referencia a la ruta con nombre:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

El asistente de etiquetas delimitadoras genera una ruta directamente a esa acción de controlador mediante la dirección URL */Speaker/Evaluations*. El código HTML generado:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

Si además de `asp-route` se especifica `asp-controller` o `asp-action`, la ruta generada puede no ser la esperada. Para evitar un conflicto de ruta, no se debe usar `asp-route` con los atributos `asp-controller` y `asp-action`.

### <a name="asp-all-route-data"></a>asp-all-route-data

El atributo [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) permite crear un diccionario de pares clave-valor. La clave es el nombre del parámetro, mientras que el valor es el valor del parámetro.

En el ejemplo siguiente, se inicializa un diccionario y se pasa a una Razor vista. Los datos también se podrían pasar con el modelo.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

El código anterior genera el siguiente código HTML:

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

Se acopla el diccionario `asp-all-route-data` para generar una cadena de consulta que cumpla los requisitos de la acción `Evaluations` sobrecargada:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

Si alguna de las claves del diccionario coincide con los parámetros de ruta, esos valores se sustituirán en la ruta según corresponda. Los demás valores no coincidentes se generarán como parámetros de solicitud.

### <a name="asp-fragment"></a>asp-fragment

El atributo [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) define un fragmento de dirección URL que se anexará a la dirección URL. El asistente de etiquetas delimitadoras agrega el carácter de almohadilla (#). Observe el siguiente marcado:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

El código HTML generado:

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

Las etiquetas hash son útiles al crear aplicaciones del lado cliente. Por ejemplo, se pueden usar para el marcado y la búsqueda en JavaScript.

### <a name="asp-area"></a>asp-area

El atributo [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) establece el nombre de área que se usa para establecer la ruta adecuada. En el siguiente ejemplo se muestra cómo el atributo `asp-area` provoca una reasignación de rutas.

#### <a name="usage-in-no-locrazor-pages"></a>Uso en Razor páginas

RazorLas áreas de páginas se admiten en ASP.NET Core 2,1 o posterior.

Tenga en cuenta la siguiente jerarquía de directorios:

* **{Nombre del proyecto}**
  * **wwwroot**
  * **Áreas**
    * **Sesiones**
      * **Páginas**
        * *\_ViewStart. cshtml*
        * *Index.cshtml*
        * *Index.cshtml.cs*
  * **Páginas**

El marcado para hacer referencia a la página de *Índice* del área de *sesiones* Razor es:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

El código HTML generado:

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> Para admitir áreas en una Razor aplicación de páginas, realice una de las siguientes acciones en `Startup.ConfigureServices` :
>
> * Establezca la [versión de compatibilidad](xref:mvc/compatibility-version) en 2.1 o posterior.
> * Establezca la propiedad [ Razor PagesOptions. AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) en `true` :
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a>Uso en MVC

Tenga en cuenta la siguiente jerarquía de directorios:

* **{Nombre del proyecto}**
  * **wwwroot**
  * **Áreas**
    * **Blogs**
      * **Controladores**
        * *HomeController.cs*
      * **Vistas**
        * **Página principal**
          * *AboutBlog.cshtml*
          * *Index.cshtml*
        * *\_ViewStart. cshtml*
  * **Controladores**

Al establecer `asp-area` en "Blogs", el directorio *Areas/Blogs* se prefija en las rutas de los controladores y vistas asociados de esta etiqueta delimitadora. El marcado para hacer referencia a la vista *AboutBlog* es:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

El código HTML generado:

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> Para admitir áreas en una aplicación de MVC, la plantilla de ruta debe incluir una referencia al área, en el caso de que exista. Esta plantilla se representa mediante el segundo parámetro de la llamada de método `routes.MapRoute` en *Startup.Configure*:
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a>asp-protocol

El atributo [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) sirve para especificar un protocolo (por ejemplo, `https`) en la dirección URL. Por ejemplo:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

El código HTML generado:

```html
<a href="https://localhost/Home/About">About</a>
```

El nombre de host en el ejemplo es localhost. El asistente de etiquetas delimitadoras utiliza el dominio público del sitio web al generar la dirección URL.

### <a name="asp-host"></a>asp-host

El atributo [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) sirve para especificar un nombre de host en la dirección URL. Por ejemplo:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

El código HTML generado:

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a>asp-page

El atributo [asp-Page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) se usa con Razor las páginas. Úselo para establecer el valor del atributo `href` de una etiqueta delimitadora en una página específica. La dirección URL se crea al prefijar el nombre de la página con una barra diagonal ("/").

El siguiente ejemplo señala a la página de asistentes Razor :

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

El código HTML generado:

```html
<a href="/Attendee">All Attendees</a>
```

El atributo `asp-page` es mutuamente excluyente con los atributos `asp-route`, `asp-controller` y `asp-action`. Pero se puede usar `asp-page` con `asp-route-{value}` para controlar el enrutamiento, como se muestra en el siguiente marcado:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

El código HTML generado:

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a>asp-page-handler

El atributo [asp-Page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) se usa con Razor las páginas. Está diseñado para crear un vínculo con controladores de página específicos.

Observe el siguiente controlador de página:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

El marcado asociado del modelo de página se vincula con el controlador de página `OnGetProfile`. Tenga en cuenta que el prefijo `On<Verb>` del nombre de método del controlador de página se omite en el valor del atributo `asp-page-handler`. Cuando el método es asincrónico, también se omite el sufijo `Async`.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

El código HTML generado:

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
