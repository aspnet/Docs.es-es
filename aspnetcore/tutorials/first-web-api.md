---
title: 'Tutorial: Creación de una API web con ASP.NET Core'
author: rick-anderson
description: Aprenda a crear de una API web con ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 63f91086a7e9d71add7f7a5d58d96f46fa76353c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407790"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Tutorial: Creación de una API web con ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) y [Mike Wasson](https://github.com/mikewasson)

En este tutorial se enseñan los conceptos básicos de la compilación de una API web con ASP.NET Core.

::: moniker range=">= aspnetcore-3.0"

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Crear un proyecto de API web.
> * Agregar una clase de modelo y un contexto de base de datos.
> * Aplicar scaffolding a un controlador con métodos CRUD.
> * Configurar el enrutamiento, las rutas de acceso URL y los valores devueltos.
> * Llamar a la API web con Postman.

Al final, tendrá una API web que pueda administrar los elementos "to-do" almacenados en una base de datos.

## <a name="overview"></a>Información general

En este tutorial se crea la siguiente API:

|API | Descripción | Cuerpo de la solicitud | Cuerpo de la respuesta |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | Obtener todas las tareas pendientes | Ninguna | Matriz de tareas pendientes|
|`GET /api/TodoItems/{id}` | Obtener un elemento por identificador | None | Tarea pendiente|
|`POST /api/TodoItems` | Incorporación de un nuevo elemento | Tarea pendiente | Tarea pendiente |
|`PUT /api/TodoItems/{id}` | Actualizar un elemento existente &nbsp; | Tarea pendiente | None |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | Eliminar un elemento &nbsp; &nbsp; | None | None|

En el diagrama siguiente, se muestra el diseño de la aplicación.

![El cliente está representado por un cuadro a la izquierda. Envía una solicitud y recibe una respuesta de la aplicación, representada por un cuadro en la parte derecha. En el cuadro de la aplicación, hay tres cuadros que representan el controlador, el modelo y la capa de acceso a datos. La solicitud entra en el controlador de la aplicación y se producen operaciones de lectura/escritura entre el controlador y la capa de acceso a datos. El modelo se serializa y se devuelve al cliente en la respuesta.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>Creación de un proyecto web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.
* Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.
* Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.
* En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas. Seleccione la plantilla **API** y haga clic en **Crear**.

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.
* Ejecute los comandos siguientes:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.

  Los comandos anteriores:

  * Crean un nuevo proyecto de API web y lo abren en Visual Studio Code.
  * Agregan los paquetes de NuGet que se requieren en la sección siguiente.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Seleccione **Archivo** > **Nueva solución**.

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**. En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.

  ![Selección de plantilla de API de macOS](first-web-api-mac/_static/api_template.png)

* Confirme que **Plataforma de destino** está establecido en **.NET Core 3.1**. Seleccione **Siguiente**.

  ![Selección de .NET Core 3.1 de macOS](first-web-api-mac/_static/api_31_config.png)

* Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Abra un terminal de comandos en la carpeta del proyecto y ejecute los comandos siguientes:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Prueba de la API

La plantilla del proyecto crea una API `WeatherForecast`. Llame al método `Get` desde un explorador para probar la aplicación.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Presione Ctrl+F5 para ejecutar la aplicación. Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/WeatherForecast`, donde `<port>` es un número de puerto elegido aleatoriamente.

Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**. En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Presione Ctrl+F5 para ejecutar la aplicación. En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/WeatherForecast`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación. Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente. Se devuelve un error HTTP 404 (No encontrado). Anexe `/WeatherForecast` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/WeatherForecast`).

---

Se devuelve un JSON similar al siguiente:

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>Incorporación de una clase de modelo

Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra. El modelo para esta aplicación es una clase `TodoItem` única.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto. Seleccione **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Models*.

* Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**. Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.

* Reemplace el código de plantilla por el código siguiente:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Agregue una carpeta denominada *Models*.

* Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Haga clic con el botón derecho en el proyecto. Seleccione **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Models*.

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* Haga clic con el botón derecho en la carpeta *Modelos* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.

* Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.

* Reemplace el código de plantilla por el código siguiente:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

La propiedad `Id` funciona como clave única en una base de datos relacional.

Las clases de modelo pueden ir en cualquier lugar del proyecto, pero convencionalmente e usa la carpeta *Models*.

## <a name="add-a-database-context"></a>Incorporación de un contexto de base de datos

El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos. Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Adición de Microsoft.EntityFrameworkCore.SqlServer

* En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución**.
* Seleccione la pestaña **Examinar** y escriba **Microsoft.EntityFrameworkCore.SqlServer** en el cuadro de búsqueda.
* Seleccione **Microsoft.EntityFrameworkCore.SqlServer** en el panel izquierdo.
* Active la casilla **Proyecto** en el panel derecho y, después, seleccione **Instalar**.
* Siga las instrucciones anteriores para agregar el paquete NuGet `Microsoft.EntityFrameworkCore.InMemory`.

![Administrador de paquetes de NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Adición del contexto de la base de datos TodoContext

* Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**. Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Agregue una clase `TodoContext` a la carpeta *Models*.

---

* Escriba el siguiente código:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registro del contexto de base de datos

En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection). El contenedor proporciona el servicio a los controladores.

Actualice *Startup.cs* con el siguiente código resaltado:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

El código anterior:

* Elimina las declaraciones `using` no utilizadas.
* Agrega el contexto de base de datos para el contenedor de DI.
* Especifica que el contexto de base de datos usará una base de datos en memoria.

## <a name="scaffold-a-controller"></a>Scaffolding de un controlador

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Haga clic con el botón derecho en la carpeta *Controllers*.
* Seleccione **Agregar** > **Nuevo elemento con scaffolding**.
* Seleccione **Controlador de API con acciones mediante Entity Framework** y, después, seleccione **Agregar**.
* En el cuadro de diálogo **Add API Controller with actions, using Entity Framework** (Agregar controlador de API con acciones mediante Entity Framework):

  * Seleccione **TodoItem (TodoApi.Models)** en **Clase de modelo**.
  * Seleccione **TodoContext (TodoApi.Models)** en **Clase de contexto de datos**.
  * Seleccione **Agregar**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Ejecute los comandos siguientes:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Los comandos anteriores:

* Agregan los paquetes NuGet necesarios para realizar scaffolding.
* Instalan el motor de scaffolding (`dotnet-aspnet-codegenerator`).
* Aplican scaffolding a `TodoItemsController`.

---

El código generado:

* Marca la clase con el atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute). Este atributo indica que el controlador responde a las solicitudes de la API web. Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.
* Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador. El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.

Las plantillas de ASP.NET Core para:

* Controladores con vistas incluyen `[action]` en la plantilla de ruta.
* Controladores de API no incluyen `[action]` en la plantilla de ruta.

Si el token `[action]` no está en la plantilla de ruta, el nombre de la [acción](xref:mvc/controllers/routing#action) se excluye de la ruta. Es decir, el nombre del método asociado a la acción no se usa en la ruta coincidente.

## <a name="examine-the-posttodoitem-create-method"></a>Examen del método create de PostTodoItem

Reemplace la instrucción return en `PostTodoItem` para usar el operador [nameof](/dotnet/csharp/language-reference/operators/nameof):

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute). El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.

El método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> realiza las acciones siguientes:

* Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente. HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.
* Agrega un encabezado [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) a la respuesta. El encabezado `Location` especifica el [URI](https://developer.mozilla.org/docs/Glossary/URI) de la tarea pendiente recién creada. Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`. La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.

### <a name="install-postman"></a>Instalación de Postman

En este tutorial se usa Postman para probar la API web.

* Instale [Postman](https://www.getpostman.com/downloads/).
* Inicie la aplicación web.
* Inicie Postman.
* Deshabilite **Comprobación del certificado SSL**.
  * En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.
    > [!WARNING]
    > Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Prueba de PostTodoItem con Postman

* Cree una nueva solicitud.
* Establezca el método HTTP en `POST`.
* Seleccione la pestaña **Cuerpo**.
* Seleccione el botón de radio **Raw** (Sin formato).
* Establezca el tipo en **JSON (application/json)** .
* En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Seleccione **Enviar**.

  ![Postman con solicitud de creación](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Prueba del URI del encabezado de ubicación

* Seleccione la pestaña **Encabezados** en el panel **Respuesta**.
* Copie el valor de encabezado **Ubicación**:

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/3/create.png)

* Establezca el método en GET.
* Pegue el URI (por ejemplo, `https://localhost:5001/api/TodoItems/1`).
* Seleccione **Enviar**.

## <a name="examine-the-get-methods"></a>Examen de los métodos GET

Estos métodos implementan dos puntos de conexión GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Llame a los dos puntos de conexión desde un explorador o Postman para probar la aplicación. Por ejemplo:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

La llamada a `GetTodoItems` genera una respuesta similar a la siguiente:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Prueba de Get con Postman

* Cree una nueva solicitud.
* Establezca el método HTTP en **GET**.
* Establezca la dirección URL de la solicitud en `https://localhost:<port>/api/TodoItems`. Por ejemplo: `https://localhost:5001/api/TodoItems`.
* Establezca **Vista de dos paneles** en Postman.
* Seleccione **Enviar**.

Esta aplicación utiliza una base de datos en memoria. Si la aplicación se detiene y se inicia, la solicitud GET precedente no devolverá ningún dato. Si no se devuelve ningún dato, publique los datos en la aplicación con [POST](#post).

## <a name="routing-and-url-paths"></a>Enrutamiento y rutas URL

El atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un método que responde a una solicitud HTTP GET. La ruta de dirección URL para cada método se construye como sigue:

* Comience por la cadena de plantilla en el atributo `Route` del controlador:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller". En este ejemplo, el nombre de clase de controlador es **TodoItems**Controller; por tanto, el nombre del controlador es "TodoItems". El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.
* Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso. En este ejemplo no se usa una plantilla. Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente. Al invocar a `GetTodoItem`, el valor `"{id}"` de la URL se proporciona al método en su parámetro `id`.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Valores devueltos

El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta. El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada. Las excepciones no controladas se convierten en errores 5xx.

Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP. Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:

* Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).
* En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON. Devolver `item` genera una respuesta HTTP 200.

## <a name="the-puttodoitem-method"></a>Método PutTodoItem

Examine el método `PutTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT. La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios. Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.

### <a name="test-the-puttodoitem-method"></a>Prueba del método PutTodoItem

En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación. Debe haber un elemento en la base de datos antes de que realice una llamada PUT. Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.

Actualice el elemento to-do que tiene el id. = 1 y establezca su nombre en "feed fish":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

En la imagen siguiente, se muestra la actualización de Postman:

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Método DeleteTodoItem

Examine el método `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Prueba del método DeleteTodoItem

Use Postman para eliminar una tarea pendiente:

* Establezca el método en `DELETE`.
* Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/TodoItems/1`).
* Seleccione **Enviar**.

<a name="over-post"></a>

## <a name="prevent-over-posting"></a>Prevención del exceso de publicación

Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`. Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo. Hay varias razones para ello y la seguridad es una de las principales. El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista. En este artículo, se usa **DTO**.

Se puede usar un DTO para:

* Evitar el exceso de publicación.
* Ocultar las propiedades que los clientes no deben ver.
* Omitir algunas propiedades para reducir el tamaño de la carga.
* Acoplar los gráficos de objetos que contienen objetos anidados. Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.

Para mostrar el enfoque del DTO, actualice la clase `TodoItem` a fin de que incluya un campo secreto:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

El campo secreto debe ocultarse en esta aplicación, pero una aplicación administrativa podría decidir exponerlo.

Compruebe que puede publicar y obtener el campo secreto.

Cree un modelo de DTO:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Actualice el valor de `TodoItemsController` para usar `TodoItemDTO`:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Compruebe que no puede publicar ni obtener el campo secreto.

## <a name="call-the-web-api-with-javascript"></a>Llamar a la API web con JavaScript

Consulte [Tutorial: Llamada a una API web de ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Crear un proyecto de API web.
> * Agregar una clase de modelo y un contexto de base de datos.
> * Agregar un controlador.
> * Agregar métodos CRUD.
> * Configurar el enrutamiento y las rutas de dirección URL.
> * Especificar los valores devueltos.
> * Llamar a la API web con Postman.
> * Llamar a la API web con JavaScript.

Al final, tendrá una API web que pueda administrar las tareas "pendientes" almacenadas en una base de datos relacional.

## <a name="overview"></a>Información general

En este tutorial se crea la siguiente API:

|API | Descripción | Cuerpo de la solicitud | Cuerpo de la respuesta |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Obtener todas las tareas pendientes | Ninguna | Matriz de tareas pendientes|
|GET /api/TodoItems/{id} | Obtener un elemento por identificador | None | Tarea pendiente|
|POST /api/TodoItems | Incorporación de un nuevo elemento | Tarea pendiente | Tarea pendiente |
|PUT /api/TodoItems/{id} | Actualizar un elemento existente &nbsp; | Tarea pendiente | None |
|DELETE /api/TodoItems/{id} &nbsp; &nbsp; | Eliminar un elemento &nbsp; &nbsp; | None | None|

En el diagrama siguiente, se muestra el diseño de la aplicación.

![El cliente está representado por un cuadro a la izquierda. Envía una solicitud y recibe una respuesta de la aplicación, representada por un cuadro en la parte derecha. En el cuadro de la aplicación, hay tres cuadros que representan el controlador, el modelo y la capa de acceso a datos. La solicitud entra en el controlador de la aplicación y se producen operaciones de lectura/escritura entre el controlador y la capa de acceso a datos. El modelo se serializa y se devuelve al cliente en la respuesta.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>Creación de un proyecto web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.
* Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.
* Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.
* En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 2.2** estén seleccionadas. Seleccione la plantilla **API** y haga clic en **Crear**. **No** seleccione **Habilitar compatibilidad con Docker**.

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.
* Ejecute los comandos siguientes:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Estos comandos crean un nuevo proyecto de API web y abren una nueva instancia de Visual Studio Code en la carpeta del proyecto nuevo.

* Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Seleccione **Archivo** > **Nueva solución**.

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**. En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.
  
* En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, acepte la **plataforma de destino** predeterminada de * *.NET Core 2.2*.

* Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>Prueba de la API

La plantilla del proyecto crea una API `values`. Llame al método `Get` desde un explorador para probar la aplicación.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Presione Ctrl+F5 para ejecutar la aplicación. Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/api/values`, donde `<port>` es un número de puerto elegido aleatoriamente.

Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**. En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Presione Ctrl+F5 para ejecutar la aplicación. En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/api/values`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación. Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente. Se devuelve un error HTTP 404 (No encontrado). Anexe `/api/values` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/api/values`).

---

Se devuelve el siguiente JSON:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Incorporación de una clase de modelo

Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra. El modelo para esta aplicación es una clase `TodoItem` única.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto. Seleccione **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Models*.

* Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**. Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.

* Reemplace el código de plantilla por el código siguiente:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Agregue una carpeta denominada *Models*.

* Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Haga clic con el botón derecho en el proyecto. Seleccione **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Models*.

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* Haga clic con el botón derecho en la carpeta *Modelos* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.

* Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.

* Reemplace el código de plantilla por el código siguiente:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

La propiedad `Id` funciona como clave única en una base de datos relacional.

Las clases de modelo pueden ir en cualquier lugar del proyecto, pero convencionalmente e usa la carpeta *Models*.

## <a name="add-a-database-context"></a>Incorporación de un contexto de base de datos

El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos. Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**. Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Agregue una clase `TodoContext` a la carpeta *Models*.

---

* Reemplace el código de plantilla por el código siguiente:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registro del contexto de base de datos

En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection). El contenedor proporciona el servicio a los controladores.

Actualice *Startup.cs* con el siguiente código resaltado:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

El código anterior:

* Elimina las declaraciones `using` no utilizadas.
* Agrega el contexto de base de datos para el contenedor de DI.
* Especifica que el contexto de base de datos usará una base de datos en memoria.

## <a name="add-a-controller"></a>Incorporación de un controlador

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Haga clic con el botón derecho en la carpeta *Controllers*.
* Seleccione **Agregar** > **Nuevo elemento**.
* En el cuadro de diálogo **Agregar nuevo elemento**, seleccione la plantilla **Clase de controlador de API**.
* Asigne a la clase el nombre *TodoController* y seleccione **Agregar**.

  ![Cuadro de diálogo Agregar nuevo elemento con la palabra "controller" en el cuadro de búsqueda y la opción Clase de controlador de API web seleccionada](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* En la carpeta *Controllers*, cree una clase denominada `TodoController`.

---

* Reemplace el código de plantilla por el código siguiente:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

El código anterior:

* Define una clase de controlador de API sin métodos.
* Marca la clase con el atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute). Este atributo indica que el controlador responde a las solicitudes de la API web. Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.
* Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador. El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.
* Si la base de datos está vacía, le agrega un elemento denominado `Item1`. Este código está en el constructor, de manera que se ejecuta cada vez que hay una nueva solicitud HTTP. Si elimina todos los elementos, el constructor volverá a crear `Item1` la próxima vez que se llame a un método de API. De este modo, es posible que parezca que la eliminación no ha funcionado, cuando en realidad sí lo ha hecho.

## <a name="add-get-methods"></a>Incorporación de métodos Get

Para proporcionar una API que recupere tareas pendientes, agregue estos métodos a la clase `TodoController`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Estos métodos implementan dos puntos de conexión GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Detenga la aplicación si todavía está en ejecución. Luego, ejecútela de nuevo para incluir los últimos cambios.

Llame a los dos puntos de conexión desde un explorador para probar la aplicación. Por ejemplo:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

La llamada a `GetTodoItems` genera la siguiente respuesta HTTP:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Enrutamiento y rutas URL

El atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un método que responde a una solicitud HTTP GET. La ruta de dirección URL para cada método se construye como sigue:

* Comience por la cadena de plantilla en el atributo `Route` del controlador:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller". En este ejemplo, el nombre de clase de controlador es **Todo**Controller; por tanto, el nombre del controlador es "todo". El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.
* Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso. En este ejemplo no se usa una plantilla. Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente. Al invocar a `GetTodoItem`, el valor `"{id}"` de la dirección URL se proporciona al método en su parámetro `id`.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Valores devueltos

El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta. El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada. Las excepciones no controladas se convierten en errores 5xx.

Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP. Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:

* Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).
* En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON. Devolver `item` genera una respuesta HTTP 200.

## <a name="test-the-gettodoitems-method"></a>Prueba del método GetTodoItems

En este tutorial se usa Postman para probar la API web.

* Instale [Postman](https://www.getpostman.com/downloads/).
* Inicie la aplicación web.
* Inicie Postman.
* Deshabilite **Comprobación del certificado SSL**.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* En **Postman** > **Preferencias** (pestaña **General**), deshabilite **Comprobación del certificado SSL**. También puede seleccionar la llave inglesa, hacer clic en **Configuración** y deshabilitar la comprobación del certificado SSL.

---
  
> [!WARNING]
> Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.

* Cree una nueva solicitud.
  * Establezca el método HTTP en **GET**.
  * Establezca la dirección URL de la solicitud en `https://localhost:<port>/api/todo`. Por ejemplo: `https://localhost:5001/api/todo`.
* Establezca **Vista de dos paneles** en Postman.
* Seleccione **Enviar**.

![Postman con solicitud Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Incorporación de un método Create

Agregue el siguiente método `PostTodoItem` en *Controllers/TodoController.cs*: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute). El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.

El método `CreatedAtAction` realiza las acciones siguientes:

* Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente. HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.
* Agrega un encabezado `Location` a la respuesta. El encabezado `Location` especifica el identificador URI de la tarea pendiente recién creada. Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`. La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>Prueba del método PostTodoItem

* Compile el proyecto.
* En Postman, establezca el método HTTP en `POST`.
* Seleccione la pestaña **Cuerpo**.
* Seleccione el botón de radio **Raw** (Sin formato).
* Establezca el tipo en **JSON (application/json)** .
* En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Seleccione **Enviar**.

  ![Postman con solicitud de creación](first-web-api/_static/create.png)

  Si recibe un error 405 (Método no permitido), probablemente sea el resultado de no haber compilado el proyecto después de agregar el método `PostTodoItem`.

### <a name="test-the-location-header-uri"></a>Prueba del URI del encabezado de ubicación

* Seleccione la pestaña **Encabezados** en el panel **Respuesta**.
* Copie el valor de encabezado **Ubicación**:

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/pmc2.png)

* Establezca el método en GET.
* Pegue el URI (por ejemplo, `https://localhost:5001/api/Todo/2`).
* Seleccione **Enviar**.

## <a name="add-a-puttodoitem-method"></a>Incorporación de un método PutTodoItem

Agregue el siguiente método `PutTodoItem`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT. La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios. Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.

### <a name="test-the-puttodoitem-method"></a>Prueba del método PutTodoItem

En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación. Debe haber un elemento en la base de datos antes de que realice una llamada PUT. Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.

Actualice la tarea pendiente que tiene el id. = 1 y establezca su nombre en "feed fish":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

En la imagen siguiente, se muestra la actualización de Postman:

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>Incorporación de un método DeleteTodoItem

Agregue el siguiente método `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

La respuesta de `DeleteTodoItem` es [204 (Sin contenido)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Prueba del método DeleteTodoItem

Use Postman para eliminar una tarea pendiente:

* Establezca el método en `DELETE`.
* Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/todo/1`).
* Seleccione **Enviar**.

La aplicación de ejemplo permite eliminar todos los elementos. Sin embargo, al eliminar el último elemento, se creará uno nuevo en el constructor de clase de modelo la próxima vez que se llame a la API.

## <a name="call-the-web-api-with-javascript"></a>Llamar a la API web con JavaScript

En esta sección, se agrega una página HTML que usa JavaScript para llamar a la API web. JQuery inicia la solicitud. JavaScript actualiza la página con los detalles de la respuesta de la API web.

Configure la aplicación para [atender archivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [habilitar la asignación de archivos predeterminada](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) mediante la actualización de *Startup.cs* con el siguiente código resaltado:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Cree una carpeta *wwwroot* en el directorio del proyecto.

Agregue un archivo HTML denominado *index.html* al directorio *wwwroot*. Reemplace el contenido por el siguiente marcado:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Agregue un archivo JavaScript denominado *site.js* al directorio *wwwroot*. Reemplace el contenido por el siguiente código:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Puede que sea necesario realizar un cambio en la configuración de inicio del proyecto de ASP.NET Core para probar la página HTML localmente:

* Abra *Properties\launchSettings.json*.
* Quite la propiedad `launchUrl` para forzar a la aplicación a abrirse en *index.html*, esto es, el archivo predeterminado del proyecto.

En este ejemplo se llama a todos los métodos CRUD de la API web. A continuación, encontrará algunas explicaciones de las llamadas a la API.

### <a name="get-a-list-of-to-do-items"></a>Obtención de una lista de tareas pendientes

jQuery envía una solicitud HTTP GET a la API web, que devuelve código JSON que representa una matriz de tareas pendientes. La función de devolución de llamada `success` se invoca si la solicitud se realiza correctamente. En la devolución de llamada, el DOM se actualiza con la información de la tarea pendiente.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Incorporación de una tarea pendiente

jQuery envía una solicitud HTTP POST con la tarea pendiente en el cuerpo de dicha solicitud. Las opciones `accepts` y `contentType` se establecen en `application/json` para especificar el tipo de medio que se va a recibir y a enviar. La tarea pendiente se convierte en JSON mediante [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Cuando la API devuelve un código de estado correcto, se invoca la función `getData` para actualizar la tabla HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Actualizar una tarea pendiente

El hecho de actualizar una tarea pendiente es similar al de agregar una. El valor `url` cambia para agregar el identificador único del elemento, y `type` es `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Eliminar una tarea pendiente

Para eliminar una tarea pendiente, hay que establecer el valor `type` de la llamada de AJAX en `DELETE` y especificar el identificador único de la tarea en la dirección URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Agregar compatibilidad con la autenticación a una API web

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a>Recursos adicionales

[Vea o descargue el código de ejemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Vea [cómo descargarlo](xref:index#how-to-download-a-sample).

Para obtener más información, vea los siguientes recursos:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Versión en YouTube de este tutorial](https://www.youtube.com/watch?v=TTkhEyGBfAk)
