---
title: Parte 4. Adición de un modelo a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 4 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.date: 01/13/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422750"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a>Parte 4. Adición de un modelo a una aplicación de ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Tom Dykstra](https://github.com/tdykstra)

En esta sección, agregará las clases para administrar películas en una base de datos. Estas clases serán el elemento " **M** odel" de la aplicación **M** VC.

Estas clases se usan con [Entity Framework Core](/ef/core) (EF Core) para trabajar con una base de datos. EF Core es un marco de trabajo de asignación relacional de objetos (ORM) que simplifica el código de acceso de datos que se debe escribir.

Las clases de modelo que se crean se conocen como clases POCO (del inglés " **p** lain **O** ld **C** LR **O** bjects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core. Simplemente definen las propiedades de los datos que se almacenan en la base de datos.

En este tutorial, se escriben primero las clases del modelo y EF Core crea la base de datos.

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a>Agregar una clase de modelo de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**. Asigne el nombre *Movie.cs* al archivo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Agregue un archivo denominado *Movie.cs* a la carpeta *Models*.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Nueva clase** > **Clase vacía**. Asigne el nombre *Movie.cs* al archivo.

---

Actualice el archivo *Movie.cs* con el código siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

La clase `Movie` contiene un campo `Id`, que la base de datos requiere para la clave principal.

El atributo <xref:System.ComponentModel.DataAnnotations.DataType> en `ReleaseDate` especifica el tipo de los datos (`Date`). Con este atributo:

* El usuario no tiene que especificar información horaria en el campo de fecha.
* Solo se muestra la fecha, no información horaria.

Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.

## <a name="add-nuget-packages"></a>Adición de paquetes NuGet

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).

![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

En la Consola del administrador de paquetes, ejecute el comando siguiente:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

El comando anterior agrega el proveedor de SQL Server de EF Core. El paquete de proveedor instala el paquete de EF Core como una dependencia. Los paquetes adicionales se instalan de forma automática en el paso de scaffolding más adelante en el tutorial.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

En el menú **Proyecto** , seleccione **Administrar paquetes NuGet**.

En el campo de **búsqueda** de la esquina superior derecha, escriba `Microsoft.EntityFrameworkCore.SQLite` y presione la tecla **Entrar** para realizar la búsqueda. Seleccione el paquete NuGet correspondiente y presione el botón **Agregar paquete**.

![Adición del paquete NuGet de Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

Se mostrará el cuadro de diálogo **Seleccionar los proyectos** , con el proyecto `MvcMovie` seleccionado. Presione el botón **Aceptar**.

Se mostrará un cuadro de diálogo **Aceptación de licencia**. Revise las licencias como quiera y, a continuación, haga clic en el botón **Aceptar**.

Repita los pasos anteriores para instalar los paquetes NuGet siguientes:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Creación de una clase de contexto de base de datos

Se necesita una clase de contexto de base de datos para coordinar la funcionalidad de EF Core (crear, leer, actualizar, eliminar) para el modelo `Movie`. El contexto de base de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) y especifica las entidades que se van a incluir en el modelo de datos.

Cree una carpeta *Data*.

Agregue un archivo *Data/MvcMovieContext.cs* con el código siguiente: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos. Una entidad se corresponde con una fila de la tabla.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Registro del contexto de base de datos

ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection). Los servicios (como el contexto de base de datos de EF Core) se deben registrar con la inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor. El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial. En esta sección, se registra el contexto de base de datos con el contenedor de inserción de dependencias.

Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs* :

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Agregue el código resaltado siguiente en `Startup.ConfigureServices`:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Agregar una cadena de conexión de base de datos

Agregue una cadena de conexión al *appsettings.json* :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Compile el proyecto para comprobar si hay errores del compilador.

## <a name="scaffold-movie-pages"></a>Scaffolding de las páginas de películas

Use la herramienta de scaffolding para crear páginas de creación, lectura, actualización y eliminación (CRUD) del modelo de película.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En el **Explorador de soluciones** , haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.

![vista del paso anterior](adding-model/_static/add_controller21.png)

En el cuadro de diálogo **Agregar scaffold** , seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

Rellene el cuadro de diálogo **Agregar controlador** :

* **Clase de modelo** : *Movie (MvcMovie.Models)*
* **Clase de contexto de datos** : *MvcMovieContext (MvcMovie.Data)*

![Adición de contexto de datos](adding-model/_static/dc5.png)

* **Vistas** : conserve el valor predeterminado de cada opción activada.
* **Nombre del controlador** : conserve el valor predeterminado *MoviesController*.
* Seleccione **Agregar**.

Visual Studio crea:

* Un controlador de películas ( *Controllers/MoviesController.cs* )
* Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice ( *Views/Movies/\*.cshtml* )

La creación automática de estos archivos se conoce como *scaffolding*.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).

* En Linux, exporte la ruta de acceso de la herramienta de scaffolding:

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Ejecute el siguiente comando:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).

* Ejecute el siguiente comando:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Todavía no se pueden usar las páginas con scaffolding porque la base de datos no existe. Si ejecuta la aplicación y hace clic en el vínculo **Movie App** , obtendrá un mensaje de error *Cannot open database* (No se puede abrir la base de datos) o *no such table: Movie* (no existe la tabla: Movie).

<a name="migration"></a>

## <a name="initial-migration"></a>Migración inicial

Use la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core para crear la base de datos. Las migraciones son un conjunto de herramientas que permiten crear y actualizar una base de datos para que coincida con el modelo de datos.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).

En PCM, escriba los siguientes comandos:

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*. El argumento `InitialCreate` es el nombre de la migración. Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración. Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos. El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`.

* `Update-Database`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior. El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs* , que crea la base de datos.

  El comando de actualización de la base de datos genera la advertencia siguiente: 

  > No type was specified for the decimal column "Price" on entity type "Movie" (No se ha especificado ningún tipo en la columna decimal "Price" en el tipo de entidad "Movie"). This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using "HasColumnType()" (Especifique de forma explícita el tipo de columna de SQL Server que pueda acomodar todos los valores mediante "HasColumnType()").

  Puede omitir dicha advertencia, ya que se corregirá en un tutorial posterior.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Ejecute los siguientes comandos CLI de .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*. El argumento `InitialCreate` es el nombre de la migración. Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración. Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos. El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs* ).

* `ef database update`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior. El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs* , que crea la base de datos.

---

### <a name="the-initialcreate-class"></a>La clase InitialCreate

Examine el archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

El método `Up` crea la tabla Movie y configura `Id` como la clave principal. El método `Down` revierte los cambios de esquema realizados por la migración `Up`.

<a name="test"></a>

## <a name="test-the-app"></a>Prueba de la aplicación

* Ejecute la aplicación y haga clic en el vínculo **Movie App**.

  Si obtiene una excepción similar a una de las siguientes:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Probablemente haya omitido el [paso de migraciones](#migration).

* Pruebe la página **Create**. Escriba y envíe los datos.

  > [!NOTE]
  > Es posible que no pueda escribir comas decimales en el campo `Price`. La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos. Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Pruebe las páginas **Edit** , **Details** y **Delete**.

## <a name="dependency-injection-in-the-controller"></a>Inserción de dependencias en el controlador

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador. El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador. El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Uso de SQLite para desarrollo y SQL Server para producción

Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo. En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio. `IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modelos fuertemente tipados y la palabra clave @model

Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`. El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.

MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista. Este enfoque fuertemente tipado permite comprobar el código en tiempo de compilación. En el mecanismo de scaffolding se ha usado este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas.

Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

El parámetro `id` suele pasarse como datos de ruta. Por ejemplo, `https://localhost:5001/movies/details/1` establece:

* El controlador en el controlador `movies` (el primer segmento de dirección URL).
* La acción en `details` (el segundo segmento de dirección URL).
* El identificador en 1 (el último segmento de dirección URL).

También puede pasar `id` con una cadena de consulta como se indica a continuación:

`https://localhost:5001/movies/details?id=1`

El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.

Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:

```csharp
return View(movie);
```

Examine el contenido del archivo *Views/Movies/Details.cshtml* :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

La instrucción `@model` de la parte superior del archivo de vista especifica el tipo de objeto que espera la vista. Cuando se ha creado el controlador de película, se ha incluido la siguiente instrucción `@model`:

```cshtml
@model MvcMovie.Models.Movie
```

Esta directiva `@model` permite el acceso a la película que el controlador ha pasado a la vista. El objeto `Model` está fuertemente tipado. Por ejemplo, en la vista *Details.cshtml* , el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado. Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.

Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies. Observe cómo el código crea un objeto `List` cuando llama al método `View`. El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Cuando se ha creado el controlador de películas, el scaffolding ha incluido la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml* :

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado. Por ejemplo, en la vista *Index.cshtml* , el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`. Entre otras ventajas, esto implica que se obtiene la comprobación del código en tiempo de compilación.

## <a name="additional-resources"></a>Recursos adicionales

* [Asistentes de etiquetas](xref:mvc/views/tag-helpers/intro)
* [Globalización y localización](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con SQL](working-with-sql.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a>Agregar una clase de modelo de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**. Asigne el nombre *Movie.cs* al archivo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Agregue un archivo denominado *Movie.cs* a la carpeta *Models*.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Nueva clase** > **Clase vacía**. Asigne el nombre *Movie.cs* al archivo.

---

Actualice el archivo *Movie.cs* con el código siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

La clase `Movie` contiene un campo `Id`, que la base de datos requiere para la clave principal.

El atributo <xref:System.ComponentModel.DataAnnotations.DataType> en `ReleaseDate` especifica el tipo de los datos (`Date`). Con este atributo:

* El usuario no tiene que especificar información horaria en el campo de fecha.
* Solo se muestra la fecha, no información horaria.

Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.

## <a name="add-nuget-packages"></a>Adición de paquetes NuGet

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).

![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

En la Consola del administrador de paquetes, ejecute el comando siguiente:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

El comando anterior agrega el proveedor de SQL Server de EF Core. El paquete de proveedor instala el paquete de EF Core como una dependencia. Los paquetes adicionales se instalan de forma automática en el paso de scaffolding más adelante en el tutorial.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

En el menú **Proyecto** , seleccione **Administrar paquetes NuGet**.

En el campo de **búsqueda** de la esquina superior derecha, escriba `Microsoft.EntityFrameworkCore.SQLite` y presione la tecla **Entrar** para realizar la búsqueda. Seleccione el paquete NuGet correspondiente y presione el botón **Agregar paquete**.

![Adición del paquete NuGet de Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

Se mostrará el cuadro de diálogo **Seleccionar los proyectos** , con el proyecto `MvcMovie` seleccionado. Presione el botón **Aceptar**.

Se mostrará un cuadro de diálogo **Aceptación de licencia**. Revise las licencias como quiera y, a continuación, haga clic en el botón **Aceptar**.

Repita los pasos anteriores para instalar los paquetes NuGet siguientes:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Creación de una clase de contexto de base de datos

Se necesita una clase de contexto de base de datos para coordinar la funcionalidad de EF Core (crear, leer, actualizar, eliminar) para el modelo `Movie`. El contexto de base de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) y especifica las entidades que se van a incluir en el modelo de datos.

Cree una carpeta *Data*.

Agregue un archivo *Data/MvcMovieContext.cs* con el código siguiente: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos. Una entidad se corresponde con una fila de la tabla.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Registro del contexto de base de datos

ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection). Los servicios (como el contexto de base de datos de EF Core) se deben registrar con la inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor. El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial. En esta sección, se registra el contexto de base de datos con el contenedor de inserción de dependencias.

Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs* :

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Agregue el código resaltado siguiente en `Startup.ConfigureServices`:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Agregar una cadena de conexión de base de datos

Agregue una cadena de conexión al *appsettings.json* :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Compile el proyecto para comprobar si hay errores del compilador.

## <a name="scaffold-movie-pages"></a>Scaffolding de las páginas de películas

Use la herramienta de scaffolding para crear páginas de creación, lectura, actualización y eliminación (CRUD) del modelo de película.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En el **Explorador de soluciones** , haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.

![vista del paso anterior](adding-model/_static/add_controller21.png)

En el cuadro de diálogo **Agregar scaffold** , seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

Rellene el cuadro de diálogo **Agregar controlador** :

* **Clase de modelo** : *Movie (MvcMovie.Models)*
* **Clase de contexto de datos** : *MvcMovieContext (MvcMovie.Data)*

![Adición de contexto de datos](adding-model/_static/dc3.png)

* **Vistas** : conserve el valor predeterminado de cada opción activada.
* **Nombre del controlador** : conserve el valor predeterminado *MoviesController*.
* Seleccione **Agregar**.

Visual Studio crea:

* Un controlador de películas ( *Controllers/MoviesController.cs* )
* Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice ( *Views/Movies/\*.cshtml* )

La creación automática de estos archivos se conoce como *scaffolding*.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).

* En Linux, exporte la ruta de acceso de la herramienta de scaffolding:

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Ejecute el siguiente comando:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).

* Ejecute el siguiente comando:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Todavía no se pueden usar las páginas con scaffolding porque la base de datos no existe. Si ejecuta la aplicación y hace clic en el vínculo **Movie App** , obtendrá un mensaje de error *Cannot open database* (No se puede abrir la base de datos) o *no such table: Movie* (no existe la tabla: Movie).

<a name="migration"></a>

## <a name="initial-migration"></a>Migración inicial

Use la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core para crear la base de datos. Las migraciones son un conjunto de herramientas que permiten crear y actualizar una base de datos para que coincida con el modelo de datos.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).

En PCM, escriba los siguientes comandos:

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*. El argumento `InitialCreate` es el nombre de la migración. Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración. Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos. El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`.

* `Update-Database`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior. El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs* , que crea la base de datos.

  El comando de actualización de la base de datos genera la advertencia siguiente: 

  > No type was specified for the decimal column "Price" on entity type "Movie" (No se ha especificado ningún tipo en la columna decimal "Price" en el tipo de entidad "Movie"). This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using "HasColumnType()" (Especifique de forma explícita el tipo de columna de SQL Server que pueda acomodar todos los valores mediante "HasColumnType()").

  Puede omitir dicha advertencia, ya que se corregirá en un tutorial posterior.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Ejecute los siguientes comandos CLI de .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*. El argumento `InitialCreate` es el nombre de la migración. Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración. Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos. El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs* ).

* `ef database update`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior. El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs* , que crea la base de datos.

---

### <a name="the-initialcreate-class"></a>La clase InitialCreate

Examine el archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

El método `Up` crea la tabla Movie y configura `Id` como la clave principal. El método `Down` revierte los cambios de esquema realizados por la migración `Up`.

<a name="test"></a>

## <a name="test-the-app"></a>Prueba de la aplicación

* Ejecute la aplicación y haga clic en el vínculo **Movie App**.

  Si obtiene una excepción similar a una de las siguientes:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Probablemente haya omitido el [paso de migraciones](#migration).

* Pruebe la página **Create**. Escriba y envíe los datos.

  > [!NOTE]
  > Es posible que no pueda escribir comas decimales en el campo `Price`. La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos. Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Pruebe las páginas **Edit** , **Details** y **Delete**.

## <a name="dependency-injection-in-the-controller"></a>Inserción de dependencias en el controlador

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador. El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador. El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Uso de SQLite para desarrollo y SQL Server para producción

Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo. En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio. `IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modelos fuertemente tipados y la palabra clave @model

Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`. El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.

MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista. Este enfoque fuertemente tipado permite comprobar el código en tiempo de compilación. En el mecanismo de scaffolding se ha usado este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas.

Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

El parámetro `id` suele pasarse como datos de ruta. Por ejemplo, `https://localhost:5001/movies/details/1` establece:

* El controlador en el controlador `movies` (el primer segmento de dirección URL).
* La acción en `details` (el segundo segmento de dirección URL).
* El identificador en 1 (el último segmento de dirección URL).

También puede pasar `id` con una cadena de consulta como se indica a continuación:

`https://localhost:5001/movies/details?id=1`

El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.

Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:

```csharp
return View(movie);
```

Examine el contenido del archivo *Views/Movies/Details.cshtml* :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

La instrucción `@model` de la parte superior del archivo de vista especifica el tipo de objeto que espera la vista. Cuando se ha creado el controlador de película, se ha incluido la siguiente instrucción `@model`:

```cshtml
@model MvcMovie.Models.Movie
```

Esta directiva `@model` permite el acceso a la película que el controlador ha pasado a la vista. El objeto `Model` está fuertemente tipado. Por ejemplo, en la vista *Details.cshtml* , el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado. Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.

Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies. Observe cómo el código crea un objeto `List` cuando llama al método `View`. El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Cuando se ha creado el controlador de películas, el scaffolding ha incluido la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml* :

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado. Por ejemplo, en la vista *Index.cshtml* , el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`. Entre otras ventajas, esto implica que se obtiene la comprobación del código en tiempo de compilación.

## <a name="additional-resources"></a>Recursos adicionales

* [Asistentes de etiquetas](xref:mvc/views/tag-helpers/intro)
* [Globalización y localización](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con SQL](working-with-sql.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Agregar una clase de modelo de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**. Asigne a la clase el nombre **Película**.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>Aplicar scaffolding al modelo de película

En esta sección se aplica scaffolding al modelo de película; es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En el **Explorador de soluciones** , haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.

![vista del paso anterior](adding-model/_static/add_controller21.png)

En el cuadro de diálogo **Agregar scaffold** , seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

Rellene el cuadro de diálogo **Agregar controlador** :

* **Clase de modelo** : *Movie (MvcMovie.Models)*
* **Clase de contexto de datos** : seleccione el icono **+** y agregue el valor predeterminado **MvcMovie.Models.MvcMovieContext**.

![Adición de contexto de datos](adding-model/_static/dc.png)

* **Vistas** : conserve el valor predeterminado de cada opción activada.
* **Nombre del controlador** : conserve el valor predeterminado *MoviesController*.
* Seleccione **Agregar**.

![Cuadro de diálogo Agregar controlador](adding-model/_static/add_controller2.png)

Visual Studio crea:

* Una [clase de contexto de base de datos](xref:data/ef-mvc/intro#create-the-database-context) de Entity Framework Core ( *Data/MvcMovieContext.cs* )
* Un controlador de películas ( *Controllers/MoviesController.cs* )
* Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice ( *Views/Movies/\*.cshtml* )

La creación automática del contexto de base de datos y de vistas y métodos de acción [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (crear, leer, actualizar y eliminar) se conoce como *scaffolding*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).
* Instale la herramienta de scaffolding:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* En Linux, exporte la ruta de acceso de la herramienta de scaffolding:

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Ejecute el siguiente comando:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).
* Instale la herramienta de scaffolding:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Ejecute el siguiente comando:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

Si ejecuta la aplicación y hace clic en el vínculo **Mvc Movie** , aparece un error similar al siguiente:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

Debe crear la base de datos y usar para ello la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core. Las migraciones permiten crear una base de datos que coincide con el modelo de datos y actualizan el esquema de base de datos cuando cambia el modelo de datos.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migración inicial

En esta sección, se completan las tareas siguientes:

* Agregar una migración inicial.
* Actualizar la base de datos con la migración inicial.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).

   ![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. En PCM, escriba los siguientes comandos:

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   El comando `Add-Migration` genera el código para crear el esquema de base de datos inicial.

   El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`. El argumento `Initial` es el nombre de la migración. Se puede usar cualquier nombre, pero, por convención, se utiliza uno que describa la migración. Para obtener más información, vea <xref:data/ef-mvc/migrations>.

   El comando `Update-Database` ejecuta el método `Up` en el archivo *Migrations/{time-stamp}_InitialCreate.cs* , con lo que se crea la base de datos.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

El comando `ef migrations add InitialCreate` genera el código para crear el esquema de base de datos inicial.

El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs* ). El argumento `InitialCreate` es el nombre de la migración. Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar el contexto registrado con la inserción de dependencias

ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection). Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor. El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.

Consulte el siguiente método `Startup.ConfigureServices`. El proveedor de scaffolding ha agregado la línea resaltada:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

El elemento `MvcMovieContext` coordina la funcionalidad de EF Core (creación, lectura, actualización, eliminación, etc.) para el modelo `Movie`. El contexto de datos (`MvcMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos. Una entidad se corresponde con una fila de la tabla.

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Ha creado un contexto de base de datos y lo ha registrado con el contenedor de inserción de dependencias.

---

<a name="test"></a>

### <a name="test-the-app"></a>Prueba de la aplicación

* Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).

Si se produce una excepción de base de datos similar a la siguiente:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Quiere decir que falta el [paso de migraciones](#pmc).

* Pruebe el vínculo **Crear**. Escriba y envíe los datos.

  > [!NOTE]
  > Es posible que no pueda escribir comas decimales en el campo `Price`. La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos. Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Pruebe los vínculos **Editar** , **Detalles** y **Eliminar**.

Examine la clase `Startup`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

En el código resaltado anterior se muestra cómo se agrega el contexto de base de datos de películas al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection):

* `services.AddDbContext<MvcMovieContext>(options =>` especifica la base de datos que se usará y la cadena de conexión.
* `=>` es un [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator).

Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador. El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modelos fuertemente tipados y la palabra clave @model

Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`. El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.

MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista. Este enfoque fuertemente tipado permite una mejor comprobación del código en tiempo de compilación. El mecanismo de scaffolding usó este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas cuando creó los métodos y las vistas.

Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

El parámetro `id` suele pasarse como datos de ruta. Por ejemplo, `https://localhost:5001/movies/details/1` establece:

* El controlador en el controlador `movies` (el primer segmento de dirección URL).
* La acción en `details` (el segundo segmento de dirección URL).
* El identificador en 1 (el último segmento de dirección URL).

También puede pasar `id` con una cadena de consulta como se indica a continuación:

`https://localhost:5001/movies/details?id=1`

El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.

Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:

```csharp
return View(movie);
   ```

Examine el contenido del archivo *Views/Movies/Details.cshtml* :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Mediante la inclusión de una instrucción `@model` en la parte superior del archivo de vista, puede especificar el tipo de objeto que espera la vista. Al crear el controlador de película, se incluyó automáticamente la siguiente instrucción `@model` en la parte superior del archivo *Details.cshtml* :

```cshtml
@model MvcMovie.Models.Movie
```

Esta directiva `@model` permite acceder a la película que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado. Por ejemplo, en la vista *Details.cshtml* , el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado. Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.

Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies. Observe cómo el código crea un objeto `List` cuando llama al método `View`. El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Cuando se creó el controlador movies, el scaffolding incluyó automáticamente la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml* :

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado. Por ejemplo, en la vista *Index.cshtml* , el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`. Entre otras ventajas, esto implica que se obtiene una comprobación del código en tiempo de compilación:

## <a name="additional-resources"></a>Recursos adicionales

* [Asistentes de etiquetas](xref:mvc/views/tag-helpers/intro)
* [Globalización y localización](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con una base de datos](working-with-sql.md)

::: moniker-end
