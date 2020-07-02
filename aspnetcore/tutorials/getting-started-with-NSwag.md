---
title: Introducción a NSwag y ASP.NET Core
author: zuckerthoben
description: Obtenga información sobre cómo usar NSwag para generar documentación y páginas de ayuda de una ASP.NET Web API.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 7c24340af0f0d589d683b0134d186bf9304d91b1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403448"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>Introducción a NSwag y ASP.NET Core

Por [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com) y [Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([cómo descargarlo](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([cómo descargarlo](xref:index#how-to-download-a-sample))

::: moniker-end

NSwag ofrece las siguientes capacidades:

* Capacidad de usar la interfaz de usuario y el generador de Swagger.
* Capacidad de generar código con flexibilidad.

Con NSwag, no es necesario que exista una API; se pueden usar API de terceros que incluyan Swagger y que generen una implementación de cliente. NSwag permite acelerar el ciclo de desarrollo y adaptarse fácilmente a los cambios de API.

## <a name="register-the-nswag-middleware"></a>Registro del middleware de NSwag

Registre el middleware de NSwag para:

* Generar la especificación de Swagger para la API web implementada.
* Proporcionar la interfaz de usuario de Swagger para examinar y probar la API web.

Para usar [NSwag](https://github.com/RicoSuter/NSwag) con middleware de ASP.NET Core, instale el paquete NuGet [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/). Este paquete contiene el middleware para generar y proporcionar la especificación de Swagger, la interfaz de usuario de Swagger (v2 y v3) y la [interfaz de usuario de ReDoc](https://github.com/Rebilly/ReDoc).

Use uno de los siguientes métodos para instalar el paquete NuGet de NSwag:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En la ventana **Consola del Administrador de paquetes**:
  * Vaya a **Vista** > **Otras ventanas** > **Consola del Administrador de paquetes**.
  * Vaya al directorio en el que está *TodoApi.csproj*.
  * Ejecute el siguiente comando:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* En el cuadro de diálogo **Administrar paquetes NuGet**:
  * Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** > **Administrar paquetes NuGet**.
  * Establezca el **origen del paquete** en "nuget.org".
  * Escriba "NSwag.AspNetCore" en el cuadro de búsqueda.
  * Seleccione el paquete "NSwag.AspNetCore" en la pestaña **Examinar** y haga clic en **Instalar**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Haga clic con el botón derecho en la carpeta *Paquetes* en **Panel de solución** > **Agregar paquetes...**
* Establezca el menú desplegable **Origen** de la ventana **Agregar paquetes** en "nuget.org".
* Escriba "NSwag.AspNetCore" en el cuadro de búsqueda.
* Seleccione el paquete "NSwag.AspNetCore" en el panel de resultados y haga clic en **Agregar paquete**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Ejecute el siguiente comando:

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Agregar y configurar el middleware de Swagger

Para agregar y configurar Swagger en su aplicación de ASP.NET Core, realice los siguientes pasos:

* En el método `Startup.ConfigureServices`, registre los servicios necesarios de Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* En el método `Startup.Configure`, habilite el middleware para servir la especificación y la interfaz de usuario de Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Inicie la aplicación. Vaya a:
  * `http://localhost:<port>/swagger` para ver la interfaz de usuario de Swagger.
  * `http://localhost:<port>/swagger/v1/swagger.json` para ver la especificación de Swagger.

## <a name="code-generation"></a>Generación de código

Para aprovechar las capacidades de generación de código de NSwag, elija una de las siguientes opciones:

* [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): aplicación de escritorio de Windows que permite generar código de cliente de API en C# o TypeScript.
* Paquetes NuGet [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) o [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) para generar código dentro del proyecto.
* NSwag desde la [línea de comandos](https://github.com/RicoSuter/NSwag/wiki/CommandLine).
* Paquete NuGet [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild).
* [Servicio conectado de Unchase OpenAPI (Swagger)](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): servicio conectado de Visual Studio para generar código de cliente de API en C# o TypeScript. También genera controladores de C# para los servicios de OpenAPI con NSwag.

### <a name="generate-code-with-nswagstudio"></a>Generación de código con NSwagStudio

* Instale NSwagStudio siguiendo las instrucciones del [repositorio de GitHub de NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio). En la página de la versión NSwag, puede descargar una versión de XCOPY que se puede iniciar sin privilegios de administrador y de instalación.
* Inicie NSwagStudio y escriba la URL del archivo *swagger.json* en el cuadro de texto **Swagger Specification URL** (URL de especificación de Swagger). Por ejemplo, *http://localhost:44354/swagger/v1/swagger.json* .
* Haga clic en el botón **Create local Copy** (Crear copia local) para generar una representación JSON de la especificación de Swagger.

  ![Creación de una copia local de la especificación de Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* En el área **Outputs** (Salidas), haga clic en la casilla **CSharp Client** (Cliente de CSharp). En función del proyecto, también puede elegir **TypeScript Client** (Cliente de TypeScript) o **CSharp Web API Controller** (Controlador de API web de CSharp). Si selecciona **CSharp Web API Controller** (Controlador de API web de CSharp), una especificación de servicio volverá a generar el servicio a modo de generación inversa.
* Haga clic en **Generate Outputs** (Generar salidas) para producir una implementación completa del cliente de C# del proyecto *TodoApi.NSwag*. Para ver el código de cliente generado, haga clic en la pestaña **CSharp Client** (Cliente de CSharp):

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> El código de cliente de C# se genera en función de las selecciones en la pestaña **Settings** (Configuración). Modifique esta configuración para realizar tareas como cambiar el nombre del espacio de nombres predeterminado y generar un método sincrónico.

* Copie el código de C# generado en un archivo del proyecto de cliente que usará la API.
* Empiece a consumir la Web API:

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a>Personalización de la documentación de API

Swagger proporciona opciones para documentar el modelo de objetos de cara a facilitar el consumo de Web API.

### <a name="api-info-and-description"></a>Información y descripción de la API

En el método `Startup.ConfigureServices`, la acción de configuración que se pasa al método `AddSwaggerDocument` agrega información, como el autor, la licencia y la descripción:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

La interfaz de usuario de Swagger muestra información de la versión:

![Interfaz de usuario de Swagger con información de la versión](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>comentarios XML

Para habilitar los comentarios XML, realice los siguientes pasos:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Haga clic con el botón derecho en el **Explorador de soluciones** y seleccione **Editar <nombre_de_proyecto>.csproj**.
* Agregue manualmente las líneas resaltadas al archivo *.csproj*:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Propiedades**.
* Active la casilla **Archivo de documentación XML** en la sección **Salida** de la pestaña **Compilar**.

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* Desde el *Panel de solución*, presione **Control** y haga clic en el nombre del proyecto. Vaya a **Herramientas** > **Editar archivo**.
* Agregue manualmente las líneas resaltadas al archivo *.csproj*:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Abra el cuadro de diálogo **Opciones del proyecto** > **Compilar** > **Compilador**.
* Active la casilla **Generar documentación XML** en la sección **Opciones generales**.

::: moniker-end

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Agregue manualmente las líneas resaltadas al archivo *.csproj*:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Anotaciones de datos

::: moniker range="<= aspnetcore-2.0"

Dado que NSwag usa [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) y el tipo de valor devuelto recomendado para acciones de API web es [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), no puede inferir su acción ni el valor que devuelve.

Considere el ejemplo siguiente:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

La acción anterior devuelve `IActionResult` pero, dentro de la acción, devuelve [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) o [BadRequest](xref:System.Web.Http.ApiController.BadRequest*). Use las anotaciones de datos para indicar a los clientes qué códigos de estado HTTP se sabe que esta acción devuelve. Marque la acción con los siguientes atributos:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Dado que NSwag usa [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) y el tipo de valor devuelto recomendado para acciones de API web es [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), solo puede inferir el tipo de valor devuelto definido por `T`. No puede inferir automáticamente ningún otro tipo de valor devuelto.

Considere el ejemplo siguiente:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

La acción anterior devuelve `ActionResult<T>`. Dentro de la acción, se devuelve [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*). Dado que el controlador tiene el atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute), también es posible una respuesta [BadRequest](xref:System.Web.Http.ApiController.BadRequest*). Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses). Use las anotaciones de datos para indicar a los clientes qué códigos de estado HTTP se sabe que esta acción devuelve. Marque la acción con los siguientes atributos:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

En ASP.NET Core 2.2 o versiones posteriores, puede usar las convenciones en lugar de decorar explícitamente acciones individuales con `[ProducesResponseType]`. Para obtener más información, vea <xref:web-api/advanced/conventions>.

::: moniker-end

Ahora, el generador de Swagger puede describir con precisión esta acción y los clientes generados sabrán lo que reciben cuando llamen al punto de conexión. Le recomendamos que marque todas las acciones con estos atributos.

Para obtener instrucciones sobre qué respuestas HTTP deben devolver las acciones de API, vea la [especificación RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).
