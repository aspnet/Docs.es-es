---
title: Creación de API web con ASP.NET Core
author: scottaddie
description: Conozca los aspectos básicos de la creación de una API web en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/20/2020
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
uid: web-api/index
ms.openlocfilehash: fb7dfa53be1fee9904b4539a5c9da0700c2aa884
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585739"
---
# <a name="create-web-apis-with-aspnet-core"></a>Creación de API web con ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie) y [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core admite la creación de servicios RESTful, lo que también se conoce como API web, mediante C#. Para gestionar las solicitudes, una API web usa controladores. Los *controladores* de una API web son clases que se derivan de `ControllerBase`. En este artículo se muestra cómo usar controladores para gestionar las solicitudes de API web.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/index/samples). ([Método de descarga](xref:index#how-to-download-a-sample)).

## <a name="controllerbase-class"></a>Clase ControllerBase

Una API web consta de una o varias clases de controlador que se derivan de <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. La plantilla de proyecto de API web proporciona un controlador de inicio:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

No cree un controlador de API web mediante la derivación de la clase <xref:Microsoft.AspNetCore.Mvc.Controller>. `Controller` se deriva de `ControllerBase` y agrega compatibilidad con vistas, por lo que sirve para gestionar páginas web, no solicitudes de API web. Hay una excepción a esta regla: si tiene pensado usar el mismo controlador tanto para las vistas como para las API web, debe derivarlo de `Controller`.

La clase `ControllerBase` ofrece muchas propiedades y métodos que son útiles para gestionar solicitudes HTTP. Por ejemplo, `ControllerBase.CreatedAtAction` devuelve un código de estado 201:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

A continuación se muestran algunos ejemplos más de métodos que proporciona `ControllerBase`.

|Método   |Notas    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| Devuelve el código de estado 400.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|Devuelve el código de estado 404.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|Devuelve un archivo.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|Invoca el [enlace de modelo](xref:mvc/models/model-binding).|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|Invoca la [validación de modelos](xref:mvc/models/validation).|

Para ver una lista de todos los métodos y propiedades disponibles, consulte <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.

## <a name="attributes"></a>Atributos

El espacio de nombres <xref:Microsoft.AspNetCore.Mvc> proporciona atributos que se pueden usar para configurar el comportamiento de los controladores API web y los métodos de acción. En el ejemplo siguiente se usan atributos para especificar el verbo de acción HTTP admitido y cualquier código de estado HTTP conocido que se pueda devolver:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Estos son algunos ejemplos más de atributos que están disponibles.

|Atributo|Notas|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Especifica el patrón de dirección URL de un controlador o una acción.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Especifica el prefijo y las propiedades que se incluirán en el enlace de modelo.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Identifica una acción que admite el verbo de acción GET HTTP.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Especifica los tipos de datos que acepta una acción.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Especifica los tipos de datos que devuelve una acción.|

Para ver una lista que incluye los atributos disponibles, consulte el espacio de nombres <xref:Microsoft.AspNetCore.Mvc>.

## <a name="apicontroller-attribute"></a>Atributo ApiController

El atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) se puede aplicar a una clase de controlador para permitir los siguientes comportamientos específicos de la API:

::: moniker range=">= aspnetcore-2.2"

* [Requisito de enrutamiento mediante atributos](#attribute-routing-requirement)
* [Respuestas HTTP 400 automáticas](#automatic-http-400-responses)
* [Inferencia de parámetro de origen de enlace](#binding-source-parameter-inference)
* [Inferencia de solicitud de varios elementos o datos de formulario](#multipartform-data-request-inference)
* [Detalles de problemas de los códigos de estado de error](#problem-details-for-error-status-codes)

La característica *Detalles de problemas de los códigos de estado de error* requiere una [versión de compatibilidad](xref:mvc/compatibility-version) de 2.2 o posterior. Las demás características requieren una versión de compatibilidad de 2.1 o posterior.

::: moniker-end

* [Requisito de enrutamiento mediante atributos](#attribute-routing-requirement)
* [Respuestas HTTP 400 automáticas](#automatic-http-400-responses)
* [Inferencia de parámetro de origen de enlace](#binding-source-parameter-inference)
* [Inferencia de solicitud de varios elementos o datos de formulario](#multipartform-data-request-inference)

Estas características requieren una [versión de compatibilidad](xref:mvc/compatibility-version) de 2.1 o posterior.

### <a name="attribute-on-specific-controllers"></a>Atributo en controladores específicos

El atributo `[ApiController]` puede aplicarse a controladores específicos, como se muestra en el siguiente ejemplo de la plantilla de proyecto:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Atributo en varios controladores

Una estrategia para el uso del atributo en más de un controlador consiste en crear una clase personalizada de controlador base anotada con el atributo `[ApiController]`. En el siguiente ejemplo se muestra una clase base personalizada y un controlador que se deriva de ella:

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Atributo en un ensamblado

Si la [versión de compatibilidad](xref:mvc/compatibility-version) está establecida en 2.2 o posterior, el atributo `[ApiController]` se puede aplicar a un ensamblado. En este contexto, la anotación aplica el comportamiento de API web a todos los controladores del ensamblado. No hay ninguna manera de excluir controladores específicos. Aplique el atributo de nivel de ensamblado a la declaración del espacio de nombres de alrededor de la clase `Startup`:

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a>Requisito de enrutamiento mediante atributos

El atributo `[ApiController]` convierte el enrutamiento de atributos en un requisito. Por ejemplo:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

No es posible acceder a las acciones mediante [rutas convencionales](xref:mvc/controllers/routing#conventional-routing) que hayan definido `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> o <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> en `Startup.Configure`.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

Las acciones no son accesibles mediante [rutas convencionales](xref:mvc/controllers/routing#conventional-routing) definidas por <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> o <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> en `Startup.Configure`.

::: moniker-end

## <a name="automatic-http-400-responses"></a>Respuestas HTTP 400 automáticas

El atributo `[ApiController]` hace que los errores de validación de un modelo desencadenen automáticamente una respuesta HTTP 400. Por lo tanto, el siguiente código no es necesario en un método de acción:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC usa el filtro de acciones <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> para realizar la comprobación anterior.

### <a name="default-badrequest-response"></a>Respuesta BadRequest predeterminada

Con una versión de compatibilidad de 2.1, el tipo de respuesta predeterminado para una respuesta HTTP 400 es <xref:Microsoft.AspNetCore.Mvc.SerializableError>. El siguiente cuerpo de solicitud es un ejemplo del tipo serializado:

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

Con una versión de compatibilidad de 2.2 o posterior, el tipo de respuesta predeterminado para una respuesta HTTP 400 es <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>. El siguiente cuerpo de solicitud es un ejemplo del tipo serializado:

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

Tipo `ValidationProblemDetails`:

* Proporciona un formato de lectura mecánica para especificar errores en las respuestas de la API web.
* Cumple los requisitos de la [especificación RFC 7807](https://tools.ietf.org/html/rfc7807).

::: moniker-end

Para que las respuestas automáticas y personalizadas sean coherentes, llame al método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A> en lugar de a <xref:System.Web.Http.ApiController.BadRequest%2A>. `ValidationProblem` devuelve un objeto <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> y la respuesta automática.

### <a name="log-automatic-400-responses"></a>Registro de respuestas 400 automáticas

Vea [Cómo registrar respuestas 400 automáticas sobre errores de validación de modelos (aspnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).

### <a name="disable-automatic-400-response"></a>Deshabilitación de las respuestas 400 automáticas

Para deshabilitar el comportamiento 400 automático, establezca la propiedad <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> en `true`. Agregue el código resaltado siguiente en `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>Inferencia de parámetro de origen de enlace

Un atributo de origen de enlace define la ubicación del valor del parámetro de una acción. Existen los atributos de origen de enlace siguientes:

|Atributo|Origen de enlace |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Cuerpo de la solicitud |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Datos del formulario en el cuerpo de la solicitud |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | Encabezado de la solicitud |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Parámetro de la cadena de consulta de la solicitud |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Datos de ruta de la solicitud actual |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Servicio de solicitud insertado como parámetro de acción |

> [!WARNING]
> No use `[FromRoute]` si los valores pueden contener `%2f` (es decir, `/`). `%2f` no incluirá el carácter sin escape `/`. Use `[FromQuery]` si el valor puede contener `%2f`.

Sin el atributo `[ApiController]` o los atributos de origen de enlace, como `[FromQuery]`, el entorno de tiempo de ejecución de ASP.NET Core intenta usar el enlazador de modelos de objetos complejos. El enlazador de modelos de objetos complejos extrae los datos de los proveedores de valor en un orden definido.

En el ejemplo siguiente, el atributo `[FromQuery]` indica que el valor del parámetro `discontinuedOnly` se proporciona en la cadena de consulta de la dirección URL de la solicitud:

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

El atributo `[ApiController]` aplica reglas de inferencia a los orígenes de datos predeterminados de los parámetros de acción. Estas reglas aplican atributos a los parámetros de acción, lo que ahorra la necesidad de identificar los orígenes de enlace manualmente. Las reglas de inferencia de orígenes de enlace se comportan de la manera siguiente:

* `[FromBody]` se infiere para parámetros de tipo complejo. La excepción a `[FromBody]` esta regla es cualquier tipo integrado complejo que tenga un significado especial, como <xref:Microsoft.AspNetCore.Http.IFormCollection> y <xref:System.Threading.CancellationToken>. El código de inferencia del origen de enlace omite esos tipos especiales.
* `[FromForm]` se infiere para los parámetros de acción de tipo <xref:Microsoft.AspNetCore.Http.IFormFile> y <xref:Microsoft.AspNetCore.Http.IFormFileCollection>. No se infiere para los tipos simples o definidos por el usuario.
* `[FromRoute]` se infiere para cualquier nombre de parámetro de acción que coincida con un parámetro de la plantilla de ruta. Si varias rutas coinciden con un parámetro de acción, cualquier valor de ruta se considera `[FromRoute]`.
* `[FromQuery]` se infiere para cualquier otro parámetro de acción.

### <a name="frombody-inference-notes"></a>Notas de la inferencia de FromBody

En el caso de los tipos simples, como `string` y `int`, `[FromBody]` no se infiere. Así pues, para los tipos simples, en los casos en los que quiera utilizar dicha funcionalidad, conviene usar el atributo `[FromBody]`.

Cuando una acción tiene más de un parámetro enlazado desde el cuerpo de solicitud, se produce una excepción. Por ejemplo, todas las firmas de acción siguientes provocan una excepción:

* `[FromBody]` se infiere en ambos porque son tipos complejos.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* El atributo `[FromBody]` en uno se infiere en el otro porque es un tipo complejo.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* El atributo `[FromBody]` se infiere en ambos.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> En ASP.NET Core 2.1, los parámetros de tipo de colección, como listas y matrices, se infieren incorrectamente como `[FromQuery]`. El atributo `[FromBody]` debe usarse con estos parámetros si van a enlazarse desde el cuerpo de solicitud. Este comportamiento se ha corregido en ASP.NET Core 2.2 o posterior, donde se infieren los parámetros de tipo de colección para enlazarse desde el cuerpo de forma predeterminada.

::: moniker-end

### <a name="disable-inference-rules"></a>Deshabilitación de las reglas de inferencia

Para deshabilitar la inferencia del origen de enlace, establezca <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> en `true`. Agregue el siguiente código en `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>Inferencia de solicitud de varios elementos o datos de formulario

El atributo `[ApiController]` aplica una regla de inferencia cuando se anota un parámetro de acción con el atributo [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute). Se infiere el tipo de contenido de la solicitud `multipart/form-data`.

Para deshabilitar el comportamiento predeterminado, establezca la propiedad <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> en `true` en `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a>Detalles de problemas de los códigos de estado de error

Cuando la versión de compatibilidad es 2.2 o posterior, MVC transforma un resultado de error (un resultado con el código de estado 400 o superior) en un resultado con <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>. El tipo `ProblemDetails` se basa en la [especificación RFC 7807](https://tools.ietf.org/html/rfc7807) para proporcionar detalles de error de lectura mecánica en una respuesta HTTP.

Observe el código siguiente en una acción de controlador:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

El método `NotFound` genera un código de estado HTTP 404 con un cuerpo `ProblemDetails`. Por ejemplo:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>Deshabilitación de la respuesta ProblemDetails

La creación automática de `ProblemDetails` para los códigos de estado de error está deshabilitada cuando la propiedad <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> está establecida en `true`. Agregue el siguiente código en `Startup.ConfigureServices`:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>Definición de tipos de contenido de la solicitud compatibles con el atributo [Consumes]

De forma predeterminada, una acción admite todos los tipos de contenido de la solicitud disponibles. Por ejemplo, si una aplicación está configurada para admitir [formateadores de entrada](xref:mvc/models/model-binding#input-formatters) JSON y XML, una acción admite varios tipos de contenido, incluidos `application/json` y `application/xml`.

El atributo [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que una acción limite los tipos de contenido de la solicitud compatibles. Aplique el atributo `[Consumes]` a una acción o controlador, especificando uno o varios tipos de contenido:

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

En el código anterior, la acción `CreateProduct` especifica el tipo de contenido `application/xml`. Las solicitudes enrutadas a esta acción deben especificar un encabezado `Content-Type` de `application/xml`. Las solicitudes que no especifican un encabezado `Content-Type` de `application/xml` generan una respuesta [415 Tipo de medio no compatible](https://developer.mozilla.org/docs/Web/HTTP/Status/415).

El atributo `[Consumes]` también permite que una acción influya en su selección en función del tipo de contenido de una solicitud entrante aplicando una restricción de tipo. Considere el ejemplo siguiente:

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

En el código anterior, `ConsumesController` se configura para controlar las solicitudes enviadas a la dirección URL `https://localhost:5001/api/Consumes`. Las dos acciones del controlador, `PostJson` y `PostForm`, controlan las solicitudes POST con la misma dirección URL. Sin el atributo `[Consumes]` que aplica una restricción de tipo, se produce una excepción de coincidencia ambigua.

El atributo `[Consumes]` se aplica a ambas acciones. La acción `PostJson` controla las solicitudes enviadas con un encabezado `Content-Type` de `application/json`. La acción `PostForm` controla las solicitudes enviadas con un encabezado `Content-Type` de `application/x-www-form-urlencoded`. 

## <a name="additional-resources"></a>Recursos adicionales

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
* [Microsoft Learn: Creación de una API web con ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
