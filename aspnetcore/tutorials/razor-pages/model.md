---
title: Parte 2. Adición de un modelo
author: rick-anderson
description: Parte 2 de la serie de tutoriales sobre Razor Pages. En esta sección, se agregan clases de modelo.
ms.author: riande
ms.date: 11/11/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6244ac8798fb470a88802389961968fb52bd3c0a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550699"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>Parte 2. Adición de un modelo a una aplicación de Razor Pages en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

En esta sección, se agregan clases para administrar películas en una base de datos. Las clases de modelo de la aplicación usan [Entity Framework Core (EF Core)](/ef/core) para trabajar con la base de datos. EF Core es un asignador relacional de objetos (ORM) que simplifica el acceso a los datos. Primero escriba las clases de modelo, y EF Core crea la base de datos.

Las clases de modelo se conocen como clases POCO (del inglés "**P** lain-**O** ld **C** LR **O** bjects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core. Definen las propiedades de los datos que se almacenan en la base de datos.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Agregar un modelo de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto *RazorPagesMovie* > **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Models*.
1. Haga clic con el botón derecho en la carpeta *Models*. Seleccione **Agregar** > **Clase**. Asigne a la clase el nombre *Película*.
1. Agregue las propiedades siguientes a la clase `Movie`:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

la clase `Movie` contiene:

* La base de datos requiere el campo `ID` para la clave principal.
* `[DataType(DataType.Date)]`: el atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`). Con este atributo:

  * El usuario no tiene que especificar información horaria en el campo de fecha.
  * Solo se muestra la fecha, no información horaria.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Agregue una carpeta denominada *Models*.
1. Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.

Agregue las propiedades siguientes a la clase `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

la clase `Movie` contiene:

* La base de datos requiere el campo `ID` para la clave principal.
* `[DataType(DataType.Date)]`: el atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`). Con este atributo:

  * El usuario no tiene que especificar información horaria en el campo de fecha.
  * Solo se muestra la fecha, no información horaria.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Agregue los paquetes NuGet y las herramientas EF.

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a>Agregar una clase de contexto de base de datos

1. En el proyecto *RazorPagesMovie*, cree una carpeta denominada *Data*.
1. En la carpeta *Data*, agregue un archivo denominado *RazorPagesMovieContext.cs* con el siguiente código:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   El código anterior crea una propiedad `DbSet` para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla. El código no se compilará hasta que se agreguen las dependencias en un paso posterior.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Agregar una cadena de conexión de base de datos

Agregue una cadena de conexión al archivo *appsettings.json* , tal como se muestra en el código resaltado siguiente:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registro del contexto de base de datos

1. Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En la **ventana de la herramienta de soluciones**, mientras mantiene presionada la tecla Ctrl, haga clic en el proyecto *RazorPagesMovie* y, después, seleccione **Agregar** > **Nueva carpeta...** Asigne a la carpeta el nombre *Models*.
1. Mientras mantiene presionada la tecla Ctrl, haga clic en la carpeta *Models* y, luego, seleccione **Agregar** > **Nuevo archivo...**
1. En el cuadro de diálogo **Nuevo archivo**:
   1. Seleccione **General** en el panel izquierdo.
   1. Seleccione **Clase vacía** en el panel central.
   1. Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.

1. Agregue las propiedades siguientes a la clase `Movie`:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

la clase `Movie` contiene:

* La base de datos requiere el campo `ID` para la clave principal.
* `[DataType(DataType.Date)]`: el atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`). Con este atributo:

  * El usuario no tiene que especificar información horaria en el campo de fecha.
  * Solo se muestra la fecha, no información horaria.

---

Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.

Compile el proyecto para comprobar que no haya errores de compilación.

## <a name="scaffold-the-movie-model"></a>Aplicar scaffolding al modelo de película

En esta sección se aplica scaffolding al modelo de película; es decir, la herramienta de scaffolding genera páginas para las operaciones de Create, lectura, actualización y Delete (CRUD) del modelo de película.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Create una carpeta *Pages/Movies*:
   1. Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.
   1. Asigne a la carpeta el nombre *Movies*.

1. Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.

   ![Imagen de las instrucciones anteriores.](model/_static/5/sca.png)

1. En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.

   ![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

1. Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :
   1. En la lista desplegable **Clase de modelo**, seleccione **Movie (RazorPagesMovie.Models)** .
   1. En la fila **Clase de contexto de datos**, seleccione el signo **+** (más).
      1. En el cuadro de diálogo **Agregar contexto de datos**, se genera el nombre de clase *RazorPagesMovie.Data.RazorPagesMovieContext*.
   1. Seleccione **Agregar**.

   ![Imagen de las instrucciones anteriores.](model/_static/3/arp.png)

El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra un shell de comandos en el directorio del proyecto, que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*

* **En Windows**: Ejecute el siguiente comando:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **En macOS y Linux**: Ejecute el siguiente comando:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> En la tabla siguiente se incluyen los detalles de las opciones del generador de código de ASP.NET Core.

| Opción               | Descripción|
| ----------------- | ------------ |
| `-m`  | Nombre del modelo |
| `-dc`  | La clase `DbContext` que se va a usar. |
| `-udl` | Usa el diseño predeterminado. |
| `-outDir` | Ruta de acceso relativa de la carpeta de salida para crear las vistas |
| `--referenceScriptLibraries` | Agrega `_ValidationScriptsPartial` a las páginas Editar y Create. |

Use la opción `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Para más información, vea [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Uso de SQLite para desarrollo y SQL Server para producción

Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo. En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en `Startup`. `IWebHostEnvironment` se inserta para que la aplicación pueda usar SQLite en desarrollo y SQL Server en producción.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Create una carpeta *Pages/Movies*:
   1. Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.
   1. Asigne a la carpeta el nombre *Movies*.

1. Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages/Movies* > **Agregar** > **Nuevo scaffolding...**

   ![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

1. En el cuadro de diálogo **Nuevo scaffolding**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Siguiente**.

   ![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

1. Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :
   1. En la fila **DbContext Class to use:** , asigne a la clase el nombre *RazorPagesMovie.Data.RazorPagesMovieContext*.
   1. Seleccione **Finalizar**.

   ![Imagen de las instrucciones anteriores.](model/_static/5/arpMac.png)

El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Uso de SQLite para desarrollo y SQL Server para producción

Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo. En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en `Startup`. `IWebHostEnvironment` se inserta para que la aplicación pueda usar SQLite en desarrollo y SQL Server en producción.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Archivos creados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

El proceso de scaffolding crea y actualiza los archivos siguientes:

* *Pages/Movies*: Create, Delete, Details, Edit y Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Actualizado

* *Startup.cs*

Los archivos creados y actualizados se explican en la sección siguiente.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

El proceso de scaffolding crea los archivos siguientes:

* *Pages/Movies*: Create, Delete, Details, Edit y Index.

Los archivos creados se explican en la sección siguiente.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

El proceso de scaffolding crea y actualiza los archivos siguientes:

* *Pages/Movies*: Create, Delete, Details, Edit y Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Actualizado

* *Startup.cs*

Los archivos creados y actualizados se explican en la sección siguiente.

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a>Create el esquema de base de datos inicial con la característica de migración de EF

La característica de migraciones de Entity Framework Core permite:

* Create el esquema de base de datos inicial.
* Actualizar incrementalmente el esquema de base de datos para mantenerlo sincronizado con el modelo de datos de la aplicación.  Se conservan los datos existentes en la base de datos.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En esta sección, la ventana de la **Consola del Administrador de paquetes** (PMC) se utiliza para:

* Agregar una migración inicial.
* Actualizar la base de datos con la migración inicial.

1. En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.

   ![Menú de PMC](model/_static/5/pmc.png)

1. En PCM, escriba los siguientes comandos:

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* Ejecute los siguientes comandos de la CLI de .NET:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Los comandos anteriores generan la advertencia siguiente: "No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." ("No se ha especificado ningún tipo en la columna decimal 'Price' en el tipo de entidad 'Movie'. Esto hará que los valores se trunquen inadvertidamente si no caben según la precisión y escala predeterminados. Especifique expresamente el tipo de columna de SQL Server que tenga cabida para todos los valores usando 'HasColumnType()'.")

Ignore la advertencia, ya que se tratará en un paso posterior.

El comando `migrations` genera el código para crear el esquema de base de datos inicial. El esquema se basa en el modelo especificado en `DbContext`. El argumento `InitialCreate` se usa para asignar nombre a las migraciones. Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.

El comando `update` ejecuta el método `Up` en las migraciones que no se han aplicado. En este caso, `update` ejecuta el método `Up` en el archivo *Migrations/\<time-stamp>_InitialCreate.cs*, que crea la base de datos.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar el contexto registrado con la inserción de dependencias

ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection). Los servicios, como el contexto de base de datos de EF Core, se registran con la inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor. El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.

La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.

Examine el método `Startup.ConfigureServices`. El proveedor de scaffolding ha agregado la línea resaltada:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

El elemento `RazorPagesMovieContext` coordina la funcionalidad de EF Core. como Create, Read, Update y Delete, para el modelo `Movie`. El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

En el código anterior se crea una propiedad [DbSet/\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos. Una entidad se corresponde con una fila de la tabla.

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). Para el desarrollo local, el [sistema de configuración](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Examine el método `Up`.

---

<a name="test"></a>

## <a name="test-the-app"></a>Prueba de la aplicación

1. Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).

   Si aparece el siguiente mensaje de error:

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   Quiere decir que falta el [paso de migraciones](#pmc).

1. Pruebe el vínculo **Create** .

   ![Página Create](model/_static/conan5.png)

   > [!NOTE]
   > Es posible que no pueda escribir comas decimales en el campo `Price`. La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos. Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

1. Pruebe los vínculos **Edit**, **Details** y **Delete** .

En el tutorial siguiente se explican los archivos creados mediante scaffolding.

## <a name="additional-resources"></a>Recursos adicionales

> [!div class="step-by-step"]
> [Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

En esta sección, se agregan clases para administrar películas. Las clases de modelo de la aplicación usan [Entity Framework Core (EF Core)](/ef/core) para trabajar con la base de datos. EF Core es un asignador relacional de objetos (ORM) que simplifica el acceso a los datos.

Las clases de modelo se conocen como clases POCO (del inglés "plain-old CLR objects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core. Definen las propiedades de los datos que se almacenan en la base de datos.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Agregar un modelo de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Haga clic con el botón derecho en el proyecto **RazorPagesMovie** > **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Models*.

Haga clic con el botón derecho en la carpeta *Models*. Seleccione **Agregar** > **Clase**. Asigne a la clase el nombre **Película**.

Agregue las propiedades siguientes a la clase `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

la clase `Movie` contiene:

* La base de datos requiere el campo `ID` para la clave principal.
* `[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`). Con este atributo:

  * El usuario no tiene que especificar información horaria en el campo de fecha.
  * Solo se muestra la fecha, no información horaria.

Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Agregue una carpeta denominada *Models*.
* Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.

Agregue las propiedades siguientes a la clase `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

la clase `Movie` contiene:

* La base de datos requiere el campo `ID` para la clave principal.
* `[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`). Con este atributo:

  * El usuario no tiene que especificar información horaria en el campo de fecha.
  * Solo se muestra la fecha, no información horaria.

Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Agregue los paquetes NuGet y las herramientas EF.

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Agregar una clase de contexto de base de datos

* En el proyecto *RazorPagesMovie*, cree una carpeta denominada *Data*.
* Agregue la clase `RazorPagesMovieContext` siguiente a la carpeta *Data*:

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

El código anterior crea una propiedad `DbSet` para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla. El código no se compilará hasta que se agreguen las dependencias en un paso posterior.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Agregar una cadena de conexión de base de datos

Agregue una cadena de conexión al archivo *appsettings.json* , tal como se muestra en el código resaltado siguiente:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registro del contexto de base de datos

Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* En la **ventana de la herramienta de soluciones**, mientras mantiene presionada la tecla Ctrl, haga clic en el proyecto **RazorPagesMovie** y, después, seleccione **Agregar** > **Nueva carpeta...** Asigne a la carpeta el nombre *Models*.
* Haga clic con el botón derecho en la carpeta *Modelos* y, luego, seleccione **Agregar** > **Nuevo archivo...** .
* En el cuadro de diálogo **Nuevo archivo**:

  * Seleccione **General** en el panel izquierdo.
  * Seleccione **Clase vacía** en el panel central.
  * Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.

Agregue las propiedades siguientes a la clase `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

la clase `Movie` contiene:

* La base de datos requiere el campo `ID` para la clave principal.
* `[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`). Con este atributo:

  * El usuario no tiene que especificar información horaria en el campo de fecha.
  * Solo se muestra la fecha, no información horaria.

---

Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.

Compile el proyecto para comprobar que no haya errores de compilación.

## <a name="scaffold-the-movie-model"></a>Aplicar scaffolding al modelo de película

En esta sección se aplica scaffolding al modelo de película; es decir, la herramienta de scaffolding genera páginas para las operaciones de Create, lectura, actualización y Delete (CRUD) del modelo de película.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Create una carpeta *Pages/Movies*:

* Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.
* Asigne a la carpeta el nombre *Movies*.

Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.

![Imagen de las instrucciones anteriores.](model/_static/sca.png)

En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :

* En la lista desplegable **Clase de modelo**, seleccione **Movie (RazorPagesMovie.Models)** .
* En la fila **Clase de contexto de datos**, seleccione el signo **+** (más) y cambie el nombre generado de RazorPagesMovie.**Models**.RazorPagesMovieContext a RazorPagesMovie.**Data**.RazorPagesMovieContext. [Este cambio](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) no es necesario. Crea la clase de contexto de datos con el espacio de nombres correcto.
* Seleccione **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/3/arp.png)

El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra una ventana Comandos en el directorio del proyecto, que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*.

* **En Windows**: Ejecute el siguiente comando:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **En macOS y Linux**: Ejecute el siguiente comando:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> En la tabla siguiente se incluyen los detalles de las opciones del generador de código de ASP.NET Core:

| Opción               | Descripción|
| ----------------- | ------------ |
| `-m`  | Nombre del modelo |
| `-dc`  | La clase `DbContext` que se va a usar. |
| `-udl` | Usa el diseño predeterminado. |
| `-outDir` | Ruta de acceso relativa de la carpeta de salida para crear las vistas |
| `--referenceScriptLibraries` | Agrega `_ValidationScriptsPartial` a las páginas Editar y Create. |

Use la opción `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Para más información, vea [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Uso de SQLite para desarrollo y SQL Server para producción

Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo. En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio. `IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Create una carpeta *Pages/Movies*:

* Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.
* Asigne a la carpeta el nombre *Movies*.

Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo scaffolding...**

![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

En el cuadro de diálogo **Nuevo scaffolding**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Siguiente**.

![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :

* En la lista desplegable **Clase de modelo**, seleccione o escriba **Movie (RazorPagesMovie.Models)** .
* En la fila **Clase de contexto de datos**, escriba el nombre de la nueva clase, RazorPagesMovie.**Data**.RazorPagesMovieContext. [Este cambio](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) no es necesario. Crea la clase de contexto de datos con el espacio de nombres correcto.
* Seleccione **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/arpMac.png)

El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.

### <a name="add-ef-tools"></a>Incorporación de herramientas de EF

Ejecute el siguiente comando de la CLI de .NET Core:

```dotnetcli
dotnet tool install --global dotnet-ef
```

El comando anterior incorpora las herramientas de Entity Framework Core para la CLI de .NET Core. Para más información, vea [Referencia de herramientas de Entity Framework Core-CLI de .NET Core](/ef/core/miscellaneous/cli/dotnet).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Uso de SQLite para desarrollo y SQL Server para producción

Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo. En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio. `IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Archivos creados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

El proceso de scaffolding crea y actualiza los archivos siguientes:

* *Pages/Movies*: Create, Delete, Details, Edit y Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Actualizado

* *Startup.cs*

Los archivos creados y actualizados se explican en la sección siguiente.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

El proceso de scaffolding crea y actualiza los archivos siguientes:

* *Pages/Movies*: Create, Delete, Details, Edit y Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Actualizado

* *Startup.cs*

Los archivos creados y actualizados se explican en la sección siguiente.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

El proceso de scaffolding crea los archivos siguientes:

* *Pages/Movies*: Create, Delete, Details, Edit y Index.

Los archivos creados se explican en la sección siguiente.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Migración inicial

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En esta sección, la Consola del administrador de paquetes (PMC) se utiliza para:

* Agregar una migración inicial.
* Actualizar la base de datos con la migración inicial.

En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.

  ![Menú de PMC](../first-mvc-app/adding-model/_static/pmc.png)

En PCM, escriba los siguientes comandos:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Ejecute los siguientes comandos CLI de .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Los comandos anteriores generan la advertencia siguiente: "No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." ("No se ha especificado ningún tipo en la columna decimal 'Price' en el tipo de entidad 'Movie'. Esto hará que los valores se trunquen inadvertidamente si no caben según la precisión y escala predeterminados. Especifique expresamente el tipo de columna de SQL Server que tenga cabida para todos los valores usando 'HasColumnType()'.")

Ignore la advertencia, ya que se tratará en un paso posterior.

El comando migrations genera código para crear el esquema de base de datos inicial. El esquema se basa en el modelo especificado en `DbContext`. El argumento `InitialCreate` se usa para asignar nombre a las migraciones. Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.

El comando `update` ejecuta el método `Up` en las migraciones que no se han aplicado. En este caso, `update` ejecuta el método `Up` en el archivo *Migrations/\<time-stamp>_InitialCreate.cs*, que crea la base de datos.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar el contexto registrado con la inserción de dependencias

ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection). Los servicios, como el contexto de base de datos de EF Core, se registran con la inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan, como páginas Razor, a través de parámetros de constructor. El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.

La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.

Examine el método `Startup.ConfigureServices`. El proveedor de scaffolding ha agregado la línea resaltada:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

El elemento `RazorPagesMovieContext` coordina la funcionalidad de EF Core. como Create, Read, Update y Delete, para el modelo `Movie`. El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos. Una entidad se corresponde con una fila de la tabla.

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). Para el desarrollo local, el [sistema de configuración](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Examine el método `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Prueba de la aplicación

* Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).

Si se produce un error:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Quiere decir que falta el [paso de migraciones](#pmc).

* Pruebe el vínculo **Create** .

  ![Página Create](model/_static/conan5.png)

  > [!NOTE]
  > Es posible que no pueda escribir comas decimales en el campo `Price`. La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos. Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Pruebe los vínculos **Edit**, **Details** y **Delete** .

En el tutorial siguiente se explican los archivos creados mediante scaffolding.

## <a name="additional-resources"></a>Recursos adicionales

> [!div class="step-by-step"]
> [Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

En esta sección, se agregan clases para administrar películas en una [base de datos SQLite](https://www.sqlite.org/index.html) multiplataforma. Las aplicaciones creadas a partir de una plantilla de ASP.NET Core usan una base de datos SQLite. Las clases de modelo de la aplicación se usan con [Entity Framework Core (EF Core)](/ef/core) ([Proveedor de base de datos SQLite para EF Core](/ef/core/providers/sqlite)) para trabajar con la base de datos. EF Core es un marco de trabajo de asignación relacional de objetos (ORM) que simplifica el acceso a los datos.

Las clases de modelo se conocen como clases POCO (del inglés "plain-old CLR objects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core. Definen las propiedades de los datos que se almacenan en la base de datos.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Agregar un modelo de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Haga clic con el botón derecho en el proyecto **RazorPagesMovie** > **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Models*.

Haga clic con el botón derecho en la carpeta *Models*. Seleccione **Agregar** > **Clase**. Asigne a la clase el nombre **Película**.

Agregue las propiedades siguientes a la clase `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

la clase `Movie` contiene:

* La base de datos requiere el campo `ID` para la clave principal.
* `[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`). Con este atributo:

  * El usuario no tiene que especificar información horaria en el campo de fecha.
  * Solo se muestra la fecha, no información horaria.

Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Agregue una carpeta denominada *Models*.
* Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.

Agregue las propiedades siguientes a la clase `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

la clase `Movie` contiene:

* La base de datos requiere el campo `ID` para la clave principal.
* `[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`). Con este atributo:

  * El usuario no tiene que especificar información horaria en el campo de fecha.
  * Solo se muestra la fecha, no información horaria.

Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Agregue los paquetes NuGet y las herramientas EF.

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Agregar una clase de contexto de base de datos

En el proyecto RazorPagesMovie, cree una carpeta denominada *Data*. 
Agregue la clase `RazorPagesMovieContext` siguiente a la carpeta *Data*:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

El código anterior crea una propiedad `DbSet` para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla. El código no se compilará hasta que se agreguen las dependencias en un paso posterior.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Agregar una cadena de conexión de base de datos

Agregue una cadena de conexión al archivo *appsettings.json* , tal como se muestra en el código resaltado siguiente:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Adición de los paquetes NuGet necesarios

Ejecute el comando siguiente de la CLI de .NET Core para agregar SQLite y CodeGeneration.Design al proyecto:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

El paquete `Microsoft.VisualStudio.Web.CodeGeneration.Design` es necesario para realizar scaffolding.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registro del contexto de base de datos

Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Compile el proyecto para comprobar si hay errores.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* En la **ventana de la herramienta de soluciones**, mientras mantiene presionada la tecla Ctrl, haga clic en el proyecto *RazorPagesMovie* y, después, seleccione **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Models*.
* Mientras mantiene presionada la tecla Ctrl, haga clic en la carpeta *Models* y, luego, seleccione **Agregar** > **Nuevo archivo**.
* En el cuadro de diálogo **Nuevo archivo**:

  * Seleccione **General** en el panel izquierdo.
  * Seleccione **Clase vacía** en el panel central.
  * Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.

Agregue las propiedades siguientes a la clase `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

la clase `Movie` contiene:

* La base de datos requiere el campo `ID` para la clave principal.
* `[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`). Con este atributo:

  * El usuario no tiene que especificar información horaria en el campo de fecha.
  * Solo se muestra la fecha, no información horaria.

Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.

---

Compile el proyecto para comprobar que no haya errores de compilación.

## <a name="scaffold-the-movie-model"></a>Aplicar scaffolding al modelo de película

En esta sección se aplica scaffolding al modelo de película; es decir, la herramienta de scaffolding genera páginas para las operaciones de Create, lectura, actualización y Delete (CRUD) del modelo de película.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Create una carpeta *Pages/Movies*:

* Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.
* Asigne a la carpeta el nombre *Movies*.

Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.

![Imagen de las instrucciones anteriores.](model/_static/sca.png)

En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* En la lista desplegable **Clase de modelo**, seleccione **Movie (RazorPagesMovie.Models)** .
* En la fila **Clase de contexto de datos**, seleccione el signo **+** (más), inicie sesión y acepte el nombre generado **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Seleccione **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/arp.png)

El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra una ventana Comandos en el directorio del proyecto, que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*.

* **En Windows**: Ejecute el siguiente comando:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **En macOS y Linux**: Ejecute el siguiente comando:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> En la tabla siguiente se incluyen los detalles de las opciones del generador de código de ASP.NET Core:

| Opción               | Descripción|
| ----------------- | ------------ |
| `-m`  | Nombre del modelo |
| `-dc`  | La clase `DbContext` que se va a usar. |
| `-udl` | Usa el diseño predeterminado. |
| `-outDir` | Ruta de acceso relativa de la carpeta de salida para crear las vistas |
| `--referenceScriptLibraries` | Agrega `_ValidationScriptsPartial` a las páginas Editar y Create. |

Use la opción `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Para más información, vea [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Create una carpeta *Pages/Movies*:

* Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.
* Asigne a la carpeta el nombre *Movies*.

Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.

![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

En el cuadro de diálogo **Agregar nuevos scaffolding**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :

* En la lista desplegable **Clase de modelo**, seleccione o escriba **Movie**.
* En la fila **Clase de contexto de datos**, escriba o seleccione **RazorPagesMovieContext**. Se creará una clase de contexto de la base de datos con el espacio de nombres correcto. En este caso, será **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Seleccione **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/arpMac.png)

El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.

---

El proceso de scaffolding crea y actualiza los archivos siguientes:

### <a name="files-created"></a>Archivos creados

* *Pages/Movies*: Create, Delete, Details, Edit y Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>Archivo actualizado

* *Startup.cs*

Los archivos creados y actualizados se explican en la sección siguiente.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migración inicial

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En esta sección, la Consola del administrador de paquetes (PMC) se utiliza para:

* Agregar una migración inicial.
* Actualizar la base de datos con la migración inicial.

En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.

  ![Menú de PMC](../first-mvc-app/adding-model/_static/pmc.png)

En PCM, escriba los siguientes comandos:

```powershell
Add-Migration Initial
Update-Database
```

El comando `Add-Migration` genera el código para crear el esquema de base de datos inicial. El esquema se basa en el modelo especificado en `DbContext`, en el archivo *RazorPagesMovieContext.cs*. El argumento `InitialCreate` se usa para asignar nombre a las migraciones. Se puede usar cualquier nombre, pero, por convención, se utiliza uno que describa la migración. Para obtener más información, vea <xref:data/ef-mvc/migrations>.

El comando `Update-Database` ejecuta el método `Up` en el archivo *Migrations/\<time-stamp>_InitialCreate.cs*. El método `Up` crea la base de datos.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Ejecute los siguientes comandos CLI de .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Los comandos anteriores generan la advertencia siguiente: "No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." ("No se ha especificado ningún tipo en la columna decimal 'Price' en el tipo de entidad 'Movie'. Esto hará que los valores se trunquen inadvertidamente si no caben según la precisión y escala predeterminados. Especifique expresamente el tipo de columna de SQL Server que tenga cabida para todos los valores usando 'HasColumnType()'.")

Ignore la advertencia, ya que se tratará en un paso posterior.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar el contexto registrado con la inserción de dependencias

ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection). Los servicios, como el contexto de base de datos de EF Core, se registran con la inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor. El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.

La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.

Examine el método `Startup.ConfigureServices`. El proveedor de scaffolding ha agregado la línea resaltada:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

El elemento `RazorPagesMovieContext` coordina la funcionalidad de EF Core. como Create, Read, Update y Delete, para el modelo `Movie`. El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos. Una entidad se corresponde con una fila de la tabla.

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). Para el desarrollo local, el [sistema de configuración](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Examine el método `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Prueba de la aplicación

* Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).

Si se produce un error:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Quiere decir que falta el [paso de migraciones](#pmc).

* Pruebe el vínculo **Create** .

  ![Página Create](model/_static/conan.png)

  > [!NOTE]
  > Es posible que no pueda escribir comas decimales en el campo `Price`. La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos. Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Pruebe los vínculos **Edit**, **Details** y **Delete** .

En el tutorial siguiente se explican los archivos creados mediante scaffolding.

## <a name="additional-resources"></a>Recursos adicionales

> [!div class="step-by-step"]
> [Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)

::: moniker-end
