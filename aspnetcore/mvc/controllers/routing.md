---
title: Enrutar a acciones de controlador de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo ASP.NET Core MVC usa el middleware de enrutamiento para encontrar direcciones URL de las solicitudes entrantes y asignarlas a acciones.
ms.author: riande
ms.date: 3/25/2020
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
uid: mvc/controllers/routing
ms.openlocfilehash: 4d367a6b15fdcf9ef6be1bac749368fd48fa259e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020371"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Enrutar a acciones de controlador de ASP.NET Core

Por [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) y [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Los controladores de ASP.NET Core usan el [middleware](xref:fundamentals/middleware/index) de enrutamiento para hacer coincidir las direcciones URL de las solicitudes entrantes y asignarlas a [las acciones](#action).  Plantillas de rutas:

* Se definen en el código de inicio o en los atributos.
* Describa cómo las rutas de dirección URL coinciden con [las acciones](#action).
* Se usan para generar direcciones URL para los vínculos. Los vínculos generados se devuelven normalmente en las respuestas.

Las acciones se [enrutan convencionalmente](#cr) o se [enrutan mediante atributos](#ar). La colocación de una ruta en el controlador o la [acción](#action) hace que se enrute el atributo. Consulte [Enrutamiento mixto](#routing-mixed-ref-label) para obtener más información.

Este documento:

* Explica las interacciones entre MVC y el enrutamiento:
  * La forma en que las aplicaciones MVC típicas usan las características de enrutamiento.
  * Abarca ambos:
    * El [enrutamiento convencional](#cr) normalmente se usa con controladores y vistas.
    * *Enrutamiento de atributos* usado con las API de REST. Si está interesado principalmente en el enrutamiento de las API de REST, vaya a la sección [enrutamiento de atributo para las API de REST](#ar) .
  * Consulte [enrutamiento](xref:fundamentals/routing) para obtener detalles de enrutamiento avanzados.
* Hace referencia al sistema de enrutamiento predeterminado agregado en ASP.NET Core 3,0, denominado enrutamiento de punto de conexión. Es posible usar controladores con la versión anterior de enrutamiento para fines de compatibilidad. Consulte la [Guía de migración 2.2-3.0](xref:migration/22-to-30) para obtener instrucciones. Consulte la [versión 2,2 de este documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) para obtener material de referencia sobre el sistema de enrutamiento heredado.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Configuración de la ruta convencional

`Startup.Configure`normalmente tiene código similar al siguiente al usar el [enrutamiento convencional](#crd):

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Dentro de la llamada a <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> se usa para crear una única ruta. La ruta única se denomina `default` Route. La mayoría de las aplicaciones con controladores y vistas usan una plantilla de ruta similar a la `default` ruta. Las API de REST deben usar el [enrutamiento de atributos](#ar).

La plantilla de ruta `"{controller=Home}/{action=Index}/{id?}"` :

* Coincide con una ruta de dirección URL como`/Products/Details/5`
* Extrae los valores de ruta `{ controller = Products, action = Details, id = 5 }` mediante el token de la ruta de acceso. La extracción de valores de ruta da como resultado una coincidencia si la aplicación tiene un controlador denominado `ProductsController` y una `Details` acción:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`el modelo enlaza el valor de `id = 5` para establecer el `id` parámetro en `5` . Vea [enlace de modelos](xref:mvc/models/model-binding) para obtener más detalles.
* `{controller=Home}`define `Home` como valor predeterminado `controller` .
* `{action=Index}`define `Index` como valor predeterminado `action` .
*  El `?` carácter de `{id?}` define `id` como opcional.
  * No es necesario que los parámetros de ruta opcionales y predeterminados estén presentes en la ruta de dirección URL para una coincidencia. Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.
* Coincide con la ruta de acceso de la dirección URL `/` .
* Genera los valores de ruta `{ controller = Home, action = Index }` .

Los valores de `controller` y `action` usan los valores predeterminados. `id`no genera un valor, ya que no hay ningún segmento correspondiente en la ruta de acceso de la dirección URL. `/`solo coincide si existe una `HomeController` acción and `Index` :

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Con la definición de controlador y la plantilla de ruta anteriores, la `HomeController.Index` acción se ejecuta para las siguientes rutas de acceso URL:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

La ruta de dirección URL `/` utiliza la acción y los controladores predeterminados de la plantilla de ruta `Home` `Index` . La ruta de dirección URL `/Home` utiliza la acción predeterminada de la plantilla de ruta `Index` .

El método de conveniencia <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Reemplazo

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> El enrutamiento se configura mediante <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> el <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware de y. Para usar controladores:
>
> * Llame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> `UseEndpoints` a dentro de para asignar los controladores [enrutados de atributo](#ar) .
> * Llame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> a o a para asignar controladores enrutados por [Convención](#cr) y controladores [enrutados por atributo](#ar) .

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Enrutamiento convencional

El enrutamiento convencional se usa con controladores y vistas. La ruta `default`:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

es un ejemplo de un *enrutamiento convencional*. Se denomina *enrutamiento convencional* porque establece una *Convención* para las rutas de dirección URL:

* El primer segmento de la ruta de acceso, `{controller=Home}` , se asigna al nombre del controlador.
* El segundo segmento, `{action=Index}` , se asigna al nombre de la [acción](#action) .
* El tercer segmento, `{id?}` se usa para un opcional `id` . `?`En `{id?}` hace que sea opcional. `id`se utiliza para asignar a una entidad del modelo.

Con esta `default` ruta, la ruta de acceso URL:

* `/Products/List`asigna a la `ProductsController.List` acción.
* `/Blog/Article/17`asigna a `BlogController.Article` y normalmente el modelo enlaza el `id` parámetro a 17.

Esta asignación:

* **Solo**se basa en los nombres de [acción](#action) y controlador.
* No se basa en espacios de nombres, ubicaciones de archivos de código fuente o parámetros de método.

El uso del enrutamiento convencional con la ruta predeterminada permite crear la aplicación sin tener que presentar un nuevo patrón de dirección URL para cada acción. Para una aplicación con acciones de estilo [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) , que tiene coherencia para las direcciones URL entre controladores:

* Ayuda a simplificar el código.
* Hace que la interfaz de usuario sea más predecible.

> [!WARNING]
> La `id` plantilla de ruta define el en el código anterior como opcional. Las acciones se pueden ejecutar sin el identificador opcional proporcionado como parte de la dirección URL. Generalmente, cuando `id` se omite en la dirección URL:
>
> * `id`está establecido en `0` por enlace de modelos.
> * No se encontró ninguna entidad en la base de datos que coincida con `id == 0` .
>
> El [enrutamiento de atributo](#ar) proporciona un control específico para que el identificador sea necesario para algunas acciones y no para otros. Por Convención, la documentación incluye parámetros opcionales como `id` cuando es probable que aparezcan en el uso correcto.

La mayoría de las aplicaciones deben elegir un esquema de enrutamiento básico y descriptivo para que las direcciones URL sean legibles y significativas. La ruta convencional predeterminada `{controller=Home}/{action=Index}/{id?}`:

* Admite un esquema de enrutamiento básico y descriptivo.
* Se trata de un punto de partida útil para las aplicaciones basadas en la interfaz de usuario.
* Es la única plantilla de ruta necesaria para muchas aplicaciones de interfaz de usuario Web. En el caso de las aplicaciones de interfaz de usuario Web más grandes, otra ruta que usa [áreas](#areas) , si suele ser necesario.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>y <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :

* Asigne automáticamente un valor de **pedido** a sus puntos de conexión en función del orden en que se invocan.

Enrutamiento de puntos de conexión en ASP.NET Core 3.0 y versiones posteriores:

* No tiene un concepto de rutas.
* No proporciona garantías de ordenación para la ejecución de la extensibilidad, todos los puntos de conexión se procesan a la vez.

Habilite el [registro](xref:fundamentals/logging/index) para ver de qué forma las implementaciones de enrutamiento integradas, como <xref:Microsoft.AspNetCore.Routing.Route>, coinciden con las solicitudes.

El [enrutamiento de atributos](#ar) se explica más adelante en este documento.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Varias rutas convencionales

Se pueden agregar varias [rutas convencionales](#cr) en agregando `UseEndpoints` más llamadas a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> y <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> . Esto permite definir varias convenciones o agregar rutas convencionales dedicadas a una [acción](#action)específica, como:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

La `blog` ruta en el código anterior es una **ruta convencional dedicada**. Se denomina una ruta convencional dedicada porque:

* Utiliza el [enrutamiento convencional](#cr).
* Está dedicado a una [acción](#action)específica.

Dado `controller` `action` que y no aparecen en la plantilla de ruta `"blog/{*article}"` como parámetros:

* Solo pueden tener los valores predeterminados `{ controller = "Blog", action = "Article" }` .
* Esta ruta siempre se asigna a la acción `BlogController.Article` .

`/Blog`, `/Blog/Article` y `/Blog/{any-string}` son las únicas rutas de acceso URL que coinciden con la ruta del blog.

El ejemplo anterior:

* `blog`la ruta tiene una prioridad más alta para las coincidencias que la `default` ruta porque se agrega en primer lugar.
* Es un ejemplo de enrutamiento de estilo de [indicaciones](https://developer.mozilla.org/docs/Glossary/Slug) en el que es habitual tener un nombre de artículo como parte de la dirección URL.

> [!WARNING]
> En ASP.NET Core 3,0 y versiones posteriores, el enrutamiento no:
> * Defina un concepto denominado *Route*. `UseRouting` agrega coincidencia de rutas a la canalización de middleware. El `UseRouting` middleware examina el conjunto de puntos de conexión definidos en la aplicación y selecciona la mejor coincidencia de punto de conexión en función de la solicitud.
> * Proporcione garantías sobre el orden de ejecución de la extensibilidad, como <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> o <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .
>
>Consulte [enrutamiento](xref:fundamentals/routing) para obtener material de referencia sobre el enrutamiento.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Orden de enrutamiento convencional

El enrutamiento convencional solo coincide con una combinación de acción y controlador que se define en la aplicación. Esto se ha diseñado para simplificar los casos en los que las rutas convencionales se superponen.
Agregar rutas mediante <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> y <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> asigna automáticamente un valor de pedido a sus puntos de conexión en función del orden en el que se invocan. Las coincidencias de una ruta que aparece antes tienen una prioridad más alta. El enrutamiento convencional depende del orden. En general, las rutas con áreas deben colocarse anteriormente ya que son más específicas que las rutas sin un área. Las [rutas convencionales dedicadas](#dcr) con parámetros de ruta catch-all como `{*article}` pueden hacer que una ruta sea demasiado [expansiva](xref:fundamentals/routing#greedy), lo que significa que coincide con las direcciones URL que desea que coincidan con otras rutas. Coloque las rutas expansivas más adelante en la tabla de rutas para evitar coincidencias expansivas.

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Resolver acciones ambiguas

Cuando dos puntos de conexión coinciden a través del enrutamiento, el enrutamiento debe realizar una de las siguientes acciones:

* Elija el mejor candidato.
* Iniciar una excepción.

Por ejemplo:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

El controlador anterior define dos acciones que coinciden con:

* La ruta de dirección URL`/Products33/Edit/17`
* Datos de ruta `{ controller = Products33, action = Edit, id = 17 }` .

Este es un patrón típico para los controladores de MVC:

* `Edit(int)`muestra un formulario para editar un producto.
* `Edit(int, Product)`procesa el formulario publicado.

Para resolver la ruta correcta:

* `Edit(int, Product)`se selecciona cuando la solicitud es un HTTP `POST` .
* `Edit(int)`se selecciona cuando el [verbo http](#verb) es cualquier otra cosa. `Edit(int)`normalmente se llama a través de `GET` .

, <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]` , Se proporciona al enrutamiento para que pueda elegir según el método HTTP de la solicitud. El `HttpPostAttribute` crea `Edit(int, Product)` una coincidencia mejor que `Edit(int)` .

Es importante comprender el rol de los atributos como `HttpPostAttribute` . Se definen atributos similares para otros [verbos http](#verb). En el [enrutamiento convencional](#cr), es habitual que las acciones usen el mismo nombre de acción cuando formen parte de un flujo de trabajo Mostrar formulario, enviar formulario. Por ejemplo, vea [examinar los dos métodos de acción de edición](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Si el enrutamiento no puede elegir un mejor candidato, <xref:System.Reflection.AmbiguousMatchException> se produce una enumeración de varios puntos de conexión coincidentes.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Nombres de ruta convencionales

Las cadenas `"blog"` y `"default"` en los ejemplos siguientes son nombres de ruta convencionales:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Los nombres de ruta proporcionan un nombre lógico a la ruta. La ruta con nombre se puede usar para la generación de direcciones URL. El uso de una ruta con nombre simplifica la creación de direcciones URL cuando el orden de las rutas podría complicar la generación de direcciones URL. Los nombres de ruta deben ser de gran ancho en la aplicación.

Nombres de ruta:

* No tiene ningún impacto en la coincidencia de direcciones URL ni en el control de solicitudes.
* Solo se usan para la generación de direcciones URL.

El concepto de nombre de ruta se representa en enrutamiento como [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata). El nombre de **ruta** de los términos y el **nombre del punto de conexión**:

* Son intercambiables.
* La que se usa en la documentación y el código depende de la API que se describe.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Enrutamiento de atributos para las API de REST

Las API de REST deben usar el enrutamiento de atributos para modelar la funcionalidad de la aplicación como un conjunto de recursos en el que las operaciones se representan mediante [verbos http](#verb).

El enrutamiento mediante atributos utiliza un conjunto de atributos para asignar acciones directamente a las plantillas de ruta. El `StartUp.Configure` código siguiente es típico para una API de REST y se usa en el ejemplo siguiente:

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

En el código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> se llama a dentro de `UseEndpoints` para asignar controladores enrutados de atributo.

En el ejemplo siguiente:

* `Configure`Se usa el método anterior.
* `HomeController`coincide con un conjunto de direcciones URL similar al que coincide con la ruta convencional predeterminada `{controller=Home}/{action=Index}/{id?}` .

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

La `HomeController.Index` acción se ejecuta para cualquiera de las rutas de dirección URL `/` ,, `/Home` `/Home/Index` o `/Home/Index/3` .

Este ejemplo resalta una diferencia de programación clave entre el enrutamiento de atributos y el [enrutamiento convencional](#cr). El enrutamiento de atributos requiere más entradas para especificar una ruta. La ruta predeterminada convencional controla las rutas de forma más concisa. Sin embargo, el enrutamiento de atributos permite y requiere un control preciso de las plantillas de ruta que se aplican a cada [acción](#action).

Con el enrutamiento de atributos, los nombres de acción y controlador no juegan ninguna parte en la que se haga coincidir la acción, a menos que se use el [reemplazo de tokens](#routing-token-replacement-templates-ref-label) . El ejemplo siguiente coincide con las mismas direcciones URL que en el ejemplo anterior:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

En el código siguiente se usa el reemplazo de tokens para `action` y `controller` :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

El código siguiente se aplica `[Route("[controller]/[action]")]` al controlador:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

En el código anterior, las `Index` plantillas de método deben anteponer `/` o `~/` a las plantillas de ruta. Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador.

Consulte [precedencia](xref:fundamentals/routing#rtp) de la plantilla de ruta para obtener información sobre la selección de plantilla de ruta.

## <a name="reserved-routing-names"></a>Nombres de enrutamientos reservados

Las siguientes palabras clave son nombres de parámetro de ruta reservados al usar controladores o Razor páginas:

* `action`
* `area`
* `controller`
* `handler`
* `page`

`page`El uso de como parámetro de ruta con enrutamiento de atributos es un error común. Esto da como resultado un comportamiento incoherente y confuso con la generación de direcciones URL.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

La generación de direcciones URL utiliza los nombres de parámetros especiales para determinar si una operación de generación de direcciones URL hace referencia a una Razor página o a un controlador.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Plantillas de verbo HTTP

ASP.NET Core tiene las siguientes plantillas de verbo HTTP:

* [HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Plantillas de ruta

ASP.NET Core tiene las siguientes plantillas de ruta:

* Todas las [plantillas de verbo http](#verb) son plantillas de ruta.
* [Distribuya](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Enrutamiento de atributos con atributos de verbo http

Considere el siguiente controlador:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

En el código anterior:

* Cada acción contiene el `[HttpGet]` atributo, que limita la coincidencia a las solicitudes HTTP GET únicamente.
* La `GetProduct` acción incluye la `"{id}"` plantilla, por lo `id` que se anexa a la `"api/[controller]"` plantilla en el controlador. La plantilla de métodos es `"api/[controller]/"{id}""` . Por lo tanto, esta acción solo coincide con las solicitudes GET de para el formulario `/api/test2/xyz` , `/api/test2/123` , `/api/test2/{any string}` , etc.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* La `GetIntProduct` acción contiene la `"int/{id:int}")` plantilla. La `:int` parte de la plantilla restringe los `id` valores de ruta a cadenas que se pueden convertir en un entero. Una solicitud GET a `/api/test2/int/abc` :
  * No coincide con esta acción.
  * Devuelve un error [404 no encontrado](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* La `GetInt2Product` acción contiene `{id}` en la plantilla, pero no `id` se limita a los valores que se pueden convertir en un entero. Una solicitud GET a `/api/test2/int2/abc` :
  * Coincide con esta ruta.
  * El enlace de modelos no se puede convertir `abc` en un entero. El `id` parámetro del método es Integer.
  * Devuelve una [Solicitud incorrecta de 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) porque el enlace de modelos no se pudo convertir `abc` en un entero.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

El enrutamiento mediante atributos puede utilizar <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> atributos como <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> y <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> . Todos los atributos de [verbo http](#verb) aceptan una plantilla de ruta. En el ejemplo siguiente se muestran dos acciones que coinciden con la misma plantilla de ruta:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Uso de la ruta de dirección URL `/products3` :

* La `MyProductsController.ListProducts` acción se ejecuta cuando el [verbo http](#verb) es `GET` .
* La `MyProductsController.CreateProduct` acción se ejecuta cuando el [verbo http](#verb) es `POST` .

Al compilar una API de REST, es poco habitual que deba usar `[Route(...)]` en un método de acción porque la acción acepta todos los métodos http. Es mejor usar el [atributo verbo http](#verb) más específico para ser precisos sobre lo que admite la API. Se espera que los clientes de API de REST sepan qué rutas y verbos HTTP se asignan a determinadas operaciones lógicas.

Las API de REST deben usar el enrutamiento de atributos para modelar la funcionalidad de la aplicación como un conjunto de recursos en el que las operaciones se representan mediante verbos HTTP. Esto significa que muchas operaciones, por ejemplo, GET y POST en el mismo recurso lógico, usan la misma dirección URL. El enrutamiento mediante atributos proporciona un nivel de control que es necesario para diseñar cuidadosamente un diseño de puntos de conexión públicos de la API.

Puesto que una ruta de atributo se aplica a una acción específica, es fácil crear parámetros necesarios como parte de la definición de plantilla de ruta. En el ejemplo siguiente, `id` se requiere como parte de la ruta de dirección URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

La `Products2ApiController.GetProduct(int)` acción:

* Se ejecuta con una ruta de dirección URL como`/products2/3`
* No se ejecuta con la ruta de dirección URL `/products2` .

El atributo [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que una acción limite los tipos de contenido de la solicitud compatibles. Para obtener más información, vea [definir tipos de contenido de solicitud admitidos con el atributo consumes](xref:web-api/index#consumes).

 Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción completa de las plantillas de ruta y las opciones relacionadas.

Para obtener más información sobre `[ApiController]` , vea el [atributo ApiController](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Nombre de ruta

El código siguiente define un nombre de ruta de `Products_List`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Los nombres de ruta se pueden utilizar para generar una dirección URL basada en una ruta específica. Nombres de ruta:

* No tiene ningún impacto en el comportamiento de coincidencia de la dirección URL del enrutamiento.
* Solo se usan para la generación de direcciones URL.

Los nombres de ruta deben ser únicos en toda la aplicación.

Compare el código anterior con la ruta predeterminada convencional, que define el `id` parámetro como opcional ( `{id?}` ). La capacidad de especificar con precisión las API tiene ventajas, como permitir `/products` y `/products/5` Enviar a acciones diferentes.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Combinar rutas de atributo

Para que el enrutamiento mediante atributos sea menos repetitivo, los atributos de ruta del controlador se combinan con los atributos de ruta de las acciones individuales. Las plantillas de ruta definidas en el controlador se anteponen a las plantillas de ruta de las acciones. La colocación de un atributo de ruta en el controlador hace que **todas** las acciones del controlador usen el enrutamiento mediante atributos.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

En el ejemplo anterior:

* La ruta de dirección URL `/products` puede coincidir`ProductsApi.ListProducts`
* La ruta de dirección URL `/products/5` puede coincidir `ProductsApi.GetProduct(int)` .

Ambas acciones solo coinciden con HTTP `GET` porque están marcadas con el `[HttpGet]` atributo.

Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador. El ejemplo siguiente coincide con un conjunto de rutas de dirección URL similar a la ruta predeterminada.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

En la tabla siguiente se explican los `[Route]` atributos del código anterior:

| Atributo               | Combina con`[Route("Home")]` | Define la plantilla de ruta |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Sí | `"Home"` |
| `[Route("Index")]` | Sí | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Sí | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Orden de la ruta de atributo

El enrutamiento crea un árbol y coincide con todos los puntos de conexión simultáneamente:

* Las entradas de ruta se comportan como si se hubieran colocado en una ordenación ideal.
* Las rutas más específicas tienen la oportunidad de ejecutarse antes que las rutas más generales.

Por ejemplo, una ruta de atributo como `blog/search/{topic}` es más específica que una ruta de atributo como `blog/{*article}` . La `blog/search/{topic}` ruta tiene una prioridad más alta, de forma predeterminada, porque es más específica. Mediante el [enrutamiento convencional](#cr), el desarrollador es responsable de colocar las rutas en el orden deseado.

Las rutas de atributo pueden configurar un orden mediante la <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> propiedad. Todos los [atributos de ruta](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) proporcionados por el marco de trabajo `Order` . Las rutas se procesan de acuerdo con el orden ascendente de la propiedad `Order`. El orden predeterminado es `0`. La configuración de una ruta mediante `Order = -1` se ejecuta antes que las rutas que no establecen un orden. Establecer una ruta mediante `Order = 1` ejecuciones después del orden de la ruta predeterminada.

**Evite** en función de `Order` . Si el espacio de la dirección URL de una aplicación requiere que los valores de orden explícitos se enruten correctamente, es probable que también sea confuso para los clientes. En general, el enrutamiento de atributos selecciona la ruta correcta con coincidencia de dirección URL. Si el orden predeterminado usado para la generación de direcciones URL no funciona, el uso de un nombre de ruta como una invalidación suele ser más sencillo que aplicar la `Order` propiedad.

Tenga en cuenta los dos controladores siguientes que definen la coincidencia de ruta `/home` :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Al solicitar `/home` el código anterior, se produce una excepción similar a la siguiente:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

`Order`La adición a uno de los atributos de ruta resuelve la ambigüedad:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Con el código anterior, `/home` ejecuta el `HomeController.Index` punto de conexión. Para llegar a la `MyDemoController.MyIndex` solicitud, `/home/MyIndex` . **Nota**:

* El código anterior es un ejemplo o un diseño de enrutamiento deficiente. Se usó para ilustrar la `Order` propiedad.
* La `Order` propiedad solo resuelve la ambigüedad, no se puede encontrar una coincidencia con esa plantilla. Sería mejor quitar la `[Route("Home")]` plantilla.

Vea [ Razor páginas ruta y convenciones de aplicación: orden de ruta](xref:razor-pages/razor-pages-conventions#route-order) para obtener información sobre el orden de las rutas con Razor las páginas.

En algunos casos, se devuelve un error HTTP 500 con rutas ambiguas. Utilice el [registro](xref:fundamentals/logging/index) para ver qué extremos produjeron el `AmbiguousMatchException` .

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Reemplazo de tokens en las plantillas de ruta [Controller], [Action], [Area]

Para mayor comodidad, las rutas de atributo admiten el reemplazo de tokens para los parámetros de ruta reservados mediante la inclusión de un token en una de las siguientes opciones:

* Corchetes:`[]`
* Llaves:`{}`

Los tokens `[action]` , `[area]` y `[controller]` se reemplazan por los valores del nombre de acción, el nombre del área y el nombre del controlador de la acción en la que se define la ruta:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

En el código anterior:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Coincide con`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Coincide con`/Products0/Edit/{id}`

El reemplazo del token se produce como último paso de la creación de las rutas de atributo. El ejemplo anterior se comporta igual que el código siguiente:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Las rutas de atributo también se pueden combinar con la herencia. Esto es eficaz combinado con el reemplazo de tokens. El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`genera un nombre de ruta único para cada acción:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
 genera un nombre de ruta único para cada acción.

Para que el delimitador de reemplazo de token `[` o `]` coincida, repita el carácter (`[[` o `]]`) para usarlo como secuencia de escape.

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Usar un transformador de parámetro para personalizar el reemplazo de tokens

El reemplazo de tokens se puede personalizarse mediante un transformador de parámetro. Un transformador de parámetro implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> y transforma el valor de parámetros. Por ejemplo, un `SlugifyParameterTransformer` transformador de parámetros personalizados cambia el `SubscriptionManagement` valor de ruta a `subscription-management` :

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> es una convención de modelo de aplicación que:

* Aplica un transformador de parámetro a todas las rutas de atributo en una aplicación.
* Personaliza los valores de token de ruta de atributo que se reemplazan.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

El `ListAll` método anterior coincide con `/subscription-management/list-all` .

`RouteTokenTransformerConvention` está registrado como una opción en `ConfigureServices`.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Consulte [documentos web de MDN en](https://developer.mozilla.org/docs/Glossary/Slug) el documento para ver la definición de Slug.

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Múltiples rutas de atributo

El enrutamiento mediante atributos permite definir varias rutas que llegan a la misma acción. El uso más común de esto es imitar el comportamiento de la ruta convencional predeterminada tal como se muestra en el ejemplo siguiente:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

La colocación de varios atributos de ruta en el controlador significa que cada uno de ellos se combina con cada uno de los atributos de ruta de los métodos de acción:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Todas las restricciones de ruta de [verbo http](#verb) implementan `IActionConstraint` .

Cuando se colocan varios atributos de ruta que implementan <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> en una acción:

* Cada restricción de acción se combina con la plantilla de ruta que se aplica al controlador.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

El uso de varias rutas en acciones podría parecer útil y eficaz, es mejor mantener el espacio de la dirección URL de la aplicación de forma básica y bien definida. Use varias rutas en acciones **solo** cuando sea necesario, por ejemplo, para admitir clientes existentes.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Especificación de parámetros opcionales de ruta de atributo, valores predeterminados y restricciones

Las rutas de atributo admiten la misma sintaxis en línea que las rutas convencionales para especificar parámetros opcionales, valores predeterminados y restricciones.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

En el código anterior, `[HttpPost("product/{id:int}")]` aplica una restricción de ruta. La `ProductsController.ShowProduct` acción coincide solo en rutas de dirección URL como `/product/3` . La parte de la plantilla de ruta `{id:int}` restringe ese segmento a solo enteros.

Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Atributos de ruta personalizados mediante IRouteTemplateProvider

Todos los [atributos de ruta](#rt) implementan <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> . El tiempo de ejecución de ASP.NET Core:

* Busca atributos en las clases de controlador y métodos de acción cuando se inicia la aplicación.
* Utiliza los atributos que implementan `IRouteTemplateProvider` para generar el conjunto inicial de rutas.

Implemente `IRouteTemplateProvider` para definir atributos de ruta personalizados. Cada `IRouteTemplateProvider` le permite definir una única ruta con una plantilla de ruta, un orden y un nombre personalizados:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

El `Get` método anterior devuelve `Order = 2, Template = api/MyTestApi` .

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Usar el modelo de aplicación para personalizar las rutas de atributo

El modelo de aplicación:

* Es un modelo de objetos creado en el inicio.
* Contiene todos los metadatos usados por ASP.NET Core para enrutar y ejecutar las acciones en una aplicación.

El modelo de aplicación incluye todos los datos recopilados de los atributos de ruta. La implementación proporciona los datos de los atributos de ruta `IRouteTemplateProvider` . Convention

* Se puede escribir para modificar el modelo de aplicación con el fin de personalizar el comportamiento del enrutamiento.
* Se leen en el inicio de la aplicación.

En esta sección se muestra un ejemplo básico de la personalización del enrutamiento mediante el modelo de aplicación. El código siguiente hace que las rutas se alinean aproximadamente con la estructura de carpetas del proyecto.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

El código siguiente impide que la `namespace` Convención se aplique a los controladores que están enrutados por atributo:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Por ejemplo, el controlador siguiente no utiliza `NamespaceRoutingConvention` :

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

El método `NamespaceRoutingConvention.Apply` realiza las acciones siguientes:

* No hace nada si el controlador está enrutando el atributo.
* Establece la plantilla Controllers basada en `namespace` , con la base `namespace` quitada.

`NamespaceRoutingConvention`Se puede aplicar en `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Por ejemplo, considere el siguiente controlador:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

En el código anterior:

* La base `namespace` es `My.Application` .
* El nombre completo del controlador anterior es `My.Application.Admin.Controllers.UsersController` .
* `NamespaceRoutingConvention`Establece la plantilla Controllers en `Admin/Controllers/Users/[action]/{id?` .

`NamespaceRoutingConvention`También se puede aplicar como atributo en un controlador:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Enrutamiento mixto: enrutamiento mediante atributos frente a enrutamiento convencional

ASP.NET Core aplicaciones pueden mezclar el uso de enrutamiento convencional y enrutamiento de atributos. Es habitual usar las rutas convencionales para controladores que sirven páginas HTML para los exploradores, y el enrutamiento mediante atributos para los controladores que sirven las API de REST.

Las acciones se enrutan bien mediante convención o bien mediante atributos. Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos. Las acciones que definen rutas de atributo no se pueden alcanzar a través de las rutas convencionales y viceversa. **Cualquier** atributo de ruta en el controlador hace que **todas** las acciones en el atributo del controlador se enruten.

El enrutamiento de atributos y el enrutamiento convencional usan el mismo motor de enrutamiento.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Generación de direcciones URL y valores de ambiente

Las aplicaciones pueden usar las características de generación de direcciones URL de enrutamiento para generar vínculos URL a acciones. La generación de direcciones URL elimina las direcciones URL de codificar, por lo que el código es más sólido y fácil de mantener. Esta sección se centra en las características de generación de direcciones URL proporcionadas por MVC y solo cubre los aspectos básicos del funcionamiento de la generación de direcciones URL. Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción detallada de la generación de direcciones URL.

La <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interfaz es el elemento subyacente de la infraestructura entre MVC y el enrutamiento para la generación de direcciones URL. Una instancia de `IUrlHelper` está disponible a través de la `Url` propiedad en los controladores, las vistas y los componentes de vista.

En el ejemplo siguiente, la `IUrlHelper` interfaz se usa a través de la `Controller.Url` propiedad para generar una dirección URL a otra acción.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Si la aplicación usa la ruta convencional predeterminada, el valor de la `url` variable es la cadena de ruta de acceso de dirección URL `/UrlGeneration/Destination` . La ruta de acceso de dirección URL se crea mediante el enrutamiento mediante la combinación de:

* Los valores de ruta de la solicitud actual, que se denominan **valores de ambiente**.
* Los valores que se pasan a `Url.Action` y sustituyen esos valores en la plantilla de ruta:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

El valor de cada uno de los parámetros de ruta incluidos en la plantilla de ruta se sustituye por nombres que coincidan con los valores y los valores de ambiente. Un parámetro de ruta que no tiene un valor puede:

* Use un valor predeterminado si tiene uno.
* Se omitirá si es opcional. Por ejemplo, `id` de la plantilla de ruta `{controller}/{action}/{id?}` .

Se produce un error en la generación de direcciones URL si algún parámetro de ruta requerido no tiene un valor correspondiente. Si se produce un error en la generación de direcciones URL para una ruta, se prueba con la ruta siguiente hasta que se hayan probado todas las rutas o se encuentra una coincidencia.

En el ejemplo anterior de `Url.Action` se presupone el [enrutamiento convencional](#cr). La generación de direcciones URL funciona de forma similar con el [enrutamiento de atributos](#ar), aunque los conceptos son diferentes. Con enrutamiento convencional:

* Los valores de ruta se usan para expandir una plantilla.
* Los valores de ruta de `controller` y `action` suelen aparecer en esa plantilla. Esto funciona porque las direcciones URL que coinciden con el enrutamiento se adhieren a una Convención.

En el ejemplo siguiente se utiliza el enrutamiento de atributos:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

La `Source` acción en el código anterior genera `custom/url/to/destination` .

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>se agregó en ASP.NET Core 3,0 como alternativa a `IUrlHelper` . `LinkGenerator`ofrece una funcionalidad similar pero más flexible. Cada método de `IUrlHelper` tiene también una familia de métodos correspondiente `LinkGenerator` .

### <a name="generating-urls-by-action-name"></a>Generación de direcciones URL por nombre de acción

[URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)y todas las sobrecargas relacionadas están diseñadas para generar el punto de conexión de destino mediante la especificación de un nombre de controlador y un nombre de acción.

Al utilizar `Url.Action` , el tiempo de ejecución proporciona los valores de ruta actuales para `controller` y `action` :

* El valor de `controller` y `action` forman parte de los valores y valores de [ambiente](#ambient) . El método `Url.Action` siempre usa los valores actuales de `action` y `controller` y genera una ruta de acceso de dirección URL que enruta a la acción actual.

El enrutamiento intenta usar los valores de los valores de ambiente para rellenar la información que no se proporcionó al generar una dirección URL. Considere una ruta como `{a}/{b}/{c}/{d}` con valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }` :

* El enrutamiento tiene suficiente información para generar una dirección URL sin valores adicionales.
* El enrutamiento tiene suficiente información porque todos los parámetros de ruta tienen un valor.

Si `{ d = Donovan }` se agrega el valor:

* `{ d = David }`Se omite el valor.
* La ruta de dirección URL generada es `Alice/Bob/Carol/Donovan` .

**ADVERTENCIA**: las rutas de dirección URL son jerárquicas. En el ejemplo anterior, si `{ c = Cheryl }` se agrega el valor:

* Ambos valores `{ c = Carol, d = David }` se omiten.
* Ya no hay un valor para `d` y se produce un error en la generación de direcciones URL.
* Se deben especificar los valores deseados de `c` y `d` para generar una dirección URL.  

Podría esperar que se produzca este problema con la ruta predeterminada `{controller}/{action}/{id?}` . Este problema es raro en la práctica porque `Url.Action` siempre especifica explícitamente un `controller` valor de y `action` .

Varias sobrecargas de [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) toman un objeto de valores de ruta para proporcionar valores para los parámetros de ruta distintos de `controller` y `action` . El objeto Route Values se usa con frecuencia con `id` . Por ejemplo: `Url.Action("Buy", "Products", new { id = 17 })`. Objeto de valores de ruta:

* Por Convención suele ser un objeto de tipo anónimo.
* Puede ser un `IDictionary<>` o un [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).

Los valores de ruta adicionales que no coinciden con los parámetros de ruta se colocan en la cadena de consulta.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

El código anterior genera `/Products/Buy/17?color=red` .

El código siguiente genera una dirección URL absoluta:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Para crear una dirección URL absoluta, use una de las siguientes opciones:

* Una sobrecarga que acepta un `protocol` . Por ejemplo, el código anterior.
* [LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), que genera identificadores URI absolutos de forma predeterminada.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Generar direcciones URL por ruta

En el código anterior se mostró la generación de una dirección URL pasando el controlador y el nombre de la acción. `IUrlHelper`también proporciona la familia de métodos [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) . Estos métodos son similares a [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), pero no copian los valores actuales de `action` y `controller` en los valores de ruta. El uso más común de `Url.RouteUrl` :

* Especifica un nombre de ruta para generar la dirección URL.
* Por lo general, no especifica un nombre de acción o controlador.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

El Razor archivo siguiente genera un vínculo HTML a `Destination_Route` :

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a>Generar direcciones URL en HTML yRazor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>proporciona los <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> métodos [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) y [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) para generar `<form>` `<a>` los elementos y, respectivamente. Estos métodos usan el método [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) para generar una dirección URL y aceptan argumentos similares. Los métodos `Url.RouteUrl` complementarios de `HtmlHelper` son `Html.BeginRouteForm` y `Html.RouteLink`, cuya funcionalidad es similar.

Las TagHelper generan direcciones URL a través de la TagHelper `form` y la TagHelper `<a>`. Ambos usan `IUrlHelper` para su implementación. Consulte [aplicaciones auxiliares de etiquetas en formularios](xref:mvc/views/working-with-forms) para obtener más información.

Dentro de las vistas, `IUrlHelper` está disponible a través de la propiedad `Url` para una generación de direcciones URL ad hoc no cubierta por los pasos anteriores.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Generación de direcciones URL en los resultados de la acción

En los ejemplos anteriores se mostró el uso `IUrlHelper` de en un controlador. El uso más común de un controlador es generar una dirección URL como parte de un resultado de acción.

Las clases base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> y <xref:Microsoft.AspNetCore.Mvc.Controller> proporcionan métodos de conveniencia para los resultados de acción que hacen referencia a otra acción. Un uso típico consiste en redirigir después de aceptar la entrada del usuario:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Los métodos del generador de resultados de la acción como <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> y <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> siguen un patrón similar a los métodos de `IUrlHelper` .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Caso especial para rutas convencionales dedicadas

El [enrutamiento convencional](#cr) puede usar un tipo especial de definición de ruta denominada [ruta convencional dedicada](#dcr). En el ejemplo siguiente, la ruta denominada `blog` es una ruta convencional dedicada:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Con las definiciones de ruta anteriores, `Url.Action("Index", "Home")` genera la ruta de acceso de la dirección URL `/` mediante la `default` ruta, pero ¿por qué? Se puede suponer que los valores de ruta `{ controller = Home, action = Index }` son suficientes para generar una dirección URL utilizando `blog`, con el resultado `/blog?action=Index&controller=Home`.

Las [rutas convencionales dedicadas](#dcr) dependen de un comportamiento especial de los valores predeterminados que no tienen un parámetro de ruta correspondiente que impide que la ruta sea demasiado [expansiva](xref:fundamentals/routing#greedy) con generación de direcciones URL. En este caso, los valores predeterminados son `{ controller = Blog, action = Article }`, y ni `controller` ni `action` aparecen como un parámetro de ruta. Cuando el enrutamiento realiza la generación de direcciones URL, los valores proporcionados deben coincidir con los valores predeterminados. `blog`Se produce un error en la generación de direcciones URL mediante los valores `{ controller = Home, action = Index }` no coinciden `{ controller = Blog, action = Article }` . Después, el enrutamiento vuelve para probar `default`, operación que se realiza correctamente.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Áreas

Las [áreas](xref:mvc/controllers/areas) son una característica de MVC que se usa para organizar la funcionalidad relacionada en un grupo como independiente:

* Espacio de nombres de enrutamiento para las acciones de controlador.
* Estructura de carpetas para las vistas.

El uso de áreas permite que una aplicación tenga varios controladores con el mismo nombre, siempre y cuando tengan áreas diferentes. El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area`, a `controller` y `action`. En esta sección se describe cómo interactúa el enrutamiento con las áreas. Vea [áreas](xref:mvc/controllers/areas) para obtener más información sobre cómo se usan las áreas con las vistas.

En el ejemplo siguiente se configura MVC para usar la ruta convencional predeterminada y una `area` ruta para un `area` denominado `Blog` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

En el código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> se llama a para crear el `"blog_route"` . El segundo parámetro, `"Blog"` , es el nombre del área.

Al buscar una coincidencia con una ruta de dirección URL como `/Manage/Users/AddUser` , la `"blog_route"` ruta genera los valores de ruta `{ area = Blog, controller = Users, action = AddUser }` . El `area` valor de ruta se genera mediante un valor predeterminado para `area` . La ruta creada por `MapAreaControllerRoute` es equivalente a la siguiente:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute` utiliza el nombre de área proporcionado, que en este caso es `Blog`, para crear una ruta con un valor predeterminado y una restricción para `area`. El valor predeterminado garantiza que la ruta siempre produce `{ area = Blog, ... }`; la restricción requiere el valor `{ area = Blog, ... }` para la generación de la dirección URL.

El enrutamiento convencional depende del orden. En general, las rutas con áreas deben colocarse anteriormente ya que son más específicas que las rutas sin un área.

Con el ejemplo anterior, los valores de ruta `{ area = Blog, controller = Users, action = AddUser }` coinciden con la siguiente acción:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

El atributo [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) es lo que denota un controlador como parte de un área. Este controlador está en el `Blog` área. Los controladores sin un `[Area]` atributo no son miembros de ningún área y no **not** coinciden cuando el `area` enrutamiento proporciona el valor de ruta. En el ejemplo siguiente, solo el primer controlador enumerado puede coincidir con los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Aquí se muestra el espacio de nombres de cada controlador por integridad. Si los controladores anteriores usan el mismo espacio de nombres, se generaría un error del compilador. Los espacios de nombres de clase no tienen ningún efecto en el enrutamiento de MVC.

Los dos primeros controladores son miembros de las áreas y solo coinciden cuando el valor de ruta `area` proporciona su respectivo nombre de área. El tercer controlador no es miembro de ningún área y solo puede coincidir cuando el enrutamiento no proporciona ningún valor para `area`.

<a name="aa"></a>

En términos de búsqueda de coincidencias de *ningún valor*, la ausencia del valor `area` es igual que si el valor de `area` fuese null o una cadena vacía.

Al ejecutar una acción dentro de un área, el valor de ruta de `area` está disponible como [valor ambiente](#ambient) para el enrutamiento que se va a usar para la generación de direcciones URL. Esto significa que, de forma predeterminada, las áreas actúan de forma *adhesiva* para la generación de direcciones URL, tal como se muestra en el ejemplo siguiente.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

El código siguiente genera una dirección URL para `/Zebra/Users/AddUser` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Definición de la acción

Los métodos públicos de un controlador, excepto aquellos con el atributo no [Action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , son acciones.

## <a name="sample-code"></a>Código de ejemplo

 * El método [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) se incluye en la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) y se usa para mostrar la información de enrutamiento.
* [Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([cómo descargarlo](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC utiliza el [middleware](xref:fundamentals/middleware/index) de enrutamiento para buscar las direcciones URL de las solicitudes entrantes y asignarlas a acciones. Las rutas se definen en el código de inicio o los atributos. Las rutas describen cómo se deben asociar las rutas de dirección URL a las acciones. Las rutas también se usan para generar direcciones URL (para vínculos) enviadas en las respuestas.

Las acciones se enrutan bien mediante convención o bien mediante atributos. Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos. Consulte [Enrutamiento mixto](#routing-mixed-ref-label) para obtener más información.

Este documento explica las interacciones entre MVC y el enrutamiento, así como el uso que las aplicaciones MVC suelen hacer de las características de enrutamiento. Consulte [Enrutamiento](xref:fundamentals/routing) para obtener más información sobre enrutamiento avanzado.

## <a name="setting-up-routing-middleware"></a>Configurar el middleware de enrutamiento

En su método *Configure* puede ver código similar al siguiente:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Dentro de la llamada a `UseMvc`, `MapRoute` se utiliza para crear una ruta única, a la que nos referiremos como la ruta `default`. La mayoría de las aplicaciones MVC usarán una ruta con una plantilla similar a la ruta `default`.

La plantilla de ruta `"{controller=Home}/{action=Index}/{id?}"` puede coincidir con una ruta de dirección URL como `/Products/Details/5` y extraerá los valores de ruta `{ controller = Products, action = Details, id = 5 }` mediante la conversión de la ruta en tokens. MVC intentará encontrar un controlador denominado `ProductsController` y ejecutar la acción `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Tenga en cuenta que, en este ejemplo, el enlace de modelos usaría el valor de `id = 5` para establecer el parámetro `id` en `5` al invocar esta acción. Consulte [Enlace de modelos](../models/model-binding.md) para obtener más detalles.

Utilizando la ruta `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

La plantilla de ruta:

* `{controller=Home}` define `Home` como el valor `controller` predeterminado

* `{action=Index}` define `Index` como el valor `action` predeterminado

* `{id?}` define `id` como opcional

No es necesario que los parámetros de ruta opcionales y predeterminados estén presentes en la ruta de dirección URL para una coincidencia. Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.

`"{controller=Home}/{action=Index}/{id?}"` puede coincidir con la ruta de dirección URL `/` y generará los valores de ruta `{ controller = Home, action = Index }`. Los valores de `controller` y `action` utilizan los valores predeterminados, `id` no genera un valor porque no hay ningún segmento correspondiente en la ruta de dirección URL. MVC utilizaría estos valores de ruta para seleccionar `HomeController` y la acción `Index`:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Usando esta definición de controlador y la plantilla de ruta, la acción `HomeController.Index` se ejecutaría para cualquiera de las rutas de dirección URL siguientes:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

El método de conveniencia `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Se puede usar para reemplazar:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` y `UseMvcWithDefaultRoute` agregan una instancia de `RouterMiddleware` a la canalización de middleware. MVC no interactúa directamente con middleware y usa el enrutamiento para controlar las solicitudes. MVC está conectado a las rutas a través de una instancia de `MvcRouteHandler`. El código dentro de `UseMvc` es similar al siguiente:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` no define directamente ninguna ruta, sino que agrega un marcador de posición a la colección de rutas para la ruta `attribute`. La sobrecarga `UseMvc(Action<IRouteBuilder>)` le permite agregar sus propias rutas y también admite el enrutamiento mediante atributos.  `UseMvc` y todas sus variaciones agregan un marcador de posición para la ruta de atributo (el enrutamiento mediante atributos siempre está disponible, independientemente de cómo configure `UseMvc`). `UseMvcWithDefaultRoute` define una ruta predeterminada y admite el enrutamiento mediante atributos. La sección [Enrutamiento mediante atributos](#attribute-routing-ref-label) incluye más detalles sobre este tipo de enrutamiento.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Enrutamiento convencional

La ruta `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

El código anterior es un ejemplo de enrutamiento convencional. Este estilo se denomina enrutamiento convencional porque establece una *Convención* para las rutas de dirección URL:

* El primer segmento de la ruta de acceso se asigna al nombre del controlador.
* El segundo se asigna al nombre de la acción.
* El tercer segmento se utiliza para un opcional `id` . `id`asigna a una entidad del modelo.

Mediante esta ruta `default`, la ruta de dirección URL `/Products/List` se asigna a la acción `ProductsController.List`, y `/Blog/Article/17` se asigna a `BlogController.Article`. Esta asignación **solo** se basa en los nombres de acción y controlador, y no en espacios de nombres, ubicaciones de archivos de origen ni parámetros de método.

> [!TIP]
> Usar el enrutamiento convencional con la ruta predeterminada permite generar rápidamente la aplicación sin necesidad de elaborar un nuevo patrón de dirección URL para cada acción que se define. Para una aplicación con acciones de estilo CRUD, la coherencia en las direcciones URL en todos los controladores puede ayudar a simplificar el código y hacer que la interfaz de usuario sea más predecible.

> [!WARNING]
> `id` se define como opcional mediante la plantilla de ruta, lo que significa que las acciones se pueden ejecutar sin el identificador proporcionado como parte de la dirección URL. Normalmente lo que ocurrirá si `id` se omite de la dirección URL es que el enlace de modelos establecerá en `0` su valor y, como resultado, no se encontrará ninguna entidad en la base de datos que coincida con `id == 0`. El enrutamiento mediante atributos proporciona un mayor control que permite requerir el identificador para algunas acciones y para otras no. Por convención, la documentación incluirá parámetros opcionales como `id` cuando sea más probable que su uso sea correcto.

## <a name="multiple-routes"></a>Varias rutas

Para agregar varias rutas dentro de `UseMvc`, agregue más llamadas a `MapRoute`. Hacerlo le permite definir varias convenciones o agregar rutas convencionales que se dedican a una acción específica, como en el ejemplo siguiente:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Aquí, la ruta `blog` es una *ruta convencional dedicada*, lo que significa que utiliza el sistema de enrutamiento convencional, pero que está dedicada a una acción específica. Puesto que `controller` y `action` no aparecen en la plantilla de ruta como parámetros, solo pueden tener los valores predeterminados y, por tanto, esta ruta siempre se asignará a la acción `BlogController.Article`.

Las rutas de la colección de rutas están ordenadas y se procesan en el orden en que se hayan agregado. Por tanto, la ruta `blog` de este ejemplo se intentará antes que la ruta `default`.

> [!NOTE]
> Las *rutas convencionales dedicadas* suelen usar parámetros de ruta **catch-all** como `{*article}` para capturar la parte restante de la ruta de dirección URL. Esto puede hacer que la ruta sea "demasiado expansiva", lo que significa que coincidirá con direcciones URL que se pretendía que coincidieran con otras rutas. Coloque las rutas "expansivas" más adelante en la tabla de rutas para resolver este problema.

### <a name="fallback"></a>Reserva

Como parte del procesamiento de la solicitud, MVC comprobará que los valores de ruta se pueden usar para buscar un controlador y la acción en la aplicación. Si los valores de ruta no coinciden con una acción, entonces la ruta no se considera una coincidencia y se intentará la ruta siguiente. Esto se denomina *reserva*, y se ha diseñado para simplificar los casos donde se superponen rutas convencionales.

### <a name="disambiguating-actions"></a>Eliminar la ambigüedad de acciones

Cuando dos acciones coinciden en el enrutamiento, MVC debe eliminar la ambigüedad para elegir el candidato "recomendado", o de lo contrario se produce una excepción. Por ejemplo:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Este controlador define dos acciones que coincidirían con la ruta de dirección URL `/Products/Edit/17` y los datos de ruta `{ controller = Products, action = Edit, id = 17 }`. Se trata de un patrón típico para controladores MVC donde `Edit(int)` muestra un formulario para editar un producto, y `Edit(int, Product)` procesa el formulario expuesto. Para hacer esto posible, MVC necesita seleccionar `Edit(int, Product)` cuando la solicitud es HTTP `POST` y `Edit(int)` cuando el verbo HTTP es cualquier otra cosa.

`HttpPostAttribute` ( `[HttpPost]` ) es una implementación de `IActionConstraint` que solo permitirá que la acción se seleccione cuando el verbo HTTP sea `POST`. La presencia de `IActionConstraint` hace que `Edit(int, Product)` sea una mejor coincidencia que `Edit(int)`, por lo que `Edit(int, Product)` se intentará en primer lugar.

Solo necesitará escribir implementaciones de `IActionConstraint` personalizadas en escenarios especializados, pero es importante comprender el rol de atributos como `HttpPostAttribute` (para otros verbos HTTP se definen atributos similares). En el enrutamiento convencional es común que las acciones utilicen el mismo nombre de acción cuando son parte de un flujo de trabajo `show form -> submit form`. La comodidad de este patrón será más evidente después de revisar la sección [Descripción de IActionConstraint](#understanding-iactionconstraint).

Si coinciden varias rutas y MVC no encuentra una ruta "recomendada", se produce una excepción `AmbiguousActionException`.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Nombres de ruta

Las cadenas `"blog"` y `"default"` de los ejemplos siguientes son nombres de ruta:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Los nombres de ruta proporcionan un nombre lógico a la ruta para que la ruta con nombre pueda utilizarse en la generación de direcciones URL. Esto simplifica en gran medida la creación de direcciones URL en los casos en que el orden de las rutas podría complicar la generación de direcciones URL. Los nombres de ruta deben ser únicos en toda la aplicación.

Los nombres de ruta no tienen ningún impacto en la coincidencia de direcciones URL ni en el control de las solicitudes; se utilizan únicamente para la generación de direcciones URL. En [Enrutamiento](xref:fundamentals/routing) se incluye información más detallada sobre la generación de direcciones URL, como la generación de direcciones URL en asistentes específicos de MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Enrutamiento mediante atributos

El enrutamiento mediante atributos utiliza un conjunto de atributos para asignar acciones directamente a las plantillas de ruta. En el ejemplo siguiente, `app.UseMvc();` se utiliza en el método `Configure` y no se pasa ninguna ruta. `HomeController` coincidirá con un conjunto de direcciones URL similares a las que coincidirían con la ruta predeterminada `{controller=Home}/{action=Index}/{id?}`:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

La acción `HomeController.Index()` se ejecutará para cualquiera de las rutas de dirección URL `/`, `/Home` o `/Home/Index`.

> [!NOTE]
> Este ejemplo resalta una diferencia clave de programación entre el enrutamiento mediante atributos y el enrutamiento convencional. El enrutamiento mediante atributos requiere más entradas para especificar una ruta; la ruta predeterminada convencional controla las rutas de manera más sucinta. Pero el enrutamiento mediante atributos permite (y requiere) un control preciso de las plantillas de ruta que se aplicarán a cada acción.

Con el enrutamiento mediante atributos, el nombre del controlador y los nombres de acción **no** juegan ningún papel en la selección de las acciones. Este ejemplo coincidirá con las mismas direcciones URL que el ejemplo anterior.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Las plantillas de ruta anteriores no definen parámetros de ruta para `action`, `area` y `controller`. De hecho, estos parámetros de ruta no se permiten en rutas de atributo. Puesto que la plantilla de ruta ya está asociada a una acción, no tendría sentido analizar el nombre de acción de la dirección URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Enrutamiento mediante atributos con atributos Http[Verb]

El enrutamiento mediante atributos también puede hacer uso de los atributos `Http[Verb]` como `HttpPostAttribute`. Todos estos atributos pueden aceptar una plantilla de ruta. Este ejemplo muestra dos acciones que coinciden con la misma plantilla de ruta:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Para una ruta de dirección URL como `/products`, la acción `ProductsApi.ListProducts` se ejecutará cuando el verbo HTTP sea `GET` y `ProductsApi.CreateProduct` se ejecutará cuando el verbo HTTP sea `POST`. El enrutamiento mediante atributos primero busca una coincidencia de la dirección URL en el conjunto de plantillas de ruta que se definen mediante atributos de ruta. Una vez que se encuentra una plantilla de ruta que coincide, las restricciones `IActionConstraint` se aplican para determinar qué acciones se pueden ejecutar.

> [!TIP]
> Cuando se compila una API de REST, es poco frecuente que se quiera usar `[Route(...)]` en un método de acción, ya que la acción aceptará todos los métodos HTTP. Es mejor usar `Http*Verb*Attributes` más específicos para precisar lo que es compatible con la API. Se espera que los clientes de API de REST sepan qué rutas y verbos HTTP se asignan a determinadas operaciones lógicas.

Puesto que una ruta de atributo se aplica a una acción específica, es fácil crear parámetros necesarios como parte de la definición de plantilla de ruta. En este ejemplo, se requiere `id` como parte de la ruta de dirección URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

La acción `ProductsApi.GetProduct(int)` se ejecutará para una ruta de dirección URL como `/products/3`, pero no para una ruta de dirección URL como `/products`. Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción completa de las plantillas de ruta y las opciones relacionadas.

## <a name="route-name"></a>Nombre de ruta

El código siguiente define un *nombre de ruta* de `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Los nombres de ruta se pueden utilizar para generar una dirección URL basada en una ruta específica. Los nombres de ruta no tienen ningún impacto en el comportamiento de coincidencia de direcciones URL del enrutamiento, y solo se usan para la generación de direcciones URL. Los nombres de ruta deben ser únicos en toda la aplicación.

> [!NOTE]
> Compare esto con la *ruta predeterminada* convencional, que define el parámetro `id` como opcional (`{id?}`). Esta capacidad de especificar con precisión las API tiene sus ventajas, como permitir que `/products` y `/products/5` se envíen a acciones diferentes.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Combinación de rutas

Para que el enrutamiento mediante atributos sea menos repetitivo, los atributos de ruta del controlador se combinan con los atributos de ruta de las acciones individuales. Las plantillas de ruta definidas en el controlador se anteponen a las plantillas de ruta de las acciones. La colocación de un atributo de ruta en el controlador hace que **todas** las acciones del controlador usen el enrutamiento mediante atributos.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

En este ejemplo, la ruta de dirección URL `/products` puede coincidir con `ProductsApi.ListProducts` y la ruta de dirección URL `/products/5` puede coincidir con `ProductsApi.GetProduct(int)`. Ambas acciones solo coinciden con el método HTTP `GET` porque están marcadas con `HttpGetAttribute`.

Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador. En este ejemplo coinciden un conjunto de rutas de dirección URL similares a la *ruta predeterminada*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Ordenación de rutas de atributo

A diferencia de las rutas convencionales, que se ejecutan en un orden definido, el enrutamiento de atributos genera un árbol y coincide con todas las rutas simultáneamente. Es como si las entradas de ruta se colocasen en un orden ideal; las rutas más específicas tienen la oportunidad de ejecutarse antes que las rutas más generales.

Por ejemplo, una ruta como `blog/search/{topic}` es más específica que una ruta como `blog/{*article}`. Desde un punto de vista lógico, la ruta `blog/search/{topic}` se ejecuta en primer lugar de forma predeterminada, ya que ese es el único orden significativo. En el enrutamiento convencional, el desarrollador es responsable de colocar las rutas en el orden deseado.

Las rutas de atributo pueden configurar un orden mediante la propiedad `Order` de todos los atributos de ruta proporcionados por el marco. Las rutas se procesan de acuerdo con el orden ascendente de la propiedad `Order`. El orden predeterminado es `0`. Si una ruta se configura con `Order = -1`, se ejecutará antes que las rutas que establecen un orden. Si una ruta se configura con `Order = 1`, se ejecutará después del orden de rutas predeterminado.

> [!TIP]
> Evite depender de `Order`. Si su espacio de direcciones URL requiere unos valores de orden explícitos para un enrutamiento correcto, es probable que también sea confuso para los clientes. Por lo general, el enrutamiento mediante atributos seleccionará la ruta correcta con la coincidencia de dirección URL. Si el orden predeterminado que se usa para la generación de direcciones URL no funciona, normalmente es más sencillo utilizar el nombre de ruta como una invalidación que aplicar la propiedad `Order`.

El enrutamiento de Razor Pages y el del controlador MVC comparten una implementación. La información sobre el orden de la ruta en los temas de las Razor páginas está disponible en [ Razor las páginas ruta y convenciones de aplicación: orden de ruta](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Reemplazo de tokens en plantillas de ruta ([controller], [action], [area])

Para mayor comodidad, las rutas de atributo admiten el *reemplazo de tokens* mediante la inclusión de un token entre corchetes ( `[` , `]` ). Los tokens `[action]`, `[area]` y `[controller]` se reemplazan con los valores del nombre de la acción, el nombre del área y el nombre del controlador de la acción donde se define la ruta. En este ejemplo, las acciones coinciden con las rutas de dirección URL, tal como se describe en los comentarios:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

El reemplazo del token se produce como último paso de la creación de las rutas de atributo. El ejemplo anterior se comportará igual que el código siguiente:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Las rutas de atributo también se pueden combinar con la herencia. Esto es especialmente eficaz si se combina con el reemplazo de token.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo. `[Route("[controller]/[action]", Name="[controller]_[action]")]` genera un nombre de ruta único para cada acción.

Para que el delimitador de reemplazo de token `[` o `]` coincida, repita el carácter (`[[` o `]]`) para usarlo como secuencia de escape.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Usar un transformador de parámetro para personalizar el reemplazo de tokens

El reemplazo de tokens se puede personalizarse mediante un transformador de parámetro. Un transformador de parámetro implementa `IOutboundParameterTransformer` y transforma el valor de parámetros. Por ejemplo, un transformador de parámetros `SlugifyParameterTransformer` personalizado cambia el valor de ruta `SubscriptionManagement` a `subscription-management`.

`RouteTokenTransformerConvention` es una convención de modelo de aplicación que:

* Aplica un transformador de parámetro a todas las rutas de atributo en una aplicación.
* Personaliza los valores de token de ruta de atributo que se reemplazan.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention` está registrado como una opción en `ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
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

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Varias rutas

El enrutamiento mediante atributos permite definir varias rutas que llegan a la misma acción. El uso más común de esto es imitar el comportamiento de la *ruta convencional predeterminada* tal como se muestra en el ejemplo siguiente:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

La colocación de varios atributos de ruta en el controlador significa que cada uno de ellos se combinará con cada uno de los atributos de ruta en los métodos de acción.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Cuando en una acción se colocan varios atributos de ruta (que implementan `IActionConstraint`), cada restricción de acción se combina con la plantilla de ruta del atributo que lo define.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Si bien el uso de varias rutas en acciones puede parecer eficaz, es mejor que el espacio de direcciones URL de la aplicación sea sencillo y esté bien definido. Utilice varias rutas en acciones solo cuando sea necesario, por ejemplo, para admitir a clientes existentes.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Especificación de parámetros opcionales de ruta de atributo, valores predeterminados y restricciones

Las rutas de atributo admiten la misma sintaxis en línea que las rutas convencionales para especificar parámetros opcionales, valores predeterminados y restricciones.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Atributos de ruta personalizados con `IRouteTemplateProvider`

Todos los atributos de ruta proporcionados en el marco (`[Route(...)]`, `[HttpGet(...)]`, etc.) implementan la interfaz `IRouteTemplateProvider`. Al iniciarse la aplicación, MVC busca atributos en las clases de controlador y los métodos de acción, y usa los que implementan `IRouteTemplateProvider` para generar el conjunto inicial de rutas.

Implemente `IRouteTemplateProvider` para definir sus propios atributos de ruta. Cada `IRouteTemplateProvider` le permite definir una única ruta con una plantilla de ruta, un orden y un nombre personalizados:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

El atributo del ejemplo anterior establece automáticamente `Template` en `"api/[controller]"` cuando se aplica `[MyApiController]`.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Uso del modelo de aplicación para personalizar las rutas de atributo

El *modelo de aplicación* es un modelo de objetos creado durante el inicio con todos los metadatos utilizados por MVC para enrutar y ejecutar las acciones. El *modelo de aplicación* incluye todos los datos recopilados de los atributos de ruta (a través de `IRouteTemplateProvider`). Puede escribir *convenciones* para modificar el modelo de aplicación en el momento del inicio y personalizar el comportamiento del enrutamiento. En esta sección se muestra un ejemplo sencillo de personalización del enrutamiento mediante el modelo de aplicación.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Enrutamiento mixto: enrutamiento mediante atributos frente a enrutamiento convencional

Las aplicaciones MVC pueden combinar el uso de enrutamiento convencional y enrutamiento mediante atributos. Es habitual usar las rutas convencionales para controladores que sirven páginas HTML para los exploradores, y el enrutamiento mediante atributos para los controladores que sirven las API de REST.

Las acciones se enrutan bien mediante convención o bien mediante atributos. Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos. Las acciones que definen rutas de atributo no se pueden alcanzar a través de las rutas convencionales y viceversa. **Cualquier** atributo de ruta en el controlador hace que todas las acciones del controlador se enruten mediante atributos.

> [!NOTE]
> Lo que distingue los dos tipos de sistemas de enrutamiento es el proceso que se aplica después de que una dirección URL coincida con una plantilla de ruta. En el enrutamiento convencional, los valores de ruta de la coincidencia se usan para elegir la acción y el controlador en una tabla de búsqueda de todas las acciones enrutadas convencionales. En el enrutamiento mediante atributos, cada plantilla ya está asociada a una acción y no es necesaria ninguna otra búsqueda.

## <a name="complex-segments"></a>Segmentos complejos

Los segmentos complejos (por ejemplo, `[Route("/dog{token}cat")]`), se procesan buscando coincidencias de literales de derecha a izquierda de un modo no expansivo. Para ver una descripción, eche un vistazo al [código fuente](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296). Para más información, vea [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Generación de direcciones URL

Las aplicaciones MVC pueden usar características de generación de direcciones URL de enrutamiento para generar vínculos URL a las acciones. La generación de direcciones URL elimina las direcciones URL codificadas de forma rígida, por lo que el código es más compacto y fácil de mantener. Esta sección se centra en las características de generación de direcciones URL proporcionadas por MVC y solo aborda los conceptos básicos de su funcionamiento. Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción detallada de la generación de direcciones URL.

La interfaz `IUrlHelper` es la pieza subyacente de la infraestructura entre MVC y el enrutamiento para la generación de direcciones URL. Encontrará una instancia de `IUrlHelper` disponible a través de la propiedad `Url` en los controladores, las vistas y los componentes de vista.

En este ejemplo, la interfaz `IUrlHelper` se usa a través de la propiedad `Controller.Url` para generar una dirección URL a otra acción.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Si la aplicación está usando la ruta convencional predeterminada, el valor de la variable `url` será la cadena de ruta de dirección URL `/UrlGeneration/Destination`. Esta ruta de dirección URL se crea por enrutamiento mediante la combinación de los valores de ruta de la solicitud actual (valores de ambiente) con los valores pasados a `Url.Action`, y sustituyendo esos valores en la plantilla de ruta:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

El valor de cada uno de los parámetros de ruta incluidos en la plantilla de ruta se sustituye por nombres que coincidan con los valores y los valores de ambiente. Si un parámetro de ruta no tiene un valor, puede utilizar un valor predeterminado en caso de tenerlo, o se puede omitir si es opcional (como en el caso de `id` en este ejemplo). La generación de direcciones URL producirá un error si cualquiera de los parámetros de ruta necesarios no tiene un valor correspondiente. Si se produce un error en la generación de direcciones URL para una ruta, se prueba con la ruta siguiente hasta que se hayan probado todas las rutas o se encuentra una coincidencia.

En el ejemplo anterior de `Url.Action` se supone que el enrutamiento es convencional, pero la generación de direcciones URL funciona del mismo modo con el enrutamiento mediante atributos, si bien los conceptos son diferentes. En el enrutamiento convencional, los valores de ruta se usan para expandir una plantilla; los valores de ruta de `controller` y `action` suelen aparecer en esa plantilla. Esto es válido porque las direcciones URL que coinciden con el enrutamiento se adhieren a una *convención*. En el enrutamiento mediante atributos, no está permitido que los valores de ruta de `controller` y `action` aparezcan en la plantilla. En vez de eso, se utilizan para buscar la plantilla que se va a usar.

Este ejemplo utiliza la enrutamiento mediante atributos:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC genera una tabla de búsqueda de todas las acciones enrutadas mediante atributos y hará coincidir los valores `controller` y `action` para seleccionar la plantilla de ruta que se usará para la generación de direcciones URL. En el ejemplo anterior se genera `custom/url/to/destination`.

### <a name="generating-urls-by-action-name"></a>Generación de direcciones URL por nombre de acción

`Url.Action` (`IUrlHelper` . `Action`) y todas las sobrecargas relacionadas se basan en la idea de especificar el destino del vínculo mediante un nombre de controlador y un nombre de acción.

> [!NOTE]
> Cuando se usa `Url.Action`, se especifican los valores actuales de la ruta para `controller` y `action`. Los valores de `controller` y `action` forman parte tanto de los *valores de ambiente* **y como de los ** *valores*. El método `Url.Action` siempre utiliza los valores actuales de `action` y `controller` y genera una ruta de dirección URL que dirige a la acción actual.

Intentos de enrutamiento para utilizar los valores en los valores de ambiente para rellenar la información que no se proporcionó al generar una dirección URL. Al utilizar una ruta como `{a}/{b}/{c}/{d}` y los valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, el enrutamiento tiene suficiente información para generar una dirección URL sin ningún valor adicional, puesto que todos los parámetros de ruta tienen un valor. Si se agregó el valor `{ d = Donovan }`, el valor `{ d = David }` se ignorará y la ruta de dirección URL generada será `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> Las rutas de dirección URL son jerárquicas. En el ejemplo anterior, si se agrega el valor `{ c = Cheryl }`, ambos valores `{ c = Carol, d = David }` se ignorarán. En este caso ya no tenemos un valor para `d` y se producirá un error en la generación de direcciones URL. Debe especificar el valor deseado de `c` y `d`.  Es probable que este problema se produzca con la ruta predeterminada (`{controller}/{action}/{id?}`), pero raramente encontrará este comportamiento en la práctica, dado que `Url.Action` especificará siempre de manera explícita un valor `controller` y `action`.

Las sobrecargas más largas de `Url.Action` también toman un objeto de *valores de ruta* adicional para proporcionar valores para parámetros de ruta distintos de `controller` y `action`. Normalmente verá esto utilizado con `id`, como `Url.Action("Buy", "Products", new { id = 17 })`. Por convención, el objeto de *valores de ruta* normalmente es un objeto de tipo anónimo, pero también puede ser `IDictionary<>` o un *objeto .NET estándar*. Los valores de ruta adicionales que no coinciden con los parámetros de ruta se colocan en la cadena de consulta.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Para crear una dirección URL absoluta, use una sobrecarga que acepte `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generación de direcciones URL por ruta

En el código anterior se pasó el nombre de acción y de controlador para generar una dirección URL. `IUrlHelper` también proporciona la familia de métodos `Url.RouteUrl`. Estos métodos son similares a `Url.Action`, pero no copian los valores actuales de `action` y `controller` en los valores de ruta. Lo más común es especificar un nombre de ruta para utilizar una ruta específica y generar la dirección URL, por lo general *sin* especificar un nombre de acción o controlador.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generación de direcciones URL en HTML

`IHtmlHelper` proporciona los métodos de `HtmlHelper``Html.BeginForm` y `Html.ActionLink` para generar elementos `<form>` y `<a>`, respectivamente. Estos métodos utilizan el método `Url.Action` para generar una dirección URL y aceptan argumentos similares. Los métodos `Url.RouteUrl` complementarios de `HtmlHelper` son `Html.BeginRouteForm` y `Html.RouteLink`, cuya funcionalidad es similar.

Las TagHelper generan direcciones URL a través de la TagHelper `form` y la TagHelper `<a>`. Ambos usan `IUrlHelper` para su implementación. Consulte [Trabajar con formularios](../views/working-with-forms.md) para obtener más información.

Dentro de las vistas, `IUrlHelper` está disponible a través de la propiedad `Url` para una generación de direcciones URL ad hoc no cubierta por los pasos anteriores.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generar direcciones URL en los resultados de acción

Los ejemplos anteriores han demostrado el uso de `IUrlHelper` en un controlador, aunque el uso más común de un controlador consiste en generar una dirección URL como parte de un resultado de acción.

Las clases base `ControllerBase` y `Controller` proporcionan métodos de conveniencia para los resultados de acción que hacen referencia a otra acción. Un uso típico es redirigir después de aceptar la entrada del usuario.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Los patrones de diseño Factory Method de resultados de acción siguen un patrón similar a los métodos en `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Caso especial para rutas convencionales dedicadas

El enrutamiento convencional puede usar un tipo especial de definición de ruta denominada *ruta convencional dedicada*. En el ejemplo siguiente, la ruta llamada `blog` es una ruta convencional dedicada.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Con estas definiciones de ruta, `Url.Action("Index", "Home")` generará la ruta de dirección URL `/` con la ruta `default`. Pero, ¿por qué? Se puede suponer que los valores de ruta `{ controller = Home, action = Index }` son suficientes para generar una dirección URL utilizando `blog`, con el resultado `/blog?action=Index&controller=Home`.

Las rutas convencionales dedicadas se basan en un comportamiento especial de los valores predeterminados que no tienen un parámetro de ruta correspondiente que impida que la ruta sea "demasiado expansiva" con la generación de direcciones URL. En este caso, los valores predeterminados son `{ controller = Blog, action = Article }`, y ni `controller` ni `action` aparecen como un parámetro de ruta. Cuando el enrutamiento realiza la generación de direcciones URL, los valores proporcionados deben coincidir con los valores predeterminados. La generación de direcciones URL con `blog` producirá un error porque los valores `{ controller = Home, action = Index }` no coinciden con `{ controller = Blog, action = Article }`. Después, el enrutamiento vuelve para probar `default`, operación que se realiza correctamente.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Áreas

Las [áreas](areas.md) son una característica de MVC que se usa para organizar funciones relacionadas en un grupo como un espacio de nombres de enrutamiento independiente (para acciones de controlador) y una estructura de carpetas (para las vistas). La utilización de áreas permite que una aplicación tenga varios controladores con el mismo nombre, siempre y cuando tengan *áreas* diferentes. El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area`, a `controller` y `action`. En esta sección veremos la interacción entre el enrutamiento y las áreas. Consulte [Áreas](areas.md) para obtener más información sobre cómo se utilizan las áreas con las vistas.

En el ejemplo siguiente se configura MVC para usar la ruta predeterminada convencional y una *ruta de área* para un área denominada `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Cuando coincide con una ruta de dirección URL como `/Manage/Users/AddUser`, la primera ruta generará los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`. El valor de ruta `area` se genera con un valor predeterminado para `area`. De hecho, la ruta creada por `MapAreaRoute` es equivalente a la siguiente:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` utiliza el nombre de área proporcionado, que en este caso es `Blog`, para crear una ruta con un valor predeterminado y una restricción para `area`. El valor predeterminado garantiza que la ruta siempre produce `{ area = Blog, ... }`; la restricción requiere el valor `{ area = Blog, ... }` para la generación de la dirección URL.

> [!TIP]
> El enrutamiento convencional depende del orden. En general, las rutas con áreas deben colocarse antes en la tabla de rutas, ya que son más específicas que las rutas sin un área.

En el ejemplo anterior, los valores de ruta coincidirían con la acción siguiente:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` es lo que denota un controlador como parte de un área. Se dice que este controlador está en el área `Blog`. Los controladores sin un atributo `[Area]` no son miembros de ningún área y **no** coincidirán cuando el enrutamiento proporcione el valor de ruta `area`. En el ejemplo siguiente, solo el primer controlador enumerado puede coincidir con los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> En aras de la exhaustividad, aquí se muestra el espacio de nombres de cada controlador; en caso contrario, los controladores tendrían un conflicto de nomenclatura y generarían un error del compilador. Los espacios de nombres de clase no tienen ningún efecto en el enrutamiento de MVC.

Los dos primeros controladores son miembros de las áreas y solo coinciden cuando el valor de ruta `area` proporciona su respectivo nombre de área. El tercer controlador no es miembro de ningún área y solo puede coincidir cuando el enrutamiento no proporciona ningún valor para `area`.

> [!NOTE]
> En términos de búsqueda de coincidencias de *ningún valor*, la ausencia del valor `area` es igual que si el valor de `area` fuese null o una cadena vacía.

Al ejecutar una acción en un área, el valor de ruta para `area` estará disponible como un *valor de ambiente* para que el enrutamiento pueda usarlo en la generación de direcciones URL. Esto significa que, de forma predeterminada, las áreas actúan de forma *adhesiva* para la generación de direcciones URL, tal como se muestra en el ejemplo siguiente.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Descripción de IActionConstraint

> [!NOTE]
> En esta sección se analiza con mayor profundidad el funcionamiento interno del marco y la elección por parte de MVC de la acción que se va a ejecutar. Una aplicación típica no necesitará un `IActionConstraint` personalizado.

Es probable que ya haya usado `IActionConstraint` incluso si no está familiarizado con la interfaz. El atributo `[HttpGet]` y los atributos `[Http-VERB]` similares implementan `IActionConstraint` con el fin de limitar la ejecución de un método de acción.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Adoptando la ruta convencional predeterminada, la ruta de dirección URL `/Products/Edit` generaría los valores `{ controller = Products, action = Edit }`, que coincidirían con **ambas** acciones que se muestran aquí. En terminología de `IActionConstraint`, diríamos que ambas acciones se consideran candidatas, puesto que las dos coinciden con los datos de ruta.

Cuando `HttpGetAttribute` se ejecute, dirá que *Edit()* es una coincidencia para *GET*, pero no para cualquier otro verbo HTTP. La acción `Edit(...)` no tiene ninguna restricción definida, por lo que coincidirá con cualquier verbo HTTP. Con `POST`, solamente `Edit(...)` coincide. Pero con `GET` ambas acciones pueden coincidir. No obstante, una acción con `IActionConstraint` siempre se considera *mejor* que una acción sin dicha restricción. Por tanto, como `Edit()` tiene `[HttpGet]`, se considera más específica y se seleccionará si ambas acciones pueden coincidir.

Conceptualmente, `IActionConstraint` es una forma de *sobrecarga*, pero en lugar de sobrecargar métodos con el mismo nombre, la sobrecarga se produce entre acciones que coinciden con la misma dirección URL. El enrutamiento mediante atributos también utiliza `IActionConstraint` y puede dar lugar a que acciones de diferentes controladores se consideren candidatas.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementación de IActionConstraint

La manera más sencilla de implementar `IActionConstraint` consiste en crear una clase derivada de `System.Attribute` y colocarla en las acciones y los controladores. MVC detectará automáticamente cualquier `IActionConstraint` que se aplique como atributo. El modelo de aplicaciones es quizá el enfoque más flexible para la aplicación de restricciones, puesto que permite metaprogramar cómo se aplican.

En el ejemplo siguiente, una restricción elige una acción basada en un *código de país* de los datos de ruta. El [ejemplo completo se encuentra en GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Usted es responsable de implementar el método `Accept` y elegir un valor para "Order" para que la restricción se ejecute. En este caso, el método `Accept` devuelve `true` para denotar que la acción es una coincidencia cuando el valor de ruta `country` coincide. Esto difiere de `RouteValueAttribute` en que permite volver a una acción sin atributos. El ejemplo muestra que si se define una acción `en-US`, entonces un código de país como `fr-FR` recurriría a un controlador más genérico que no tenga `[CountrySpecific(...)]` aplicado.

La propiedad `Order` decide de qué *fase* forma parte la restricción. Restricciones de acciones que se ejecutan en grupos basados en la `Order`. Por ejemplo, todos los atributos del método HTTP proporcionados por el marco utilizan el mismo valor `Order` para que se ejecuten en la misma fase. Puede tener las fases que sean necesarias para implementar las directivas deseadas.

> [!TIP]
> Para decidir el valor de `Order`, piense si la restricción se debería o no aplicar antes que los métodos HTTP. Los números más bajos se ejecutan primero.

::: moniker-end
