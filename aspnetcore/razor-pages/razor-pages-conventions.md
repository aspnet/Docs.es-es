---
title: Convenciones de aplicación y de ruta de Razor Pages en ASP.NET Core
author: rick-anderson
description: Vea cómo las convenciones de proveedor de modelos de aplicación y de ruta sirven para controlar el enrutamiento, la detección y el procesamiento de páginas.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 2947bf0b697ca01f17d260b9f31aa3cc79d457b6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059876"
---
# <a name="no-locrazor-pages-route-and-app-conventions-in-aspnet-core"></a>Convenciones de aplicación y de ruta de Razor Pages en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Aquí encontrará información para saber cómo usar las [convenciones de proveedor de modelos de aplicación y de ruta](xref:mvc/controllers/application-model#conventions) con objeto de controlar el enrutamiento, la detección y el procesamiento en aplicaciones de Razor Pages.

Si necesita configurar rutas de una página personalizadas para páginas concretas, configure el enrutamiento a esas páginas con la [convención AddPageRoute](#configure-a-page-route), descrita más adelante en este tema.

Para especificar una ruta de página, agregar segmentos de ruta o agregar parámetros a una ruta, use la directiva de página `@page`. Para obtener más información, consulte [Rutas personalizadas](xref:razor-pages/index#custom-routes).

Hay ciertas palabras reservadas que no se pueden usar como segmentos de ruta o nombres de parámetro. Para obtener más información, vea [Enrutamiento: Nombres de enrutamientos reservados](xref:mvc/controllers/routing#reserved-routing-names).

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

| Escenario | El ejemplo explica cómo... |
| -------- | --------------------------- |
| [Convenciones de modelo](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Agregar un encabezado y una plantilla de ruta a las páginas de una aplicación. |
| [Convenciones de acción de ruta de página](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Agregar una plantilla de ruta a las páginas de una carpeta y a una sola página. |
| [Convenciones de acción del modelo de página](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (clase de filtro, expresión lambda o fábrica de filtros)</li></ul> | Agregar un encabezado a las páginas de una carpeta, agregar un encabezado a una sola página y configurar una [fábrica de filtros](xref:mvc/controllers/filters#ifilterfactory) para agregar un encabezado a las páginas de la aplicación. |

Las convenciones de Razor Pages se configuran mediante una sobrecarga de <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> que configura <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> en `Startup.ConfigureServices`. Más avanzado el tema veremos los siguientes ejemplos de convención:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add( ... );
        options.Conventions.AddFolderRouteModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageRouteModelConvention(
            "/About", model => { ... });
        options.Conventions.AddPageRoute(
            "/Contact", "TheContactPage/{text?}");
        options.Conventions.AddFolderApplicationModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageApplicationModelConvention(
            "/About", model => { ... });
        options.Conventions.ConfigureFilter(model => { ... });
        options.Conventions.ConfigureFilter( ... );
    });
}
```

## <a name="route-order"></a>Ordenación de la ruta

Las rutas especifican un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> para su procesamiento (una coincidencia de rutas).

| Ordenar            | Comportamiento |
| :--------------: | -------- |
| -1               | La ruta se procesa antes de que se procesen otras rutas. |
| 0                | No se ha especificado ningún orden (valor predeterminado). Si no se asigna un valor `Order` (`Order = null`), el valor `Order` predeterminado de la ruta para su procesamiento es 0 (cero). |
| 1, 2, &hellip; n | Especifica el orden de procesamiento de la ruta. |

El procesamiento de rutas se establece por convención:

* Las rutas se procesan en orden secuencial (-1, 0, 1, 2&hellip; n).
* Cuando las rutas tienen el mismo orden (`Order`), primero se asigna la ruta más específica y, después, las rutas menos específicas.
* Cuando varias rutas con el mismo orden (`Order`) y el mismo número de parámetros coinciden con una URL de solicitud, se procesan en el orden en que se han agregado a la clase <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

Si es posible, evite depender de un orden de procesamiento de rutas establecido. Por lo general, el enrutamiento selecciona la ruta correcta con la coincidencia de dirección URL. Si tiene que establecer propiedades de orden (`Order`) de las rutas para enrutar las solicitudes correctamente, es probable que el esquema de enrutamiento de la aplicación resulte confuso para los clientes y cueste mantenerlo. Trate de simplificar el esquema de enrutamiento de la aplicación. La aplicación de ejemplo requiere un orden de procesamiento de rutas explícito para mostrar varios escenarios de enrutamiento con una sola aplicación. Sin embargo, intente evitar configurar un orden (`Order`) de enrutamiento en aplicaciones de producción.

El enrutamiento de Razor Pages y el del controlador MVC comparten una implementación. Puede encontrar más información sobre el orden de las rutas y MVC en el artículo [Enrutamiento a acciones de controlador: Ordenación de rutas de atributo](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenciones de modelo

Agregue un delegado de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> con el fin de agregar [convenciones de modelo](xref:mvc/controllers/application-model#conventions) que se apliquen a Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Adición de una convención de modelo de ruta a todas las páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a la colección de instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que se van a aplicar durante la construcción del modelo de ruta de página.

En la aplicación de ejemplo se agrega una plantilla de ruta `{globalTemplate?}` a todas las páginas de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La propiedad <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> está establecida en `1`. Esto garantiza el siguiente comportamiento de coincidencia de rutas en la aplicación de ejemplo:

* Más adelante en este mismo tema se incluye una plantilla de ruta para `TheContactPage/{text?}`. La ruta de la página Contact tiene un orden predeterminado de `null` (`Order = 0`), por lo que coincide antes de la plantilla de ruta `{globalTemplate?}`.
* Más adelante en este mismo tema se incluye una plantilla de ruta `{aboutTemplate?}`. La plantilla `{aboutTemplate?}` tiene un `Order` con un valor de `2`. Cuando la página About se solicita en `/About/RouteDataValue`, "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no `RouteData.Values["aboutTemplate"]` (`Order = 2`), debido a la configuración de la propiedad `Order`.
* Más adelante en este mismo tema se incluye una plantilla de ruta `{otherPagesTemplate?}`. La plantilla `{otherPagesTemplate?}` tiene un `Order` con un valor de `2`. Cuando se solicita una página de la carpeta *Pages/OtherPages* con un parámetro de ruta (por ejemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no en `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) debido a la configuración de la propiedad `Order`.

Siempre que sea posible, no especifique el valor de `Order`, lo que da como resultado `Order = 0`. Deje que el enrutamiento seleccione la ruta correcta.

Las opciones de Razor Pages (por ejemplo, agregar <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>) se agregan cuando Razor Pages se agrega a la colección de servicios en `Startup.ConfigureServices`. Para obtener un ejemplo, vea la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Solicite la página About del ejemplo en `localhost:5000/About/GlobalRouteValue` y revise el resultado:

![La página About se solicita con un segmento de ruta de GlobalRouteValue. La página presentada refleja que el valor de datos de ruta se captura en el método OnGet de la página.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Adición de una convención de modelo de aplicación a todas las páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a la colección de instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que se van a aplicar durante la construcción del modelo de aplicación de página.

Para demostrar esta y otras convenciones más adelante en este tema, la aplicación de ejemplo incluye una clase `AddHeaderAttribute`. El constructor de esta clase acepta una cadena `name` y una matriz de cadenas `values`. Estos valores se usan en su método `OnResultExecuting` para establecer un encabezado de respuesta. La clase completa se muestra en la sección [Convenciones de acción del modelo de página](#page-model-action-conventions) más adelante en este tema.

En la aplicación de ejemplo se usa la clase `AddHeaderAttribute` para agregar un encabezado (`GlobalHeader`) a todas las páginas de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Solicite la página About del ejemplo en `localhost:5000/About` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página About ponen de manifiesto que GlobalHeader se ha agregado.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Adición de una convención de modelo de controlador a todas las páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> a la colección de instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que se van a aplicar durante la construcción del modelo de controlador de página.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenciones de acción de ruta de página

El proveedor de modelos de ruta predeterminado que se deriva de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invoca convenciones diseñadas para proporcionar puntos de extensibilidad que permitan configurar rutas de página.

### <a name="folder-route-model-convention"></a>Convención de modelo de ruta de carpeta

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca una acción en <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para todas las páginas de la carpeta especificada.

En la aplicación de ejemplo se usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para agregar una plantilla de ruta `{otherPagesTemplate?}` a las páginas de la carpeta *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

La propiedad <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> está establecida en `2`. Esto garantiza que la plantilla `{globalTemplate?}` (establecida en `1` anteriormente en este tema) tiene prioridad para colocarse en la primera posición de valor de datos de ruta cuando se proporcione un solo valor de ruta. Si se solicita una página de la carpeta *Pages/OtherPages* con un valor de parámetro de ruta (por ejemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no en `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) debido a la configuración de la propiedad `Order`.

Siempre que sea posible, no especifique el valor de `Order`, lo que da como resultado `Order = 0`. Deje que el enrutamiento seleccione la ruta correcta.

Solicite la página Page1 del ejemplo en `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` y revise el resultado:

![Page1 en la carpeta OtherPages solicitada con un segmento de ruta de GlobalRouteValue y OtherPagesRouteValue La página presentada refleja que los valores de datos de ruta se capturan en el método OnGet de la página.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convención de modelo de ruta de página

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca una acción en <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para la página con el nombre especificado.

En la aplicación de ejemplo se usa `AddPageRouteModelConvention` para agregar una plantilla de ruta `{aboutTemplate?}` a la página About:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

La propiedad <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> está establecida en `2`. Esto garantiza que la plantilla `{globalTemplate?}` (establecida en `1` anteriormente en este tema) tiene prioridad para colocarse en la primera posición de valor de datos de ruta cuando se proporcione un solo valor de ruta. Si se solicita la página About con un valor de parámetro de ruta en `/About/RouteDataValue`, "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no en `RouteData.Values["aboutTemplate"]` (`Order = 2`), debido a la configuración de la propiedad `Order`.

Siempre que sea posible, no especifique el valor de `Order`, lo que da como resultado `Order = 0`. Deje que el enrutamiento seleccione la ruta correcta.

Solicite la página About del ejemplo en `localhost:5000/About/GlobalRouteValue/AboutRouteValue` y revise el resultado:

![La página About se solicita con los segmentos de ruta de GlobalRouteValue y AboutRouteValue. La página presentada refleja que los valores de datos de ruta se capturan en el método OnGet de la página.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Uso de transformadores de parámetros para personalizar rutas de página

Las rutas de página generadas por ASP.NET Core se pueden personalizar mediante el uso de un transformador de parámetros. Un transformador de parámetro implementa `IOutboundParameterTransformer` y transforma el valor de parámetros. Por ejemplo, un transformador de parámetros `SlugifyParameterTransformer` personalizado cambia el valor de ruta `SubscriptionManagement` a `subscription-management`.

La convención de modelo de ruta de página `PageRouteTransformerConvention` aplica un transformador de parámetros a los segmentos de nombre de archivo y carpeta de las rutas de página generadas automáticamente en una aplicación. Por ejemplo, para el archivo de Razor Pages en */Pages/SubscriptionManagement/ViewAll.cshtml* , su ruta cambiaría de `/SubscriptionManagement/ViewAll` a `/subscription-management/view-all`.

`PageRouteTransformerConvention` solo transforma los segmentos de una ruta de página generados automáticamente que proceden de la carpeta y del archivo llamados Razor Pages. No transforma segmentos de ruta agregados con la directiva `@page`. Esta convención tampoco transforma las rutas agregadas por <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.

La convención `PageRouteTransformerConvention` está registrada como una opción en `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add(
            new PageRouteTransformerConvention(
                new SlugifyParameterTransformer()));
    });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a>Configurar una ruta de página

Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> para configurar una ruta a una página en la ruta de acceso de página especificada. Los vínculos generados que apuntan a esa página usan la ruta especificada. `AddPageRoute` usa `AddPageRouteModelConvention` para establecer la ruta.

En la aplicación de ejemplo se crea una ruta a `/TheContactPage` para *Contact.cshtml* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

A la página Contact también se puede llegar en `/Contact` a través de su ruta predeterminada.

La ruta personalizada de la aplicación de ejemplo que lleva a la página Contact tiene cabida para un segmento de ruta `text` opcional (`{text?}`). La página también incluye este segmento opcional en su directiva `@page` por si el usuario que la visita tiene acceso a la página en su ruta `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Observe que la dirección URL generada para el vínculo **Contact** en la página presentada refleja la ruta actualizada:

![Vínculo Contact de la aplicación de ejemplo en la barra de navegación](razor-pages-conventions/_static/contact-link.png)

![Si revisamos el vínculo Contact en el HTML presentado, vemos que href está establecido en "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

Visite la página Contact a través de su ruta normal, `/Contact`, o de la ruta personalizada, `/TheContactPage`. Si se proporciona un segmento de ruta `text` adicional, la página muestra el segmento codificado en HTML que se ha proporcionado:

![Ejemplo del explorador Microsoft Edge en el que se proporciona un segmento de ruta opcional "text" de "TextValue" en la dirección URL La página presentada muestra el valor del segmento "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Convenciones de acción del modelo de página

El proveedor de modelos de página predeterminado que implementa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invoca convenciones que están diseñadas para proporcionar puntos de extensibilidad que permitan configurar modelos de página. Estas convenciones son útiles al crear y modificar escenarios de detección y procesamiento de páginas.

En los ejemplos de esta sección, la aplicación de ejemplo usa una clase `AddHeaderAttribute` (la cual es un atributo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>) que aplica un encabezado de respuesta:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Si empleamos convenciones, el ejemplo indica cómo aplicar el atributo a todas las páginas de una carpeta y a una sola página.

**Convención de modelo de aplicación de carpeta**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca una acción en las instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> para todas las páginas de la carpeta especificada.

En el ejemplo se demuestra el uso de `AddFolderApplicationModelConvention` agregando un encabezado (`OtherPagesHeader`) a las páginas de la carpeta *OtherPages* de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Solicite la página Page1 del ejemplo en `localhost:5000/OtherPages/Page1` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página OtherPages/Page1 ponen de manifiesto que OtherPagesHeader se ha agregado.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convención de modelo de aplicación de página**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca una acción en <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> para la página con el nombre especificado.

En el ejemplo se demuestra el uso de `AddPageApplicationModelConvention` agregando un encabezado (`AboutHeader`) a la página About:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Solicite la página About del ejemplo en `localhost:5000/About` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página About ponen de manifiesto que AboutHeader se ha agregado.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurar un filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> permite configurar el filtro que se quiere aplicar. Se puede implementar una clase de filtro, pero en la aplicación de ejemplo se muestra cómo implementar un filtro en una expresión lambda, cosa que sucede en segundo plano como una fábrica que devuelve un filtro:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

El modelo de páginas de aplicación se usa para comprobar la ruta de acceso relativa de los segmentos que conducen a la página Page2 en la carpeta *OtherPages*. Si la condición se supera, se agrega un encabezado. Si no, se aplica `EmptyFilter`.

`EmptyFilter` es un [filtro de acciones](xref:mvc/controllers/filters#action-filters). Razor Pages pasa por alto los filtros de acciones, por lo que `EmptyFilter` no funciona del modo previsto si la ruta de acceso no contiene `OtherPages/Page2`.

Solicite la página Page2 del ejemplo en `localhost:5000/OtherPages/Page2` y revise los encabezados para ver el resultado:

![OtherPagesPage2Header se agrega a la respuesta de Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurar una fábrica de filtros**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura la fábrica especificada para aplicar [filtros](xref:mvc/controllers/filters) a todas las instancias de Razor Pages.

En la aplicación de ejemplo se proporciona un ejemplo del uso de una [fábrica de filtros](xref:mvc/controllers/filters#ifilterfactory), para lo cual se agrega un encabezado (`FilterFactoryHeader`) con dos valores a las páginas de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Solicite la página About del ejemplo en `localhost:5000/About` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página About ponen de manifiesto que se han agregado dos encabezados FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtros de MVC y filtro de página (IPageFilter)

Razor Pages no tiene en cuenta los [filtros de acciones](xref:mvc/controllers/filters#action-filters) de MVC, porque Razor Pages usa métodos de control. Otros tipos de filtros de MVC que tiene a su disposición son: filtros de [autorización](xref:mvc/controllers/filters#authorization-filters), de [excepciones](xref:mvc/controllers/filters#exception-filters), de [recursos](xref:mvc/controllers/filters#resource-filters) y de [resultados](xref:mvc/controllers/filters#result-filters). Para más información, vea el tema [Filters](xref:mvc/controllers/filters) (Filtros).

El filtro de páginas (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) se aplica a Razor Pages. Para obtener más información, vea [Métodos de filtrado de Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Aquí encontrará información para saber cómo usar las [convenciones de proveedor de modelos de aplicación y de ruta](xref:mvc/controllers/application-model#conventions) con objeto de controlar el enrutamiento, la detección y el procesamiento en aplicaciones de Razor Pages.

Si necesita configurar rutas de una página personalizadas para páginas concretas, configure el enrutamiento a esas páginas con la [convención AddPageRoute](#configure-a-page-route), descrita más adelante en este tema.

Para especificar una ruta de página, agregar segmentos de ruta o agregar parámetros a una ruta, use la directiva de página `@page`. Para obtener más información, consulte [Rutas personalizadas](xref:razor-pages/index#custom-routes).

Hay ciertas palabras reservadas que no se pueden usar como segmentos de ruta o nombres de parámetro. Para obtener más información, vea [Enrutamiento: Nombres de enrutamientos reservados](xref:fundamentals/routing#reserved-routing-names).

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

| Escenario | El ejemplo explica cómo... |
| -------- | --------------------------- |
| [Convenciones de modelo](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Agregar un encabezado y una plantilla de ruta a las páginas de una aplicación. |
| [Convenciones de acción de ruta de página](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Agregar una plantilla de ruta a las páginas de una carpeta y a una sola página. |
| [Convenciones de acción del modelo de página](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (clase de filtro, expresión lambda o fábrica de filtros)</li></ul> | Agregar un encabezado a las páginas de una carpeta, agregar un encabezado a una sola página y configurar una [fábrica de filtros](xref:mvc/controllers/filters#ifilterfactory) para agregar un encabezado a las páginas de la aplicación. |

Las convenciones de Razor Pages se agregan y configuran a través del método de extensión <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> en la colección de servicios de la clase `Startup`. Más avanzado el tema veremos los siguientes ejemplos de convención:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordenación de la ruta

Las rutas especifican un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> para su procesamiento (una coincidencia de rutas).

| Ordenar            | Comportamiento |
| :--------------: | -------- |
| -1               | La ruta se procesa antes de que se procesen otras rutas. |
| 0                | No se ha especificado ningún orden (valor predeterminado). Si no se asigna un valor `Order` (`Order = null`), el valor `Order` predeterminado de la ruta para su procesamiento es 0 (cero). |
| 1, 2, &hellip; n | Especifica el orden de procesamiento de la ruta. |

El procesamiento de rutas se establece por convención:

* Las rutas se procesan en orden secuencial (-1, 0, 1, 2&hellip; n).
* Cuando las rutas tienen el mismo orden (`Order`), primero se asigna la ruta más específica y, después, las rutas menos específicas.
* Cuando varias rutas con el mismo orden (`Order`) y el mismo número de parámetros coinciden con una URL de solicitud, se procesan en el orden en que se han agregado a la clase <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

Si es posible, evite depender de un orden de procesamiento de rutas establecido. Por lo general, el enrutamiento selecciona la ruta correcta con la coincidencia de dirección URL. Si tiene que establecer propiedades de orden (`Order`) de las rutas para enrutar las solicitudes correctamente, es probable que el esquema de enrutamiento de la aplicación resulte confuso para los clientes y cueste mantenerlo. Trate de simplificar el esquema de enrutamiento de la aplicación. La aplicación de ejemplo requiere un orden de procesamiento de rutas explícito para mostrar varios escenarios de enrutamiento con una sola aplicación. Sin embargo, intente evitar configurar un orden (`Order`) de enrutamiento en aplicaciones de producción.

El enrutamiento de Razor Pages y el del controlador MVC comparten una implementación. Puede encontrar más información sobre el orden de las rutas y MVC en el artículo [Enrutamiento a acciones de controlador: Ordenación de rutas de atributo](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenciones de modelo

Agregue un delegado de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> con el fin de agregar [convenciones de modelo](xref:mvc/controllers/application-model#conventions) que se apliquen a Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Adición de una convención de modelo de ruta a todas las páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a la colección de instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que se van a aplicar durante la construcción del modelo de ruta de página.

En la aplicación de ejemplo se agrega una plantilla de ruta `{globalTemplate?}` a todas las páginas de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La propiedad <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> está establecida en `1`. Esto garantiza el siguiente comportamiento de coincidencia de rutas en la aplicación de ejemplo:

* Más adelante en este mismo tema se incluye una plantilla de ruta para `TheContactPage/{text?}`. La ruta de la página Contact tiene un orden predeterminado de `null` (`Order = 0`), por lo que coincide antes de la plantilla de ruta `{globalTemplate?}`.
* Más adelante en este mismo tema se incluye una plantilla de ruta `{aboutTemplate?}`. La plantilla `{aboutTemplate?}` tiene un `Order` con un valor de `2`. Cuando la página About se solicita en `/About/RouteDataValue`, "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no `RouteData.Values["aboutTemplate"]` (`Order = 2`), debido a la configuración de la propiedad `Order`.
* Más adelante en este mismo tema se incluye una plantilla de ruta `{otherPagesTemplate?}`. La plantilla `{otherPagesTemplate?}` tiene un `Order` con un valor de `2`. Cuando se solicita una página de la carpeta *Pages/OtherPages* con un parámetro de ruta (por ejemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no en `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) debido a la configuración de la propiedad `Order`.

Siempre que sea posible, no especifique el valor de `Order`, lo que da como resultado `Order = 0`. Deje que el enrutamiento seleccione la ruta correcta.

Las opciones de Razor Pages (por ejemplo, agregar <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>) se agregan cuando se incluye MVC en la colección de servicios de `Startup.ConfigureServices`. Para obtener un ejemplo, vea la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Solicite la página About del ejemplo en `localhost:5000/About/GlobalRouteValue` y revise el resultado:

![La página About se solicita con un segmento de ruta de GlobalRouteValue. La página presentada refleja que el valor de datos de ruta se captura en el método OnGet de la página.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Adición de una convención de modelo de aplicación a todas las páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a la colección de instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que se van a aplicar durante la construcción del modelo de aplicación de página.

Para demostrar esta y otras convenciones más adelante en este tema, la aplicación de ejemplo incluye una clase `AddHeaderAttribute`. El constructor de esta clase acepta una cadena `name` y una matriz de cadenas `values`. Estos valores se usan en su método `OnResultExecuting` para establecer un encabezado de respuesta. La clase completa se muestra en la sección [Convenciones de acción del modelo de página](#page-model-action-conventions) más adelante en este tema.

En la aplicación de ejemplo se usa la clase `AddHeaderAttribute` para agregar un encabezado (`GlobalHeader`) a todas las páginas de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Solicite la página About del ejemplo en `localhost:5000/About` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página About ponen de manifiesto que GlobalHeader se ha agregado.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Adición de una convención de modelo de controlador a todas las páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> a la colección de instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que se van a aplicar durante la construcción del modelo de controlador de página.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenciones de acción de ruta de página

El proveedor de modelos de ruta predeterminado que se deriva de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invoca convenciones diseñadas para proporcionar puntos de extensibilidad que permitan configurar rutas de página.

### <a name="folder-route-model-convention"></a>Convención de modelo de ruta de carpeta

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca una acción en <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para todas las páginas de la carpeta especificada.

En la aplicación de ejemplo se usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para agregar una plantilla de ruta `{otherPagesTemplate?}` a las páginas de la carpeta *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

La propiedad <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> está establecida en `2`. Esto garantiza que la plantilla `{globalTemplate?}` (establecida en `1` anteriormente en este tema) tiene prioridad para colocarse en la primera posición de valor de datos de ruta cuando se proporcione un solo valor de ruta. Si se solicita una página de la carpeta *Pages/OtherPages* con un valor de parámetro de ruta (por ejemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no en `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) debido a la configuración de la propiedad `Order`.

Siempre que sea posible, no especifique el valor de `Order`, lo que da como resultado `Order = 0`. Deje que el enrutamiento seleccione la ruta correcta.

Solicite la página Page1 del ejemplo en `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` y revise el resultado:

![Page1 en la carpeta OtherPages solicitada con un segmento de ruta de GlobalRouteValue y OtherPagesRouteValue La página presentada refleja que los valores de datos de ruta se capturan en el método OnGet de la página.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convención de modelo de ruta de página

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca una acción en <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para la página con el nombre especificado.

En la aplicación de ejemplo se usa `AddPageRouteModelConvention` para agregar una plantilla de ruta `{aboutTemplate?}` a la página About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

La propiedad <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> está establecida en `2`. Esto garantiza que la plantilla `{globalTemplate?}` (establecida en `1` anteriormente en este tema) tiene prioridad para colocarse en la primera posición de valor de datos de ruta cuando se proporcione un solo valor de ruta. Si se solicita la página About con un valor de parámetro de ruta en `/About/RouteDataValue`, "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no en `RouteData.Values["aboutTemplate"]` (`Order = 2`), debido a la configuración de la propiedad `Order`.

Siempre que sea posible, no especifique el valor de `Order`, lo que da como resultado `Order = 0`. Deje que el enrutamiento seleccione la ruta correcta.

Solicite la página About del ejemplo en `localhost:5000/About/GlobalRouteValue/AboutRouteValue` y revise el resultado:

![La página About se solicita con los segmentos de ruta de GlobalRouteValue y AboutRouteValue. La página presentada refleja que los valores de datos de ruta se capturan en el método OnGet de la página.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Uso de transformadores de parámetros para personalizar rutas de página

Las rutas de página generadas por ASP.NET Core se pueden personalizar mediante el uso de un transformador de parámetros. Un transformador de parámetro implementa `IOutboundParameterTransformer` y transforma el valor de parámetros. Por ejemplo, un transformador de parámetros `SlugifyParameterTransformer` personalizado cambia el valor de ruta `SubscriptionManagement` a `subscription-management`.

La convención de modelo de ruta de página `PageRouteTransformerConvention` aplica un transformador de parámetros a los segmentos de nombre de archivo y carpeta de las rutas de página generadas automáticamente en una aplicación. Por ejemplo, para el archivo de Razor Pages en */Pages/SubscriptionManagement/ViewAll.cshtml* , su ruta cambiaría de `/SubscriptionManagement/ViewAll` a `/subscription-management/view-all`.

`PageRouteTransformerConvention` solo transforma los segmentos de una ruta de página generados automáticamente que proceden de la carpeta y del archivo llamados Razor Pages. No transforma segmentos de ruta agregados con la directiva `@page`. Esta convención tampoco transforma las rutas agregadas por <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.

La convención `PageRouteTransformerConvention` está registrada como una opción en `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a>Configurar una ruta de página

Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> para configurar una ruta a una página en la ruta de acceso de página especificada. Los vínculos generados que apuntan a esa página usan la ruta especificada. `AddPageRoute` usa `AddPageRouteModelConvention` para establecer la ruta.

En la aplicación de ejemplo se crea una ruta a `/TheContactPage` para *Contact.cshtml* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

A la página Contact también se puede llegar en `/Contact` a través de su ruta predeterminada.

La ruta personalizada de la aplicación de ejemplo que lleva a la página Contact tiene cabida para un segmento de ruta `text` opcional (`{text?}`). La página también incluye este segmento opcional en su directiva `@page` por si el usuario que la visita tiene acceso a la página en su ruta `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Observe que la dirección URL generada para el vínculo **Contact** en la página presentada refleja la ruta actualizada:

![Vínculo Contact de la aplicación de ejemplo en la barra de navegación](razor-pages-conventions/_static/contact-link.png)

![Si revisamos el vínculo Contact en el HTML presentado, vemos que href está establecido en "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

Visite la página Contact a través de su ruta normal, `/Contact`, o de la ruta personalizada, `/TheContactPage`. Si se proporciona un segmento de ruta `text` adicional, la página muestra el segmento codificado en HTML que se ha proporcionado:

![Ejemplo del explorador Microsoft Edge en el que se proporciona un segmento de ruta opcional "text" de "TextValue" en la dirección URL La página presentada muestra el valor del segmento "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Convenciones de acción del modelo de página

El proveedor de modelos de página predeterminado que implementa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invoca convenciones que están diseñadas para proporcionar puntos de extensibilidad que permitan configurar modelos de página. Estas convenciones son útiles al crear y modificar escenarios de detección y procesamiento de páginas.

En los ejemplos de esta sección, la aplicación de ejemplo usa una clase `AddHeaderAttribute` (la cual es un atributo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>) que aplica un encabezado de respuesta:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Si empleamos convenciones, el ejemplo indica cómo aplicar el atributo a todas las páginas de una carpeta y a una sola página.

**Convención de modelo de aplicación de carpeta**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca una acción en las instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> para todas las páginas de la carpeta especificada.

En el ejemplo se demuestra el uso de `AddFolderApplicationModelConvention` agregando un encabezado (`OtherPagesHeader`) a las páginas de la carpeta *OtherPages* de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Solicite la página Page1 del ejemplo en `localhost:5000/OtherPages/Page1` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página OtherPages/Page1 ponen de manifiesto que OtherPagesHeader se ha agregado.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convención de modelo de aplicación de página**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca una acción en <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> para la página con el nombre especificado.

En el ejemplo se demuestra el uso de `AddPageApplicationModelConvention` agregando un encabezado (`AboutHeader`) a la página About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Solicite la página About del ejemplo en `localhost:5000/About` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página About ponen de manifiesto que AboutHeader se ha agregado.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurar un filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> permite configurar el filtro que se quiere aplicar. Se puede implementar una clase de filtro, pero en la aplicación de ejemplo se muestra cómo implementar un filtro en una expresión lambda, cosa que sucede en segundo plano como una fábrica que devuelve un filtro:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

El modelo de páginas de aplicación se usa para comprobar la ruta de acceso relativa de los segmentos que conducen a la página Page2 en la carpeta *OtherPages*. Si la condición se supera, se agrega un encabezado. Si no, se aplica `EmptyFilter`.

`EmptyFilter` es un [filtro de acciones](xref:mvc/controllers/filters#action-filters). Razor Pages pasa por alto los filtros de acciones, por lo que `EmptyFilter` no funciona del modo previsto si la ruta de acceso no contiene `OtherPages/Page2`.

Solicite la página Page2 del ejemplo en `localhost:5000/OtherPages/Page2` y revise los encabezados para ver el resultado:

![OtherPagesPage2Header se agrega a la respuesta de Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurar una fábrica de filtros**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura la fábrica especificada para aplicar [filtros](xref:mvc/controllers/filters) a todas las instancias de Razor Pages.

En la aplicación de ejemplo se proporciona un ejemplo del uso de una [fábrica de filtros](xref:mvc/controllers/filters#ifilterfactory), para lo cual se agrega un encabezado (`FilterFactoryHeader`) con dos valores a las páginas de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Solicite la página About del ejemplo en `localhost:5000/About` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página About ponen de manifiesto que se han agregado dos encabezados FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtros de MVC y filtro de página (IPageFilter)

Razor Pages no tiene en cuenta los [filtros de acciones](xref:mvc/controllers/filters#action-filters) de MVC, porque Razor Pages usa métodos de control. Otros tipos de filtros de MVC que tiene a su disposición son: filtros de [autorización](xref:mvc/controllers/filters#authorization-filters), de [excepciones](xref:mvc/controllers/filters#exception-filters), de [recursos](xref:mvc/controllers/filters#resource-filters) y de [resultados](xref:mvc/controllers/filters#result-filters). Para más información, vea el tema [Filters](xref:mvc/controllers/filters) (Filtros).

El filtro de páginas (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) se aplica a Razor Pages. Para obtener más información, vea [Métodos de filtrado de Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Aquí encontrará información para saber cómo usar las [convenciones de proveedor de modelos de aplicación y de ruta](xref:mvc/controllers/application-model#conventions) con objeto de controlar el enrutamiento, la detección y el procesamiento en aplicaciones de Razor Pages.

Si necesita configurar rutas de una página personalizadas para páginas concretas, configure el enrutamiento a esas páginas con la [convención AddPageRoute](#configure-a-page-route), descrita más adelante en este tema.

Para especificar una ruta de página, agregar segmentos de ruta o agregar parámetros a una ruta, use la directiva de página `@page`. Para obtener más información, consulte [Rutas personalizadas](xref:razor-pages/index#custom-routes).

Hay ciertas palabras reservadas que no se pueden usar como segmentos de ruta o nombres de parámetro. Para obtener más información, vea [Enrutamiento: Nombres de enrutamientos reservados](xref:fundamentals/routing#reserved-routing-names).

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

| Escenario | El ejemplo explica cómo... |
| -------- | --------------------------- |
| [Convenciones de modelo](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Agregar un encabezado y una plantilla de ruta a las páginas de una aplicación. |
| [Convenciones de acción de ruta de página](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Agregar una plantilla de ruta a las páginas de una carpeta y a una sola página. |
| [Convenciones de acción del modelo de página](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (clase de filtro, expresión lambda o fábrica de filtros)</li></ul> | Agregar un encabezado a las páginas de una carpeta, agregar un encabezado a una sola página y configurar una [fábrica de filtros](xref:mvc/controllers/filters#ifilterfactory) para agregar un encabezado a las páginas de la aplicación. |

Las convenciones de Razor Pages se agregan y configuran a través del método de extensión <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> en la colección de servicios de la clase `Startup`. Más avanzado el tema veremos los siguientes ejemplos de convención:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordenación de la ruta

Las rutas especifican un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> para su procesamiento (una coincidencia de rutas).

| Ordenar            | Comportamiento |
| :--------------: | -------- |
| -1               | La ruta se procesa antes de que se procesen otras rutas. |
| 0                | No se ha especificado ningún orden (valor predeterminado). Si no se asigna un valor `Order` (`Order = null`), el valor `Order` predeterminado de la ruta para su procesamiento es 0 (cero). |
| 1, 2, &hellip; n | Especifica el orden de procesamiento de la ruta. |

El procesamiento de rutas se establece por convención:

* Las rutas se procesan en orden secuencial (-1, 0, 1, 2&hellip; n).
* Cuando las rutas tienen el mismo orden (`Order`), primero se asigna la ruta más específica y, después, las rutas menos específicas.
* Cuando varias rutas con el mismo orden (`Order`) y el mismo número de parámetros coinciden con una URL de solicitud, se procesan en el orden en que se han agregado a la clase <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

Si es posible, evite depender de un orden de procesamiento de rutas establecido. Por lo general, el enrutamiento selecciona la ruta correcta con la coincidencia de dirección URL. Si tiene que establecer propiedades de orden (`Order`) de las rutas para enrutar las solicitudes correctamente, es probable que el esquema de enrutamiento de la aplicación resulte confuso para los clientes y cueste mantenerlo. Trate de simplificar el esquema de enrutamiento de la aplicación. La aplicación de ejemplo requiere un orden de procesamiento de rutas explícito para mostrar varios escenarios de enrutamiento con una sola aplicación. Sin embargo, intente evitar configurar un orden (`Order`) de enrutamiento en aplicaciones de producción.

El enrutamiento de Razor Pages y el del controlador MVC comparten una implementación. Puede encontrar más información sobre el orden de las rutas y MVC en el artículo [Enrutamiento a acciones de controlador: Ordenación de rutas de atributo](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenciones de modelo

Agregue un delegado de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> con el fin de agregar [convenciones de modelo](xref:mvc/controllers/application-model#conventions) que se apliquen a Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Adición de una convención de modelo de ruta a todas las páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a la colección de instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que se van a aplicar durante la construcción del modelo de ruta de página.

En la aplicación de ejemplo se agrega una plantilla de ruta `{globalTemplate?}` a todas las páginas de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La propiedad <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> está establecida en `1`. Esto garantiza el siguiente comportamiento de coincidencia de rutas en la aplicación de ejemplo:

* Más adelante en este mismo tema se incluye una plantilla de ruta para `TheContactPage/{text?}`. La ruta de la página Contact tiene un orden predeterminado de `null` (`Order = 0`), por lo que coincide antes de la plantilla de ruta `{globalTemplate?}`.
* Más adelante en este mismo tema se incluye una plantilla de ruta `{aboutTemplate?}`. La plantilla `{aboutTemplate?}` tiene un `Order` con un valor de `2`. Cuando la página About se solicita en `/About/RouteDataValue`, "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no `RouteData.Values["aboutTemplate"]` (`Order = 2`), debido a la configuración de la propiedad `Order`.
* Más adelante en este mismo tema se incluye una plantilla de ruta `{otherPagesTemplate?}`. La plantilla `{otherPagesTemplate?}` tiene un `Order` con un valor de `2`. Cuando se solicita una página de la carpeta *Pages/OtherPages* con un parámetro de ruta (por ejemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no en `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) debido a la configuración de la propiedad `Order`.

Siempre que sea posible, no especifique el valor de `Order`, lo que da como resultado `Order = 0`. Deje que el enrutamiento seleccione la ruta correcta.

Las opciones de Razor Pages (por ejemplo, agregar <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>) se agregan cuando se incluye MVC en la colección de servicios de `Startup.ConfigureServices`. Para obtener un ejemplo, vea la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Solicite la página About del ejemplo en `localhost:5000/About/GlobalRouteValue` y revise el resultado:

![La página About se solicita con un segmento de ruta de GlobalRouteValue. La página presentada refleja que el valor de datos de ruta se captura en el método OnGet de la página.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Adición de una convención de modelo de aplicación a todas las páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a la colección de instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que se van a aplicar durante la construcción del modelo de aplicación de página.

Para demostrar esta y otras convenciones más adelante en este tema, la aplicación de ejemplo incluye una clase `AddHeaderAttribute`. El constructor de esta clase acepta una cadena `name` y una matriz de cadenas `values`. Estos valores se usan en su método `OnResultExecuting` para establecer un encabezado de respuesta. La clase completa se muestra en la sección [Convenciones de acción del modelo de página](#page-model-action-conventions) más adelante en este tema.

En la aplicación de ejemplo se usa la clase `AddHeaderAttribute` para agregar un encabezado (`GlobalHeader`) a todas las páginas de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Solicite la página About del ejemplo en `localhost:5000/About` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página About ponen de manifiesto que GlobalHeader se ha agregado.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Adición de una convención de modelo de controlador a todas las páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> a la colección de instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que se van a aplicar durante la construcción del modelo de controlador de página.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenciones de acción de ruta de página

El proveedor de modelos de ruta predeterminado que se deriva de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invoca convenciones diseñadas para proporcionar puntos de extensibilidad que permitan configurar rutas de página.

### <a name="folder-route-model-convention"></a>Convención de modelo de ruta de carpeta

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca una acción en <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para todas las páginas de la carpeta especificada.

En la aplicación de ejemplo se usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para agregar una plantilla de ruta `{otherPagesTemplate?}` a las páginas de la carpeta *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

La propiedad <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> está establecida en `2`. Esto garantiza que la plantilla `{globalTemplate?}` (establecida en `1` anteriormente en este tema) tiene prioridad para colocarse en la primera posición de valor de datos de ruta cuando se proporcione un solo valor de ruta. Si se solicita una página de la carpeta *Pages/OtherPages* con un valor de parámetro de ruta (por ejemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no en `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) debido a la configuración de la propiedad `Order`.

Siempre que sea posible, no especifique el valor de `Order`, lo que da como resultado `Order = 0`. Deje que el enrutamiento seleccione la ruta correcta.

Solicite la página Page1 del ejemplo en `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` y revise el resultado:

![Page1 en la carpeta OtherPages solicitada con un segmento de ruta de GlobalRouteValue y OtherPagesRouteValue La página presentada refleja que los valores de datos de ruta se capturan en el método OnGet de la página.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convención de modelo de ruta de página

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca una acción en <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para la página con el nombre especificado.

En la aplicación de ejemplo se usa `AddPageRouteModelConvention` para agregar una plantilla de ruta `{aboutTemplate?}` a la página About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

La propiedad <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> está establecida en `2`. Esto garantiza que la plantilla `{globalTemplate?}` (establecida en `1` anteriormente en este tema) tiene prioridad para colocarse en la primera posición de valor de datos de ruta cuando se proporcione un solo valor de ruta. Si se solicita la página About con un valor de parámetro de ruta en `/About/RouteDataValue`, "RouteDataValue" se carga en `RouteData.Values["globalTemplate"]` (`Order = 1`) y no en `RouteData.Values["aboutTemplate"]` (`Order = 2`), debido a la configuración de la propiedad `Order`.

Siempre que sea posible, no especifique el valor de `Order`, lo que da como resultado `Order = 0`. Deje que el enrutamiento seleccione la ruta correcta.

Solicite la página About del ejemplo en `localhost:5000/About/GlobalRouteValue/AboutRouteValue` y revise el resultado:

![La página About se solicita con los segmentos de ruta de GlobalRouteValue y AboutRouteValue. La página presentada refleja que los valores de datos de ruta se capturan en el método OnGet de la página.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Configurar una ruta de página

Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> para configurar una ruta a una página en la ruta de acceso de página especificada. Los vínculos generados que apuntan a esa página usan la ruta especificada. `AddPageRoute` usa `AddPageRouteModelConvention` para establecer la ruta.

En la aplicación de ejemplo se crea una ruta a `/TheContactPage` para *Contact.cshtml* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

A la página Contact también se puede llegar en `/Contact` a través de su ruta predeterminada.

La ruta personalizada de la aplicación de ejemplo que lleva a la página Contact tiene cabida para un segmento de ruta `text` opcional (`{text?}`). La página también incluye este segmento opcional en su directiva `@page` por si el usuario que la visita tiene acceso a la página en su ruta `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Observe que la dirección URL generada para el vínculo **Contact** en la página presentada refleja la ruta actualizada:

![Vínculo Contact de la aplicación de ejemplo en la barra de navegación](razor-pages-conventions/_static/contact-link.png)

![Si revisamos el vínculo Contact en el HTML presentado, vemos que href está establecido en "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

Visite la página Contact a través de su ruta normal, `/Contact`, o de la ruta personalizada, `/TheContactPage`. Si se proporciona un segmento de ruta `text` adicional, la página muestra el segmento codificado en HTML que se ha proporcionado:

![Ejemplo del explorador Microsoft Edge en el que se proporciona un segmento de ruta opcional "text" de "TextValue" en la dirección URL La página presentada muestra el valor del segmento "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Convenciones de acción del modelo de página

El proveedor de modelos de página predeterminado que implementa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invoca convenciones que están diseñadas para proporcionar puntos de extensibilidad que permitan configurar modelos de página. Estas convenciones son útiles al crear y modificar escenarios de detección y procesamiento de páginas.

En los ejemplos de esta sección, la aplicación de ejemplo usa una clase `AddHeaderAttribute` (la cual es un atributo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>) que aplica un encabezado de respuesta:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Si empleamos convenciones, el ejemplo indica cómo aplicar el atributo a todas las páginas de una carpeta y a una sola página.

**Convención de modelo de aplicación de carpeta**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca una acción en las instancias <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> para todas las páginas de la carpeta especificada.

En el ejemplo se demuestra el uso de `AddFolderApplicationModelConvention` agregando un encabezado (`OtherPagesHeader`) a las páginas de la carpeta *OtherPages* de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Solicite la página Page1 del ejemplo en `localhost:5000/OtherPages/Page1` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página OtherPages/Page1 ponen de manifiesto que OtherPagesHeader se ha agregado.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convención de modelo de aplicación de página**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> para crear y agregar un elemento <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca una acción en <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> para la página con el nombre especificado.

En el ejemplo se demuestra el uso de `AddPageApplicationModelConvention` agregando un encabezado (`AboutHeader`) a la página About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Solicite la página About del ejemplo en `localhost:5000/About` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página About ponen de manifiesto que AboutHeader se ha agregado.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurar un filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> permite configurar el filtro que se quiere aplicar. Se puede implementar una clase de filtro, pero en la aplicación de ejemplo se muestra cómo implementar un filtro en una expresión lambda, cosa que sucede en segundo plano como una fábrica que devuelve un filtro:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

El modelo de páginas de aplicación se usa para comprobar la ruta de acceso relativa de los segmentos que conducen a la página Page2 en la carpeta *OtherPages*. Si la condición se supera, se agrega un encabezado. Si no, se aplica `EmptyFilter`.

`EmptyFilter` es un [filtro de acciones](xref:mvc/controllers/filters#action-filters). Razor Pages pasa por alto los filtros de acciones, por lo que `EmptyFilter` no funciona del modo previsto si la ruta de acceso no contiene `OtherPages/Page2`.

Solicite la página Page2 del ejemplo en `localhost:5000/OtherPages/Page2` y revise los encabezados para ver el resultado:

![OtherPagesPage2Header se agrega a la respuesta de Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurar una fábrica de filtros**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura la fábrica especificada para aplicar [filtros](xref:mvc/controllers/filters) a todas las instancias de Razor Pages.

En la aplicación de ejemplo se proporciona un ejemplo del uso de una [fábrica de filtros](xref:mvc/controllers/filters#ifilterfactory), para lo cual se agrega un encabezado (`FilterFactoryHeader`) con dos valores a las páginas de la aplicación:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Solicite la página About del ejemplo en `localhost:5000/About` y revise los encabezados para ver el resultado:

![Los encabezados de respuesta de la página About ponen de manifiesto que se han agregado dos encabezados FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtros de MVC y filtro de página (IPageFilter)

Razor Pages no tiene en cuenta los [filtros de acciones](xref:mvc/controllers/filters#action-filters) de MVC, porque Razor Pages usa métodos de control. Otros tipos de filtros de MVC que tiene a su disposición son: filtros de [autorización](xref:mvc/controllers/filters#authorization-filters), de [excepciones](xref:mvc/controllers/filters#exception-filters), de [recursos](xref:mvc/controllers/filters#resource-filters) y de [resultados](xref:mvc/controllers/filters#result-filters). Para más información, vea el tema [Filters](xref:mvc/controllers/filters) (Filtros).

El filtro de páginas (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) se aplica a Razor Pages. Para obtener más información, vea [Métodos de filtrado de Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
