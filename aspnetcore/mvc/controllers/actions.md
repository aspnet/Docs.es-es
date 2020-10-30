---
title: Control de solicitudes con controladores en ASP.NET Core MVC
author: ardalis
description: ''
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/controllers/actions
ms.openlocfilehash: a9319e74d0213b178c2a71be69a0332270d9446c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061462"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a>Control de solicitudes con controladores en ASP.NET Core MVC

Por [Steve Smith](https://ardalis.com/) y [Scott Addie](https://github.com/scottaddie)

Los controladores, las acciones y los resultados de acciones son una parte fundamental del proceso que siguen los desarrolladores para compilar aplicaciones con ASP.NET Core MVC.

## <a name="what-is-a-controller"></a>¿Qué es un controlador?

Los controladores se usan para definir y agrupar un conjunto de acciones. Una acción (o *método de acción* ) es un método en un controlador que controla las solicitudes. Los controladores agrupan lógicamente acciones similares. Esta agregación de acciones permite aplicar de forma colectiva conjuntos comunes de reglas, como el enrutamiento, el almacenamiento en caché y la autorización. Las solicitudes se asignan a acciones mediante el [enrutamiento](xref:mvc/controllers/routing).

Por convención, las clases de controlador:

* Residen en la carpeta *Controllers* del nivel de raíz del proyecto.
* Heredan de `Microsoft.AspNetCore.Mvc.Controller`.

Un controlador es una clase instanciable en la que se cumple al menos una de las siguientes condiciones:

* El nombre de clase tiene el sufijo `Controller`.
* La clase se hereda de una clase cuyo nombre tiene el sufijo `Controller`.
* El atributo `[Controller]` se aplica a la clase.

Una clase de controlador no debe tener asociado un atributo `[NonController]`.

Los controladores deben seguir el [principio de dependencias explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies). Existen dos maneras de implementar este principio. Si varias acciones de controlador requieren el mismo servicio, considere la posibilidad de usar la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection) para solicitar esas dependencias. Si el servicio solo lo requiere un único método de acción, considere la posibilidad de usar la [inserción de acciones](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) para solicitar la dependencia.

En el patrón de **M** odel- **V** iew- **C** ontroller, un controlador se encarga del procesamiento inicial de la solicitud y la creación de instancias del modelo. Por lo general, las decisiones empresariales deben realizarse dentro del modelo.

El controlador toma el resultado del procesamiento del modelo (si existe) y devuelve o bien la vista correcta y sus datos de vista asociados, o bien el resultado de la llamada API. Obtenga más información en [Información general de ASP.NET Core MVC](xref:mvc/overview) e [Introducción a ASP.NET Core MVC y Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

El controlador es una abstracción de *nivel de interfaz de usuario* . Se encarga de garantizar que los datos de la solicitud son válidos y de elegir qué vista (o resultado de una API) se debe devolver. En las aplicaciones factorizadas correctamente, no incluye directamente acceso a datos ni lógica de negocios. En su lugar, el controlador delega en servicios el control de estas responsabilidades.

## <a name="defining-actions"></a>Definición de acciones

Los métodos públicos de un controlador, excepto los que incluyen el atributo `[NonAction]`, son acciones. Los parámetros de las acciones están enlazados a los datos de la solicitud y se validan mediante el [enlace de modelos](xref:mvc/models/model-binding). La validación de modelos se lleva a cabo para todo lo que está enlazado a un modelo. El valor de la propiedad `ModelState.IsValid` indica si el enlace de modelos y la validación se han realizado correctamente.

Los métodos de acción deben contener lógica para asignar una solicitud a una cuestión empresarial. Normalmente las cuestiones empresariales se deben representar como servicios a los que el controlador obtiene acceso mediante la [inserción de dependencias](xref:mvc/controllers/dependency-injection). Después, las acciones asignan el resultado de la acción empresarial a un estado de aplicación.

Las acciones pueden devolver de todo, pero suelen devolver una instancia de `IActionResult` (o `Task<IActionResult>` para métodos asincrónicos) que genera una respuesta. El método de acción se encarga de elegir el *tipo de respuesta* . El resultado de la acción se encarga de la *respuesta* .

### <a name="controller-helper-methods"></a>métodos del asistente de controlador

Los controladores normalmente heredan de [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), aunque esto no es necesario. Al derivar de `Controller`, se proporciona acceso a tres categorías de métodos del asistente:

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. métodos que producen un cuerpo de respuesta vacío

No se incluye ningún encabezado de respuesta HTTP `Content-Type`, ya que el cuerpo de la respuesta no tiene contenido que describir.

Hay dos tipos de resultados en esta categoría: redireccionamiento y código de estado HTTP.

* **Código de estado HTTP**

    Este tipo devuelve un código de estado HTTP. `BadRequest`, `NotFound` y `Ok` son ejemplos de métodos del asistente de este tipo. Por ejemplo, `return BadRequest();` genera un código de estado 400 cuando se ejecuta. Cuando métodos como `BadRequest`, `NotFound` y `Ok` están sobrecargados, ya no se consideran respondedores de código de estado HTTP, dado que se lleva a cabo una negociación de contenido.

* **Redirect**

    Este tipo devuelve un redireccionamiento a una acción o destino (mediante `Redirect`, `LocalRedirect`, `RedirectToAction` o `RedirectToRoute`). Por ejemplo, `return RedirectToAction("Complete", new {id = 123});` pasa un objeto anónimo y redirige a `Complete`.

    El tipo de resultado de redireccionamiento difiere del tipo de código de estado HTTP principalmente en que se agrega un encabezado de respuesta HTTP `Location`.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. métodos que producen un cuerpo de respuesta no vacío con un tipo de contenido predefinido

La mayoría de los métodos del asistente de esta categoría incluye una propiedad `ContentType`, lo que permite establecer el encabezado de respuesta `Content-Type` para describir el cuerpo de la respuesta.

Hay dos tipos de resultados en esta categoría: [vista](xref:mvc/views/overview) y [respuesta con formato](xref:web-api/advanced/formatting).

* **Ver**

    Este tipo devuelve una vista que usa un modelo para representar HTML. Por ejemplo, `return View(customer);` pasa un modelo a la vista para el enlace de datos.

* **Respuesta con formato**

    Este tipo devuelve JSON o un formato similar de intercambio de datos para representar un objeto de una manera específica. Por ejemplo, `return Json(customer);` serializa el objeto proporcionado en formato JSON.
    
    Otros métodos comunes de este tipo son `File` y `PhysicalFile`. Por ejemplo, `return PhysicalFile(customerFilePath, "text/xml");` devuelve [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult).

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. métodos que producen un cuerpo de respuesta no vacío con formato en un tipo de contenido negociado con el cliente

Esta categoría también se conoce como **negociación de contenido** . La [negociación de contenido](xref:web-api/advanced/formatting#content-negotiation) se aplica siempre que una acción devuelve un tipo [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) o un valor distinto de una implementación [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult). Si una acción devuelve una implementación que no sea `IActionResult` (por ejemplo, `object`), también devolverá una respuesta con formato.

Algunos métodos del asistente de este tipo son `BadRequest`, `CreatedAtRoute` y `Ok`. Algunos ejemplos de estos métodos son `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);` y `return Ok(value);`, respectivamente. Tenga en cuenta que `BadRequest` y `Ok` realizan la negociación de contenido solo cuando se pasa un valor; si no se pasa un valor, actúan como tipos de resultado de código de estado HTTP. Por otro lado, el método `CreatedAtRoute` siempre realiza la negociación de contenido, ya que todas sus sobrecargas requieren que se pase un valor.

### <a name="cross-cutting-concerns"></a>Cuestiones transversales

Normalmente, las aplicaciones comparten partes de su flujo de trabajo. Un ejemplo de esto podría ser una aplicación que requiere autenticación para obtener acceso al carro de la compra o una aplicación que almacena en caché datos en algunas páginas. Para llevar a cabo la lógica antes o después de un método de acción, use un *filtro* . El uso de [filtros](xref:mvc/controllers/filters) en cuestiones transversales puede reducir la duplicación.

La mayoría de los atributos de filtro, como `[Authorize]`, se puede aplicar en el nivel de controlador o de acción en función del nivel deseado de granularidad.

El control de errores y el almacenamiento en caché de respuestas suelen ser cuestiones transversales:
* [Gestión de errores](xref:mvc/controllers/filters#exception-filters)
* [Almacenamiento en caché de respuestas](xref:performance/caching/response)

Es posible controlar muchas cuestiones transversales mediante el uso de filtros o [software intermedio](xref:fundamentals/middleware/index) personalizado.
