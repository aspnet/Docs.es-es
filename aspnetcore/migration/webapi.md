---
title: Migrar de ASP.NET Web API a ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo migrar una implementación de Web API desde la API Web de ASP.NET 4. x ASP.NET Core a MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 320805c0d40bf06cee384e6d98caea5c420d45bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061475"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Migrar de ASP.NET Web API a ASP.NET Core

Por [Scott Addie](https://twitter.com/scott_addie) y [Steve Smith](https://ardalis.com/)

Una API Web ASP.NET 4. x es un servicio HTTP que llega a una amplia gama de clientes, incluidos exploradores y dispositivos móviles. ASP.NET Core combina modelos de aplicación de MVC y Web API de ASP.NET 4. x en un modelo de programación único conocido como ASP.NET Core MVC. En este artículo se muestran los pasos necesarios para migrar desde la API Web de ASP.NET 4. x ASP.NET Core a MVC.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Revisar el proyecto de Web API de ASP.NET 4. x

En este artículo se usa el proyecto *ProductsApp* creado en [Introducción con ASP.net web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). En ese proyecto, se configura un proyecto de API Web ASP.NET 4. x básico como se indica a continuación.

En *global.asax.CS* , se realiza una llamada a `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

La `WebApiConfig` clase se encuentra en la carpeta *App_Start* y tiene un `Register` método estático:

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

La clase anterior:

* Configura el [enrutamiento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), aunque no se usa realmente.
* Configura la tabla de enrutamiento.
El código de ejemplo espera que las direcciones URL coincidan con el formato `/api/{controller}/{id}` , `{id}` siendo opcionales.

En las secciones siguientes se muestra cómo migrar el proyecto de Web API a ASP.NET Core MVC.

## <a name="create-the-destination-project"></a>Crear el proyecto de destino

Cree una nueva solución en blanco en Visual Studio y agregue el proyecto de API Web ASP.NET 4. x para migrar:

1. En el menú **Archivo** , seleccione **Nuevo** > **Proyecto** .
1. Seleccione la plantilla **solución en blanco** y seleccione **siguiente** .
1. Asigne a la solución el nombre *WebAPIMigration* . Seleccione **Crear** .
1. Agregue el proyecto *ProductsApp* existente a la solución.

Agregue un nuevo proyecto de API para migrar a:

1. Agregue un nuevo proyecto de **aplicación Web de ASP.net Core** a la solución.
1. En el cuadro de diálogo **configurar el nuevo proyecto** , asigne al proyecto el nombre *ProductsCore* y seleccione **crear** .
1. En el cuadro de diálogo **Crear una aplicación web ASP.NET Core** , confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas. Seleccione la plantilla de proyecto **API** y, luego, **Crear** .
1. Quite los archivos de ejemplo *WeatherForecast.CS* y *Controllers/WeatherForecastController. CS* del nuevo proyecto *ProductsCore* .

La solución ahora contiene dos proyectos. En las secciones siguientes se explica cómo migrar el contenido del proyecto *ProductsApp* al proyecto *ProductsCore* .

## <a name="migrate-configuration"></a>Migración de la configuración

ASP.NET Core no utiliza la carpeta *App_Start* o el archivo *global. asax* . Además, el archivo de *web.config* se agrega en el momento de la publicación.

La clase `Startup`:

* Reemplaza a *global. asax* .
* Controla todas las tareas de inicio de la aplicación.

Para obtener más información, vea <xref:fundamentals/startup>.

## <a name="migrate-models-and-controllers"></a>Migración de modelos y controladores

En el código siguiente se muestra el `ProductsController` que se va a actualizar para ASP.net Core:

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

Actualice el `ProductsController` para ASP.net Core:

1. Copie *Controllers/ProductsController. CS* y la carpeta *Models* del proyecto original al nuevo.
1. Cambie el espacio de nombres de la raíz de los archivos copiados a `ProductsCore` .
1. Actualice la `using ProductsApp.Models;` instrucción a `using ProductsCore.Models;` .

Los componentes siguientes no existen en ASP.NET Core:

* Clase `ApiController`
* Espacio de nombres `System.Web.Http`
* `IHttpActionResult` (interfaz)

Se han realizado los siguientes cambios:

1. Cambio de `ApiController` a <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Agregue `using Microsoft.AspNetCore.Mvc;` para resolver la `ControllerBase` referencia.
1. Elimine `using System.Web.Http;`.
1. Cambie el `GetProduct` tipo de valor devuelto de la acción de `IHttpActionResult` a `ActionResult<Product>` .
1. Simplifique la `GetProduct` instrucción de la acción `return` para lo siguiente:

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Configuración del enrutamiento

La plantilla de proyecto de *API* de ASP.net Core incluye la configuración de enrutamiento de punto de conexión en el código generado.

Lo siguiente <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> y <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> llama a:

* Registra la coincidencia de rutas y la ejecución de puntos de conexión en la canalización de [middleware](xref:fundamentals/middleware/index) .
* Reemplace el archivo *App_Start/webapiconfig.CS* del proyecto *ProductsApp* .

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

Configure el enrutamiento de la siguiente manera:

1. Marque la `ProductsController` clase con los siguientes atributos:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    El [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atributo anterior configura el patrón de enrutamiento de atributo del controlador. El [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo hace que el enrutamiento de atributos sea un requisito para todas las acciones de este controlador.

    El enrutamiento de atributos admite tokens, como `[controller]` y `[action]` . En tiempo de ejecución, cada token se reemplaza por el nombre del controlador o la acción, respectivamente, a la que se ha aplicado el atributo. Los tokens:
    * Reduzca el número de cadenas mágicas en el proyecto.
    * Asegúrese de que las rutas permanecen sincronizadas con las acciones y controladores correspondientes cuando se aplican las refactorizaciones de cambio de nombre automáticas.
1. Habilite las solicitudes HTTP GET para las `ProductController` acciones:
    * Aplique el [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo a la `GetAllProducts` acción.
    * Aplique el `[HttpGet("{id}")]` atributo a la `GetProduct` acción.

Ejecute el proyecto migrado y vaya a `/api/products` . Aparece una lista completa de tres productos. Vaya a `/api/products/1`. Aparece el primer producto.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Revisar el proyecto de Web API de ASP.NET 4. x

En este artículo se usa el proyecto *ProductsApp* creado en [Introducción con ASP.net web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). En ese proyecto, se configura un proyecto de API Web ASP.NET 4. x básico como se indica a continuación.

En *global.asax.CS* , se realiza una llamada a `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

La `WebApiConfig` clase se encuentra en la carpeta *App_Start* y tiene un `Register` método estático:

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

Esta clase configura el [enrutamiento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), aunque en realidad no se usa en el proyecto. También configura la tabla de enrutamiento, que se usa en ASP.NET Web API. En este caso, la API Web de ASP.NET 4. x espera que las direcciones URL coincidan con el formato `/api/{controller}/{id}` , `{id}` siendo opcionales.

En las secciones siguientes se muestra cómo migrar el proyecto de Web API a ASP.NET Core MVC.

## <a name="create-the-destination-project"></a>Crear el proyecto de destino

Siga los pasos que se describen a continuación en Visual Studio:

* Vaya a **archivo**  >  **nuevo**  >  **proyecto**  >  **otros tipos de proyecto**  >  **soluciones de Visual Studio** . Seleccione **solución en blanco** y asigne el nombre *WebAPIMigration* a la solución. Haga clic en el botón **Aceptar** .
* Agregue el proyecto *ProductsApp* existente a la solución.
* Agregue un nuevo proyecto de **aplicación Web de ASP.net Core** a la solución. Seleccione la plataforma de destino de **.net Core** en la lista desplegable y seleccione la plantilla de proyecto de **API** . Asigne al proyecto el nombre *ProductsCore* y haga clic en el botón **Aceptar** .

La solución ahora contiene dos proyectos. En las secciones siguientes se explica cómo migrar el contenido del proyecto *ProductsApp* al proyecto *ProductsCore* .

## <a name="migrate-configuration"></a>Migración de la configuración

ASP.NET Core no usa:

* *App_Start* carpeta o el archivo *global. asax*
* *web.config* archivo se agrega en el momento de la publicación.

La clase `Startup`:

* Reemplaza a *global. asax* .
* Controla todas las tareas de inicio de la aplicación.

Para obtener más información, vea <xref:fundamentals/startup>.

En ASP.NET Core MVC, el enrutamiento de atributos se incluye de forma predeterminada cuando <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> se llama a en `Startup.Configure` . La siguiente `UseMvc` llamada reemplaza el archivo *App_Start/webapiconfig.CS* del proyecto *ProductsApp* :

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a>Migración de modelos y controladores

En el código siguiente se muestra la `ProductsController` actualización para ASP.net Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]

Actualice el `ProductsController` para ASP.net Core:

1. Copie *Controllers/ProductsController. CS* del proyecto original al nuevo.
1. Copie la carpeta *Models* del proyecto original al nuevo.
1. Cambie el espacio de nombres de la raíz de los archivos copiados a `ProductsCore` .
1. Actualice la `using ProductsApp.Models;` instrucción a `using ProductsCore.Models;` .

Los componentes siguientes no existen en ASP.NET Core:

* Clase `ApiController`
* Espacio de nombres `System.Web.Http`
* `IHttpActionResult` (interfaz)

Se han realizado los siguientes cambios:

1. Cambio de `ApiController` a <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Agregue `using Microsoft.AspNetCore.Mvc;` para resolver la `ControllerBase` referencia.
1. Elimine `using System.Web.Http;`.
1. Cambie el `GetProduct` tipo de valor devuelto de la acción de `IHttpActionResult` a `ActionResult<Product>` .
1. Simplifique la `GetProduct` instrucción de la acción `return` para lo siguiente:

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Configuración del enrutamiento

Configure el enrutamiento de la siguiente manera:

1. Marque la `ProductsController` clase con los siguientes atributos:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    El [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atributo anterior configura el patrón de enrutamiento de atributo del controlador. El [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo hace que el enrutamiento de atributos sea un requisito para todas las acciones de este controlador.

    El enrutamiento de atributos admite tokens, como `[controller]` y `[action]` . En tiempo de ejecución, cada token se reemplaza por el nombre del controlador o la acción, respectivamente, a la que se ha aplicado el atributo. Los tokens reducen el número de cadenas mágicas del proyecto. Los tokens también garantizan que las rutas permanezcan sincronizadas con los controladores y acciones correspondientes cuando se apliquen las refactorizaciones de cambio de nombre automáticas.
1. Establezca el modo de compatibilidad del proyecto en ASP.NET Core 2,2:

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    El cambio anterior:

    * Se requiere para usar el `[ApiController]` atributo en el nivel de controlador.
    * Opta por los posibles saltos de los comportamientos introducidos en ASP.NET Core 2,2.
1. Habilite las solicitudes HTTP GET para las `ProductController` acciones:
    * Aplique el [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo a la `GetAllProducts` acción.
    * Aplique el `[HttpGet("{id}")]` atributo a la `GetProduct` acción.

Ejecute el proyecto migrado y vaya a `/api/products` . Aparece una lista completa de tres productos. Vaya a `/api/products/1`. Aparece el primer producto.

## <a name="compatibility-shim"></a>Corrección de compatibilidad

La biblioteca [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) proporciona una corrección de compatibilidad para migrar los proyectos de API Web de ASP.net 4. x a ASP.net Core. La corrección de compatibilidad amplía ASP.NET Core para admitir una serie de convenciones de la API Web de ASP.NET 4. x. El ejemplo que se ha migrado anteriormente en este documento es lo suficientemente básico como para que la corrección de compatibilidad no fuera necesaria. En el caso de los proyectos de mayor tamaño, el uso de la corrección de compatibilidad puede ser útil para el puente temporal entre ASP.NET Core y ASP.NET 4. x Web API 2.

La corrección de compatibilidad de la API Web está pensada para usarse como medida temporal para admitir la migración de proyectos de API Web de ASP.NET 4. x grandes a ASP.NET Core. Con el tiempo, los proyectos deben actualizarse para usar patrones de ASP.NET Core en lugar de basarse en la corrección de compatibilidad.

Las características de compatibilidad incluidas en `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluyen:

* Agrega un `ApiController` tipo para que no sea necesario actualizar los tipos base de los controladores.
* Habilita el enlace de modelos de estilo de API Web. ASP.NET Core enlace de modelos de MVC funciona de forma predeterminada con el de ASP.NET 4. x MVC 5. La corrección de compatibilidad cambia el enlace de modelos para que sea más similar a las convenciones de enlace de modelos de la API Web 2 de ASP.NET 4. x. Por ejemplo, los tipos complejos se enlazan automáticamente desde el cuerpo de la solicitud.
* Extiende el enlace de modelos para que las acciones de controlador puedan tomar parámetros de tipo `HttpRequestMessage` .
* Agrega formateadores de mensajes que permiten que las acciones devuelvan resultados de tipo `HttpResponseMessage` .
* Agrega métodos de respuesta adicionales que pueden usar las acciones de Web API 2 para servir respuestas:
  * `HttpResponseMessage` productores
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Métodos del resultado de la acción:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Agrega una instancia de `IContentNegotiator` al contenedor de inserción de dependencias (di) de la aplicación y pone a disposición los tipos relacionados con la negociación de contenido de [Microsoft. Aspnet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/). Entre los ejemplos de estos tipos se incluyen `DefaultContentNegotiator` y `MediaTypeFormatter` .

Para usar la corrección de compatibilidad:

1. Instale el paquete NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .
1. Registre los servicios de la corrección de compatibilidad con el contenedor de DI de la aplicación mediante una llamada a `services.AddMvc().AddWebApiConventions()` en `Startup.ConfigureServices` .
1. Defina rutas específicas de la API Web mediante `MapWebApiRoute` en en `IRouteBuilder` la llamada de la aplicación `IApplicationBuilder.UseMvc` .

## <a name="additional-resources"></a>Recursos adicionales

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
