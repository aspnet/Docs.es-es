---
title: 'Tutorial: Llamada a una API web de ASP.NET Core con JavaScript'
author: rick-anderson
description: Obtenga información sobre cómo llamar a una API web de ASP.NET Core con JavaScript.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: 4e5c6bdea97f54e3cef60d03ea0b63ac3e665bbf
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021294"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Tutorial: Llamada a una API web de ASP.NET Core con JavaScript

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

En este tutorial se muestra cómo llamar a una API web de ASP.NET Core con JavaScript mediante la [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API).

::: moniker range="< aspnetcore-3.0"

Para ASP.NET Core 2.2, consulte la versión 2.2 de [Llamada a la API web con JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Requisitos previos

* Finalización del [Tutorial: Creación de una API web](xref:tutorials/first-web-api)
* Familiaridad con CSS, HTML y JavaScript

## <a name="call-the-web-api-with-javascript"></a>Llamar a la API web con JavaScript

En esta sección, agregará una página HTML que contiene formularios para crear y administrar las tareas pendientes. Los controladores de eventos se asocian a los elementos de la página. Los controladores de eventos producen solicitudes HTTP a los métodos de acción de la API web. La función `fetch` de la API Fetch inicia cada solicitud HTTP.

La función `fetch` devuelve un objeto [Promise`Response`, que contiene una respuesta HTTP representada como objeto ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise). Un patrón común es extraer el cuerpo de la respuesta JSON invocando la función `json` en el `Response` objeto. JavaScript actualiza la página con los detalles de la respuesta de la API web.

La llamada `fetch` más simple acepta un único parámetro que representa la ruta. Un segundo parámetro, conocido como el objeto `init`, es opcional. `init` se utiliza para configurar la solicitud HTTP.

1. Configure la aplicación para [atender archivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [habilitar la asignación de archivos predeterminada](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_). El siguiente código resaltado es necesario en el método `Configure` de *Startup.cs*:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Cree una carpeta *wwwroot* en la raíz del proyecto.

1. Cree una carpeta *js* dentro de la carpeta *wwwroot*.

1. Agregue un archivo HTML denominado *index.html* a la carpeta *wwwroot*. Reemplace el contenido de *index.html* por el marcado siguiente:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Agregue un archivo de JavaScript denominado *site.js*, a la carpeta *wwwroot/js*. Reemplace el contenido de *site.js* por el código siguiente:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Puede que sea necesario realizar un cambio en la configuración de inicio del proyecto de ASP.NET Core para probar la página HTML localmente:

1. Abra *Properties\launchSettings.json*.
1. Quite la propiedad `launchUrl` para forzar a la aplicación a abrirse en *index.html*, esto es, el archivo predeterminado del proyecto.

En este ejemplo se llama a todos los métodos CRUD de la API web. A continuación, encontrará algunas explicaciones de las solicitudes de la API web.

### <a name="get-a-list-of-to-do-items"></a>Obtención de una lista de tareas pendientes

En el código siguiente, se envía una solicitud HTTP GET a la ruta *api/TodoItems*:

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Cuando la API web devuelve un código de estado correcto, se invoca la función `_displayItems`. Cada tarea pendiente en el parámetro de matriz aceptado por `_displayItems` se agrega a una tabla con los botones **Editar** y **Eliminar**. Si se produce un error en la solicitud de la API web, dicho error se registra en la consola del explorador.

### <a name="add-a-to-do-item"></a>Incorporación de una tarea pendiente

En el código siguiente:

* Se declara una variable `item` para construir una representación literal de objeto de la tarea pendiente.
* Una solicitud Fetch se configura con las siguientes opciones:
  * `method`&mdash;especifica el verbo de acción HTTP POST.
  * `body`&mdash;especifica la representación JSON del cuerpo de la solicitud. El JSON se genera pasando el literal de objeto almacenado en `item` a la función [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).
  * `headers`&mdash;especifica los encabezados de solicitud HTTP `Accept` y `Content-Type`. Ambos encabezados se establecen en `application/json` para especificar el tipo de medio que se va a recibir y a enviar, respectivamente.
* Se envía una solicitud HTTP POST a la ruta *api/TodoItems*.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Cuando la API web devuelve un código de estado correcto, se invoca la función `getItems` para actualizar la tabla HTML. Si se produce un error en la solicitud de la API web, dicho error se registra en la consola del explorador.

### <a name="update-a-to-do-item"></a>Actualizar una tarea pendiente

Actualizar una tarea pendientes es similar a agregar una; sin embargo, hay dos diferencias importantes:

* La ruta tiene como sufijo el identificador único del elemento que se va a actualizar. Por ejemplo, *api/TodoItems/1*.
* El verbo de acción HTTP es PUT, como se indica mediante la opción `method`.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Eliminar una tarea pendiente

Para eliminar una tarea pendiente, establezca la opción `method` de la solicitud en `DELETE` y especifique el identificador único de la tarea en la dirección URL.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Pase al siguiente tutorial para obtener información sobre cómo generar páginas de ayuda de API web:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
