---
title: API JSON básicas con Route-to-code en ASP.net Core
author: jamesnk
description: Obtenga información sobre cómo usar Route-to-code los métodos de extensión JSON y para crear API Web JSON ligeras.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: f8a3804a887ebfa0f5284d8991e903c978b18208
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556611"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>API JSON básicas con Route-to-code en ASP.net Core

Por [James Newton-King](https://github.com/jamesnk)

ASP.NET Core admite varias maneras de crear API Web JSON:

* [ASP.net Core API Web](xref:web-api/index) proporciona un marco de trabajo completo para crear API. Un servicio se crea heredando de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Algunas de las características proporcionadas por el marco incluyen el enlace de modelos, la validación, la negociación de contenido, el formato de entrada y salida, y OpenAPI.
* Route-to-code es una alternativa que no es de marco a ASP.NET Core API Web. Route-to-code conecta el [enrutamiento de ASP.net Core](xref:fundamentals/routing) directamente al código. El código Lee de la solicitud y escribe la respuesta. Route-to-code no tiene las características avanzadas de la API Web, pero tampoco hay ninguna configuración necesaria para usarla.

Route-to-code es un buen enfoque a la hora de compilar API Web JSON pequeñas y básicas.

## <a name="create-json-web-apis"></a>Creación de API Web JSON

ASP.NET Core proporciona métodos auxiliares que facilitan la creación de API Web JSON:

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> comprueba el `Content-Type` encabezado para un tipo de contenido JSON.
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> Lee JSON de la solicitud y lo deserializa en el tipo especificado.
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> escribe el valor especificado como JSON en el cuerpo de la respuesta y establece el tipo de contenido de respuesta en `application/json` .

Las API de JSON basadas en rutas ligeras se especifican en *Startup.CS*. La ruta y la lógica de la API se configuran en <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> como parte de la canalización de solicitudes de una aplicación.

### <a name="write-json-response"></a>Escribir respuesta JSON

Considere el siguiente código que configura una API JSON para una aplicación:

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

El código anterior:

* Agrega un punto de conexión de API HTTP GET con `/hello/{name:alpha}` como plantilla de ruta.
* Cuando se encuentra una coincidencia con la ruta, la API lee el `name` valor de ruta de la solicitud.
* Escribe un tipo anónimo como respuesta JSON con `WriteAsJsonAsync` .

### <a name="read-json-request"></a>Leer solicitud JSON

`HasJsonContentType` y `ReadFromJsonAsync` se pueden usar para deserializar una respuesta JSON en una API JSON basada en rutas:

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

El código anterior:

* Agrega un punto de conexión de API HTTP POST con `/weather` como plantilla de ruta.
* Cuando se encuentra una coincidencia con la ruta, `HasJsonContentType` valida el tipo de contenido de la solicitud. Un tipo de contenido no JSON devuelve un código de estado 415.
* Si el tipo de contenido es JSON, se deserializa el contenido de la solicitud `ReadFromJsonAsync` .

### <a name="configure-json-serialization"></a>Configurar la serialización de JSON

Hay dos maneras de personalizar la serialización de JSON:

* Las opciones de serialización predeterminadas se pueden configurar con <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> en el `Startup.ConfigureServices` método.
* `WriteAsJsonAsync` y `ReadFromJsonAsync` tienen sobrecargas que aceptan un <xref:System.Text.Json.JsonSerializerOptions> objeto. Este objeto de opciones invalida las opciones predeterminadas.

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>Autenticación y autorización

Route-to-code admite la autenticación y autorización. Los atributos, como `[Authorize]` y `[AllowAnonymous]` , no se pueden colocar en puntos de conexión que se asignan a un delegado de solicitud. En su lugar, los metadatos de autorización se agregan mediante los <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> métodos de extensión y.

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>Inserción de dependencias

La [inserción de dependencias (di)](xref:fundamentals/dependency-injection) mediante un constructor no es posible con Route-to-code . Web API crea un controlador para usted con los servicios insertados en el constructor. Un tipo no se crea cuando se ejecuta un punto de conexión, por lo que los servicios se deben resolver manualmente.

Las API basadas en rutas pueden usar <xref:System.IServiceProvider> para resolver servicios:

* Los servicios de duración transitoria y de ámbito, como `DbContext` , se deben resolver desde [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) dentro del delegado de solicitud de un punto de conexión.
* Los servicios de duración de singleton, como `ILogger` , se pueden resolver desde [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider). Los servicios se pueden resolver fuera de los delegados de solicitud y compartirse entre los extremos.

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

Las API que utilizan DI extensamente deberían considerar el uso de un tipo de aplicación ASP.NET Core que admita DI. Por ejemplo, ASP.NET Core API Web. La inserción de servicios mediante el constructor de un controlador es más fácil que resolver los servicios de forma manual.

## <a name="api-project-structure"></a>Estructura del proyecto de API

No es necesario que las API basadas en enrutamiento se encuentren en *Startup.CS*. Las API se pueden colocar en otros archivos y asignar al inicio con `UseEndpoints` . Este enfoque reduce el tamaño del archivo de configuración de inicio.

Considere la siguiente `UserApi` clase estática que define un `Map` método. El método asigna las API basadas en rutas.

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

En el `Startup.Configure` método, `Map` se llama al método y a los métodos estáticos de la clase en `UseEndpoints` :

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>Características destacadas que faltan en comparación con Web API

Route-to-code está diseñado para las API básicas de JSON. No es compatible con muchas de las características avanzadas que proporciona ASP.NET Core API Web.

Entre las características no proporcionadas por se Route-to-code incluyen:

* Enlace de modelos
* Validación de modelos
* OpenAPI/Swagger
* Negociación de contenido
* Inserción de dependencias de constructor
* `ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))

Considere la posibilidad de usar [ASP.net Core API Web](xref:web-api/index) para crear una API si requiere algunas de las características de la lista anterior.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
