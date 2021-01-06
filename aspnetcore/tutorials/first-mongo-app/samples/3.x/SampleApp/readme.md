---
page_type: sample
description: En este tutorial se muestra cómo crear una API web de ASP.NET Core con una base de datos NoSQL de MongoDB.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 95a2a6fcda0a4f7148183981f7dbacd06388329d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "84106525"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Creación de una API Web con ASP.NET Core y MongoDB

En este tutorial se crea una API web que realiza operaciones de creación, lectura, actualización y eliminación (CRUD) en una base de datos NoSQL de [MongoDB](https://www.mongodb.com/what-is-mongodb).

En este tutorial aprenderá a:

* Configurar MongoDB
* Crear una base de datos de MongoDB
* Definir un esquema y una colección de MongoDB
* Realizar operaciones de CRUD de MongoDB desde una API web
* Personalizar la serialización de JSON

## <a name="prerequisites"></a>Requisitos previos

* [.NET Core SDK 3.0 o posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [Versión preliminar de Visual Studio 2019](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) con la carga de trabajo **ASP.NET y desarrollo web**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a>Configurar MongoDB

Si usa Windows, MongoDB está instalado en *C:\\Archivos de programa\\MongoDB* de forma predeterminada. Agregue *C:\\Archivos de programa\\MongoDB\\Server\\\<version_number>\\bin* a la variable de entorno `Path`. Este cambio permite el acceso a MongoDB desde cualquier lugar en el equipo de desarrollo.

Use el Shell de mongo en los pasos siguientes para crear una base de datos, hacer colecciones y almacenar documentos. Para obtener más información sobre los comandos de Shell de mongo, consulte [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell) (Trabajo con el shell de Mongo).

1. Elija un directorio en el equipo de desarrollo para almacenar los datos. Por ejemplo, *C:\\BooksData* en Windows. Si no existe el directorio, créelo. El shell de mongo no crea nuevos directorios.
1. Abra un shell de comandos. Ejecute el comando siguiente para conectarse a MongoDB en el puerto predeterminado 27017. No olvide reemplazar `<data_directory_path>` por el directorio que eligió en el paso anterior.

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. Abra otra instancia del shell de comandos. Conéctese a la base de datos de prueba de forma predeterminada ejecutando el comando siguiente:

    ```console
    mongo
    ```

1. Ejecute lo siguiente en un shell de comandos:

    ```console
    use BookstoreDb
    ```

    Si aún no existe, se crea una base de datos denominada *BookstoreDb*. Si la base de datos existe, su conexión se abre para las transacciones.

1. Cree una colección `Books` con el comando siguiente:

    ```console
    db.createCollection('Books')
    ```

    Se muestra el siguiente resultado:

    ```console
    { "ok" : 1 }
    ```

1. Defina un esquema para la colección `Books` e inserte dos documentos con el comando siguiente:

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    Se muestra el siguiente resultado:

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

  > [!NOTE]
  > Los identificadores que se muestran en este artículo no coinciden con los que se mostrarán cuando ejecute este ejemplo.

1. Vea los documentos en la base de datos mediante el comando siguiente:

    ```console
    db.Books.find({}).pretty()
    ```

    Se muestra el siguiente resultado:

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    El esquema agrega una propiedad `_id` generada automáticamente del tipo `ObjectId` para cada documento.

La base de datos está lista. Puede empezar a crear la API web de ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Creación de un proyecto de API web de ASP.NET Core

1. Vaya a **Archivo** > **Nuevo** > **Proyecto**.
1. Seleccione el tipo de proyecto **Aplicación web de ASP.NET Core** y, luego, **Siguiente**.
1. Denomine el proyecto *BooksApi* y seleccione **Crear**.
1. Seleccione **.NET Core** como plataforma de destino y **ASP.NET Core 3.0**. Seleccione la plantilla de proyecto **API** y, luego, **Crear**.
1. Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB. En la ventana **Consola del Administrador de paquetes**, desplácese hasta la raíz del proyecto. Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a>Adición de un modelo de entidad

1. Agregue un directorio *Modelos* a la raíz del proyecto.
1. Agregue una clase `Book` al directorio *Modelos* con el código siguiente:

    ```csharp
    using MongoDB.Bson;
    using MongoDB.Bson.Serialization.Attributes;

    namespace BooksApi.Models
    {
        public class Book
        {
            [BsonId]
            [BsonRepresentation(BsonType.ObjectId)]
            public string Id { get; set; }

            [BsonElement("Name")]
            public string BookName { get; set; }

            public decimal Price { get; set; }

            public string Category { get; set; }

            public string Author { get; set; }
        }
    }
    ```

    En la clase anterior, se requiere la propiedad `Id`

    * para asignar el objeto de Common Language Runtime (CLR) a la colección de MongoDB.
    * para anotarla con [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) a fin de designarla como clave principal del documento.
    * para anotarla con [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) a fin de permitir que se pase el parámetro como tipo `string` en lugar de una estructura [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm). Mongo controla la conversión de `string` a `ObjectId`.

    La propiedad `BookName` se anota con el atributo [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm). El valor `Name` del atributo representa el nombre de propiedad en la colección de MongoDB.

## <a name="add-a-configuration-model"></a>Adición de un modelo configuración

1. Agregue los siguientes valores de configuración de base de datos a *appsettings.json*:

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. Agregue un archivo *BookstoreDatabaseSettings.cs* al directorio *Models* con el código siguiente:

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    La clase anterior `BookstoreDatabaseSettings` se utiliza para almacenar los valores de propiedad `BookstoreDatabaseSettings` del archivo *appsettings.json*. Los nombres de las propiedades de JSON y C# son iguales para facilitar el proceso de asignación.

1. Agregue el código resaltado siguiente a `Startup.ConfigureServices`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    En el código anterior:

    * La instancia de configuración a la que la sección `BookstoreDatabaseSettings` del archivo *appsettings.json* enlaza está registrada en el contenedor de inserción de dependencias (DI). Por ejemplo, una propiedad `ConnectionString` del objeto `BookstoreDatabaseSettings` se rellena con la propiedad `BookstoreDatabaseSettings:ConnectionString` en *appsettings.json*.
    * La interfaz `IBookstoreDatabaseSettings` se registra en la inserción de dependencias con una [duración de servicio](xref:fundamentals/dependency-injection#service-lifetimes) de tipo singleton. Cuando se inserta, la instancia de la interfaz se resuelve en un objeto `BookstoreDatabaseSettings`.

1. Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver las referencias a `BookstoreDatabaseSettings` y `IBookstoreDatabaseSettings`:

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a>Adición de un servicio de operaciones CRUD

1. Agregue un directorio *Servicios* a la raíz del proyecto.
1. Agregue una clase `BookService` al directorio *Servicios* con el código siguiente:

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    En el código anterior, se recuperó una instancia de `IBookstoreDatabaseSettings` de la inserción de dependencias mediante la inserción de un constructor. Esta técnica proporciona acceso a los valores de configuración de *appsettings.json* que se agregaron en la sección [Adición de un modelo de configuración](#add-a-configuration-model).

1. Agregue el código resaltado siguiente a `Startup.ConfigureServices`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    En el código anterior, la clase `BookService` se registra con inserción de dependencias para admitir la inserción del constructor en las clases de consumo. La duración de servicio de tipo singleton es la más adecuada porque `BookService` toma una dependencia directa sobre `MongoClient`. Según las [instrucciones oficiales de reutilización de cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` debe registrarse en la inserción de dependencias con una duración de servicio de tipo singleton.

1. Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver la referencia a `BookService`:


    ```csharp
    using BooksApi.Services;
    ```

La clase`BookService` usa los miembros `MongoDB.Driver` siguientes para realizar operaciones CRUD en la base de datos:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lee la instancia del servidor para realizar operaciones de base de datos. Se proporciona la cadena de conexión de MongoDB al constructor de esta clase:

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): representa la base de datos de Mongo para realizar operaciones. Este tutorial usa el método genérico [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) en la interfaz para tener acceso a los datos de una colección específica. Realice las operaciones CRUD en la colección después de llamar a este método. En la llamada al método `GetCollection<TDocument>(collection)`:
  * `collection` representa el nombre de la colección.
  * `TDocument` representa el tipo de objeto CLR almacenado en la colección.

`GetCollection<TDocument>(collection)` devuelve un objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa la colección. En este tutorial, se invocan los métodos siguientes en la colección:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): elimina un único documento que cumple los criterios de búsqueda proporcionados.
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): devuelve todos los documentos de la colección que cumplen los criterios de búsqueda indicados.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): inserta el objeto proporcionado como un nuevo documento en la colección.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): reemplaza un único documento que cumple los criterios de búsqueda indicados por el objeto proporcionado.

## <a name="add-a-controller"></a>Incorporación de un controlador

Agregue una clase `BooksController` al directorio *Controladores* con el código siguiente:

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

El controlador de API web anterior:

* Usa la clase `BookService` para realizar operaciones CRUD.
* Contiene métodos de acción para admitir las solicitudes GET, POST, PUT y DELETE de HTTP.
* Llama a <xref:System.Web.Http.ApiController.CreatedAtRoute*> en el método de acción `Create` para devolver una respuesta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). El código de estado 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor. `CreatedAtRoute` también agrega un encabezado `Location` a la respuesta. El encabezado `Location` especifica el identificador URI del libro recién creado.

## <a name="test-the-web-api"></a>Prueba de la API web

1. Compile y ejecute la aplicación.

1. Vaya a `http://localhost:<port>/api/books` para probar el método de acción `Get` sin parámetros del controlador. Se muestra la siguiente respuesta JSON:

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

1. Vaya a `http://localhost:<port>/api/books/{id here}` para probar el método de acción `Get` sobrecargado del controlador. Se muestra la siguiente respuesta JSON:

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a>Configuración de las opciones de serialización de JSON

Hay dos detalles que cambiar sobre las respuestas JSON devueltas en la sección [Prueba de la API web](#test-the-web-api):

* Las mayúsculas y minúsculas Camel predeterminadas de los nombres de propiedad se deben cambiar para que coincidan con el uso de mayúsculas y minúsculas de Pascal de los nombres de propiedad del objeto CLR.
* La propiedad `bookName` se debe devolver como `Name`.

Para satisfacer los requisitos anteriores, realice los cambios siguientes:

1. JSON.NET se ha quitado de la plataforma compartida de ASP.NET. Agregue una referencia de paquete a [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).

1. En `Startup.ConfigureServices`, cambie el código resaltado siguiente en la llamada al método `AddMvc`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    Con el cambio anterior, los nombres de propiedad de la respuesta JSON serializada de la API web coinciden con sus nombres de propiedad correspondientes en el tipo de objeto CLR. Por ejemplo, la propiedad `Author` de la clase `Book` se serializa como `Author`.

1. En *Models/Book.cs*, anote la propiedad `BookName` con el atributo [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) siguiente:

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    El valor `Name` del atributo `[JsonProperty]` representa el nombre de propiedad en la respuesta JSON serializada de la API web.

1. Agregue el código siguiente en la parte superior del archivo *Models/Book.cs* para resolver la referencia al atributo `[JsonProperty]`:

    ```csharp
    using Newtonsoft.Json;
    ```

1. Repita los pasos definidos en la sección [Prueba de la API web](#test-the-web-api). Observe la diferencia en los nombres de propiedad JSON.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la creación de las API web de ASP.NET Core, consulte los siguientes recursos:

* [Versión de YouTube de este artículo](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [Creación de API web con ASP.NET Core](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
