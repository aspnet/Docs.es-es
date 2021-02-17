---
title: Crear servicios back-end para aplicaciones móviles nativas con ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo crear servicios back-end con ASP.NET Core MVC que admitan aplicaciones móviles nativas.
ms.author: riande
ms.date: 2/12/2021
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564032"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Crear servicios back-end para aplicaciones móviles nativas con ASP.NET Core

Por [James Montemagno](https://twitter.com/JamesMontemagno)

Las aplicaciones móviles pueden comunicarse con servicios back-end de ASP.NET Core. Para obtener instrucciones sobre cómo conectar servicios web locales desde simuladores de iOS y emuladores de Android, vea [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services) (Conexión a servicios web locales desde simuladores de iOS y emuladores de Android).

[Ver o descargar código de ejemplo de servicios back-end](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a>La aplicación móvil nativa de ejemplo

En este tutorial se muestra cómo crear servicios back-end mediante ASP.NET Core para admitir aplicaciones móviles nativas. Usa la [aplicación Xamarin. Forms TodoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) como su cliente nativo, que incluye clientes nativos independientes para Android, iOS y Windows. Puede seguir el tutorial vinculado para crear la aplicación nativa (e instalar las herramientas de Xamarin gratuitas necesarias), así como descargar la solución de ejemplo de Xamarin. El ejemplo de Xamarin incluye un ASP.NET Core proyecto de servicios de API Web, que reemplaza la aplicación ASP.NET Core de este artículo (sin necesidad de realizar cambios en el cliente).

![Aplicación ToDoRest que se ejecuta en un smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Características

La [aplicación TodoREST](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) permite enumerar, agregar, eliminar y actualizar elementos To-Do. Cada tarea tiene un identificador, un nombre, notas y una propiedad que indica si ya se ha realizado.

La vista principal de las tareas, como se muestra anteriormente, indica el nombre de cada tarea e indica si se ha realizado con una marca de verificación.

Al pulsar el icono `+` se abre un cuadro de diálogo para agregar un elemento:

![Cuadro de diálogo para agregar un elemento](native-mobile-backend/_static/todo-android-new-item.png)

Al pulsar un elemento en la pantalla de la lista principal se abre un cuadro de diálogo de edición, donde se puede modificar el nombre del elemento, las notas y la configuración de Done (Listo), o se puede eliminar el elemento:

![Cuadro de diálogo de edición del elemento](native-mobile-backend/_static/todo-android-edit-item.png)

Para probarlo usted mismo con la aplicación ASP.NET Core creada en la siguiente sección, que se ejecuta en el equipo, actualice la constante de la aplicación [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) .

Los emuladores de Android no se ejecutan en el equipo local y usan una dirección IP de bucle invertido (10.0.2.2) para comunicarse con el equipo local. Aproveche [Xamarin. Essentials DeviceInfo](/xamarin/essentials/device-information/) para detectar el funcionamiento del sistema para usar la dirección URL correcta.

Navegue hasta el [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) proyecto y abra el [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) archivo. El archivo *constants.CS* contiene la configuración siguiente.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

Opcionalmente, puede implementar el servicio Web en un servicio en la nube como Azure y actualizar el `RestUrl` .

## <a name="creating-the-aspnet-core-project"></a>Creación del proyecto de ASP.NET Core

Cree una aplicación web de ASP.NET Core en Visual Studio. Elija la plantilla API Web. Asigne al proyecto el nombre *TodoAPI*.

![Cuadro de diálogo Nueva aplicación web ASP.NET con la plantilla de proyecto API web seleccionada](native-mobile-backend/_static/web-api-template.png)

La aplicación debe responder a todas las solicitudes realizadas en el puerto 5000, incluido el tráfico HTTP de texto no cifrado para nuestro cliente móvil. Actualice *Startup.CS* de modo que <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> no se ejecute en el desarrollo:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> Ejecute la aplicación directamente, en lugar de detrás IIS Express. IIS Express omite las solicitudes no locales de forma predeterminada. Ejecute [dotnet Run](/dotnet/core/tools/dotnet-run) desde un símbolo del sistema o elija el perfil de nombre de la aplicación en la lista desplegable destino de depuración de la barra de herramientas de Visual Studio.

Agregue una clase de modelo para representar las tareas pendientes. Marque los campos obligatorios mediante el atributo `[Required]`:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

Los métodos de API necesitan alguna manera de trabajar con los datos. Use la misma interfaz de `ITodoRepository` que usa el ejemplo original de Xamarin:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

En este ejemplo, la implementación usa solo una colección de elementos privada:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

Configure la implementación en *Startup.cs*:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a>Crear el controlador

Agregue un nuevo controlador al proyecto, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs). Debe heredar de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Agregue un atributo `Route` para indicar que el controlador controlará las solicitudes realizadas a las rutas de acceso que comiencen con `api/todoitems`. El token `[controller]` de la ruta se sustituye por el nombre del controlador (si se omite el sufijo `Controller`) y es especialmente útil para las rutas globales. Obtenga más información sobre el [enrutamiento](../fundamentals/routing.md).

El controlador necesita un `ITodoRepository` para funcionar. Solicite una instancia de este tipo a través del constructor del controlador. En tiempo de ejecución, esta instancia se proporcionará con la compatibilidad del marco con la [inserción de dependencias](../fundamentals/dependency-injection.md).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

Esta API es compatible con cuatro verbos HTTP diferentes para realizar operaciones CRUD (creación, lectura, actualización, eliminación) en el origen de datos. La más simple de ellas es la operación de lectura, que corresponde a una solicitud HTTP GET.

### <a name="reading-items"></a>Leer elementos

La solicitud de una lista de elementos se realiza con una solicitud GET al método `List`. El atributo `[HttpGet]` en el método `List` indica que esta acción solo debe controlar las solicitudes GET. La ruta de esta acción es la ruta especificada en el controlador. No es necesario usar el nombre de acción como parte de la ruta. Solo debe asegurarse de que cada acción tiene una ruta única e inequívoca. El enrutamiento de atributos se puede aplicar tanto a los niveles de controlador como de método para crear rutas específicas.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

El `List` método devuelve un código de respuesta 200 aceptar y todos los elementos de la lista de tareas, serializados como JSON.

Puede probar el nuevo método de API con una variedad de herramientas, como [Postman](https://www.getpostman.com/docs/), que se muestra a continuación:

![Consola de Postman que muestra una solicitud GET para las tareas pendientes y el cuerpo de la respuesta que muestra el JSON de los tres elementos devueltos](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Crear elementos

Por convención, la creación de elementos de datos se asigna al verbo HTTP POST. El método `Create` tiene un atributo `[HttpPost]` aplicado y acepta una instancia `TodoItem`. Puesto que el argumento `item` se pasa en el cuerpo de la solicitud POST, este parámetro especifica el atributo `[FromBody]`.

Dentro del método, se comprueba la validez del elemento y si existió anteriormente en el almacén de datos y, si no hay problemas, se agrega mediante el repositorio. Al comprobar `ModelState.IsValid` se realiza una [validación de modelos](../mvc/models/validation.md), y debe realizarse en cada método de API que acepte datos proporcionados por usuario.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

En el ejemplo se usa un que `enum` contiene códigos de error que se pasan al cliente móvil:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

Pruebe a agregar nuevos elementos con Postman eligiendo el verbo POST que proporciona el nuevo objeto en formato JSON en el cuerpo de la solicitud. También debe agregar un encabezado de solicitud que especifica un `Content-Type` de `application/json`.

![Consola Postman que muestra una solicitud POST y una respuesta](native-mobile-backend/_static/postman-post.png)

El método devuelve el elemento recién creado en la respuesta.

### <a name="updating-items"></a>Actualizar elementos

La modificación de registros se realiza mediante solicitudes HTTP PUT. Aparte de este cambio, el método `Edit` es casi idéntico a `Create`. Tenga en cuenta que, si no se encuentra el registro, la acción `Edit` devolverá una respuesta `NotFound` (404).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

Para probar con Postman, cambie el verbo a PUT. Especifique los datos actualizados del objeto en el cuerpo de la solicitud.

![Consola Postman que muestra una solicitud PUT y una respuesta](native-mobile-backend/_static/postman-put.png)

Este método devuelve una respuesta `NoContent` (204) cuando se realiza correctamente, para mantener la coherencia con la API existente.

### <a name="deleting-items"></a>Eliminar elementos

La eliminación de registros se consigue mediante solicitudes DELETE al servicio y pasando el identificador del elemento que va a eliminar. Al igual que con las actualizaciones, las solicitudes para elementos que no existen recibirán respuestas `NotFound`. De lo contrario, las solicitudes correctas recibirán una respuesta `NoContent` (204).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

Tenga en cuenta que, al probar la funcionalidad de eliminar, no se necesita nada en el cuerpo de la solicitud.

![Consola Postman que muestra una solicitud DELETE y una respuesta](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a>Prevención del exceso de publicación

Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`. Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo. Hay varias razones para ello y la seguridad es una de las principales. El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista. En este artículo, se usa **DTO**.

Se puede usar un DTO para:

* Evitar el exceso de publicación.
* Ocultar las propiedades que los clientes no deben ver.
* Omitir algunas propiedades para reducir el tamaño de la carga.
* Acoplar los gráficos de objetos que contienen objetos anidados. Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.

Para demostrar el enfoque DTO, consulte [evitar la sobreexposición](xref:tutorials/first-web-api#prevent-over-posting) .

## <a name="common-web-api-conventions"></a>Convenciones comunes de Web API

Al desarrollar los servicios back-end de la aplicación, necesitará acceder a un conjunto coherente de convenciones o directivas para controlar cuestiones transversales. Por ejemplo, en el servicio mostrado anteriormente, las solicitudes de registros específicos que no se encontraron recibieron una respuesta `NotFound`, en lugar de una respuesta `BadRequest`. De forma similar, los comandos realizados a este servicio que pasaron en tipos enlazados a un modelo siempre se comprobaron como `ModelState.IsValid` y devolvieron una `BadRequest` para los tipos de modelos no válidos.

Después de identificar una directiva común para las API, normalmente puede encapsularla en un [filtro](../mvc/controllers/filters.md). Obtenga más información sobre [cómo encapsular directivas de API comunes en aplicaciones de ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Recursos adicionales

- [Xamarin. Forms: autenticación de servicio Web](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin. Forms: consumo de un servicio web RESTful](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn: consumir servicios web REST en aplicaciones Xamarin](/learn/modules/consume-rest-services/)
- [Microsoft Learn: Creación de una API web con ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
