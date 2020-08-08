---
title: Aplicación de formato a datos de respuesta en ASP.NET Core Web API
author: ardalis
description: Obtenga información sobre cómo dar formato a datos de respuesta en ASP.NET Core Web API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 8aa94bd1f33d1dd8ce8e7f50468ed60b4ccb2515
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019942"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Aplicación de formato a datos de respuesta en ASP.NET Core Web API

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC tiene compatibilidad para formatear datos de respuesta. Se pueden formatear los datos de respuesta con formatos específicos o en respuesta al formato solicitado por el cliente.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Resultados de acción específicos del formato

Algunos tipos de resultado de acción son específicos de un formato determinado, como <xref:Microsoft.AspNetCore.Mvc.JsonResult> y <xref:Microsoft.AspNetCore.Mvc.ContentResult>. Las acciones pueden devolver resultados con un formato determinado, independientemente de las preferencias del cliente. Por ejemplo, la devolución de `JsonResult` devuelve datos con formato JSON. Al devolver `ContentResult` o una cadena se devuelven datos de cadena con formato de texto sin formato.

No se requiere una acción para devolver ningún tipo específico. ASP.NET Core admite cualquier valor devuelto de objeto.  Los resultados de acciones que devuelven objetos que no son tipos <xref:Microsoft.AspNetCore.Mvc.IActionResult> se serializan con la implementación <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> correspondiente. Para obtener más información, vea <xref:web-api/action-return-types>.

El método auxiliar integrado <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> devuelve datos con formato JSON: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

La descarga de ejemplo devuelve la lista de autores. Con las herramientas para desarrolladores del explorador F12 o [Postman](https://www.getpostman.com/tools) con el código anterior:

* Se muestra el encabezado de respuesta que contiene **content-type:** `application/json; charset=utf-8`.
* Se muestran los encabezados de solicitud. Por ejemplo, el encabezado `Accept`. El código anterior omite el encabezado `Accept`.

Para devolver datos con formato de texto sin formato, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> y el método del asistente <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A>:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

En el código anterior, el `Content-Type` devuelto es `text/plain`. Al devolver una cadena se proporciona `Content-Type` de `text/plain`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Para las acciones con varios tipos de valor devuelto, devuelva `IActionResult`. Por ejemplo, la devolución de códigos de estado HTTP diferentes en función del resultado de las operaciones realizadas.

## <a name="content-negotiation"></a>Negociación de contenido

La negociación de contenido se produce cuando el cliente especifica un [encabezado Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). El formato predeterminado que ASP.NET Core usa es [JSON](https://json.org/). La negociación de contenido:

* La implementa <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.
* Se integra en los resultados de acción específicos del código de estado devueltos por los métodos auxiliares. Los métodos auxiliares de los resultados de acción se basan en `ObjectResult`.

Cuando se devuelve un tipo de modelo, el tipo de valor devuelto es `ObjectResult`.

El siguiente método de acción usa los métodos del asistente `Ok` y `NotFound`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

De forma predeterminada, ASP.NET Core admite los tipos de medios `application/json`, `text/json` y `text/plain`. Las herramientas como [Fiddler](https://www.telerik.com/fiddler) o [Postman](https://www.getpostman.com/tools) pueden establecer el encabezado de solicitud `Accept` para especificar el formato de devolución. Cuando el encabezado `Accept` contiene un tipo que el servidor admite, se devuelve ese tipo. En la sección siguiente se muestra cómo agregar otros formateadores.

Las acciones del controlador pueden devolver POCO (objetos CLR antiguos sin formato). Cuando se devuelve un objeto POCO, el tiempo de ejecución crea automáticamente un `ObjectResult` que encapsula al objeto. El cliente obtiene el objeto serializado con formato. Si el objeto que se va a devolver es `null`, se devuelve una respuesta `204 No Content`.

Devolución de un tipo de objeto:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

En el código anterior, una solicitud de un alias de autor válido devuelve una respuesta `200 OK` con los datos del autor. Una solicitud de un alias no válido devuelve una respuesta `204 No Content`.

### <a name="the-accept-header"></a>El encabezado Accept

La *negociación* de contenido se lleva a cabo cuando en la solicitud aparece un encabezado `Accept`. Cuando una solicitud contiene un encabezado Accept, ASP.NET Core:

* Enumera los tipos de medios del encabezado Accept en orden de preferencia.
* Intenta encontrar un formateador que pueda generar una respuesta en uno de los formatos especificados.

Si no se encuentra ningún formateador que pueda satisfacer la solicitud del cliente, ASP.NET Core:

* Devuelve `406 Not Acceptable` si se ha establecido <xref:Microsoft.AspNetCore.Mvc.MvcOptions>, o bien
* Intenta encontrar el primer formateador que puede generar una respuesta.

Si no se ha configurado ningún formateador para el formato solicitado, se usará el primer formateador que pueda dar formato al objeto. Si no aparece ningún encabezado `Accept` en la solicitud:

* El primer formateador que puede controlar el objeto se usa para serializar la respuesta.
* No tiene lugar ninguna negociación. El servidor está determinando el formato que se va a devolver.

Si el encabezado Accept contiene `*/*`, el encabezado se omite a menos que `RespectBrowserAcceptHeader` esté establecido en true en <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.

### <a name="browsers-and-content-negotiation"></a>Exploradores y negociación de contenido

A diferencia de los clientes de API típicos, los exploradores web proporcionan encabezados `Accept`. El explorador web especifica muchos formatos, incluidos los comodines. De forma predeterminada, cuando el marco detecta que la solicitud procede de un explorador:

* El encabezado `Accept` se omite.
* El contenido se devuelve en JSON, a menos que se configure de otra manera.

Esto proporciona una experiencia más coherente entre los exploradores al consumir las API.

Para configurar una aplicación para que respete los encabezados de aceptación del explorador, establezca <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> en `true`:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Configuración de formateadores

Las aplicaciones que necesitan admitir formatos adicionales pueden agregar los paquetes NuGet adecuados y configurar la compatibilidad. Hay formateadores independientes para la entrada y la salida. El [enlace de modelos](xref:mvc/models/model-binding) usa formateadores de entrada. Los formateadores de salida se usan para dar formato a las respuestas. Para obtener información sobre la creación de un formateador personalizado, vea [Formateadores personalizados](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Adición de compatibilidad con el formato XML

Los formateadores XML que se implementan mediante <xref:System.Xml.Serialization.XmlSerializer> se configuran llamando a <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

El código anterior serializa los resultados mediante `XmlSerializer`.

Cuando se usa el código anterior, los métodos de controlador devuelven el formato adecuado en función del encabezado `Accept` de la solicitud.

### <a name="configure-systemtextjson-based-formatters"></a>Configuración de formateadores basados en System.Text.Json

Las características para los formateadores basados en `System.Text.Json` pueden configurarse mediante `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Las opciones de serialización de salida se pueden configurar para cada acción mediante `JsonResult`. Por ejemplo:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Adición de compatibilidad con el formato JSON basado en Newtonsoft.Json

Antes de ASP.NET Core 3.0, los formateadores JSON usados de forma predeterminada son los implementados mediante el paquete `Newtonsoft.Json`. En ASP.NET Core 3.0 o posterior, los formateadores JSON predeterminados se basan en `System.Text.Json`. La compatibilidad con `Newtonsoft.Json` formateadores y características basados en está disponible mediante la instalación del [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) paquete de NuGet y su configuración en `Startup.ConfigureServices` .

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Es posible que algunas características no funcionen bien con formateadores basados en `System.Text.Json` y requieren una referencia a los formateadores basados en `Newtonsoft.Json`. Siga usando los formateadores basados en `Newtonsoft.Json` si la aplicación:

* Usa atributos `Newtonsoft.Json`.  Por ejemplo, `[JsonProperty]` o `[JsonIgnore]`.
* Proporciona la configuración de la serialización.
* Se basa en las características que `Newtonsoft.Json` proporciona.
* Configura `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Antes de ASP.NET Core 3.0, `JsonResult.SerializerSettings` acepta una instancia de `JsonSerializerSettings` específica de `Newtonsoft.Json`.
* Genera la documentación de [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).

Las características para los formateadores basados en `Newtonsoft.Json` pueden configurarse mediante `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

Las opciones de serialización de salida se pueden configurar para cada acción mediante `JsonResult`. Por ejemplo:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Adición de compatibilidad con el formato XML

El formato XML requiere el paquete NuGet [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/).

Los formateadores XML que se implementan mediante <xref:System.Xml.Serialization.XmlSerializer> se configuran llamando a <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

El código anterior serializa los resultados mediante `XmlSerializer`.

Cuando se usa el código anterior, los métodos de controlador deben devolver el formato adecuado en función del encabezado `Accept` de la solicitud.

::: moniker-end

### <a name="specify-a-format"></a>Especificación de un formato

Para restringir los formatos de respuesta, aplique el [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro. Al igual que la mayoría de los [filtros](xref:mvc/controllers/filters), `[Produces]` se pueden aplicar en el ámbito de la acción, el controlador o global:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

El [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro anterior:

* Obliga a que todas las acciones dentro del controlador devuelvan respuestas con formato JSON.
* Si se configuran otros formateadores y el cliente especifica un formato diferente, se devuelve JSON.

Para más información, consulte [Filtros](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Formateadores de casos especiales

Algunos casos especiales se implementan mediante formateadores integrados. De forma predeterminada, los tipos de valor devueltos `string` se formatean como *texto/sin formato* (*texto/html* si se solicita a través del encabezado `Accept`). Este comportamiento se puede quitar mediante la eliminación de <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>. Los formateadores se quitan en el método `ConfigureServices`. Las acciones que tienen un tipo de valor devuelto de objeto de modelo devuelven `204 No Content` al devolver `null`. Este comportamiento se puede quitar mediante la eliminación de <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>. El código siguiente quita `StringOutputFormatter` y `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Sin `StringOutputFormatter`, el formateador de JSON integrado aplica formato a los tipos devueltos `string`. Si se quita el formateador JSON integrado y está disponible un formateador XML, el formateador XML aplica formato a los tipos devueltos `string`. De lo contrario, los tipos devueltos `string` devuelven `406 Not Acceptable`.

Sin `HttpNoContentOutputFormatter`, se da formato a los objetos nulos mediante el formateador configurado. Por ejemplo:

* El formateador JSON devuelve una respuesta con un cuerpo de `null`.
* El formateador XML devuelve un elemento XML vacío con el atributo `xsi:nil="true"` establecido.

## <a name="response-format-url-mappings"></a>Asignaciones de direcciones URL de formato de respuesta

Los clientes pueden solicitar un formato determinado como parte de la dirección URL, por ejemplo:

* En la cadena de consulta o en una parte de la ruta de acceso.
* Mediante el uso de una extensión de archivo específica del formato como .xml o .json.

La asignación de la ruta de acceso de la solicitud debe especificarse en la ruta que use la API. Por ejemplo:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Esta ruta anterior permite especificar el formato solicitado como una extensión de archivo opcional. El [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) atributo comprueba la existencia del valor de formato en `RouteData` y asigna el formato de respuesta al formateador adecuado cuando se crea la respuesta.

|           Enrutar        |             Formateador              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Formateador de salida predeterminado    |
| `/api/products/5.json` | Formateador JSON (si está configurado) |
| `/api/products/5.xml`  | Formateador XML (si está configurado)  |
