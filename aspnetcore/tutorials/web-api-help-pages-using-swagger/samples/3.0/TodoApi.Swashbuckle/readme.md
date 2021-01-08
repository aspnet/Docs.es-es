---
page_type: sample
description: Obtenga información sobre cómo agregar Swashbuckle a un proyecto de ASP.NET Core Web API para integrar la interfaz de usuario de Swagger.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: e02247325f430b0ce23dbb3f5bc344a60a1a164a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "78647591"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="aba6b-102">Introducción a Swashbuckle y ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aba6b-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="aba6b-103">Cuando un desarrollador usa una API Web, puede que le resulte complicado comprender sus diversos métodos.</span><span class="sxs-lookup"><span data-stu-id="aba6b-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="aba6b-104">[Swagger](https://swagger.io/), también conocido como [OpenAPI](https://www.openapis.org/), resuelve el problema de generar páginas útiles de ayuda y documentación relativas a las API web.</span><span class="sxs-lookup"><span data-stu-id="aba6b-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="aba6b-105">Así, reporta ventajas como una documentación interactiva, la generación de SDK de cliente y la detectabilidad de API.</span><span class="sxs-lookup"><span data-stu-id="aba6b-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="aba6b-106">En este ejemplo, se muestra la implementación .NET [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="aba6b-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="aba6b-107">Agregar y configurar el middleware de Swagger</span><span class="sxs-lookup"><span data-stu-id="aba6b-107">Add and configure Swagger middleware</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

<span data-ttu-id="aba6b-108">En el método `Startup.Configure`, habilite el middleware para servir el documento JSON generado y la interfaz de usuario de Swagger:</span><span class="sxs-lookup"><span data-stu-id="aba6b-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"); 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="aba6b-109">La llamada de método `UseSwaggerUI` anterior habilita el [middleware de archivos estáticos](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="aba6b-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="aba6b-110">Si el destino es .NET Framework o .NET Core 1.x, agregue el paquete NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="aba6b-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="aba6b-111">Inicie la aplicación y vaya a `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="aba6b-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="aba6b-112">El documento generado en el que se describen los puntos de conexión aparecerá según se muestra en la [especificación de Swagger (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="aba6b-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="aba6b-113">La interfaz de usuario de Swagger se encuentra en `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="aba6b-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="aba6b-114">Explore la API a través de la interfaz de usuario de Swagger e incorpórela a otros programas.</span><span class="sxs-lookup"><span data-stu-id="aba6b-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="aba6b-115">Para servir la interfaz de usuario de Swagger en la raíz de la aplicación (`http://localhost:<port>/`), establezca la propiedad `RoutePrefix` en una cadena vacía:</span><span class="sxs-lookup"><span data-stu-id="aba6b-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="aba6b-116">Si usa directorios con IIS o un proxy inverso, establezca el punto de conexión de Swagger en una ruta de acceso relativa mediante el prefijo `./`.</span><span class="sxs-lookup"><span data-stu-id="aba6b-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="aba6b-117">Por ejemplo: `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="aba6b-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="aba6b-118">El uso de `/swagger/v1/swagger.json` indica a la aplicación que debe buscar el archivo JSON en la verdadera raíz de la dirección URL (junto con el prefijo de ruta, si se usa).</span><span class="sxs-lookup"><span data-stu-id="aba6b-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="aba6b-119">Por ejemplo, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` en lugar de `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="aba6b-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="aba6b-120">Personalizar y ampliar</span><span class="sxs-lookup"><span data-stu-id="aba6b-120">Customize and extend</span></span>

<span data-ttu-id="aba6b-121">Swagger proporciona opciones para documentar el modelo de objetos y personalizar la interfaz de usuario para que coincida con el tema.</span><span class="sxs-lookup"><span data-stu-id="aba6b-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="aba6b-122">En la clase `Startup`, agregue los siguientes espacios de nombres:</span><span class="sxs-lookup"><span data-stu-id="aba6b-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="aba6b-123">Información y descripción de la API</span><span class="sxs-lookup"><span data-stu-id="aba6b-123">API info and description</span></span>

<span data-ttu-id="aba6b-124">La acción de configuración que se pasa al método `AddSwaggerGen` agrega información, como el autor, la licencia y la descripción:</span><span class="sxs-lookup"><span data-stu-id="aba6b-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

<span data-ttu-id="aba6b-125">La interfaz de usuario de Swagger muestra información de la versión:</span><span class="sxs-lookup"><span data-stu-id="aba6b-125">The Swagger UI displays the version's information:</span></span>

![Interfaz de usuario de Swagger con información de la versión: descripción, autor y el vínculo See more (Ver más)](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="aba6b-127">comentarios XML</span><span class="sxs-lookup"><span data-stu-id="aba6b-127">XML comments</span></span>

<span data-ttu-id="aba6b-128">Los comentarios XML se pueden habilitar con los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="aba6b-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="aba6b-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aba6b-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aba6b-130">Haga clic con el botón derecho en el **Explorador de soluciones** y seleccione **Editar <nombre_de_proyecto>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="aba6b-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="aba6b-131">Agregue manualmente las líneas resaltadas al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="aba6b-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aba6b-132">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="aba6b-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="aba6b-133">Desde el *Panel de solución*, presione **Control** y haga clic en el nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="aba6b-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="aba6b-134">Vaya a **Herramientas** > **Editar archivo**.</span><span class="sxs-lookup"><span data-stu-id="aba6b-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="aba6b-135">Agregue manualmente las líneas resaltadas al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="aba6b-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[<span data-ttu-id="aba6b-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aba6b-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aba6b-137">Agregue manualmente las líneas resaltadas al archivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="aba6b-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="aba6b-138">Puede habilitar los comentarios XML para proporcionar información de depuración para miembros y tipos públicos sin documentación.</span><span class="sxs-lookup"><span data-stu-id="aba6b-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="aba6b-139">Los miembros y tipos que no estén documentados se indican por medio del mensaje de advertencia.</span><span class="sxs-lookup"><span data-stu-id="aba6b-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="aba6b-140">Por ejemplo, el siguiente mensaje señala una infracción con el código de advertencia 1591:</span><span class="sxs-lookup"><span data-stu-id="aba6b-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="aba6b-141">Para eliminar las advertencias a nivel de proyecto, defina una lista delimitada por punto y coma de códigos de advertencia que se deban omitir en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="aba6b-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="aba6b-142">Al anexar los códigos de advertencia a `$(NoWarn);`, se aplican también los [valores predeterminados de C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).</span><span class="sxs-lookup"><span data-stu-id="aba6b-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="aba6b-143">Para suprimir las advertencias solo para miembros específicos, incluya el código en directivas de preprocesador de [advertencias #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning).</span><span class="sxs-lookup"><span data-stu-id="aba6b-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="aba6b-144">Este enfoque es útil para el código que no debe exponerse mediante los documentos de API. En el ejemplo siguiente, se omite el código de advertencia CS1591 para toda la clase `Program`.</span><span class="sxs-lookup"><span data-stu-id="aba6b-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="aba6b-145">La ejecución del código de advertencia se restaura al cerrar la definición de clase.</span><span class="sxs-lookup"><span data-stu-id="aba6b-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="aba6b-146">Especifique varios códigos de advertencia con una lista delimitada por comas.</span><span class="sxs-lookup"><span data-stu-id="aba6b-146">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="aba6b-147">Configure Swagger para usar el archivo XML que se generó con las instrucciones anteriores.</span><span class="sxs-lookup"><span data-stu-id="aba6b-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="aba6b-148">En Linux o sistemas operativos que no sean Windows, las rutas de acceso y los nombres de archivo pueden distinguir entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="aba6b-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="aba6b-149">Por ejemplo, un archivo *TodoApi.XML* es válido en Windows, pero no en CentOS.</span><span class="sxs-lookup"><span data-stu-id="aba6b-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

<span data-ttu-id="aba6b-150">En el código anterior, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) se usa para crear un nombre de archivo XML que coincida con el proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="aba6b-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="aba6b-151">La propiedad [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) sirve para crear una ruta de acceso al archivo XML.</span><span class="sxs-lookup"><span data-stu-id="aba6b-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="aba6b-152">Algunas características de Swagger (por ejemplo, esquemas de parámetros de entrada o métodos HTTP y códigos de respuesta de los atributos respectivos) funcionan sin el uso de un archivo de documentación XML.</span><span class="sxs-lookup"><span data-stu-id="aba6b-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="aba6b-153">Para la mayoría de las características, es decir, los resúmenes de los métodos y las descripciones de los parámetros y los códigos de respuesta, el uso de un archivo XML es obligatorio.</span><span class="sxs-lookup"><span data-stu-id="aba6b-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="aba6b-154">Agregar comentarios con la triple barra diagonal a una acción mejora la interfaz de usuario de Swagger en tanto agrega una descripción de la acción al encabezado de la sección.</span><span class="sxs-lookup"><span data-stu-id="aba6b-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="aba6b-155">Agregue un elemento [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) antes de la acción `Delete`:</span><span class="sxs-lookup"><span data-stu-id="aba6b-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
<span data-ttu-id="aba6b-156">La interfaz de usuario de Swagger muestra el texto interno del elemento `<summary>` del código anterior:</span><span class="sxs-lookup"><span data-stu-id="aba6b-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Interfaz de usuario de Swagger en la que se muestra el comentario XML "Deletes a specific TodoItem" (Elimina una tarea pendiente específica)](sample_images/triple-slash-comments.png)

<span data-ttu-id="aba6b-159">La interfaz de usuario se controla por medio del esquema JSON generado:</span><span class="sxs-lookup"><span data-stu-id="aba6b-159">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```
<span data-ttu-id="aba6b-160">Agregue un elemento [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) a la documentación del método de acción `Create`.</span><span class="sxs-lookup"><span data-stu-id="aba6b-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="aba6b-161">Este elemento complementa la información especificada en el elemento `<summary>` y proporciona una interfaz de usuario de Swagger más sólida.</span><span class="sxs-lookup"><span data-stu-id="aba6b-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="aba6b-162">El contenido del elemento `<remarks>` puede estar formado por texto, JSON o XML.</span><span class="sxs-lookup"><span data-stu-id="aba6b-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
<span data-ttu-id="aba6b-163">Fíjese en las mejoras de la interfaz de usuario con estos comentarios extra:</span><span class="sxs-lookup"><span data-stu-id="aba6b-163">Notice the UI enhancements with these additional comments:</span></span>

![Interfaz de usuario de Swagger con comentarios adicionales](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="aba6b-165">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="aba6b-165">Data annotations</span></span>

<span data-ttu-id="aba6b-166">Marque el modelo con atributos que se encuentren en el espacio de nombres [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) para controlar los componentes de la interfaz de usuario de Swagger.</span><span class="sxs-lookup"><span data-stu-id="aba6b-166">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="aba6b-167">Agregue el atributo `[Required]` a la propiedad `Name` de la clase `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="aba6b-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="aba6b-168">La presencia de este atributo cambia el comportamiento de la interfaz de usuario y modifica el esquema JSON subyacente:</span><span class="sxs-lookup"><span data-stu-id="aba6b-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="aba6b-169">Agregue el atributo `[Produces("application/json")]` al controlador de API.</span><span class="sxs-lookup"><span data-stu-id="aba6b-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="aba6b-170">Su propósito consiste en declarar que las acciones del controlador admiten un contenido de respuesta de tipo *application/json*:</span><span class="sxs-lookup"><span data-stu-id="aba6b-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="aba6b-171">En el menú desplegable **Response Content Type** (Tipo de contenido de respuesta) se selecciona este tipo de contenido como el valor predeterminado para las acciones GET del controlador:</span><span class="sxs-lookup"><span data-stu-id="aba6b-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Interfaz de usuario de Swagger con el tipo de contenido de respuesta predeterminado](sample_images/json-response-content-type.png)

<span data-ttu-id="aba6b-173">A medida que aumenta el uso de anotaciones de datos en la API web, la interfaz de usuario y las páginas de ayuda de la API pasan a ser más descriptivas y útiles.</span><span class="sxs-lookup"><span data-stu-id="aba6b-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="aba6b-174">Describir los tipos de respuesta</span><span class="sxs-lookup"><span data-stu-id="aba6b-174">Describe response types</span></span>

<span data-ttu-id="aba6b-175">Lo que más preocupa a los desarrolladores que consumen una API web es lo que se devuelve; sobre todo, los tipos de respuesta y los códigos de error (si no son los habituales).</span><span class="sxs-lookup"><span data-stu-id="aba6b-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="aba6b-176">Los tipos de respuesta y los códigos de error se indican en las anotaciones de datos y los comentarios XML.</span><span class="sxs-lookup"><span data-stu-id="aba6b-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="aba6b-177">La acción `Create` devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="aba6b-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="aba6b-178">Cuando el cuerpo de solicitud enviado es null, se devuelve un código de estado HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="aba6b-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="aba6b-179">Sin la documentación correcta en la interfaz de usuario de Swagger, el consumidor no dispone de la información necesaria de estos resultados esperados.</span><span class="sxs-lookup"><span data-stu-id="aba6b-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="aba6b-180">Corrija este problema agregando las líneas resaltadas en el siguiente ejemplo:</span><span class="sxs-lookup"><span data-stu-id="aba6b-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="aba6b-181">Ahora, la interfaz de usuario de Swagger documenta de forma clara los códigos de respuesta HTTP esperados:</span><span class="sxs-lookup"><span data-stu-id="aba6b-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Interfaz de usuario de Swagger, donde se muestra la descripción de la clase de respuesta POST "Returns the newly created item" (Devuelve el elemento recién creado) y "400 - If the item is null" (400 - Si el elemento es nulo) para el código de estado y el motivo en Response Messages (Mensajes de respuesta)](sample_images/data-annotations-response-types.png)

<span data-ttu-id="aba6b-183">En ASP.NET Core 2.2 o versiones posteriores, las convenciones se pueden usar como alternativa a la representación explícita de acciones individuales con `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="aba6b-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="aba6b-184">Para más información, consulte [Uso de convenciones de API web](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="aba6b-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="aba6b-185">Para información sobre cómo personalizar la interfaz de usuario, consulte: [Personalización de la interfaz de usuario](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend).</span><span class="sxs-lookup"><span data-stu-id="aba6b-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
