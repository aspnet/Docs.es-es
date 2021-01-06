---
title: Áreas de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre las áreas, una característica de ASP.NET MVC que se usa para organizar funciones relacionadas en un grupo como un espacio de nombres independiente (para el enrutamiento) y una estructura de carpetas (para las vistas).
ms.author: riande
ms.date: 03/21/2019
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
uid: mvc/controllers/areas
ms.openlocfilehash: f3d76f612e67aeabf1f7fef694199332c732c593
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878417"
---
# <a name="areas-in-aspnet-core"></a>Áreas de ASP.NET Core

Por [Dhananjay Kumar](https://twitter.com/debug_mode) y [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Las áreas son una característica ASP.NET que se usa para organizar la funcionalidad relacionada en un grupo como independiente:

* Espacio de nombres para el enrutamiento.
* Estructura de carpetas para vistas y Razor páginas.

El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area` , a `controller` y `action` o a una Razor página `page` .

Las áreas proporcionan una manera de particionar una aplicación Web ASP.NET Core en grupos funcionales más pequeños, cada uno con su propio conjunto de Razor páginas, controladores, vistas y modelos. Un área es en realidad una estructura dentro de una aplicación. En un proyecto web ASP.NET Core, los componentes lógicos como Páginas, Modelo, Vista y Controlador se mantienen en carpetas diferentes. El runtime de ASP.NET Core usa convenciones de nomenclatura para crear la relación entre estos componentes. Para una aplicación grande, puede ser conveniente dividir la aplicación en distintas áreas de funciones de alto nivel. Por ejemplo, una aplicación de comercio electrónico con varias unidades de negocio, como las de finalización de la compra, facturación y búsqueda. Cada una de estas unidades tiene su propia área para contener vistas, controladores, Razor páginas y modelos.

Considere el uso de áreas en un proyecto en los casos siguientes:

* La aplicación está formada por varios componentes funcionales generales que se pueden separar de forma lógica.
* Le interesa dividir la aplicación para que se pueda trabajar en cada área funcional de forma independiente.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)). En el ejemplo de descarga se proporciona una aplicación básica para probar las áreas.

Si utiliza Razor páginas, vea [áreas con Razor páginas](#areas-with-razor-pages) en este documento.

## <a name="areas-for-controllers-with-views"></a>Áreas para controladores con vistas

Una aplicación web ASP.NET Core típica en la que se usen áreas, controladores y vistas contiene lo siguiente:

* Una [estructura de carpetas de área](#area-folder-structure).
* Controladores con el [`[Area]`](#attribute) atributo para asociar el controlador al área:

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Ruta de área agregada al inicio](#add-area-route):

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Estructura de carpetas de área

Considere una aplicación que tiene dos grupos lógicos, *Productos* y *Servicios*. Mediante las áreas, la estructura de carpetas sería similar a la siguiente:

* Nombre de proyecto
  * Áreas
    * Productos
      * Controladores
        * HomeController.cs
        * ManageController.cs
      * Vistas
        * Página principal
          * Index.cshtml
        * Administrar
          * Index.cshtml
          * About.cshtml
    * Servicios
      * Controladores
        * HomeController.cs
      * Vistas
        * Página principal
          * Index.cshtml

Aunque el diseño anterior es típico cuando se usan áreas, solo los archivos de vista tienen que usar esta estructura de carpetas. La detección de vista busca un archivo de vista de área coincidente en el orden siguiente:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Asociación del controlador a un área

Los controladores de área se designan con el atributo de [ &lbrack; área &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Adición de la ruta de área

Las rutas de área suelen usar  [enrutamiento convencional](xref:mvc/controllers/routing#cr) en lugar de [enrutamiento de atributos](xref:mvc/controllers/routing#ar). El enrutamiento convencional depende del orden. En general, las rutas con áreas deben colocarse antes en la tabla de rutas, ya que son más específicas que las rutas sin un área.

Se puede `{area:...}` usar como un token en las plantillas de ruta, si el espacio de direcciones URL es uniforme en todas las áreas:

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

En el código anterior, `exists` aplica la restricción de que la ruta debe coincidir con un área. Usar `{area:...}` con `MapControllerRoute` :

* Es el mecanismo menos complicado para agregar enrutamiento a las áreas.
* Coincide con todos los controladores con el `[Area("Area name")]` atributo.

En el código siguiente se usa <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> para crear dos rutas de área con nombre:

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

Para más información, vea [Enrutamiento de áreas](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Generación de vínculos con áreas de MVC

En el código siguiente de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) se muestra la generación de vínculos con el área especificada:

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

La descarga de ejemplo incluye una [vista parcial](xref:mvc/views/partial) que contiene:

* Los vínculos anteriores.
* No se especifican vínculos similares a los anteriores, excepto `area` .

En el [archivo de diseño](xref:mvc/views/layout) se hace referencia a la vista parcial, por lo que en todas las páginas de la aplicación se muestran los vínculos generados. Los vínculos generados sin especificar el área solo son válidos cuando se les hace referencia desde una página en el mismo área y controlador.

Cuando no se especifica el área o el controlador, el enrutamiento depende de los valores de [ambiente](xref:mvc/controllers/routing#ambient). Los valores de ruta actuales de la solicitud actual se consideran valores de ambiente para la generación de vínculos. En muchos casos para la aplicación de ejemplo, el uso de los valores de ambiente genera vínculos incorrectos con el marcado que no especifica el área.

Para más información, vea [Enrutar a acciones de controlador](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Diseño Compartido para áreas con el archivo _ViewStart.cshtml

Para compartir un diseño común para toda la aplicación, mantenga el *_ViewStart. cshtml* en la [carpeta raíz](#arf)de la aplicación. Para obtener más información, vea <xref:mvc/views/layout>.

<a name="arf"></a>

### <a name="application-root-folder"></a>Carpeta raíz de la aplicación

La carpeta raíz de la aplicación es la carpeta que contiene *Startup.CS* en la aplicación Web creada con las plantillas de ASP.net Core.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

 */Views/_ViewImports. cshtml*, para MVC y */pages/_ViewImports. cshtml* para Razor páginas, no se importa a las vistas de las áreas. Use uno de los métodos siguientes para proporcionar las importaciones de vistas a todas las vistas:

* Agregue *_ViewImports. cshtml* a la [carpeta raíz](#arf)de la aplicación. Un *_ViewImports. cshtml* en la carpeta raíz de la aplicación se aplicará a todas las vistas de la aplicación.
* Copie el archivo *_ViewImports. cshtml* en la carpeta vista adecuada en áreas.

El archivo *_ViewImports. cshtml* normalmente contiene las importaciones de [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) , `@using` y `@inject` . Para obtener más información, vea [importar directivas compartidas](xref:mvc/views/layout#importing-shared-directives).

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Cambio de la carpeta de área predeterminada donde se almacenan las vistas

En el código siguiente se cambia la carpeta de área predeterminada de `"Areas"` a `"MyAreas"`:

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a>Áreas con Razor páginas

Las áreas con Razor páginas requieren una `Areas/<area name>/Pages` carpeta en la raíz de la aplicación. La siguiente estructura de carpetas se usa con la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):

* Nombre de proyecto
  * Áreas
    * Productos
      * Páginas
        * _ViewImports
        * Acerca de
        * Índice
    * Servicios
      * Páginas
        * Administrar
          * Acerca de
          * Índice

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a>Generación de vínculos con Razor páginas y áreas

El código siguiente de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) muestra la generación de vínculos con el área especificada (por ejemplo, `asp-area="Products"`):

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

En la descarga de ejemplo se incluye una [vista parcial](xref:mvc/views/partial) que contiene los vínculos anteriores y los mismos vínculos sin especificar el área. En el [archivo de diseño](xref:mvc/views/layout) se hace referencia a la vista parcial, por lo que en todas las páginas de la aplicación se muestran los vínculos generados. Los vínculos generados sin especificar el área solo son válidos cuando se les hace referencia desde una página de la misma área.

Cuando no se especifica el área, el enrutamiento depende de los valores *ambient*. Los valores de ruta actuales de la solicitud actual se consideran valores de ambiente para la generación de vínculos. En muchos casos de la aplicación de ejemplo, el uso de valores de ambiente genera vínculos incorrectos. Por ejemplo, tenga en cuenta los vínculos generados desde el código siguiente:

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

En el código anterior:

* El vínculo generado a partir de `<a asp-page="/Manage/About">` es correcto solo cuando la última solicitud fue de una página del área `Services`. Por ejemplo, `/Services/Manage/`, `/Services/Manage/Index` o `/Services/Manage/About`.
* El vínculo generado a partir de `<a asp-page="/About">` es correcto solo cuando la última solicitud fue de una página de `/Home`.
* El código está tomado de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importación del espacio de nombres y los asistentes de etiquetas con el archivo _ViewImports

Se puede Agregar un archivo *_ViewImports. cshtml* a cada carpeta de *páginas* de área para importar las aplicaciones auxiliares de espacio de nombres y etiquetas a cada Razor Página de la carpeta.

Tenga en cuenta el área *Services* del código de ejemplo, que no contiene un archivo *_ViewImports.cshtml*. En el marcado siguiente se muestra la página */Services/Manage/about* Razor :

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

En el marcado anterior:

* El nombre completo de la clase debe usarse para especificar el modelo ( `@model RPareas.Areas.Services.Pages.Manage.AboutModel` ).
* Habilita las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro)`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

En la descarga de ejemplo, el área Products contiene el siguiente archivo *_ViewImports.cshtml*:

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

En el marcado siguiente se muestra la página */Products/about* Razor :

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

En el archivo anterior, el espacio de nombres y la directiva `@addTagHelper` se importan en el archivo mediante el archivo *Areas/Products/Pages/_ViewImports.cshtml*.

Para más información, consulte [Administración del ámbito de los asistentes de etiquetas](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) y [Importar directivas compartidas](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-no-locrazor-pages-areas"></a>Diseño compartido para Razor áreas de páginas

Para compartir un diseño común para toda la aplicación, mueva el archivo *_ViewStart.cshtml* a la carpeta raíz de la aplicación.

### <a name="publishing-areas"></a>Publicación de áreas

Todos los archivos *.cshtml y los archivos que formen parte del directorio *wwwroot* se publicarán como resultados si `<Project Sdk="Microsoft.NET.Sdk.Web">` está incluido en el archivo *.csproj.
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Las áreas son una característica de ASP.NET que se usa para organizar funciones relacionadas en un grupo como un espacio de nombres independiente (para el enrutamiento) y una estructura de carpetas (para las vistas). El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area` , a `controller` y `action` o a una Razor página `page` .

Las áreas proporcionan una manera de particionar una aplicación Web ASP.NET Core en grupos funcionales más pequeños, cada uno con su propio conjunto de Razor páginas, controladores, vistas y modelos. Un área es en realidad una estructura dentro de una aplicación. En un proyecto web ASP.NET Core, los componentes lógicos como Páginas, Modelo, Vista y Controlador se mantienen en carpetas diferentes. El runtime de ASP.NET Core usa convenciones de nomenclatura para crear la relación entre estos componentes. Para una aplicación grande, puede ser conveniente dividir la aplicación en distintas áreas de funciones de alto nivel. Por ejemplo, una aplicación de comercio electrónico con varias unidades de negocio, como las de finalización de la compra, facturación y búsqueda. Cada una de estas unidades tiene su propia área para contener vistas, controladores, Razor páginas y modelos.

Considere el uso de áreas en un proyecto en los casos siguientes:

* La aplicación está formada por varios componentes funcionales generales que se pueden separar de forma lógica.
* Le interesa dividir la aplicación para que se pueda trabajar en cada área funcional de forma independiente.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)). En el ejemplo de descarga se proporciona una aplicación básica para probar las áreas.

Si utiliza Razor páginas, vea [áreas con Razor páginas](#areas-with-razor-pages) en este documento.

## <a name="areas-for-controllers-with-views"></a>Áreas para controladores con vistas

Una aplicación web ASP.NET Core típica en la que se usen áreas, controladores y vistas contiene lo siguiente:

* Una [estructura de carpetas de área](#area-folder-structure).
* Controladores con el [`[Area]`](#attribute) atributo para asociar el controlador al área:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Ruta de área agregada al inicio](#add-area-route):

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Estructura de carpetas de área

Considere una aplicación que tiene dos grupos lógicos, *Productos* y *Servicios*. Mediante las áreas, la estructura de carpetas sería similar a la siguiente:

* Nombre de proyecto
  * Áreas
    * Productos
      * Controladores
        * HomeController.cs
        * ManageController.cs
      * Vistas
        * Página principal
          * Index.cshtml
        * Administrar
          * Index.cshtml
          * About.cshtml
    * Servicios
      * Controladores
        * HomeController.cs
      * Vistas
        * Página principal
          * Index.cshtml

Aunque el diseño anterior es típico cuando se usan áreas, solo los archivos de vista tienen que usar esta estructura de carpetas. La detección de vista busca un archivo de vista de área coincidente en el orden siguiente:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Asociación del controlador a un área

Los controladores de área se designan con el atributo de [ &lbrack; área &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Adición de la ruta de área

Las rutas de área normalmente usan el enrutamiento convencional en lugar del enrutamiento mediante atributos. El enrutamiento convencional depende del orden. En general, las rutas con áreas deben colocarse antes en la tabla de rutas, ya que son más específicas que las rutas sin un área.

Se puede `{area:...}` usar como un token en las plantillas de ruta, si el espacio de direcciones URL es uniforme en todas las áreas:

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

En el código anterior, `exists` aplica la restricción de que la ruta debe coincidir con un área. El uso de `{area:...}` es el mecanismo menos complicado para agregar enrutamiento a las áreas.

En el código siguiente se usa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> para crear dos rutas de área con nombre:

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

Cuando use `MapAreaRoute` con ASP.NET Core 2.2, vea [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/7772).

Para más información, vea [Enrutamiento de áreas](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Generación de vínculos con áreas de MVC

En el código siguiente de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) se muestra la generación de vínculos con el área especificada:

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Los vínculos generados con el código anterior son válidos en cualquier parte de la aplicación.

En la descarga de ejemplo se incluye una [vista parcial](xref:mvc/views/partial) que contiene los vínculos anteriores y los mismos vínculos sin especificar el área. En el [archivo de diseño](xref:mvc/views/layout) se hace referencia a la vista parcial, por lo que en todas las páginas de la aplicación se muestran los vínculos generados. Los vínculos generados sin especificar el área solo son válidos cuando se les hace referencia desde una página en el mismo área y controlador.

Cuando no se especifica el área o el controlador, el enrutamiento depende de los valores de *ambiente*. Los valores de ruta actuales de la solicitud actual se consideran valores de ambiente para la generación de vínculos. En muchos casos de la aplicación de ejemplo, el uso de valores de ambiente genera vínculos incorrectos.

Para más información, vea [Enrutar a acciones de controlador](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Diseño Compartido para áreas con el archivo _ViewStart.cshtml

Para compartir un diseño común para toda la aplicación, mueva el archivo *_ViewStart.cshtml* a la carpeta raíz de la aplicación.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

En su ubicación estándar, */Views/_ViewImports.cshtml* no se aplica a las áreas. Para usar [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro)comunes, `@using` o `@inject` en su área, asegúrese de que se aplica un archivo *_ViewImports. cshtml* adecuado [a las vistas de área](xref:mvc/views/layout#importing-shared-directives). Si quiere el mismo comportamiento en todas las vistas, mueva */Views/_ViewImports.cshtml* a la raíz de la aplicación.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Cambio de la carpeta de área predeterminada donde se almacenan las vistas

En el código siguiente se cambia la carpeta de área predeterminada de `"Areas"` a `"MyAreas"`:

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a>Áreas con Razor páginas

Las áreas con Razor páginas requieren una `Areas/<area name>/Pages` carpeta en la raíz de la aplicación. La siguiente estructura de carpetas se usa con la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):

* Nombre de proyecto
  * Áreas
    * Productos
      * Páginas
        * _ViewImports
        * Acerca de
        * Índice
    * Servicios
      * Páginas
        * Administrar
          * Acerca de
          * Índice

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a>Generación de vínculos con Razor páginas y áreas

El código siguiente de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) muestra la generación de vínculos con el área especificada (por ejemplo, `asp-area="Products"`):

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Los vínculos generados con el código anterior son válidos en cualquier parte de la aplicación.

En la descarga de ejemplo se incluye una [vista parcial](xref:mvc/views/partial) que contiene los vínculos anteriores y los mismos vínculos sin especificar el área. En el [archivo de diseño](xref:mvc/views/layout) se hace referencia a la vista parcial, por lo que en todas las páginas de la aplicación se muestran los vínculos generados. Los vínculos generados sin especificar el área solo son válidos cuando se les hace referencia desde una página de la misma área.

Cuando no se especifica el área, el enrutamiento depende de los valores *ambient*. Los valores de ruta actuales de la solicitud actual se consideran valores de ambiente para la generación de vínculos. En muchos casos de la aplicación de ejemplo, el uso de valores de ambiente genera vínculos incorrectos. Por ejemplo, tenga en cuenta los vínculos generados desde el código siguiente:

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

En el código anterior:

* El vínculo generado a partir de `<a asp-page="/Manage/About">` es correcto solo cuando la última solicitud fue de una página del área `Services`. Por ejemplo, `/Services/Manage/`, `/Services/Manage/Index` o `/Services/Manage/About`.
* El vínculo generado a partir de `<a asp-page="/About">` es correcto solo cuando la última solicitud fue de una página de `/Home`.
* El código está tomado de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importación del espacio de nombres y los asistentes de etiquetas con el archivo _ViewImports

Se puede Agregar un archivo *_ViewImports. cshtml* a cada carpeta de *páginas* de área para importar las aplicaciones auxiliares de espacio de nombres y etiquetas a cada Razor Página de la carpeta.

Tenga en cuenta el área *Services* del código de ejemplo, que no contiene un archivo *_ViewImports.cshtml*. En el marcado siguiente se muestra la página */Services/Manage/about* Razor :

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

En el marcado anterior:

* El nombre de dominio completo debe usarse para especificar el modelo (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).
* Habilita las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro)`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

En la descarga de ejemplo, el área Products contiene el siguiente archivo *_ViewImports.cshtml*:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

En el marcado siguiente se muestra la página */Products/about* Razor :

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

En el archivo anterior, el espacio de nombres y la directiva `@addTagHelper` se importan en el archivo mediante el archivo *Areas/Products/Pages/_ViewImports.cshtml*.

Para más información, consulte [Administración del ámbito de los asistentes de etiquetas](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) y [Importar directivas compartidas](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-no-locrazor-pages-areas"></a>Diseño compartido para Razor áreas de páginas

Para compartir un diseño común para toda la aplicación, mueva el archivo *_ViewStart.cshtml* a la carpeta raíz de la aplicación.

### <a name="publishing-areas"></a>Publicación de áreas

Todos los archivos *.cshtml y los archivos que formen parte del directorio *wwwroot* se publicarán como resultados si `<Project Sdk="Microsoft.NET.Sdk.Web">` está incluido en el archivo *.csproj.
::: moniker-end
