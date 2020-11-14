---
title: Creación de una API Web con ASP.NET Core y MongoDB
author: prkhandelwal
description: En este tutorial se muestra cómo crear una API web de ASP.NET Core con una base de datos NoSQL de MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mongo-app
ms.openlocfilehash: 350df417886fe1ea5fef89dc221c217d596768b3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060747"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="76a77-103">Creación de una API Web con ASP.NET Core y MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="76a77-104">Por [Pratik Khandelwal](https://twitter.com/K2Prk) y [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="76a77-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="76a77-105">En este tutorial se crea una API web que realiza operaciones de creación, lectura, actualización y eliminación (CRUD) en una base de datos NoSQL de [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="76a77-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="76a77-106">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="76a77-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="76a77-107">Configurar MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-107">Configure MongoDB</span></span>
> * <span data-ttu-id="76a77-108">Crear una base de datos de MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="76a77-109">Definir un esquema y una colección de MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="76a77-110">Realizar operaciones de CRUD de MongoDB desde una API web</span><span class="sxs-lookup"><span data-stu-id="76a77-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="76a77-111">Personalizar la serialización de JSON</span><span class="sxs-lookup"><span data-stu-id="76a77-111">Customize JSON serialization</span></span>

<span data-ttu-id="76a77-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76a77-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="76a77-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="76a77-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76a77-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76a77-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="76a77-115">.NET Core SDK 3.0 o posterior</span><span class="sxs-lookup"><span data-stu-id="76a77-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="76a77-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="76a77-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="76a77-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="76a77-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76a77-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="76a77-119">.NET Core SDK 3.0 o posterior</span><span class="sxs-lookup"><span data-stu-id="76a77-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="76a77-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76a77-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="76a77-121">C# para Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76a77-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="76a77-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76a77-123">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="76a77-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="76a77-124">.NET Core SDK 3.0 o posterior</span><span class="sxs-lookup"><span data-stu-id="76a77-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="76a77-125">Visual Studio para Mac, versión 7.7 o posterior</span><span class="sxs-lookup"><span data-stu-id="76a77-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="76a77-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="76a77-127">Configurar MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-127">Configure MongoDB</span></span>

<span data-ttu-id="76a77-128">Si usa Windows, MongoDB está instalado en *C:\\Archivos de programa\\MongoDB* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="76a77-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="76a77-129">Agregue *C:\\Archivos de programa\\MongoDB\\Server\\\<version_number>\\bin* a la variable de entorno `Path`.</span><span class="sxs-lookup"><span data-stu-id="76a77-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="76a77-130">Este cambio permite el acceso a MongoDB desde cualquier lugar en el equipo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="76a77-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="76a77-131">Use el Shell de mongo en los pasos siguientes para crear una base de datos, hacer colecciones y almacenar documentos.</span><span class="sxs-lookup"><span data-stu-id="76a77-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="76a77-132">Para obtener más información sobre los comandos de Shell de mongo, consulte [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell) (Trabajo con el shell de Mongo).</span><span class="sxs-lookup"><span data-stu-id="76a77-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="76a77-133">Elija un directorio en el equipo de desarrollo para almacenar los datos.</span><span class="sxs-lookup"><span data-stu-id="76a77-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="76a77-134">Por ejemplo, *C:\\BooksData* en Windows.</span><span class="sxs-lookup"><span data-stu-id="76a77-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="76a77-135">Si no existe el directorio, créelo.</span><span class="sxs-lookup"><span data-stu-id="76a77-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="76a77-136">El shell de mongo no crea nuevos directorios.</span><span class="sxs-lookup"><span data-stu-id="76a77-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="76a77-137">Abra un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="76a77-137">Open a command shell.</span></span> <span data-ttu-id="76a77-138">Ejecute el comando siguiente para conectarse a MongoDB en el puerto predeterminado 27017.</span><span class="sxs-lookup"><span data-stu-id="76a77-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="76a77-139">No olvide reemplazar `<data_directory_path>` por el directorio que eligió en el paso anterior.</span><span class="sxs-lookup"><span data-stu-id="76a77-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="76a77-140">Abra otra instancia del shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="76a77-140">Open another command shell instance.</span></span> <span data-ttu-id="76a77-141">Conéctese a la base de datos de prueba de forma predeterminada ejecutando el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="76a77-142">Ejecute lo siguiente en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="76a77-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="76a77-143">Si aún no existe, se crea una base de datos denominada *BookstoreDb*.</span><span class="sxs-lookup"><span data-stu-id="76a77-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="76a77-144">Si la base de datos existe, su conexión se abre para las transacciones.</span><span class="sxs-lookup"><span data-stu-id="76a77-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="76a77-145">Cree una colección `Books` con el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="76a77-146">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="76a77-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="76a77-147">Defina un esquema para la colección `Books` e inserte dos documentos con el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="76a77-148">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="76a77-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="76a77-149">Los identificadores que se muestran en este artículo no coinciden con los que se mostrarán cuando ejecute este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="76a77-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="76a77-150">Vea los documentos en la base de datos mediante el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="76a77-151">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="76a77-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="76a77-152">El esquema agrega una propiedad `_id` generada automáticamente del tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="76a77-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="76a77-153">La base de datos está lista.</span><span class="sxs-lookup"><span data-stu-id="76a77-153">The database is ready.</span></span> <span data-ttu-id="76a77-154">Puede empezar a crear la API web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="76a77-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="76a77-155">Creación de un proyecto de API web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="76a77-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76a77-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76a77-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="76a77-157">Vaya a **Archivo** > **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="76a77-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="76a77-158">Seleccione el tipo de proyecto **Aplicación web de ASP.NET Core** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="76a77-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="76a77-159">Denomine el proyecto *BooksApi* y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="76a77-159">Name the project *BooksApi* , and select **Create**.</span></span>
1. <span data-ttu-id="76a77-160">Seleccione **.NET Core** como plataforma de destino y **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="76a77-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="76a77-161">Seleccione la plantilla de proyecto **API** y, luego, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="76a77-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="76a77-162">Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="76a77-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="76a77-163">En la ventana **Consola del Administrador de paquetes** , desplácese hasta la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="76a77-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="76a77-164">Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="76a77-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="76a77-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76a77-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="76a77-166">Ejecute los siguientes comandos en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="76a77-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="76a77-167">Se genera un nuevo proyecto de API web de ASP.NET Core destinado a .NET Core, que puede abrir en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="76a77-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="76a77-168">Cuando el icono de llama de OmniSharp de la barra de estado se ponga verde, aparecerá un cuadro de diálogo en el que se le indicará que **faltan los activos necesarios para compilar y depurar en "RazonPagesMovie" y, luego, si quiere agregarlos**.</span><span class="sxs-lookup"><span data-stu-id="76a77-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="76a77-169">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="76a77-169">Select **Yes**.</span></span>
1. <span data-ttu-id="76a77-170">Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="76a77-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="76a77-171">Abra **Terminal integrado** y navegue hasta la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="76a77-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="76a77-172">Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="76a77-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76a77-173">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="76a77-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="76a77-174">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **Archivo** > **Nueva solución** >  **.NET Core** > **Aplicación** en la barra lateral.</span><span class="sxs-lookup"><span data-stu-id="76a77-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="76a77-175">En la versión 8.6 o posteriores, seleccione **Archivo** > **Nueva solución** > **Web y consola** > **Aplicación** en la barra lateral.</span><span class="sxs-lookup"><span data-stu-id="76a77-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="76a77-176">Seleccione la plantilla de proyecto de C# **ASP.NET Core** > **API** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="76a77-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="76a77-177">Seleccione **.NET Core 3.1** en la lista desplegable **Plataforma de destino** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="76a77-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="76a77-178">Escriba *BooksApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="76a77-178">Enter *BooksApi* for the **Project Name** , and select **Create**.</span></span>
1. <span data-ttu-id="76a77-179">En el panel **Explorador de soluciones** , haga clic con el botón derecho en el nodo **Dependencias** del proyecto y seleccione **Agregar paquetes**.</span><span class="sxs-lookup"><span data-stu-id="76a77-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="76a77-180">Escriba *MongoDB.Driver* en el cuadro de búsqueda, seleccione el paquete *MongoDB.Driver* y, luego, **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="76a77-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="76a77-181">Seleccione el botón **Aceptar** del cuadro de diálogo **Aceptación de la licencia**.</span><span class="sxs-lookup"><span data-stu-id="76a77-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="76a77-182">Adición de un modelo de entidad</span><span class="sxs-lookup"><span data-stu-id="76a77-182">Add an entity model</span></span>

1. <span data-ttu-id="76a77-183">Agregue un directorio *Modelos* a la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="76a77-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="76a77-184">Agregue una clase `Book` al directorio *Modelos* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="76a77-185">En la clase anterior, se requiere la propiedad `Id`</span><span class="sxs-lookup"><span data-stu-id="76a77-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="76a77-186">para asignar el objeto de Common Language Runtime (CLR) a la colección de MongoDB.</span><span class="sxs-lookup"><span data-stu-id="76a77-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="76a77-187">para anotarla con [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) a fin de designarla como clave principal del documento.</span><span class="sxs-lookup"><span data-stu-id="76a77-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="76a77-188">para anotarla con [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) a fin de permitir que se pase el parámetro como tipo `string` en lugar de una estructura [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm).</span><span class="sxs-lookup"><span data-stu-id="76a77-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="76a77-189">Mongo controla la conversión de `string` a `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="76a77-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="76a77-190">La propiedad `BookName` se anota con el atributo [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm).</span><span class="sxs-lookup"><span data-stu-id="76a77-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="76a77-191">El valor `Name` del atributo representa el nombre de propiedad en la colección de MongoDB.</span><span class="sxs-lookup"><span data-stu-id="76a77-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="76a77-192">Adición de un modelo configuración</span><span class="sxs-lookup"><span data-stu-id="76a77-192">Add a configuration model</span></span>

1. <span data-ttu-id="76a77-193">Agregue los siguientes valores de configuración de base de datos a *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="76a77-193">Add the following database configuration values to *appsettings.json* :</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="76a77-194">Agregue un archivo *BookstoreDatabaseSettings.cs* al directorio *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="76a77-195">La clase anterior `BookstoreDatabaseSettings` se utiliza para almacenar los valores de propiedad `BookstoreDatabaseSettings` del archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="76a77-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="76a77-196">Los nombres de las propiedades de JSON y C# son iguales para facilitar el proceso de asignación.</span><span class="sxs-lookup"><span data-stu-id="76a77-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="76a77-197">Agregue el código resaltado siguiente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="76a77-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="76a77-198">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="76a77-198">In the preceding code:</span></span>

   * <span data-ttu-id="76a77-199">La instancia de configuración a la que la sección `BookstoreDatabaseSettings` del archivo *appsettings.json* enlaza está registrada en el contenedor de inserción de dependencias (DI).</span><span class="sxs-lookup"><span data-stu-id="76a77-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="76a77-200">Por ejemplo, una propiedad `ConnectionString` del objeto `BookstoreDatabaseSettings` se rellena con la propiedad `BookstoreDatabaseSettings:ConnectionString` en *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="76a77-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="76a77-201">La interfaz `IBookstoreDatabaseSettings` se registra en la inserción de dependencias con una [duración de servicio](xref:fundamentals/dependency-injection#service-lifetimes) de tipo singleton.</span><span class="sxs-lookup"><span data-stu-id="76a77-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="76a77-202">Cuando se inserta, la instancia de la interfaz se resuelve en un objeto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="76a77-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="76a77-203">Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver las referencias a `BookstoreDatabaseSettings` y `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="76a77-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="76a77-204">Adición de un servicio de operaciones CRUD</span><span class="sxs-lookup"><span data-stu-id="76a77-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="76a77-205">Agregue un directorio *Servicios* a la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="76a77-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="76a77-206">Agregue una clase `BookService` al directorio *Servicios* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="76a77-207">En el código anterior, se recuperó una instancia de `IBookstoreDatabaseSettings` de la inserción de dependencias mediante la inserción de un constructor.</span><span class="sxs-lookup"><span data-stu-id="76a77-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="76a77-208">Esta técnica proporciona acceso a los valores de configuración de *appsettings.json* que se agregaron en la sección [Adición de un modelo de configuración](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="76a77-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="76a77-209">Agregue el código resaltado siguiente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="76a77-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="76a77-210">En el código anterior, la clase `BookService` se registra con inserción de dependencias para admitir la inserción del constructor en las clases de consumo.</span><span class="sxs-lookup"><span data-stu-id="76a77-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="76a77-211">La duración de servicio de tipo singleton es la más adecuada porque `BookService` toma una dependencia directa sobre `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="76a77-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="76a77-212">Según las [instrucciones oficiales de reutilización de cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` debe registrarse en la inserción de dependencias con una duración de servicio de tipo singleton.</span><span class="sxs-lookup"><span data-stu-id="76a77-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="76a77-213">Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver la referencia a `BookService`:</span><span class="sxs-lookup"><span data-stu-id="76a77-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="76a77-214">La clase`BookService` usa los miembros `MongoDB.Driver` siguientes para realizar operaciones CRUD en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="76a77-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="76a77-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lee la instancia del servidor para realizar operaciones de base de datos.</span><span class="sxs-lookup"><span data-stu-id="76a77-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="76a77-216">Se proporciona la cadena de conexión de MongoDB al constructor de esta clase:</span><span class="sxs-lookup"><span data-stu-id="76a77-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="76a77-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): representa la base de datos de Mongo para realizar operaciones.</span><span class="sxs-lookup"><span data-stu-id="76a77-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="76a77-218">Este tutorial usa el método genérico [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) en la interfaz para tener acceso a los datos de una colección específica.</span><span class="sxs-lookup"><span data-stu-id="76a77-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="76a77-219">Realice las operaciones CRUD en la colección después de llamar a este método.</span><span class="sxs-lookup"><span data-stu-id="76a77-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="76a77-220">En la llamada al método `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="76a77-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="76a77-221">`collection` representa el nombre de la colección.</span><span class="sxs-lookup"><span data-stu-id="76a77-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="76a77-222">`TDocument` representa el tipo de objeto CLR almacenado en la colección.</span><span class="sxs-lookup"><span data-stu-id="76a77-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="76a77-223">`GetCollection<TDocument>(collection)` devuelve un objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa la colección.</span><span class="sxs-lookup"><span data-stu-id="76a77-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="76a77-224">En este tutorial, se invocan los métodos siguientes en la colección:</span><span class="sxs-lookup"><span data-stu-id="76a77-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="76a77-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): elimina un único documento que cumple los criterios de búsqueda proporcionados.</span><span class="sxs-lookup"><span data-stu-id="76a77-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="76a77-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): devuelve todos los documentos de la colección que cumplen los criterios de búsqueda indicados.</span><span class="sxs-lookup"><span data-stu-id="76a77-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="76a77-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): inserta el objeto proporcionado como un nuevo documento en la colección.</span><span class="sxs-lookup"><span data-stu-id="76a77-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="76a77-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): reemplaza un único documento que cumple los criterios de búsqueda indicados por el objeto proporcionado.</span><span class="sxs-lookup"><span data-stu-id="76a77-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="76a77-229">Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="76a77-229">Add a controller</span></span>

<span data-ttu-id="76a77-230">Agregue una clase `BooksController` al directorio *Controladores* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="76a77-231">El controlador de API web anterior:</span><span class="sxs-lookup"><span data-stu-id="76a77-231">The preceding web API controller:</span></span>

* <span data-ttu-id="76a77-232">Usa la clase `BookService` para realizar operaciones CRUD.</span><span class="sxs-lookup"><span data-stu-id="76a77-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="76a77-233">Contiene métodos de acción para admitir las solicitudes GET, POST, PUT y DELETE de HTTP.</span><span class="sxs-lookup"><span data-stu-id="76a77-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="76a77-234">Llama a <xref:System.Web.Http.ApiController.CreatedAtRoute*> en el método de acción `Create` para devolver una respuesta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="76a77-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="76a77-235">El código de estado 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="76a77-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="76a77-236">`CreatedAtRoute` también agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="76a77-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="76a77-237">El encabezado `Location` especifica el identificador URI del libro recién creado.</span><span class="sxs-lookup"><span data-stu-id="76a77-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="76a77-238">Prueba de la API web</span><span class="sxs-lookup"><span data-stu-id="76a77-238">Test the web API</span></span>

1. <span data-ttu-id="76a77-239">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="76a77-239">Build and run the app.</span></span>

1. <span data-ttu-id="76a77-240">Vaya a `http://localhost:<port>/api/books` para probar el método de acción `Get` sin parámetros del controlador.</span><span class="sxs-lookup"><span data-stu-id="76a77-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="76a77-241">Se muestra la siguiente respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="76a77-241">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="76a77-242">Vaya a `http://localhost:<port>/api/books/{id here}` para probar el método de acción `Get` sobrecargado del controlador.</span><span class="sxs-lookup"><span data-stu-id="76a77-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="76a77-243">Se muestra la siguiente respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="76a77-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="76a77-244">Configuración de las opciones de serialización de JSON</span><span class="sxs-lookup"><span data-stu-id="76a77-244">Configure JSON serialization options</span></span>

<span data-ttu-id="76a77-245">Hay dos detalles que cambiar sobre las respuestas JSON devueltas en la sección [Prueba de la API web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="76a77-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="76a77-246">Las mayúsculas y minúsculas Camel predeterminadas de los nombres de propiedad se deben cambiar para que coincidan con el uso de mayúsculas y minúsculas de Pascal de los nombres de propiedad del objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="76a77-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="76a77-247">La propiedad `bookName` se debe devolver como `Name`.</span><span class="sxs-lookup"><span data-stu-id="76a77-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="76a77-248">Para satisfacer los requisitos anteriores, realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="76a77-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="76a77-249">JSON.NET se ha quitado de la plataforma compartida de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="76a77-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="76a77-250">Agregue una referencia de paquete a [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="76a77-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="76a77-251">En `Startup.ConfigureServices`, cambie el código resaltado siguiente en la llamada al método `AddControllers`:</span><span class="sxs-lookup"><span data-stu-id="76a77-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="76a77-252">Con el cambio anterior, los nombres de propiedad de la respuesta JSON serializada de la API web coinciden con sus nombres de propiedad correspondientes en el tipo de objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="76a77-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="76a77-253">Por ejemplo, la propiedad `Author` de la clase `Book` se serializa como `Author`.</span><span class="sxs-lookup"><span data-stu-id="76a77-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="76a77-254">En *Models/Book.cs* , anote la propiedad `BookName` con el atributo [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-254">In *Models/Book.cs* , annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="76a77-255">El valor `Name` del atributo `[JsonProperty]` representa el nombre de propiedad en la respuesta JSON serializada de la API web.</span><span class="sxs-lookup"><span data-stu-id="76a77-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="76a77-256">Agregue el código siguiente en la parte superior del archivo *Models/Book.cs* para resolver la referencia al atributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="76a77-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="76a77-257">Repita los pasos definidos en la sección [Prueba de la API web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="76a77-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="76a77-258">Observe la diferencia en los nombres de propiedad JSON.</span><span class="sxs-lookup"><span data-stu-id="76a77-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="76a77-259">En este tutorial se crea una API web que realiza operaciones de creación, lectura, actualización y eliminación (CRUD) en una base de datos NoSQL de [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="76a77-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="76a77-260">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="76a77-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="76a77-261">Configurar MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-261">Configure MongoDB</span></span>
> * <span data-ttu-id="76a77-262">Crear una base de datos de MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="76a77-263">Definir un esquema y una colección de MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="76a77-264">Realizar operaciones de CRUD de MongoDB desde una API web</span><span class="sxs-lookup"><span data-stu-id="76a77-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="76a77-265">Personalizar la serialización de JSON</span><span class="sxs-lookup"><span data-stu-id="76a77-265">Customize JSON serialization</span></span>

<span data-ttu-id="76a77-266">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76a77-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="76a77-267">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="76a77-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76a77-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76a77-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="76a77-269">SDK de .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="76a77-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="76a77-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="76a77-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="76a77-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="76a77-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76a77-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="76a77-273">SDK de .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="76a77-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="76a77-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76a77-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="76a77-275">C# para Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76a77-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="76a77-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76a77-277">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="76a77-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="76a77-278">SDK de .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="76a77-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="76a77-279">Visual Studio para Mac, versión 7.7 o posterior</span><span class="sxs-lookup"><span data-stu-id="76a77-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="76a77-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="76a77-281">Configurar MongoDB</span><span class="sxs-lookup"><span data-stu-id="76a77-281">Configure MongoDB</span></span>

<span data-ttu-id="76a77-282">Si usa Windows, MongoDB está instalado en *C:\\Archivos de programa\\MongoDB* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="76a77-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="76a77-283">Agregue *C:\\Archivos de programa\\MongoDB\\Server\\\<version_number>\\bin* a la variable de entorno `Path`.</span><span class="sxs-lookup"><span data-stu-id="76a77-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="76a77-284">Este cambio permite el acceso a MongoDB desde cualquier lugar en el equipo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="76a77-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="76a77-285">Use el Shell de mongo en los pasos siguientes para crear una base de datos, hacer colecciones y almacenar documentos.</span><span class="sxs-lookup"><span data-stu-id="76a77-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="76a77-286">Para obtener más información sobre los comandos de Shell de mongo, consulte [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell) (Trabajo con el shell de Mongo).</span><span class="sxs-lookup"><span data-stu-id="76a77-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="76a77-287">Elija un directorio en el equipo de desarrollo para almacenar los datos.</span><span class="sxs-lookup"><span data-stu-id="76a77-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="76a77-288">Por ejemplo, *C:\\BooksData* en Windows.</span><span class="sxs-lookup"><span data-stu-id="76a77-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="76a77-289">Si no existe el directorio, créelo.</span><span class="sxs-lookup"><span data-stu-id="76a77-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="76a77-290">El shell de mongo no crea nuevos directorios.</span><span class="sxs-lookup"><span data-stu-id="76a77-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="76a77-291">Abra un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="76a77-291">Open a command shell.</span></span> <span data-ttu-id="76a77-292">Ejecute el comando siguiente para conectarse a MongoDB en el puerto predeterminado 27017.</span><span class="sxs-lookup"><span data-stu-id="76a77-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="76a77-293">No olvide reemplazar `<data_directory_path>` por el directorio que eligió en el paso anterior.</span><span class="sxs-lookup"><span data-stu-id="76a77-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="76a77-294">Abra otra instancia del shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="76a77-294">Open another command shell instance.</span></span> <span data-ttu-id="76a77-295">Conéctese a la base de datos de prueba de forma predeterminada ejecutando el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="76a77-296">Ejecute lo siguiente en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="76a77-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="76a77-297">Si aún no existe, se crea una base de datos denominada *BookstoreDb*.</span><span class="sxs-lookup"><span data-stu-id="76a77-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="76a77-298">Si la base de datos existe, su conexión se abre para las transacciones.</span><span class="sxs-lookup"><span data-stu-id="76a77-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="76a77-299">Cree una colección `Books` con el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="76a77-300">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="76a77-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="76a77-301">Defina un esquema para la colección `Books` e inserte dos documentos con el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="76a77-302">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="76a77-302">The following result is displayed:</span></span>

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
   > <span data-ttu-id="76a77-303">Los identificadores que se muestran en este artículo no coinciden con los que se mostrarán cuando ejecute este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="76a77-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="76a77-304">Vea los documentos en la base de datos mediante el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="76a77-305">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="76a77-305">The following result is displayed:</span></span>

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

   <span data-ttu-id="76a77-306">El esquema agrega una propiedad `_id` generada automáticamente del tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="76a77-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="76a77-307">La base de datos está lista.</span><span class="sxs-lookup"><span data-stu-id="76a77-307">The database is ready.</span></span> <span data-ttu-id="76a77-308">Puede empezar a crear la API web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="76a77-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="76a77-309">Creación de un proyecto de API web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="76a77-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76a77-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76a77-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="76a77-311">Vaya a **Archivo** > **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="76a77-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="76a77-312">Seleccione el tipo de proyecto **Aplicación web de ASP.NET Core** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="76a77-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="76a77-313">Denomine el proyecto *BooksApi* y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="76a77-313">Name the project *BooksApi* , and select **Create**.</span></span>
1. <span data-ttu-id="76a77-314">Seleccione el marco de destino **.NET Core** y **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="76a77-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="76a77-315">Seleccione la plantilla de proyecto **API** y, luego, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="76a77-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="76a77-316">Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="76a77-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="76a77-317">En la ventana **Consola del Administrador de paquetes** , desplácese hasta la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="76a77-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="76a77-318">Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="76a77-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="76a77-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="76a77-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="76a77-320">Ejecute los siguientes comandos en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="76a77-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="76a77-321">Se genera un nuevo proyecto de API web de ASP.NET Core destinado a .NET Core, que puede abrir en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="76a77-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="76a77-322">Cuando el icono de llama de OmniSharp de la barra de estado se ponga verde, aparecerá un cuadro de diálogo en el que se le indicará que **faltan los activos necesarios para compilar y depurar en "RazonPagesMovie" y, luego, si quiere agregarlos**.</span><span class="sxs-lookup"><span data-stu-id="76a77-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="76a77-323">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="76a77-323">Select **Yes**.</span></span>
1. <span data-ttu-id="76a77-324">Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="76a77-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="76a77-325">Abra **Terminal integrado** y navegue hasta la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="76a77-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="76a77-326">Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="76a77-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76a77-327">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="76a77-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="76a77-328">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **Archivo** > **Nueva solución** >  **.NET Core** > **Aplicación** en la barra lateral.</span><span class="sxs-lookup"><span data-stu-id="76a77-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="76a77-329">En la versión 8.6 o posteriores, seleccione **Archivo** > **Nueva solución** > **Web y consola** > **Aplicación** en la barra lateral.</span><span class="sxs-lookup"><span data-stu-id="76a77-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="76a77-330">Seleccione la plantilla de proyecto de C# **API web ASP.NET Core** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="76a77-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="76a77-331">Seleccione **.NET Core 2.2** en la lista desplegable **Plataforma de destino** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="76a77-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="76a77-332">Escriba *BooksApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="76a77-332">Enter *BooksApi* for the **Project Name** , and select **Create**.</span></span>
1. <span data-ttu-id="76a77-333">En el panel **Explorador de soluciones** , haga clic con el botón derecho en el nodo **Dependencias** del proyecto y seleccione **Agregar paquetes**.</span><span class="sxs-lookup"><span data-stu-id="76a77-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="76a77-334">Escriba *MongoDB.Driver* en el cuadro de búsqueda, seleccione el paquete *MongoDB.Driver* y, luego, **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="76a77-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="76a77-335">Seleccione el botón **Aceptar** del cuadro de diálogo **Aceptación de la licencia**.</span><span class="sxs-lookup"><span data-stu-id="76a77-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="76a77-336">Adición de un modelo de entidad</span><span class="sxs-lookup"><span data-stu-id="76a77-336">Add an entity model</span></span>

1. <span data-ttu-id="76a77-337">Agregue un directorio *Modelos* a la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="76a77-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="76a77-338">Agregue una clase `Book` al directorio *Modelos* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="76a77-339">En la clase anterior, se requiere la propiedad `Id`</span><span class="sxs-lookup"><span data-stu-id="76a77-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="76a77-340">para asignar el objeto de Common Language Runtime (CLR) a la colección de MongoDB.</span><span class="sxs-lookup"><span data-stu-id="76a77-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="76a77-341">para anotarla con [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) a fin de designarla como clave principal del documento.</span><span class="sxs-lookup"><span data-stu-id="76a77-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="76a77-342">para anotarla con [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) a fin de permitir que se pase el parámetro como tipo `string` en lugar de una estructura [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm).</span><span class="sxs-lookup"><span data-stu-id="76a77-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="76a77-343">Mongo controla la conversión de `string` a `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="76a77-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="76a77-344">La propiedad `BookName` se anota con el atributo [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm).</span><span class="sxs-lookup"><span data-stu-id="76a77-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="76a77-345">El valor `Name` del atributo representa el nombre de propiedad en la colección de MongoDB.</span><span class="sxs-lookup"><span data-stu-id="76a77-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="76a77-346">Adición de un modelo configuración</span><span class="sxs-lookup"><span data-stu-id="76a77-346">Add a configuration model</span></span>

1. <span data-ttu-id="76a77-347">Agregue los siguientes valores de configuración de base de datos a *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="76a77-347">Add the following database configuration values to *appsettings.json* :</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="76a77-348">Agregue un archivo *BookstoreDatabaseSettings.cs* al directorio *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="76a77-349">La clase anterior `BookstoreDatabaseSettings` se utiliza para almacenar los valores de propiedad `BookstoreDatabaseSettings` del archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="76a77-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="76a77-350">Los nombres de las propiedades de JSON y C# son iguales para facilitar el proceso de asignación.</span><span class="sxs-lookup"><span data-stu-id="76a77-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="76a77-351">Agregue el código resaltado siguiente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="76a77-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="76a77-352">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="76a77-352">In the preceding code:</span></span>

   * <span data-ttu-id="76a77-353">La instancia de configuración a la que la sección `BookstoreDatabaseSettings` del archivo *appsettings.json* enlaza está registrada en el contenedor de inserción de dependencias (DI).</span><span class="sxs-lookup"><span data-stu-id="76a77-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="76a77-354">Por ejemplo, una propiedad `ConnectionString` del objeto `BookstoreDatabaseSettings` se rellena con la propiedad `BookstoreDatabaseSettings:ConnectionString` en *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="76a77-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="76a77-355">La interfaz `IBookstoreDatabaseSettings` se registra en la inserción de dependencias con una [duración de servicio](xref:fundamentals/dependency-injection#service-lifetimes) de tipo singleton.</span><span class="sxs-lookup"><span data-stu-id="76a77-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="76a77-356">Cuando se inserta, la instancia de la interfaz se resuelve en un objeto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="76a77-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="76a77-357">Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver las referencias a `BookstoreDatabaseSettings` y `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="76a77-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="76a77-358">Adición de un servicio de operaciones CRUD</span><span class="sxs-lookup"><span data-stu-id="76a77-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="76a77-359">Agregue un directorio *Servicios* a la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="76a77-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="76a77-360">Agregue una clase `BookService` al directorio *Servicios* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="76a77-361">En el código anterior, se recuperó una instancia de `IBookstoreDatabaseSettings` de la inserción de dependencias mediante la inserción de un constructor.</span><span class="sxs-lookup"><span data-stu-id="76a77-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="76a77-362">Esta técnica proporciona acceso a los valores de configuración de *appsettings.json* que se agregaron en la sección [Adición de un modelo de configuración](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="76a77-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="76a77-363">Agregue el código resaltado siguiente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="76a77-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="76a77-364">En el código anterior, la clase `BookService` se registra con inserción de dependencias para admitir la inserción del constructor en las clases de consumo.</span><span class="sxs-lookup"><span data-stu-id="76a77-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="76a77-365">La duración de servicio de tipo singleton es la más adecuada porque `BookService` toma una dependencia directa sobre `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="76a77-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="76a77-366">Según las [instrucciones oficiales de reutilización de cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` debe registrarse en la inserción de dependencias con una duración de servicio de tipo singleton.</span><span class="sxs-lookup"><span data-stu-id="76a77-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="76a77-367">Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver la referencia a `BookService`:</span><span class="sxs-lookup"><span data-stu-id="76a77-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="76a77-368">La clase`BookService` usa los miembros `MongoDB.Driver` siguientes para realizar operaciones CRUD en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="76a77-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="76a77-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lee la instancia del servidor para realizar operaciones de base de datos.</span><span class="sxs-lookup"><span data-stu-id="76a77-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="76a77-370">Se proporciona la cadena de conexión de MongoDB al constructor de esta clase:</span><span class="sxs-lookup"><span data-stu-id="76a77-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="76a77-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): representa la base de datos de Mongo para realizar operaciones.</span><span class="sxs-lookup"><span data-stu-id="76a77-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="76a77-372">Este tutorial usa el método genérico [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) en la interfaz para tener acceso a los datos de una colección específica.</span><span class="sxs-lookup"><span data-stu-id="76a77-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="76a77-373">Realice las operaciones CRUD en la colección después de llamar a este método.</span><span class="sxs-lookup"><span data-stu-id="76a77-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="76a77-374">En la llamada al método `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="76a77-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="76a77-375">`collection` representa el nombre de la colección.</span><span class="sxs-lookup"><span data-stu-id="76a77-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="76a77-376">`TDocument` representa el tipo de objeto CLR almacenado en la colección.</span><span class="sxs-lookup"><span data-stu-id="76a77-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="76a77-377">`GetCollection<TDocument>(collection)` devuelve un objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa la colección.</span><span class="sxs-lookup"><span data-stu-id="76a77-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="76a77-378">En este tutorial, se invocan los métodos siguientes en la colección:</span><span class="sxs-lookup"><span data-stu-id="76a77-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="76a77-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): elimina un único documento que cumple los criterios de búsqueda proporcionados.</span><span class="sxs-lookup"><span data-stu-id="76a77-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="76a77-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): devuelve todos los documentos de la colección que cumplen los criterios de búsqueda indicados.</span><span class="sxs-lookup"><span data-stu-id="76a77-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="76a77-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): inserta el objeto proporcionado como un nuevo documento en la colección.</span><span class="sxs-lookup"><span data-stu-id="76a77-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="76a77-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): reemplaza un único documento que cumple los criterios de búsqueda indicados por el objeto proporcionado.</span><span class="sxs-lookup"><span data-stu-id="76a77-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="76a77-383">Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="76a77-383">Add a controller</span></span>

<span data-ttu-id="76a77-384">Agregue una clase `BooksController` al directorio *Controladores* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="76a77-385">El controlador de API web anterior:</span><span class="sxs-lookup"><span data-stu-id="76a77-385">The preceding web API controller:</span></span>

* <span data-ttu-id="76a77-386">Usa la clase `BookService` para realizar operaciones CRUD.</span><span class="sxs-lookup"><span data-stu-id="76a77-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="76a77-387">Contiene métodos de acción para admitir las solicitudes GET, POST, PUT y DELETE de HTTP.</span><span class="sxs-lookup"><span data-stu-id="76a77-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="76a77-388">Llama a <xref:System.Web.Http.ApiController.CreatedAtRoute*> en el método de acción `Create` para devolver una respuesta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="76a77-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="76a77-389">El código de estado 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="76a77-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="76a77-390">`CreatedAtRoute` también agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="76a77-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="76a77-391">El encabezado `Location` especifica el identificador URI del libro recién creado.</span><span class="sxs-lookup"><span data-stu-id="76a77-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="76a77-392">Prueba de la API web</span><span class="sxs-lookup"><span data-stu-id="76a77-392">Test the web API</span></span>

1. <span data-ttu-id="76a77-393">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="76a77-393">Build and run the app.</span></span>

1. <span data-ttu-id="76a77-394">Vaya a `http://localhost:<port>/api/books` para probar el método de acción `Get` sin parámetros del controlador.</span><span class="sxs-lookup"><span data-stu-id="76a77-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="76a77-395">Se muestra la siguiente respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="76a77-395">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="76a77-396">Vaya a `http://localhost:<port>/api/books/{id here}` para probar el método de acción `Get` sobrecargado del controlador.</span><span class="sxs-lookup"><span data-stu-id="76a77-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="76a77-397">Se muestra la siguiente respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="76a77-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="76a77-398">Configuración de las opciones de serialización de JSON</span><span class="sxs-lookup"><span data-stu-id="76a77-398">Configure JSON serialization options</span></span>

<span data-ttu-id="76a77-399">Hay dos detalles que cambiar sobre las respuestas JSON devueltas en la sección [Prueba de la API web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="76a77-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="76a77-400">Las mayúsculas y minúsculas Camel predeterminadas de los nombres de propiedad se deben cambiar para que coincidan con el uso de mayúsculas y minúsculas de Pascal de los nombres de propiedad del objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="76a77-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="76a77-401">La propiedad `bookName` se debe devolver como `Name`.</span><span class="sxs-lookup"><span data-stu-id="76a77-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="76a77-402">Para satisfacer los requisitos anteriores, realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="76a77-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="76a77-403">En `Startup.ConfigureServices`, cambie el código resaltado siguiente en la llamada al método `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="76a77-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="76a77-404">Con el cambio anterior, los nombres de propiedad de la respuesta JSON serializada de la API web coinciden con sus nombres de propiedad correspondientes en el tipo de objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="76a77-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="76a77-405">Por ejemplo, la propiedad `Author` de la clase `Book` se serializa como `Author`.</span><span class="sxs-lookup"><span data-stu-id="76a77-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="76a77-406">En *Models/Book.cs* , anote la propiedad `BookName` con el atributo [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a77-406">In *Models/Book.cs* , annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="76a77-407">El valor `Name` del atributo `[JsonProperty]` representa el nombre de propiedad en la respuesta JSON serializada de la API web.</span><span class="sxs-lookup"><span data-stu-id="76a77-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="76a77-408">Agregue el código siguiente en la parte superior del archivo *Models/Book.cs* para resolver la referencia al atributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="76a77-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="76a77-409">Repita los pasos definidos en la sección [Prueba de la API web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="76a77-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="76a77-410">Observe la diferencia en los nombres de propiedad JSON.</span><span class="sxs-lookup"><span data-stu-id="76a77-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="76a77-411">Agregar compatibilidad con la autenticación a una API web</span><span class="sxs-lookup"><span data-stu-id="76a77-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="76a77-412">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="76a77-412">Next steps</span></span>

<span data-ttu-id="76a77-413">Para obtener más información sobre la creación de las API web de ASP.NET Core, consulte los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="76a77-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="76a77-414">Versión de YouTube de este artículo</span><span class="sxs-lookup"><span data-stu-id="76a77-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
