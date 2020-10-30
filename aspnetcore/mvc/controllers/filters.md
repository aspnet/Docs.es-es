---
title: Filtros en ASP.NET Core
author: Rick-Anderson
description: Obtenga información sobre cómo funcionan los filtros y cómo se pueden usar en ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: ecb4de3439656eb56507b920db704048d8f96759
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058511"
---
# <a name="filters-in-aspnet-core"></a>Filtros en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)

Los *filtros* en ASP.NET Core permiten que se ejecute el código antes o después de determinadas fases de la canalización del procesamiento de la solicitud.

Los filtros integrados se encargan de tareas como las siguientes:

* Autorización (impedir el acceso a los recursos a un usuario que no está autorizado).
* Almacenamiento en caché de respuestas (cortocircuitar la canalización de solicitud para devolver una respuesta almacenada en caché).

Se pueden crear filtros personalizados que se encarguen de cuestiones transversales. Entre los ejemplos de cuestiones transversales se incluyen el control de errores, el almacenamiento en caché, la configuración, la autorización y el registro.  Los filtros evitan la duplicación de código. Así, por ejemplo, un filtro de excepción de control de errores puede consolidar el control de errores.

Este documento se aplica a Razor las páginas, controladores de API y controladores con vistas. Los filtros no funcionan directamente con [ Razor los componentes](xref:blazor/components/index)de. Un filtro solo puede afectar indirectamente a un componente cuando:

* El componente está insertado en una página o vista.
* La página o el controlador o la vista utiliza el filtro.

[Vea o descargue el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Funcionamiento de los filtros

Los filtros se ejecutan dentro de la *canalización de invocación de acciones de ASP.NET Core* , a veces denominada *canalización de filtro* . La canalización de filtro se ejecuta después de que ASP.NET Core seleccione la acción que se va a ejecutar.

![La solicitud se procesa a través de las fases Otro middleware, Middleware de enrutamiento, Selección de acción y Canalización de invocación de acción. El procesamiento de la solicitud continúa a la inversa, pasando por Selección de acción, Middleware de enrutamiento y varias fases de Otro middleware, antes de convertirse en una respuesta para enviarla al cliente.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Tipos de filtro

Cada tipo de filtro se ejecuta en una fase diferente dentro de la canalización de filtro:

* Los [filtros de autorización](#authorization-filters) se ejecutan en primer lugar y sirven para averiguar si el usuario está autorizado para realizar la solicitud. Los filtros de autorización pueden cortocircuitar la canalización si una solicitud no está autorizada.

* [Filtros de recursos](#resource-filters):

  * Se ejecutan después de la autorización.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> ejecuta código antes que el resto de la canalización del filtro. Por ejemplo, `OnResourceExecuting` ejecuta código antes que el enlace de modelos.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> ejecuta el código una vez que el resto de la canalización se haya completado.

* [Filtros de acción](#action-filters):

  * Ejecutan código inmediatamente antes y después de llamar a un método de acción.
  * Pueden cambiar los argumentos pasados a una acción.
  * Pueden cambiar el resultado devuelto de la acción.
  * **No** se admiten en Razor las páginas de.

* Los [filtros de excepciones](#exception-filters) aplican directivas globales a las excepciones no controladas que se producen antes de que se escriba el cuerpo de respuesta.

* Los [filtros de resultados](#result-filters) ejecutan código inmediatamente antes y después de la ejecución de resultados de acción. Se ejecutan solo cuando el método de acción se ha ejecutado correctamente. Son útiles para la lógica que debe regir la ejecución de la vista o el formateador.

En el siguiente diagrama se muestra cómo interactúan los tipos de filtro en la canalización de filtro.

![La solicitud se procesa a través de las fases Filtros de autorización, Filtros de recursos, Enlace de modelos, Filtros de acciones, Ejecución de acciones/Conversión del resultado de acción, Filtros de excepción, Filtros de resultados y Ejecución del resultado. Como resultado, la solicitud solo se procesa por las fases Filtros de resultados y Filtros de recursos antes de convertirse en una respuesta para enviarla al cliente.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementación

Los filtros admiten implementaciones tanto sincrónicas como asincrónicas a través de diferentes definiciones de interfaz.

Los filtros sincrónicos ejecutan código antes y después de la fase de canalización. Por ejemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> se llama antes de llamar al método de acción. <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> se llama después de devolver el método de acción.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

En el código anterior, [aldebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) es una función de utilidad en la [descarga del ejemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).

Los filtros asincrónicos definen un método `On-Stage-ExecutionAsync`. Por ejemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

En el código anterior, `SampleAsyncActionFilter` tiene un delegado <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que ejecuta el método de acción.

### <a name="multiple-filter-stages"></a>Varias fases de filtro

Se pueden implementar interfaces para varias fases de filtro en una sola clase. Por ejemplo, la clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa:

* Sincrónicas: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>
* Asincrónicas: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero **no** ambas. El entorno de ejecución comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, llama a la interfaz. De lo contrario, llamará a métodos de interfaz sincrónicos. Si se implementan las interfaces asincrónicas y sincrónicas en una clase, solo se llama al método asincrónico. Al usar clases abstractas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , invalide solo los métodos sincrónicos o los métodos asincrónicos para cada tipo de filtro.

### <a name="built-in-filter-attributes"></a>Atributos de filtros integrados

ASP.NET Core incluye filtros integrados basados en atributos que se pueden personalizar y a partir de los cuales crear subclases. Por ejemplo, el siguiente filtro de resultados agrega un encabezado a la respuesta:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Los atributos permiten a los filtros aceptar argumentos, como se muestra en el ejemplo anterior. Aplique el `AddHeaderAttribute` a un método de acción o controlador y especifique el nombre y el valor del encabezado HTTP:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

Use una herramienta como las [herramientas de desarrollo del explorador](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) para examinar los encabezados. En **Encabezados de respuesta** , se muestra `author: Rick Anderson`.

El código siguiente implementa un atributo `ActionFilterAttribute` que:

* Lee el título y el nombre del sistema de configuración. A diferencia del ejemplo anterior, el siguiente código no requiere que se agreguen parámetros de filtro al código.
* Agrega el título y el nombre al encabezado de respuesta.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

Las opciones de configuración las proporciona el [sistema de configuración](xref:fundamentals/configuration/index) mediante el [patrón de opciones](xref:fundamentals/configuration/options). Por ejemplo, desde el *appsettings.json* archivo:

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

En `StartUp.ConfigureServices`:

* La clase `PositionOptions` se agrega al contenedor de servicios con el área de configuración `"Position"`.
* `MyActionFilterAttribute` se agrega al contenedor de servicios.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

En el código siguiente se muestra la clase `PositionOptions` :

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

El siguiente código aplica el atributo `MyActionFilterAttribute` al método `Index2`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

En **encabezados de respuesta** , `author: Rick Anderson` y `Editor: Joe Smith` se muestra cuando `Sample/Index2` se llama al extremo.

El código siguiente aplica `MyActionFilterAttribute` y `AddHeaderAttribute` a la Razor página:

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

Los filtros no se pueden aplicar a Razor métodos de control de páginas. Se pueden aplicar al Razor modelo de página o globalmente.

Algunas de las interfaces de filtro tienen atributos correspondientes que se pueden usar como clases base en las implementaciones personalizadas.

Atributos de filtro:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Ámbitos del filtro y orden de ejecución

Un filtro se puede agregar a la canalización en uno de tres *ámbitos* posibles:

* Mediante un atributo en una acción del controlador. Los atributos de filtro no se pueden aplicar a Razor los métodos de control de páginas.
* Usar un atributo en un controlador o una Razor página.
* Globalmente para todos los controladores, acciones y Razor páginas, tal y como se muestra en el código siguiente:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a>Orden de ejecución predeterminado

Cuando hay varios filtros en una determinada fase de la canalización, el ámbito determina el orden predeterminado en el que esos filtros se van a ejecutar.  Los filtros globales abarcan a los filtros de clase, que a su vez engloban a los filtros de método.

Como resultado de este anidamiento de filtros, el código de filtros *posterior* se ejecuta en el orden inverso al código *anterior* . La secuencia de filtro:

* El código *anterior* de los filtros globales.
  * El código *anterior* de los filtros de controlador y de Razor página.
    * El código *anterior* de los filtros de métodos de acción.
    * El código *posterior* de los filtros de métodos de acción.
  * *Después* del código de los filtros de controlador y de Razor página.
* El código *posterior* de los filtros globales.
  
El ejemplo siguiente que ilustra el orden en el que se llama a los métodos de filtro relativos a filtros de acciones sincrónicos.

| Secuencia | Ámbito del filtro | Método de filtro |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | Controlador o Razor Página| `OnActionExecuting` |
| 3 | Método | `OnActionExecuting` |
| 4 | Método | `OnActionExecuted` |
| 5 | Controlador o Razor Página | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

### <a name="controller-level-filters"></a>Filtros de nivel de controlador

Cada controlador que hereda de la clase base <xref:Microsoft.AspNetCore.Mvc.Controller> incluye los métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) y [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`. Estos métodos:

* Encapsulan los filtros que se ejecutan para una acción determinada.
* `OnActionExecuting` se llama antes de cualquiera de los filtros de acciones.
* `OnActionExecuted` se llama después de todos los filtros de acciones.
* `OnActionExecutionAsync` se llama antes de cualquiera de los filtros de acciones. El código del filtro después de `next` se ejecuta después del método de acción.

Por ejemplo, en el ejemplo de descarga, se aplica `MySampleActionFilter` globalmente al inicio.

El `TestController`:

* Aplica `SampleActionFilterAttribute` (`[SampleActionFilter]`) a la acción `FilterTest2`.
* Invalida `OnActionExecuting` y `OnActionExecuted`.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

Si se dirige a `https://localhost:5001/Test/FilterTest2`, se ejecuta el código siguiente:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Los filtros de nivel de controlador establecen la propiedad [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) en `int.MinValue`. Los filtros de nivel de controlador **no** pueden establecerse para ejecutarse tras aplicarse los filtros a los métodos. Order se explica en la sección siguiente.

Para Razor las páginas, consulte [implementar Razor filtros de página mediante la invalidación de métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Invalidación del orden predeterminado

La secuencia de ejecución predeterminada se puede invalidar con la implementación de <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>. <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> expone la propiedad `IOrderedFilter` que tiene prioridad sobre el ámbito a la hora de determinar el orden de ejecución. Un filtro con un valor `Order` menor:

* Ejecuta el código *anterior* antes que el de un filtro con un valor mayor de `Order`.
* Ejecuta el código *posterior* después que el de un filtro con un valor mayor de `Order`.

La propiedad `Order` se establece con un parámetro de constructor:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

Tenga en cuenta los dos filtros de acción en el siguiente controlador:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

Un filtro global se agrega en `StartUp.ConfigureServices`:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

Los tres filtros se ejecutan en el siguiente orden:

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

La propiedad `Order` invalida el ámbito al determinar el orden en el que se ejecutarán los filtros. Los filtros se clasifican por orden en primer lugar y, después, se usa el ámbito para priorizar en caso de igualdad. Todos los filtros integrados implementan `IOrderedFilter` y establecen el valor predeterminado de `Order` en 0. Como se mencionó anteriormente, los filtros de nivel de controlador establecen la propiedad [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) en `int.MinValue`. En el caso de los filtros integrados, el ámbito determina el orden a menos que se establezca `Order` en un valor distinto de cero.

En el código anterior, `MySampleActionFilter` tiene ámbito global, por lo que se ejecuta antes de `MyAction2FilterAttribute`, que tiene ámbito de controlador. Para que `MyAction2FilterAttribute` se ejecute en primer lugar, establezca el orden en `int.MinValue`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

Para que el filtro global `MySampleActionFilter` se ejecute en primer lugar, establezca `Order` en `int.MinValue`:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a>Cancelación y cortocircuito

La canalización de filtro se puede cortocircuitar en cualquier momento mediante el establecimiento de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> en el parámetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> que se ha proporcionado al método de filtro. Por ejemplo, el siguiente filtro de recursos impide que el resto de la canalización se ejecute:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

En el siguiente código, tanto el filtro `ShortCircuitingResourceFilter` como el filtro `AddHeader` tienen como destino el método de acción `SomeResource`. El `ShortCircuitingResourceFilter`:

* Se ejecuta en primer lugar, porque es un filtro de recursos y `AddHeader` es un filtro de acciones.
* Cortocircuita el resto de la canalización.

Por tanto, el filtro `AddHeader` nunca se ejecuta en relación con la acción `SomeResource`. Este comportamiento sería el mismo si ambos filtros se aplicaran en el nivel de método de acción, siempre y cuando `ShortCircuitingResourceFilter` se haya ejecutado primero. `ShortCircuitingResourceFilter` se ejecuta primero debido a su tipo de filtro o al uso explícito de la propiedad `Order`.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a>Inserción de dependencias

Los filtros se pueden agregar por tipo o por instancia. Si se agrega una instancia, esta se utiliza para todas las solicitudes. Si se agrega un tipo, se activa por tipo. Un filtro activado por tipo significa:

* Se crea una instancia para cada solicitud.
* Las dependencias de constructor se rellenan mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).

Los filtros que se implementan como atributos y se agregan directamente a las clases de controlador o a los métodos de acción no pueden tener dependencias de constructor proporcionadas por la [inserción de dependencias](xref:fundamentals/dependency-injection). Las dependencias de constructor no pueden proporcionarse mediante la inserción de dependencias porque:

* Los atributos deben tener los parámetros de constructor proporcionados allá donde se apliquen. 
* Se trata de una limitación de cómo funcionan los atributos.

Los filtros siguientes admiten dependencias de constructor proporcionadas en la inserción de dependencias:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> se implementa en el atributo.

Los filtros anteriores se pueden aplicar a un método de controlador o de acción:

Los registradores están disponibles en la inserción de dependencias. Sin embargo, evite crear y utilizar filtros únicamente con fines de registro. El [registro del marco integrado](xref:fundamentals/logging/index) proporciona normalmente lo que se necesita para el registro. Registro agregado a los filtros:

* Debe centrarse en cuestiones de dominio empresarial o en el comportamiento específico del filtro.
* **No** debe registrar acciones u otros eventos del marco. Los filtros integrados registran acciones y eventos del marco.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Los tipos de implementación de filtro de servicio se registran en `ConfigureServices`. <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera una instancia del filtro de la inserción de dependencias.

El código siguiente muestra `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

En el código siguiente, `AddHeaderResultServiceFilter` se agrega al contenedor de inserción de dependencias:

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

En el código siguiente, el atributo `ServiceFilter` recupera una instancia del filtro `AddHeaderResultServiceFilter` desde la inserción de dependencias:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Al usar `ServiceFilterAttribute`, si se establece [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* Proporciona una sugerencia que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó. El entorno de ejecución de ASP.NET Core no garantiza:

  * Que se creará una única instancia del filtro.
  * El filtro no volverá a solicitarse desde el contenedor de inserción de dependencias en algún momento posterior.

* No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` carga el tipo especificado desde la inserción de dependencias.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> es similar a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, pero su tipo no se resuelve directamente desde el contenedor de inserción de dependencias, sino que crea una instancia del tipo usando el elemento <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.

Dado que los tipos `TypeFilterAttribute` no se resuelven directamente desde el contenedor de inserción de dependencias:

* Los tipos a los que se hace referencia con `TypeFilterAttribute` no tienen que estar registrados con el contenedor de inserción de dependencias.  Sus dependencias se completan a través del contenedor de inserción de dependencias.
* `TypeFilterAttribute` puede aceptar opcionalmente argumentos de constructor del tipo en cuestión.

Al usar `TypeFilterAttribute`, si se establece [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):
* Proporciona una sugerencia que indica que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó. El entorno de ejecución de ASP.NET Core no ofrece ninguna garantía de que se vaya a crear una única instancia del filtro.

* No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.

En el siguiente ejemplo se muestra cómo pasar argumentos a un tipo mediante `TypeFilterAttribute`:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Filtros de autorización

Filtros de autorización:

* Son los primeros filtros que se ejecutan en la canalización del filtro.
* Controlan el acceso a los métodos de acción.
* Tienen un método anterior, pero no uno posterior.

Los filtros de autorización personalizados requieren un marco de autorización personalizado. Es preferible configurar directivas de autorización o escribir una directiva de autorización personalizada a escribir un filtro personalizado. El filtro de autorización integrado:

* Llama a la autorización del sistema.
* No autoriza las solicitudes.

**No** inicie excepciones dentro de los filtros de autorización:

* La excepción no se controlará.
* Los filtros de excepciones no controlarán la excepción.

Considere la posibilidad de emitir un desafío cuando se produzca una excepción en un filtro de autorizaciones.

[Aquí](xref:security/authorization/introduction) encontrará más información sobre la autorización.

## <a name="resource-filters"></a>Filtros de recursos

Filtros de recursos:

* Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.
* La ejecución encapsula la mayor parte de la canalización de filtro.
* Los [filtros de autorizaciones](#authorization-filters) son los únicos que se ejecutan antes que los filtros de recursos.

Los filtros de recursos son útiles para cortocircuitar la mayor parte de la canalización. Por ejemplo, un filtro de almacenamiento en caché puede evitar que se ejecute el resto de la canalización en un acierto de caché.

Ejemplos de filtros de recursos:

* [El filtro de recursos de cortocircuito](#short-circuiting-resource-filter) mostrado anteriormente.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Evita que el enlace de modelos tenga acceso a los datos del formulario.
  * Se utiliza cuando hay cargas de archivos muy voluminosos para impedir que los datos del formulario se lean en la memoria.

## <a name="action-filters"></a>Filtros de acciones

Los filtros de acción **no** se aplican a Razor las páginas. Razor Pages admite <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Para obtener más información, vea [Métodos de filtrado de Razor Pages](xref:razor-pages/filter).

Filtros de acciones:

* Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.
* Su ejecución rodea la ejecución de los métodos de acción.

El código siguiente muestra un ejemplo de filtro de acciones:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> ofrece las siguientes propiedades:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: permite leer las entradas de un método de acción.
* <xref:Microsoft.AspNetCore.Mvc.Controller>: permite manipular la instancia del controlador.
* <xref:System.Web.Mvc.ActionExecutingContext.Result>: si se establece `Result`, se cortocircuita la ejecución del método de acción y de los filtros de acciones posteriores.

Inicio de una excepción en un método de acción:

* Impide la ejecución de los filtros subsiguientes.
* A diferencia del establecimiento de `Result`, se trata como un error en lugar de como un resultado correcto.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> proporciona `Controller` y `Result`, además de las siguientes propiedades:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled>: es true si otro filtro ha cortocircuitado la ejecución de la acción.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception>: es un valor distinto de NULL si la acción o un filtro de acción de ejecución anterior han producido una excepción. Si se establece esta propiedad en un valor NULL:

  * Controla la excepción eficazmente.
  * `Result` se ejecuta como si se devolviera desde el método de acción.

En un `IAsyncActionFilter`, una llamada a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:

* Ejecuta cualquier filtro de acciones posterior y el método de acción.
* Devuelve `ActionExecutedContext`.

Para cortocircuitar esto, asigne <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a una instancia de resultado y no llame a `next` (la clase `ActionExecutionDelegate`).

El marco proporciona una clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstracta de la que se pueden crear subclases.

Se puede usar el filtro de acción `OnActionExecuting` para:

* Validar el estado del modelo.
* Devolver un error si el estado no es válido.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> Los controladores anotados con el `[ApiController]` atributo validan automáticamente el estado del modelo y devuelven una respuesta 400. Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).

El método `OnActionExecuted` se ejecuta después del método de acción:

* Y puede ver y manipular los resultados de la acción a través de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> se establece en true si otro filtro ha cortocircuitado la ejecución de la acción.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> se establece en un valor distinto de NULL si la acción o un filtro de acción posterior han producido una excepción. Si `Exception` se establece como nulo:

  * Controla una excepción eficazmente.
  * `ActionExecutedContext.Result` se ejecuta como si se devolviera con normalidad desde el método de acción.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Filtros de excepciones

Los filtros de excepciones:

* Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.
* Se pueden usar para implementar directivas de control de errores comunes.

En el siguiente filtro de excepciones de ejemplo se usa una vista de error personalizada para mostrar los detalles sobre las excepciones que se producen cuando la aplicación está en fase de desarrollo:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

El código siguiente prueba el filtro de excepciones:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

Los filtros de excepciones:

* No tienen eventos anteriores ni posteriores.
* Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.
* Controlar las excepciones no controladas que se producen en la Razor creación de páginas o controladores, [enlace de modelos](xref:mvc/models/model-binding), filtros de acción o métodos de acción.
* **No** detectan aquellas excepciones que se produzcan en los filtros de recursos, en los filtros de resultados o en la ejecución de resultados de MVC.

Para controlar una excepción, establezca la propiedad <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> en `true` o escriba una respuesta. Esto detiene la propagación de la excepción. Un filtro de excepciones no tiene capacidad para convertir una excepción en un proceso "correcto". Eso solo lo pueden hacer los filtros de acciones.

Los filtros de excepciones:

* Son adecuados para interceptar las excepciones que se producen en las acciones.
* No son tan flexibles como el middleware de control de errores.

Es preferible usar middleware de control de excepciones. Utilice los filtros de excepciones solo cuando el control de errores *es diferente* en función del método de acción que se llama. Por ejemplo, puede que una aplicación tenga métodos de acción tanto para los puntos de conexión de API como para las vistas/HTML. Los puntos de conexión de API podrían devolver información sobre errores como JSON, mientras que las acciones basadas en vistas podrían devolver una página de error como HTML.

## <a name="result-filters"></a>Filtros de resultados

Filtros de resultados:

* Implementar una interfaz:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Su ejecución rodea la ejecución de los resultados de acción.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter e IAsyncResultFilter

El código siguiente muestra un filtro de resultados que agrega un encabezado HTTP:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

El tipo de resultado que se ejecute dependerá de la acción. Una acción que devuelve una vista incluye todo el procesamiento de Razor como parte del elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> que se está ejecutando. Un método API puede llevar a cabo algunas funciones de serialización como parte de la ejecución del resultado. [Aquí](xref:mvc/controllers/actions) encontrará más información sobre los resultados de acciones.

Los filtros de resultados solo se ejecutan cuando una acción o un filtro de acción genera un resultado de acción. Los filtros de resultados no se ejecutan cuando:

* Un filtro de autorización o un filtro de recursos genera un cortocircuito en la canalización.
* Un filtro de excepciones controla una excepción al producir un resultado de acción.

El método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> puede cortocircuitar la ejecución del resultado de la acción y de los filtros de resultados posteriores mediante el establecimiento de <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> en `true`. Escriba en el objeto de respuesta cuando el proceso se cortocircuite, ya que así evitará que se genere una respuesta vacía. Generación de una excepción en `IResultFilter.OnResultExecuting`:

* Impide la ejecución del resultado de la acción y de los filtros subsiguientes.
* Se trata como un error en lugar de como un resultado correcto.

Cuando se ejecuta el método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, es probable que la respuesta ya se haya enviado al cliente. En este caso, no se puede cambiar.

`ResultExecutedContext.Canceled` se establece en `true` si otro filtro ha cortocircuitado la ejecución del resultado de la acción.

`ResultExecutedContext.Exception` se establece en un valor distinto de NULL si el resultado de la acción o un filtro de resultado posterior ha producido una excepción. Establecer `Exception` en un valor null hace que una excepción se controle de forma eficaz y evita que se vuelva a producir dicha excepción más adelante en la canalización. No hay una forma confiable de escribir datos en una respuesta cuando se controla una excepción en un filtro de resultados. Si los encabezados ya se han vaciado en el cliente si el resultado de una acción inicia una excepción, no hay ningún mecanismo confiable que permita enviar un código de error.

En un elemento <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una llamada a `await next` en <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ejecuta cualquier filtro de resultados posterior y el resultado de la acción. Para cortocircuitarlo, establezca [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) en `true` y no llame a `ResultExecutionDelegate`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

El marco proporciona una clase `ResultFilterAttribute` abstracta de la que se pueden crear subclases. La clase [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente es un ejemplo de un atributo de filtro de resultados.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter

Las interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaran una implementación <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que se ejecuta para obtener todos los resultados de la acción. Esto incluye los resultados de la acción generados por:

* Filtros de autorización y filtros de recursos que generan un cortocircuito.
* Filtros de excepción.

Por ejemplo, el siguiente filtro siempre ejecuta y establece un resultado de la acción (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un código de estado *422 - Entidad no procesable* cuando se produce un error en la negociación de contenido:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Por tanto, una instancia de `IFilterFactory` se puede usar como una instancia de `IFilterMetadata` en cualquier parte de la canalización de filtro. Cuando el entorno de ejecución se prepara para invocar el filtro, intenta convertirlo a un `IFilterFactory`. Si esa conversión se realiza correctamente, se llama al método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear la instancia `IFilterMetadata` que se va a invocar. Esto proporciona un diseño flexible, dado que no hay que establecer la canalización de filtro exacta de forma explícita cuando la aplicación se inicia.

Puede implementar `IFilterFactory` con las implementaciones de atributos personalizados como método alternativo para crear filtros:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

El filtro se aplica en el código siguiente:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

Pruebe el código anterior mediante la ejecución del [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):

* Invoque las herramientas de desarrollador de F12.
* Vaya a `https://localhost:5001/Sample/HeaderWithFactory`.

Las herramientas de desarrollador F12 muestran los siguientes encabezados de respuesta agregados por el código de ejemplo:

* **autor:**`Rick Anderson`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **interno:**`My header`

El código anterior crea el encabezado de respuesta **internal:** `My header`.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>IFilterFactory implementado en un atributo

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Los filtros que implementan `IFilterFactory` son útiles para los filtros que:

* No requieren pasar parámetros.
* Tienen dependencias de constructor que deben completarse por medio de la inserción de dependencias.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` carga el tipo especificado desde el contenedor de servicios (inserción de dependencias).

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

El código siguiente muestra tres métodos para aplicar `[SampleActionFilter]`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

En el código anterior, decorar el método con `[SampleActionFilter]` es el enfoque preferido para aplicar `SampleActionFilter`.

## <a name="using-middleware-in-the-filter-pipeline"></a>Uso de middleware en la canalización de filtro

Los filtros de recursos funcionan como el [middleware](xref:fundamentals/middleware/index), en el sentido de que se encargan de la ejecución de todo lo que viene después en la canalización. Pero los filtros se diferencian del middleware en que forman parte del runtime, lo que significa que tienen acceso al contexto y las construcciones.

Para usar middleware como un filtro, cree un tipo con un método `Configure` en el que se especifique el middleware para insertar en la canalización de filtro. El ejemplo siguiente usa el middleware de localización para establecer la referencia cultural actual de una solicitud:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para ejecutar el middleware:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Los filtros de middleware se ejecutan en la misma fase de la canalización de filtro que los filtros de recursos, antes del enlace de modelos y después del resto de la canalización.

## <a name="next-actions"></a>Siguientes acciones

* Vea [métodos de filtro para Razor páginas](xref:razor-pages/filter).
* Para experimentar con los filtros, [descargue, pruebe y modifique el ejemplo de GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)

Los *filtros* en ASP.NET Core permiten que se ejecute el código antes o después de determinadas fases de la canalización del procesamiento de la solicitud.

Los filtros integrados se encargan de tareas como las siguientes:

* Autorización (impedir el acceso a los recursos a un usuario que no está autorizado).
* Almacenamiento en caché de respuestas (cortocircuitar la canalización de solicitud para devolver una respuesta almacenada en caché).

Se pueden crear filtros personalizados que se encarguen de cuestiones transversales. Entre los ejemplos de cuestiones transversales se incluyen el control de errores, el almacenamiento en caché, la configuración, la autorización y el registro.  Los filtros evitan la duplicación de código. Así, por ejemplo, un filtro de excepción de control de errores puede consolidar el control de errores.

Este documento se aplica a Razor las páginas, controladores de API y controladores con vistas.

[Vea o descargue el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Funcionamiento de los filtros

Los filtros se ejecutan dentro de la *canalización de invocación de acciones de ASP.NET Core* , a veces denominada *canalización de filtro* .  La canalización de filtro se ejecuta después de que ASP.NET Core seleccione la acción que se va a ejecutar.

![La solicitud se procesa a través de las fases de otro middleware, del middleware de enrutamiento, de la selección de acción y de la canalización de invocación de acciones de ASP.NET Core. El procesamiento de la solicitud continúa a la inversa, pasando por Selección de acción, Middleware de enrutamiento y varias fases de Otro middleware, antes de convertirse en una respuesta para enviarla al cliente.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Tipos de filtro

Cada tipo de filtro se ejecuta en una fase diferente dentro de la canalización de filtro:

* Los [filtros de autorización](#authorization-filters) se ejecutan en primer lugar y sirven para averiguar si el usuario está autorizado para realizar la solicitud. Los filtros de autorización pueden cortocircuitar la canalización si una solicitud no está autorizada.

* [Filtros de recursos](#resource-filters):

  * Se ejecutan después de la autorización.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> puede ejecutar código antes que el resto de la canalización del filtro. Por ejemplo, `OnResourceExecuting` puede ejecutar código antes que el enlace de modelos.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> puede ejecutar el código una vez que el resto de la canalización se haya completado.

* Los [filtros de acciones](#action-filters) pueden ejecutar código inmediatamente antes y después de llamar a un método de acción individual. Se pueden usar para manipular los argumentos pasados a una acción y el resultado obtenido de la acción. **No** se admiten filtros de acción en Razor las páginas.

* Los [filtros de excepciones](#exception-filters) sirven para aplicar directivas globales a las excepciones no controladas que se producen antes de que se escriba algo en el cuerpo de respuesta.

* Los [filtros de resultados](#result-filters) pueden ejecutar código inmediatamente antes y después de la ejecución de resultados de acción individuales. Se ejecutan solo cuando el método de acción se ha ejecutado correctamente. Son útiles para la lógica que debe regir la ejecución de la vista o el formateador.

En el siguiente diagrama se muestra cómo interactúan los tipos de filtro en la canalización de filtro.

![La solicitud se procesa a través de las fases Filtros de autorización, Filtros de recursos, Enlace de modelos, Filtros de acciones, Ejecución de acciones/Conversión del resultado de acción, Filtros de excepción, Filtros de resultados y Ejecución del resultado. Como resultado, la solicitud solo se procesa por las fases Filtros de resultados y Filtros de recursos antes de convertirse en una respuesta para enviarla al cliente.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementación

Los filtros admiten implementaciones tanto sincrónicas como asincrónicas a través de diferentes definiciones de interfaz.

Los filtros sincrónicos pueden ejecutar código antes (`On-Stage-Executing`) y después (`On-Stage-Executed`) de la fase de canalización. Por ejemplo, `OnActionExecuting` se llama antes de llamar al método de acción. `OnActionExecuted` se llama después de devolver el método de acción.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Los filtros asincrónicos definen un método `On-Stage-ExecutionAsync`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

En el código anterior, `SampleAsyncActionFilter` tiene un delegado <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que ejecuta el método de acción.  Cada uno de los métodos `On-Stage-ExecutionAsync` toman un `FilterType-ExecutionDelegate` que ejecuta la fase de canalización del filtro.

### <a name="multiple-filter-stages"></a>Varias fases de filtro

Se pueden implementar interfaces para varias fases de filtro en una sola clase. Por ejemplo, la clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` y sus equivalentes asincrónicos.

Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero **no** ambas. El entorno de ejecución comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, llama a la interfaz. De lo contrario, llamará a métodos de interfaz sincrónicos. Si se implementan las interfaces asincrónicas y sincrónicas en una clase, solo se llama al método asincrónico. Cuando se usan clases abstractas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, se invalidan solo los métodos sincrónicos o el método asincrónico de cada tipo de filtro.

### <a name="built-in-filter-attributes"></a>Atributos de filtros integrados

ASP.NET Core incluye filtros integrados basados en atributos que se pueden personalizar y a partir de los cuales crear subclases. Por ejemplo, el siguiente filtro de resultados agrega un encabezado a la respuesta:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Los atributos permiten a los filtros aceptar argumentos, como se muestra en el ejemplo anterior. Aplique el `AddHeaderAttribute` a un método de acción o controlador y especifique el nombre y el valor del encabezado HTTP:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

Algunas de las interfaces de filtro tienen atributos correspondientes que se pueden usar como clases base en las implementaciones personalizadas.

Atributos de filtro:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Ámbitos del filtro y orden de ejecución

Un filtro se puede agregar a la canalización en uno de tres *ámbitos* posibles:

* Mediante un atributo en una acción.
* Mediante un atributo en un controlador.
* Globalmente para todos los controladores y las acciones, tal como se muestra en el siguiente código:

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

El código anterior agrega tres filtros globalmente mediante la colección [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).

### <a name="default-order-of-execution"></a>Orden de ejecución predeterminado

Cuando hay varios filtros *del mismo tipo* , el ámbito determina el orden predeterminado en el que esos filtros se van a ejecutar.  Los filtros globales delimitan los filtros de clase. Los filtros de clase delimitan los filtros de método.

Como resultado de este anidamiento de filtros, el código de filtros *posterior* se ejecuta en el orden inverso al código *anterior* . La secuencia de filtro:

* El código *anterior* de los filtros globales.
  * El código *anterior* de los filtros de controlador.
    * El código *anterior* de los filtros de métodos de acción.
    * El código *posterior* de los filtros de métodos de acción.
  * El código *posterior* de los filtros de controlador.
* El código *posterior* de los filtros globales.
  
El ejemplo siguiente que ilustra el orden en el que se llama a los métodos de filtro relativos a filtros de acciones sincrónicos.

| Secuencia | Ámbito del filtro | Método de filtro |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | Controller | `OnActionExecuting` |
| 3 | Método | `OnActionExecuting` |
| 4 | Método | `OnActionExecuted` |
| 5 | Controller | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

Esta secuencia pone de manifiesto lo siguiente:

* El filtro de método está anidado en el filtro de controlador.
* El filtro de controlador está anidado en el filtro global.

### <a name="controller-and-no-locrazor-page-level-filters"></a>Filtros de Razor nivel de página y controlador

Cada controlador que hereda de la clase base <xref:Microsoft.AspNetCore.Mvc.Controller> incluye los métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) y [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`. Estos métodos:

* Encapsulan los filtros que se ejecutan para una acción determinada.
* `OnActionExecuting` se llama antes de cualquiera de los filtros de acciones.
* `OnActionExecuted` se llama después de todos los filtros de acciones.
* `OnActionExecutionAsync` se llama antes de cualquiera de los filtros de acciones. El código del filtro después de `next` se ejecuta después del método de acción.

Por ejemplo, en el ejemplo de descarga, se aplica `MySampleActionFilter` globalmente al inicio.

El `TestController`:

* Aplica `SampleActionFilterAttribute` (`[SampleActionFilter]`) a la acción `FilterTest2`.
* Invalida `OnActionExecuting` y `OnActionExecuted`.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

Si se dirige a `https://localhost:5001/Test/FilterTest2`, se ejecuta el código siguiente:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Para Razor las páginas, consulte [implementar Razor filtros de página mediante la invalidación de métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Invalidación del orden predeterminado

La secuencia de ejecución predeterminada se puede invalidar con la implementación de <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>. <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> expone la propiedad `IOrderedFilter` que tiene prioridad sobre el ámbito a la hora de determinar el orden de ejecución. Un filtro con un valor `Order` menor:

* Ejecuta el código *anterior* antes que el de un filtro con un valor mayor de `Order`.
* Ejecuta el código *posterior* después que el de un filtro con un valor mayor de `Order`.

La propiedad `Order` se puede establecer con un parámetro de constructor:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Considere los mismos tres filtros de acción que se muestran en el ejemplo anterior. Si la propiedad `Order` del controlador y de los filtros globales está establecida en 1 y 2 respectivamente, el orden de ejecución se invierte.

| Secuencia | Ámbito del filtro | Propiedad`Order` | Método de filtro |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Método | 0 | `OnActionExecuting` |
| 2 | Controller | 1  | `OnActionExecuting` |
| 3 | Global | 2  | `OnActionExecuting` |
| 4 | Global | 2  | `OnActionExecuted` |
| 5 | Controller | 1  | `OnActionExecuted` |
| 6 | Método | 0  | `OnActionExecuted` |

La propiedad `Order` invalida el ámbito al determinar el orden en el que se ejecutarán los filtros. Los filtros se clasifican por orden en primer lugar y, después, se usa el ámbito para priorizar en caso de igualdad. Todos los filtros integrados implementan `IOrderedFilter` y establecen el valor predeterminado de `Order` en 0. En los filtros integrados, el ámbito determina el orden, a menos que `Order` se establezca en un valor distinto de cero.

## <a name="cancellation-and-short-circuiting"></a>Cancelación y cortocircuito

La canalización de filtro se puede cortocircuitar en cualquier momento mediante el establecimiento de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> en el parámetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> que se ha proporcionado al método de filtro. Por ejemplo, el siguiente filtro de recursos impide que el resto de la canalización se ejecute:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

En el siguiente código, tanto el filtro `ShortCircuitingResourceFilter` como el filtro `AddHeader` tienen como destino el método de acción `SomeResource`. El `ShortCircuitingResourceFilter`:

* Se ejecuta en primer lugar, porque es un filtro de recursos y `AddHeader` es un filtro de acciones.
* Cortocircuita el resto de la canalización.

Por tanto, el filtro `AddHeader` nunca se ejecuta en relación con la acción `SomeResource`. Este comportamiento sería el mismo si ambos filtros se aplicaran en el nivel de método de acción, siempre y cuando `ShortCircuitingResourceFilter` se haya ejecutado primero. `ShortCircuitingResourceFilter` se ejecuta primero debido a su tipo de filtro o al uso explícito de la propiedad `Order`.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Inserción de dependencias

Los filtros se pueden agregar por tipo o por instancia. Si se agrega una instancia, esta se utiliza para todas las solicitudes. Si se agrega un tipo, se activa por tipo. Un filtro activado por tipo significa:

* Se crea una instancia para cada solicitud.
* Las dependencias de constructor se rellenan mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).

Los filtros que se implementan como atributos y se agregan directamente a las clases de controlador o a los métodos de acción no pueden tener dependencias de constructor proporcionadas por la [inserción de dependencias](xref:fundamentals/dependency-injection). Las dependencias de constructor no pueden proporcionarse mediante la inserción de dependencias porque:

* Los atributos deben tener los parámetros de constructor proporcionados allá donde se apliquen. 
* Se trata de una limitación de cómo funcionan los atributos.

Los filtros siguientes admiten dependencias de constructor proporcionadas en la inserción de dependencias:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> se implementa en el atributo.

Los filtros anteriores se pueden aplicar a un método de controlador o de acción:

Los registradores están disponibles en la inserción de dependencias. Sin embargo, evite crear y utilizar filtros únicamente con fines de registro. El [registro del marco integrado](xref:fundamentals/logging/index) proporciona normalmente lo que se necesita para el registro. Registro agregado a los filtros:

* Debe centrarse en cuestiones de dominio empresarial o en el comportamiento específico del filtro.
* **No** debe registrar acciones u otros eventos del marco. Los filtros integrados registran acciones y eventos del marco.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Los tipos de implementación de filtro de servicio se registran en `ConfigureServices`. <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera una instancia del filtro de la inserción de dependencias.

El código siguiente muestra `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

En el código siguiente, `AddHeaderResultServiceFilter` se agrega al contenedor de inserción de dependencias:

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

En el código siguiente, el atributo `ServiceFilter` recupera una instancia del filtro `AddHeaderResultServiceFilter` desde la inserción de dependencias:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Al usar `ServiceFilterAttribute`, si se establece [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* Proporciona una sugerencia que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó. El entorno de ejecución de ASP.NET Core no garantiza:

  * Que se creará una única instancia del filtro.
  * El filtro no volverá a solicitarse desde el contenedor de inserción de dependencias en algún momento posterior.

* No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` carga el tipo especificado desde la inserción de dependencias.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> es similar a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, pero su tipo no se resuelve directamente desde el contenedor de inserción de dependencias, sino que crea una instancia del tipo usando el elemento <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.

Dado que los tipos `TypeFilterAttribute` no se resuelven directamente desde el contenedor de inserción de dependencias:

* Los tipos a los que se hace referencia con `TypeFilterAttribute` no tienen que estar registrados con el contenedor de inserción de dependencias.  Sus dependencias se completan a través del contenedor de inserción de dependencias.
* `TypeFilterAttribute` puede aceptar opcionalmente argumentos de constructor del tipo en cuestión.

Al usar `TypeFilterAttribute`, si se establece [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):
* Proporciona una sugerencia que indica que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó. El entorno de ejecución de ASP.NET Core no ofrece ninguna garantía de que se vaya a crear una única instancia del filtro.

* No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.

En el siguiente ejemplo se muestra cómo pasar argumentos a un tipo mediante `TypeFilterAttribute`:

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Filtros de autorización

Filtros de autorización:

* Son los primeros filtros que se ejecutan en la canalización del filtro.
* Controlan el acceso a los métodos de acción.
* Tienen un método anterior, pero no uno posterior.

Los filtros de autorización personalizados requieren un marco de autorización personalizado. Es preferible configurar directivas de autorización o escribir una directiva de autorización personalizada a escribir un filtro personalizado. El filtro de autorización integrado:

* Llama a la autorización del sistema.
* No autoriza las solicitudes.

**No** inicie excepciones dentro de los filtros de autorización:

* La excepción no se controlará.
* Los filtros de excepciones no controlarán la excepción.

Considere la posibilidad de emitir un desafío cuando se produzca una excepción en un filtro de autorizaciones.

[Aquí](xref:security/authorization/introduction) encontrará más información sobre la autorización.

## <a name="resource-filters"></a>Filtros de recursos

Filtros de recursos:

* Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.
* La ejecución encapsula la mayor parte de la canalización de filtro.
* Los [filtros de autorizaciones](#authorization-filters) son los únicos que se ejecutan antes que los filtros de recursos.

Los filtros de recursos son útiles para cortocircuitar la mayor parte de la canalización. Por ejemplo, un filtro de almacenamiento en caché puede evitar que se ejecute el resto de la canalización en un acierto de caché.

Ejemplos de filtros de recursos:

* [El filtro de recursos de cortocircuito](#short-circuiting-resource-filter) mostrado anteriormente.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Evita que el enlace de modelos tenga acceso a los datos del formulario.
  * Se utiliza cuando hay cargas de archivos muy voluminosos para impedir que los datos del formulario se lean en la memoria.

## <a name="action-filters"></a>Filtros de acciones

> [!IMPORTANT]
> Los filtros de acción **no** se aplican a Razor las páginas. Razor Pages admite <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Para obtener más información, vea [Métodos de filtrado de Razor Pages](xref:razor-pages/filter).

Filtros de acciones:

* Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.
* Su ejecución rodea la ejecución de los métodos de acción.

El código siguiente muestra un ejemplo de filtro de acciones:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> ofrece las siguientes propiedades:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: permite leer las entradas de un método de acción.
* <xref:Microsoft.AspNetCore.Mvc.Controller>: permite manipular la instancia del controlador.
* <xref:System.Web.Mvc.ActionExecutingContext.Result>: si se establece `Result`, se cortocircuita la ejecución del método de acción y de los filtros de acciones posteriores.

Inicio de una excepción en un método de acción:

* Impide la ejecución de los filtros subsiguientes.
* A diferencia del establecimiento de `Result`, se trata como un error en lugar de como un resultado correcto.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> proporciona `Controller` y `Result`, además de las siguientes propiedades:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled>: es true si otro filtro ha cortocircuitado la ejecución de la acción.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception>: es un valor distinto de NULL si la acción o un filtro de acción de ejecución anterior han producido una excepción. Si se establece esta propiedad en un valor NULL:

  * Controla la excepción eficazmente.
  * `Result` se ejecuta como si se devolviera desde el método de acción.

En un `IAsyncActionFilter`, una llamada a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:

* Ejecuta cualquier filtro de acciones posterior y el método de acción.
* Devuelve `ActionExecutedContext`.

Para cortocircuitar esto, asigne <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a una instancia de resultado y no llame a `next` (la clase `ActionExecutionDelegate`).

El marco proporciona una clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstracta de la que se pueden crear subclases.

Se puede usar el filtro de acción `OnActionExecuting` para:

* Validar el estado del modelo.
* Devolver un error si el estado no es válido.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

El método `OnActionExecuted` se ejecuta después del método de acción:

* Y puede ver y manipular los resultados de la acción a través de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> se establece en true si otro filtro ha cortocircuitado la ejecución de la acción.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> se establece en un valor distinto de NULL si la acción o un filtro de acción posterior han producido una excepción. Si `Exception` se establece como nulo:

  * Controla una excepción eficazmente.
  * `ActionExecutedContext.Result` se ejecuta como si se devolviera con normalidad desde el método de acción.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Filtros de excepciones

Los filtros de excepciones:

* Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>. 
* Se pueden usar para implementar directivas de control de errores comunes.

En el siguiente filtro de excepciones de ejemplo se usa una vista de error personalizada para mostrar los detalles sobre las excepciones que se producen cuando la aplicación está en fase de desarrollo:

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Los filtros de excepciones:

* No tienen eventos anteriores ni posteriores.
* Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.
* Controlar las excepciones no controladas que se producen en la Razor creación de páginas o controladores, [enlace de modelos](xref:mvc/models/model-binding), filtros de acción o métodos de acción.
* **No** detectan aquellas excepciones que se produzcan en los filtros de recursos, en los filtros de resultados o en la ejecución de resultados de MVC.

Para controlar una excepción, establezca la propiedad <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> en `true` o escriba una respuesta. Esto detiene la propagación de la excepción. Un filtro de excepciones no tiene capacidad para convertir una excepción en un proceso "correcto". Eso solo lo pueden hacer los filtros de acciones.

Los filtros de excepciones:

* Son adecuados para interceptar las excepciones que se producen en las acciones.
* No son tan flexibles como el middleware de control de errores.

Es preferible usar middleware de control de excepciones. Utilice los filtros de excepciones solo cuando el control de errores *es diferente* en función del método de acción que se llama. Por ejemplo, puede que una aplicación tenga métodos de acción tanto para los puntos de conexión de API como para las vistas/HTML. Los puntos de conexión de API podrían devolver información sobre errores como JSON, mientras que las acciones basadas en vistas podrían devolver una página de error como HTML.

## <a name="result-filters"></a>Filtros de resultados

Filtros de resultados:

* Implementar una interfaz:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Su ejecución rodea la ejecución de los resultados de acción.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter e IAsyncResultFilter

El código siguiente muestra un filtro de resultados que agrega un encabezado HTTP:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

El tipo de resultado que se ejecute dependerá de la acción. Una acción que devuelve una vista incluye todo el procesamiento de Razor como parte del elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> que se está ejecutando. Un método API puede llevar a cabo algunas funciones de serialización como parte de la ejecución del resultado. [Aquí](xref:mvc/controllers/actions) encontrará más información sobre los resultados de acciones.

Los filtros de resultados solo se ejecutan cuando una acción o un filtro de acción genera un resultado de acción. Los filtros de resultados no se ejecutan cuando:

* Un filtro de autorización o un filtro de recursos genera un cortocircuito en la canalización.
* Un filtro de excepciones controla una excepción al producir un resultado de acción.

El método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> puede cortocircuitar la ejecución del resultado de la acción y de los filtros de resultados posteriores mediante el establecimiento de <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> en `true`. Escriba en el objeto de respuesta cuando el proceso se cortocircuite, ya que así evitará que se genere una respuesta vacía. Si se inicia una excepción en `IResultFilter.OnResultExecuting`, sucederá lo siguiente:

* Se impedirá la ejecución del resultado de la acción y de los filtros subsiguientes.
* Se tratará como un error en lugar de como un resultado correcto.

Cuando se ejecuta el método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, es probable que la respuesta ya se haya enviado al cliente. En este caso, no se puede cambiar más.

`ResultExecutedContext.Canceled` se establece en `true` si otro filtro ha cortocircuitado la ejecución del resultado de la acción.

`ResultExecutedContext.Exception` se establece en un valor distinto de NULL si el resultado de la acción o un filtro de resultado posterior ha producido una excepción. Establecer `Exception` en un valor NULL hace que una excepción se "controle" de forma eficaz y evita que ASP.NET Core vuelva a producir dicha excepción más adelante en la canalización. No hay una forma confiable de escribir datos en una respuesta cuando se controla una excepción en un filtro de resultados. Si los encabezados ya se han vaciado en el cliente si el resultado de una acción inicia una excepción, no hay ningún mecanismo confiable que permita enviar un código de error.

En un elemento <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una llamada a `await next` en <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ejecuta cualquier filtro de resultados posterior y el resultado de la acción. Para cortocircuitarlo, establezca [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) en `true` y no llame a `ResultExecutionDelegate`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

El marco proporciona una clase `ResultFilterAttribute` abstracta de la que se pueden crear subclases. La clase [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente es un ejemplo de un atributo de filtro de resultados.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter

Las interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaran una implementación <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que se ejecuta para obtener todos los resultados de la acción. Esto incluye los resultados de la acción generados por:

* Filtros de autorización y filtros de recursos que generan un cortocircuito.
* Filtros de excepción.

Por ejemplo, el siguiente filtro siempre ejecuta y establece un resultado de la acción (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un código de estado *422 - Entidad no procesable* cuando se produce un error en la negociación de contenido:

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Por tanto, una instancia de `IFilterFactory` se puede usar como una instancia de `IFilterMetadata` en cualquier parte de la canalización de filtro. Cuando el entorno de ejecución se prepara para invocar el filtro, intenta convertirlo a un `IFilterFactory`. Si esa conversión se realiza correctamente, se llama al método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear la instancia `IFilterMetadata` que se va a invocar. Esto proporciona un diseño flexible, dado que no hay que establecer la canalización de filtro exacta de forma explícita cuando la aplicación se inicia.

Puede implementar `IFilterFactory` con las implementaciones de atributos personalizados como método alternativo para crear filtros:

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

El código anterior se puede probar mediante la ejecución del [ejemplo de descargar](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):

* Invoque las herramientas de desarrollador de F12.
* Vaya a `https://localhost:5001/Sample/HeaderWithFactory`.

Las herramientas de desarrollador F12 muestran los siguientes encabezados de respuesta agregados por el código de ejemplo:

* **autor:**`Joe Smith`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **interno:**`My header`

El código anterior crea el encabezado de respuesta **internal:** `My header`.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>IFilterFactory implementado en un atributo

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Los filtros que implementan `IFilterFactory` son útiles para los filtros que:

* No requieren pasar parámetros.
* Tienen dependencias de constructor que deben completarse por medio de la inserción de dependencias.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` carga el tipo especificado desde el contenedor de servicios (inserción de dependencias).

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

El código siguiente muestra tres métodos para aplicar `[SampleActionFilter]`:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

En el código anterior, decorar el método con `[SampleActionFilter]` es el enfoque preferido para aplicar `SampleActionFilter`.

## <a name="using-middleware-in-the-filter-pipeline"></a>Uso de middleware en la canalización de filtro

Los filtros de recursos funcionan como el [middleware](xref:fundamentals/middleware/index), en el sentido de que se encargan de la ejecución de todo lo que viene después en la canalización. Pero los filtros se diferencian del middleware en que forman parte del entorno de ejecución de ASP.NET Core, lo que significa que tienen acceso al contexto y las construcciones de ASP.NET Core.

Para usar middleware como un filtro, cree un tipo con un método `Configure` en el que se especifique el middleware para insertar en la canalización de filtro. El ejemplo siguiente usa el middleware de localización para establecer la referencia cultural actual de una solicitud:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para ejecutar el middleware:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Los filtros de middleware se ejecutan en la misma fase de la canalización de filtro que los filtros de recursos, antes del enlace de modelos y después del resto de la canalización.

## <a name="next-actions"></a>Siguientes acciones

* Vea [métodos de filtro para Razor páginas](xref:razor-pages/filter).
* Para experimentar con los filtros, [descargue, pruebe y modifique el ejemplo de GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

::: moniker-end
