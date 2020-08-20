---
title: Creación de una API Web con ASP.NET Core y MongoDB
author: prkhandelwal
description: En este tutorial se muestra cómo crear una API web de ASP.NET Core con una base de datos NoSQL de MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
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
uid: tutorials/first-mongo-app
ms.openlocfilehash: 61f72c4d281e7957b520e1660440e536ebd4c78a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631776"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Creación de una API Web con ASP.NET Core y MongoDB

Por [Pratik Khandelwal](https://twitter.com/K2Prk) y [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

En este tutorial se crea una API web que realiza operaciones de creación, lectura, actualización y eliminación (CRUD) en una base de datos NoSQL de [MongoDB](https://www.mongodb.com/what-is-mongodb).

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Configurar MongoDB
> * Crear una base de datos de MongoDB
> * Definir un esquema y una colección de MongoDB
> * Realizar operaciones de CRUD de MongoDB desde una API web
> * Personalizar la serialización de JSON

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 3.0 o posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 3.0 o posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* [.NET Core SDK 3.0 o posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio para Mac, versión 7.7 o posterior](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

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

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vaya a **Archivo** > **Nuevo** > **Proyecto**.
1. Seleccione el tipo de proyecto **Aplicación web de ASP.NET Core** y, luego, **Siguiente**.
1. Denomine el proyecto *BooksApi* y seleccione **Crear**.
1. Seleccione **.NET Core** como plataforma de destino y **ASP.NET Core 3.0**. Seleccione la plantilla de proyecto **API** y, luego, **Crear**.
1. Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB. En la ventana **Consola del Administrador de paquetes**, desplácese hasta la raíz del proyecto. Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Ejecute los siguientes comandos en un shell de comandos:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Se genera un nuevo proyecto de API web de ASP.NET Core destinado a .NET Core, que puede abrir en Visual Studio Code.

1. Cuando el icono de llama de OmniSharp de la barra de estado se ponga verde, aparecerá un cuadro de diálogo en el que se le indicará que **faltan los activos necesarios para compilar y depurar en "RazonPagesMovie" y, luego, si quiere agregarlos**. Seleccione **Sí**.
1. Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB. Abra **Terminal integrado** y navegue hasta la raíz del proyecto. Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **Archivo** > **Nueva solución** >  **.NET Core** > **Aplicación** en la barra lateral. En la versión 8.6 o posteriores, seleccione **Archivo** > **Nueva solución** > **Web y consola** > **Aplicación** en la barra lateral.
1. Seleccione la plantilla de proyecto de C# **ASP.NET Core** > **API** y, luego, **Siguiente**.
1. Seleccione **.NET Core 3.1** en la lista desplegable **Plataforma de destino** y, luego, **Siguiente**.
1. Escriba *BooksApi* en **Nombre del proyecto** y seleccione **Crear**.
1. En el panel **Explorador de soluciones**, haga clic con el botón derecho en el nodo **Dependencias** del proyecto y seleccione **Agregar paquetes**.
1. Escriba *MongoDB.Driver* en el cuadro de búsqueda, seleccione el paquete *MongoDB.Driver* y, luego, **Agregar paquete**.
1. Seleccione el botón **Aceptar** del cuadro de diálogo **Aceptación de la licencia**.

---

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

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. Agregue un archivo *BookstoreDatabaseSettings.cs* al directorio *Models* con el código siguiente:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   La clase anterior `BookstoreDatabaseSettings` se utiliza para almacenar los valores de propiedad `BookstoreDatabaseSettings` del archivo *appsettings.json*. Los nombres de las propiedades de JSON y C# son iguales para facilitar el proceso de asignación.

1. Agregue el código resaltado siguiente a `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   En el código anterior:

   * La instancia de configuración a la que la sección `BookstoreDatabaseSettings` del archivo *appsettings.json* enlaza está registrada en el contenedor de inserción de dependencias (DI). Por ejemplo, una propiedad `ConnectionString` del objeto `BookstoreDatabaseSettings` se rellena con la propiedad `BookstoreDatabaseSettings:ConnectionString` en *appsettings.json*.
   * La interfaz `IBookstoreDatabaseSettings` se registra en la inserción de dependencias con una [duración de servicio](xref:fundamentals/dependency-injection#service-lifetimes) de tipo singleton. Cuando se inserta, la instancia de la interfaz se resuelve en un objeto `BookstoreDatabaseSettings`.

1. Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver las referencias a `BookstoreDatabaseSettings` y `IBookstoreDatabaseSettings`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Adición de un servicio de operaciones CRUD

1. Agregue un directorio *Servicios* a la raíz del proyecto.
1. Agregue una clase `BookService` al directorio *Servicios* con el código siguiente:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   En el código anterior, se recuperó una instancia de `IBookstoreDatabaseSettings` de la inserción de dependencias mediante la inserción de un constructor. Esta técnica proporciona acceso a los valores de configuración de *appsettings.json* que se agregaron en la sección [Adición de un modelo de configuración](#add-a-configuration-model).

1. Agregue el código resaltado siguiente a `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   En el código anterior, la clase `BookService` se registra con inserción de dependencias para admitir la inserción del constructor en las clases de consumo. La duración de servicio de tipo singleton es la más adecuada porque `BookService` toma una dependencia directa sobre `MongoClient`. Según las [instrucciones oficiales de reutilización de cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` debe registrarse en la inserción de dependencias con una duración de servicio de tipo singleton.

1. Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver la referencia a `BookService`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

La clase`BookService` usa los miembros `MongoDB.Driver` siguientes para realizar operaciones CRUD en la base de datos:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lee la instancia del servidor para realizar operaciones de base de datos. Se proporciona la cadena de conexión de MongoDB al constructor de esta clase:

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

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

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

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

1. En `Startup.ConfigureServices`, cambie el código resaltado siguiente en la llamada al método `AddControllers`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Con el cambio anterior, los nombres de propiedad de la respuesta JSON serializada de la API web coinciden con sus nombres de propiedad correspondientes en el tipo de objeto CLR. Por ejemplo, la propiedad `Author` de la clase `Book` se serializa como `Author`.

1. En *Models/Book.cs*, anote la propiedad `BookName` con el atributo [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) siguiente:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   El valor `Name` del atributo `[JsonProperty]` representa el nombre de propiedad en la respuesta JSON serializada de la API web.

1. Agregue el código siguiente en la parte superior del archivo *Models/Book.cs* para resolver la referencia al atributo `[JsonProperty]`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Repita los pasos definidos en la sección [Prueba de la API web](#test-the-web-api). Observe la diferencia en los nombres de propiedad JSON.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este tutorial se crea una API web que realiza operaciones de creación, lectura, actualización y eliminación (CRUD) en una base de datos NoSQL de [MongoDB](https://www.mongodb.com/what-is-mongodb).

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Configurar MongoDB
> * Crear una base de datos de MongoDB
> * Definir un esquema y una colección de MongoDB
> * Realizar operaciones de CRUD de MongoDB desde una API web
> * Personalizar la serialización de JSON

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [SDK de .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [SDK de .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* [SDK de .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio para Mac, versión 7.7 o posterior](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

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

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vaya a **Archivo** > **Nuevo** > **Proyecto**.
1. Seleccione el tipo de proyecto **Aplicación web de ASP.NET Core** y, luego, **Siguiente**.
1. Denomine el proyecto *BooksApi* y seleccione **Crear**.
1. Seleccione el marco de destino **.NET Core** y **ASP.NET Core 2.2**. Seleccione la plantilla de proyecto **API** y, luego, **Crear**.
1. Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB. En la ventana **Consola del Administrador de paquetes**, desplácese hasta la raíz del proyecto. Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Ejecute los siguientes comandos en un shell de comandos:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Se genera un nuevo proyecto de API web de ASP.NET Core destinado a .NET Core, que puede abrir en Visual Studio Code.

1. Cuando el icono de llama de OmniSharp de la barra de estado se ponga verde, aparecerá un cuadro de diálogo en el que se le indicará que **faltan los activos necesarios para compilar y depurar en "RazonPagesMovie" y, luego, si quiere agregarlos**. Seleccione **Sí**.
1. Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB. Abra **Terminal integrado** y navegue hasta la raíz del proyecto. Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **Archivo** > **Nueva solución** >  **.NET Core** > **Aplicación** en la barra lateral. En la versión 8.6 o posteriores, seleccione **Archivo** > **Nueva solución** > **Web y consola** > **Aplicación** en la barra lateral.
1. Seleccione la plantilla de proyecto de C# **API web ASP.NET Core** y, luego, **Siguiente**.
1. Seleccione **.NET Core 2.2** en la lista desplegable **Plataforma de destino** y, luego, **Siguiente**.
1. Escriba *BooksApi* en **Nombre del proyecto** y seleccione **Crear**.
1. En el panel **Explorador de soluciones**, haga clic con el botón derecho en el nodo **Dependencias** del proyecto y seleccione **Agregar paquetes**.
1. Escriba *MongoDB.Driver* en el cuadro de búsqueda, seleccione el paquete *MongoDB.Driver* y, luego, **Agregar paquete**.
1. Seleccione el botón **Aceptar** del cuadro de diálogo **Aceptación de la licencia**.

---

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

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. Agregue un archivo *BookstoreDatabaseSettings.cs* al directorio *Models* con el código siguiente:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   La clase anterior `BookstoreDatabaseSettings` se utiliza para almacenar los valores de propiedad `BookstoreDatabaseSettings` del archivo *appsettings.json*. Los nombres de las propiedades de JSON y C# son iguales para facilitar el proceso de asignación.

1. Agregue el código resaltado siguiente a `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   En el código anterior:

   * La instancia de configuración a la que la sección `BookstoreDatabaseSettings` del archivo *appsettings.json* enlaza está registrada en el contenedor de inserción de dependencias (DI). Por ejemplo, una propiedad `ConnectionString` del objeto `BookstoreDatabaseSettings` se rellena con la propiedad `BookstoreDatabaseSettings:ConnectionString` en *appsettings.json*.
   * La interfaz `IBookstoreDatabaseSettings` se registra en la inserción de dependencias con una [duración de servicio](xref:fundamentals/dependency-injection#service-lifetimes) de tipo singleton. Cuando se inserta, la instancia de la interfaz se resuelve en un objeto `BookstoreDatabaseSettings`.

1. Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver las referencias a `BookstoreDatabaseSettings` y `IBookstoreDatabaseSettings`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Adición de un servicio de operaciones CRUD

1. Agregue un directorio *Servicios* a la raíz del proyecto.
1. Agregue una clase `BookService` al directorio *Servicios* con el código siguiente:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   En el código anterior, se recuperó una instancia de `IBookstoreDatabaseSettings` de la inserción de dependencias mediante la inserción de un constructor. Esta técnica proporciona acceso a los valores de configuración de *appsettings.json* que se agregaron en la sección [Adición de un modelo de configuración](#add-a-configuration-model).

1. Agregue el código resaltado siguiente a `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   En el código anterior, la clase `BookService` se registra con inserción de dependencias para admitir la inserción del constructor en las clases de consumo. La duración de servicio de tipo singleton es la más adecuada porque `BookService` toma una dependencia directa sobre `MongoClient`. Según las [instrucciones oficiales de reutilización de cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` debe registrarse en la inserción de dependencias con una duración de servicio de tipo singleton.

1. Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver la referencia a `BookService`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

La clase`BookService` usa los miembros `MongoDB.Driver` siguientes para realizar operaciones CRUD en la base de datos:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lee la instancia del servidor para realizar operaciones de base de datos. Se proporciona la cadena de conexión de MongoDB al constructor de esta clase:

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

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

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

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

1. En `Startup.ConfigureServices`, cambie el código resaltado siguiente en la llamada al método `AddMvc`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Con el cambio anterior, los nombres de propiedad de la respuesta JSON serializada de la API web coinciden con sus nombres de propiedad correspondientes en el tipo de objeto CLR. Por ejemplo, la propiedad `Author` de la clase `Book` se serializa como `Author`.

1. En *Models/Book.cs*, anote la propiedad `BookName` con el atributo [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) siguiente:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   El valor `Name` del atributo `[JsonProperty]` representa el nombre de propiedad en la respuesta JSON serializada de la API web.

1. Agregue el código siguiente en la parte superior del archivo *Models/Book.cs* para resolver la referencia al atributo `[JsonProperty]`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Repita los pasos definidos en la sección [Prueba de la API web](#test-the-web-api). Observe la diferencia en los nombres de propiedad JSON.

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>Agregar compatibilidad con la autenticación a una API web

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la creación de las API web de ASP.NET Core, consulte los siguientes recursos:

* [Versión de YouTube de este artículo](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
