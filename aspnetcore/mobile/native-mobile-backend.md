---
title: Crear servicios back-end para aplicaciones móviles nativas con ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo crear servicios back-end con ASP.NET Core MVC que admitan aplicaciones móviles nativas.
ms.author: riande
ms.date: 12/05/2019
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: 75943a15878634568830db74f895653571c54d0f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019435"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Crear servicios back-end para aplicaciones móviles nativas con ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

Las aplicaciones móviles pueden comunicarse con servicios back-end de ASP.NET Core. Para obtener instrucciones sobre cómo conectar servicios web locales desde simuladores de iOS y emuladores de Android, vea [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services) (Conexión a servicios web locales desde simuladores de iOS y emuladores de Android).

[Ver o descargar código de ejemplo de servicios back-end](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>La aplicación móvil nativa de ejemplo

Este tutorial muestra cómo crear servicios back-end mediante ASP.NET Core MVC para admitir aplicaciones móviles nativas. Usa la [aplicación Xamarin Forms ToDoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) como su cliente nativo, lo que incluye clientes nativos independientes para dispositivos Android, iOS, Windows Universal y Windows Phone. Puede seguir el tutorial vinculado para crear la aplicación nativa (e instalar las herramientas de Xamarin gratuitas necesarias), así como descargar la solución de ejemplo de Xamarin. El ejemplo de Xamarin incluye un proyecto de servicios de ASP.NET Web API 2, que sustituye a las aplicaciones de ASP.NET Core de este artículo (sin cambios requeridos por el cliente).

![Aplicación ToDoRest que se ejecuta en un smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Características

La aplicación ToDoRest permite enumerar, agregar, eliminar y actualizar tareas pendientes. Cada tarea tiene un identificador, un nombre, notas y una propiedad que indica si ya se ha realizado.

La vista principal de las tareas, como se muestra anteriormente, indica el nombre de cada tarea e indica si se ha realizado con una marca de verificación.

Al pulsar el icono `+` se abre un cuadro de diálogo para agregar un elemento:

![Cuadro de diálogo para agregar un elemento](native-mobile-backend/_static/todo-android-new-item.png)

Al pulsar un elemento en la pantalla de la lista principal se abre un cuadro de diálogo de edición, donde se puede modificar el nombre del elemento, las notas y la configuración de Done (Listo), o se puede eliminar el elemento:

![Cuadro de diálogo de edición del elemento](native-mobile-backend/_static/todo-android-edit-item.png)

Este ejemplo está configurado para usar de forma predeterminada servicios back-end hospedados en developer.xamarin.com, lo que permite operaciones de solo lectura. Para probarlo usted mismo con la aplicación de ASP.NET Core que creó en la siguiente sección que se ejecuta en el equipo, debe actualizar la constante `RestUrl` de la aplicación. Vaya hasta el proyecto `ToDoREST` y abra el archivo *Constants.cs*. Reemplace `RestUrl` con una dirección URL que incluya la dirección IP de su equipo (no localhost ni 127.0.0.1, puesto que esta dirección se usa desde el emulador de dispositivo, no desde el equipo). Incluya también el número de puerto (5000). Para comprobar que los servicios funcionan con un dispositivo, asegúrese de que no tiene un firewall activo que bloquea el acceso a este puerto.

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>Creación del proyecto de ASP.NET Core

Cree una aplicación web de ASP.NET Core en Visual Studio. Elija la plantilla de API web y Sin autenticación. Denomine el proyecto *ToDoApi*.

![Cuadro de diálogo Nueva aplicación web ASP.NET con la plantilla de proyecto API web seleccionada](native-mobile-backend/_static/web-api-template.png)

La aplicación debería responder a todas las solicitudes realizadas al puerto 5000. Actualice *Program.cs* para que incluya `.UseUrls("http://*:5000")` para conseguir lo siguiente:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> Asegúrese de que ejecuta la aplicación directamente, en lugar de tras IIS Express, que omite las solicitudes no locales de forma predeterminada. Ejecute [dotnet run](/dotnet/core/tools/dotnet-run) desde un símbolo del sistema o elija el perfil del nombre de aplicación en la lista desplegable de destino de depuración en la barra de herramientas de Visual Studio.

Agregue una clase de modelo para representar las tareas pendientes. Marque los campos obligatorios mediante el atributo `[Required]`:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

Los métodos de API necesitan alguna manera de trabajar con los datos. Use la misma interfaz de `IToDoRepository` que usa el ejemplo original de Xamarin:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

En este ejemplo, la implementación usa solo una colección de elementos privada:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

Configure la implementación en *Startup.cs*:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

En este punto, está listo para crear el *ToDoItemsController*.

> [!TIP]
> Obtenga más información sobre cómo crear API web en [Cree su primera API web con ASP.NET Core MVC y Visual Studio](../tutorials/first-web-api.md).

## <a name="creating-the-controller"></a>Crear el controlador

Agregue un nuevo controlador para el proyecto: *ToDoItemsController*. Debe heredar de Microsoft.AspNetCore.Mvc.Controller. Agregue un atributo `Route` para indicar que el controlador controlará las solicitudes realizadas a las rutas de acceso que comiencen con `api/todoitems`. El token `[controller]` de la ruta se sustituye por el nombre del controlador (si se omite el sufijo `Controller`) y es especialmente útil para las rutas globales. Obtenga más información sobre el [enrutamiento](../fundamentals/routing.md).

El controlador necesita un `IToDoRepository` para funcionar. Solicite una instancia de este tipo a través del constructor del controlador. En tiempo de ejecución, esta instancia se proporcionará con la compatibilidad del marco con la [inserción de dependencias](../fundamentals/dependency-injection.md).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

Esta API es compatible con cuatro verbos HTTP diferentes para realizar operaciones CRUD (creación, lectura, actualización, eliminación) en el origen de datos. La más simple de ellas es la operación de lectura, que corresponde a una solicitud HTTP GET.

### <a name="reading-items"></a>Leer elementos

La solicitud de una lista de elementos se realiza con una solicitud GET al método `List`. El atributo `[HttpGet]` en el método `List` indica que esta acción solo debe controlar las solicitudes GET. La ruta de esta acción es la ruta especificada en el controlador. No es necesario usar el nombre de acción como parte de la ruta. Solo debe asegurarse de que cada acción tiene una ruta única e inequívoca. El enrutamiento de atributos se puede aplicar tanto a los niveles de controlador como de método para crear rutas específicas.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

El método `List` devuelve un código de respuesta 200 OK y todos los elementos de lista de tareas, serializados como JSON.

Puede probar el nuevo método de API con una variedad de herramientas, como [Postman](https://www.getpostman.com/docs/), que se muestra a continuación:

![Consola de Postman que muestra una solicitud GET para las tareas pendientes y el cuerpo de la respuesta que muestra el JSON de los tres elementos devueltos](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Crear elementos

Por convención, la creación de elementos de datos se asigna al verbo HTTP POST. El método `Create` tiene un atributo `[HttpPost]` aplicado y acepta una instancia `ToDoItem`. Puesto que el argumento `item` se pasa en el cuerpo de la solicitud POST, este parámetro especifica el atributo `[FromBody]`.

Dentro del método, se comprueba la validez del elemento y si existió anteriormente en el almacén de datos y, si no hay problemas, se agrega mediante el repositorio. Al comprobar `ModelState.IsValid` se realiza una [validación de modelos](../mvc/models/validation.md), y debe realizarse en cada método de API que acepte datos proporcionados por usuario.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

El ejemplo usa una enumeración que contiene códigos de error que se pasan al cliente móvil:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

Pruebe a agregar nuevos elementos con Postman eligiendo el verbo POST que proporciona el nuevo objeto en formato JSON en el cuerpo de la solicitud. También debe agregar un encabezado de solicitud que especifica un `Content-Type` de `application/json`.

![Consola Postman que muestra una solicitud POST y una respuesta](native-mobile-backend/_static/postman-post.png)

El método devuelve el elemento recién creado en la respuesta.

### <a name="updating-items"></a>Actualizar elementos

La modificación de registros se realiza mediante solicitudes HTTP PUT. Aparte de este cambio, el método `Edit` es casi idéntico a `Create`. Tenga en cuenta que, si no se encuentra el registro, la acción `Edit` devolverá una respuesta `NotFound` (404).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

Para probar con Postman, cambie el verbo a PUT. Especifique los datos actualizados del objeto en el cuerpo de la solicitud.

![Consola Postman que muestra una solicitud PUT y una respuesta](native-mobile-backend/_static/postman-put.png)

Este método devuelve una respuesta `NoContent` (204) cuando se realiza correctamente, para mantener la coherencia con la API existente.

### <a name="deleting-items"></a>Eliminar elementos

La eliminación de registros se consigue mediante solicitudes DELETE al servicio y pasando el identificador del elemento que va a eliminar. Al igual que con las actualizaciones, las solicitudes para elementos que no existen recibirán respuestas `NotFound`. De lo contrario, las solicitudes correctas recibirán una respuesta `NoContent` (204).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

Tenga en cuenta que, al probar la funcionalidad de eliminar, no se necesita nada en el cuerpo de la solicitud.

![Consola Postman que muestra una solicitud DELETE y una respuesta](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>Convenciones comunes de Web API

Al desarrollar los servicios back-end de la aplicación, necesitará acceder a un conjunto coherente de convenciones o directivas para controlar cuestiones transversales. Por ejemplo, en el servicio mostrado anteriormente, las solicitudes de registros específicos que no se encontraron recibieron una respuesta `NotFound`, en lugar de una respuesta `BadRequest`. De forma similar, los comandos realizados a este servicio que pasaron en tipos enlazados a un modelo siempre se comprobaron como `ModelState.IsValid` y devolvieron una `BadRequest` para los tipos de modelos no válidos.

Después de identificar una directiva común para las API, normalmente puede encapsularla en un [filtro](../mvc/controllers/filters.md). Obtenga más información sobre [cómo encapsular directivas de API comunes en aplicaciones de ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Recursos adicionales

* [Autenticación y autorización](/xamarin/xamarin-forms/enterprise-application-patterns/authentication-and-authorization)
