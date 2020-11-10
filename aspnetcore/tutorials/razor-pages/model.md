---
title: Parte 2. Adición de un modelo a una aplicación de Razor Pages en ASP.NET Core
author: rick-anderson
description: Parte 2 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 84198760cf8302d379c7630b65641e65b66d72a2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050932"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>Parte 2. Adición de un modelo a una aplicación de Razor Pages en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

En esta sección, se agregan clases para administrar películas. Las clases de modelo de la aplicación usan [Entity Framework Core (EF Core)](/ef/core) para trabajar con la base de datos. EF Core es un asignador relacional de objetos (ORM) que simplifica el acceso a los datos.

Las clases de modelo se conocen como clases POCO (del inglés "plain-old CLR objects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core. Definen las propiedades de los datos que se almacenan en la base de datos.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Agregar un modelo de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Haga clic con el botón derecho en el proyecto **RazorPagesMovie** > **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Models*.

Haga clic con el botón derecho en la carpeta *Models*. Seleccione **Agregar** > **Clase**. Asigne a la clase el nombre **Película**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Agregue una carpeta denominada *Models*.
* Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* En el Panel de solución, haga clic con el botón derecho en el proyecto **RazorPagesMovie** y seleccione **Agregar** > **Nueva carpeta...** . Asigne a la carpeta el nombre *Models*.
* Haga clic con el botón derecho en la carpeta *Modelos* y, luego, seleccione **Agregar** > **Nuevo archivo...** .
* En el cuadro de diálogo **Nuevo archivo** :

  * Seleccione **General** en el panel izquierdo.
  * Seleccione **Clase vacía** en el panel central.
  * Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Compile el proyecto para comprobar que no haya errores de compilación.

## <a name="scaffold-the-movie-model"></a>Aplicar scaffolding al modelo de película

En esta sección se aplica scaffolding al modelo de película; es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cree una carpeta *Pages/Movies* :

* Haga clic con el botón derecho en la carpeta *Páginas* > **Agregar** > **Nueva carpeta**.
* Asigne a la carpeta el nombre *Movies*.

Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.

![Imagen de las instrucciones anteriores.](model/_static/sca.png)

En el cuadro de diálogo **Agregar scaffold** , seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :

* En la lista desplegable **Clase de modelo** , seleccione **Movie (RazorPagesMovie.Models)** .
* En la fila **Clase de contexto de datos** , seleccione el signo **+** (más) y cambie el nombre generado de RazorPagesMovie. **Models**.RazorPagesMovieContext a RazorPagesMovie. **Data**.RazorPagesMovieContext. [Este cambio](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) no es necesario. Crea la clase de contexto de datos con el espacio de nombres correcto.
* Seleccione **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/3/arp.png)

El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).
* Instale la herramienta de scaffolding:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **En Windows** : Ejecute el siguiente comando:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **En macOS y Linux** : Ejecute el siguiente comando:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Cree una carpeta *Pages/Movies* :

* Haga clic con el botón derecho en la carpeta *Páginas* > **Agregar** > **Nueva carpeta**.
* Asigne a la carpeta el nombre *Movies*.

Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo scaffolding...** .

![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

En el cuadro de diálogo **Nuevo scaffolding** , seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Siguiente**.

![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :

* En la lista desplegable **Clase de modelo** , seleccione o escriba **Movie (RazorPagesMovie.Models)** .
* En la fila **Clase de contexto de datos** , escriba el nombre de la nueva clase, RazorPagesMovie. **Data**.RazorPagesMovieContext. [Este cambio](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) no es necesario. Crea la clase de contexto de datos con el espacio de nombres correcto.
* Seleccione **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/arpMac.png)

El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.

### <a name="add-ef-tools"></a>Incorporación de herramientas de EF

Ejecute el siguiente comando de la CLI de .NET Core:

```dotnetcli
dotnet tool install --global dotnet-ef
```

El comando anterior incorpora las herramientas de Entity Framework Core para la CLI de .NET Core.

---

### <a name="files-created"></a>Archivos creados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

El proceso de scaffolding crea y actualiza los archivos siguientes:

* *Pages/Movies* : Create, Delete, Details, Edit e Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Actualizado

* *Startup.cs*

Los archivos creados y actualizados se explican en la sección siguiente.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

El proceso de scaffolding crea y actualiza los archivos siguientes:

* *Pages/Movies* : Create, Delete, Details, Edit e Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Actualizado

* *Startup.cs*

Los archivos creados y actualizados se explican en la sección siguiente.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

El proceso de scaffolding crea los archivos siguientes:

* *Pages/Movies* : Create, Delete, Details, Edit e Index.

Los archivos creados se explican en la sección siguiente.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Migración inicial

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En esta sección, la Consola del administrador de paquetes (PMC) se utiliza para:

* Agregar una migración inicial.
* Actualizar la base de datos con la migración inicial.

En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.

  ![Menú de PMC](../first-mvc-app/adding-model/_static/pmc.png)

En PCM, escriba los siguientes comandos:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Los comandos anteriores generan la advertencia siguiente: "No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." ("No se ha especificado ningún tipo en la columna decimal 'Price' en el tipo de entidad 'Movie'. Esto hará que los valores se trunquen inadvertidamente si no caben según la precisión y escala predeterminados. Especifique expresamente el tipo de columna de SQL Server que tenga cabida para todos los valores usando 'HasColumnType()'.")

Puede omitir dicha advertencia, ya que se corregirá en un tutorial posterior.

El comando migrations genera código para crear el esquema de base de datos inicial. El esquema se basa en el modelo especificado en `DbContext`. El argumento `InitialCreate` se usa para asignar nombre a las migraciones. Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.

El comando `update` ejecuta el método `Up` en las migraciones que no se han aplicado. En este caso, `update` ejecuta el método `Up` en *Migrations/\<time-stamp>_InitialCreate.cs* , que crea la base de datos.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar el contexto registrado con la inserción de dependencias

ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection). Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor. El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.

La herramienta de scaffolding ha creado un contexto de base de datos de forma automática y lo ha registrado con el contenedor de inserción de dependencias.

Examine el método `Startup.ConfigureServices`. El proveedor de scaffolding ha agregado la línea resaltada:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

El elemento `RazorPagesMovieContext` coordina la funcionalidad de EF Core (creación, lectura, actualización, eliminación, etc.) para el modelo `Movie`. El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos. Una entidad se corresponde con una fila de la tabla.

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Examine el método `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

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

* Pruebe el vínculo **Crear**.

  ![Página Crear](model/_static/conan.png)

  > [!NOTE]
  > Es posible que no pueda escribir comas decimales en el campo `Price`. La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos. Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Pruebe los vínculos **Editar** , **Detalles** y **Eliminar**.

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

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Agregar un modelo de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Haga clic con el botón derecho en el proyecto **RazorPagesMovie** > **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Models*.

Haga clic con el botón derecho en la carpeta *Models*. Seleccione **Agregar** > **Clase**. Asigne a la clase el nombre **Película**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Agregue una carpeta denominada *Models*.
* Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **RazorPagesMovie** y seleccione  **Agregar** > **Carpeta nueva**. Asigne a la carpeta el nombre *Models*.
* Haga clic con el botón derecho en la carpeta *Modelos* y, luego, seleccione **Agregar** > **Nuevo archivo**.
* En el cuadro de diálogo **Nuevo archivo** :

  * Seleccione **General** en el panel izquierdo.
  * Seleccione **Clase vacía** en el panel central.
  * Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Compile el proyecto para comprobar que no haya errores de compilación.

## <a name="scaffold-the-movie-model"></a>Aplicar scaffolding al modelo de película

En esta sección se aplica scaffolding al modelo de película; es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cree una carpeta *Pages/Movies* :

* Haga clic con el botón derecho en la carpeta *Páginas* > **Agregar** > **Nueva carpeta**.
* Asigne a la carpeta el nombre *Movies*.

Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.

![Imagen de las instrucciones anteriores.](model/_static/sca.png)

En el cuadro de diálogo **Agregar scaffold** , seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* En la lista desplegable **Clase de modelo** , seleccione **Movie (RazorPagesMovie.Models)** .
* En la fila **Clase de contexto de datos** , seleccione el signo **+** (más), inicie sesión y acepte el nombre generado **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Seleccione **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/arp.png)

El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).

* **En Windows** : Ejecute el siguiente comando:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **En macOS y Linux** : Ejecute el siguiente comando:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Cree una carpeta *Pages/Movies* :

* Haga clic con el botón derecho en la carpeta *Páginas* > **Agregar** > **Nueva carpeta**.
* Asigne a la carpeta el nombre *Movies*.

Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.

![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

En el cuadro de diálogo **Agregar nuevos scaffolding** , seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :

* En la lista desplegable **Clase de modelo** , seleccione o escriba **Movie**.
* En la fila **Clase de contexto de datos** , escriba o seleccione **RazorPagesMovieContext**. Se creará una nueva clase de contexto de la base de datos con el espacio de nombres correcto. En este caso, será **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Seleccione **Agregar**.

![Imagen de las instrucciones anteriores.](model/_static/arpMac.png)

El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.

---

El proceso de scaffolding crea y actualiza los archivos siguientes:

### <a name="files-created"></a>Archivos creados

* *Pages/Movies* : Create, Delete, Details, Edit e Index.
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

En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.

  ![Menú de PMC](../first-mvc-app/adding-model/_static/pmc.png)

En PCM, escriba los siguientes comandos:

```powershell
Add-Migration Initial
Update-Database
```

El comando `Add-Migration` genera el código para crear el esquema de base de datos inicial. El esquema se basa en el modelo especificado en `DbContext` (en el archivo *RazorPagesMovieContext.cs* ). El argumento `InitialCreate` se usa para asignar nombre a las migraciones. Se puede usar cualquier nombre, pero, por convención, se utiliza uno que describa la migración. Para obtener más información, vea <xref:data/ef-mvc/migrations>.

El comando `Update-Database` ejecuta el método `Up` en el archivo *Migrations/\<time-stamp>_InitialCreate.cs*. El método `Up` crea la base de datos.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> Los comandos anteriores generan la advertencia siguiente: *No type was specified for the decimal column "Price" on entity type "Movie" (No se ha especificado ningún tipo en la columna decimal "Price" en el tipo de entidad "Movie"). This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using "HasColumnType()" (Especifique de forma explícita el tipo de columna de SQL Server que pueda acomodar todos los valores mediante "HasColumnType()").* Esta advertencia se puede omitir, ya que se corregirá en un tutorial posterior.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar el contexto registrado con la inserción de dependencias

ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection). Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor. El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.

La herramienta de scaffolding ha creado un contexto de base de datos de forma automática y lo ha registrado con el contenedor de inserción de dependencias.

Examine el método `Startup.ConfigureServices`. El proveedor de scaffolding ha agregado la línea resaltada:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

El elemento `RazorPagesMovieContext` coordina la funcionalidad de EF Core (creación, lectura, actualización, eliminación, etc.) para el modelo `Movie`. El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos. Una entidad se corresponde con una fila de la tabla.

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Examine el método `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

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

* Pruebe el vínculo **Crear**.

  ![Página Crear](model/_static/conan.png)

  > [!NOTE]
  > Es posible que no pueda escribir comas decimales en el campo `Price`. La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos. Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Pruebe los vínculos **Editar** , **Detalles** y **Eliminar**.

En el tutorial siguiente se explican los archivos creados mediante scaffolding.

## <a name="additional-resources"></a>Recursos adicionales

> [!div class="step-by-step"]
> [Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)

::: moniker-end
