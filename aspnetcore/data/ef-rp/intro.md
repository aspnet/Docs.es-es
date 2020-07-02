---
title: 'Razor Pages con Entity Framework Core en ASP.NET Core: Tutorial 1 de 8'
author: rick-anderson
description: Se muestra cómo crear una aplicación de Razor Pages mediante Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/intro
ms.openlocfilehash: 79cfe50f7e074954291c88689940c3263b68e151
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401362"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Razor Pages con Entity Framework Core en ASP.NET Core: Tutorial 1 de 8

Por [Tom Dykstra](https://github.com/tdykstra) y [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Este es el primero de una serie de tutoriales en los que se muestra cómo usar Entity Framework (EF) Core en una [aplicación Razor Pages](xref:razor-pages/index) en ASP.NET Core. En el tutorial se crea un sitio web de una universidad ficticia, Contoso University. El sitio incluye funciones como la admisión de alumnos, la creación de cursos y las asignaciones de instructores. En el tutorial se usa el enfoque de Code First. Para obtener información sobre cómo seguir este tutorial mediante el enfoque de Database First, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instrucciones de descarga](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Requisitos previos

* Si no está familiarizado con Razor Pages, consulte la serie de tutoriales [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) antes de empezar este.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Motores de bases de datos

En las instrucciones de Visual Studio se usa [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versión de SQL Server Express que solo se ejecuta en Windows.

En las instrucciones de Visual Studio Code se usa [SQLite](https://www.sqlite.org/), un motor de base de datos multiplataforma.

Si decide usar SQLite, descargue e instale una herramienta de terceros para administrar y ver una base de datos de SQLite, como [DB Browser for SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Solución de problemas

Si experimenta un problema que no puede resolver, compare el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Una buena forma de obtener ayuda consiste en publicar una pregunta en StackOverflow.com con la [etiqueta ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o la [etiqueta EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>La aplicación de ejemplo

La aplicación compilada en estos tutoriales es un sitio web básico de una universidad. Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores. Estas son algunas de las pantallas que se crean en el tutorial.

![Página de índice de Students](intro/_static/students-index30.png)

![Página de edición de estudiantes](intro/_static/student-edit30.png)

El estilo de la interfaz de usuario de este sitio se basa en las plantillas de proyecto integradas. El enfoque del tutorial es cómo usar EF Core, no cómo personalizar la interfaz de usuario.

Siga el vínculo de la parte superior de la página para obtener el código fuente para el proyecto completado. La carpeta *cu30* contiene el código para la versión ASP.NET Core 3.0 del tutorial. Los archivos que reflejan el estado del código para los tutoriales 1-7 se pueden encontrar en la carpeta *cu30snapshots*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Para ejecutar la aplicación después de descargar el proyecto completado:

* Compile el proyecto.
* En la Consola del administrador de paquetes (PMC), ejecute el comando siguiente:

  ```powershell
  Update-Database
  ```

* Ejecute el proyecto para inicializar la base de datos.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para ejecutar la aplicación después de descargar el proyecto completado:

* Elimine *ContosoUniversity.csproj* y cambie el nombre de *ContosoUniversitySQLite.csproj* por *ContosoUniversity.csproj*.
* Elimine *Startup.cs*, y cambie el nombre de *StartupSQLite.cs* por *Startup.cs*.
* Elimine *appSettings.json* y cambie el nombre de *appSettingsSQLite.json* por *appSettings.json*.
* Elimine la carpeta *Migrations* y cambie el nombre de *MigrationsSQL* por *Migrations*.
* Realice una búsqueda global `#if SQLiteVersion` y quite `#if SQLiteVersion` y la instrucción `#endif` asociada.
* Compile el proyecto.
* En un símbolo del sistema en la carpeta del proyecto, ejecute los comandos siguientes:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* En la herramienta SQLite, ejecute la siguiente instrucción SQL:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Ejecute el proyecto para inicializar la base de datos.

---

## <a name="create-the-web-app-project"></a>Creación del proyecto de aplicación web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.
* Seleccione **Aplicación web de ASP.NET Core**.
* Asigne el nombre *ContosoUniversity* al proyecto. Es importante usar este nombre exacto incluido el uso de mayúsculas, para que los espacios de nombres coincidan cuando se copie y pegue el código.
* Seleccione **.NET Core** y **ASP.NET Core 3.0** en las listas desplegables y, luego, **Aplicación web**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* En un terminal, vaya a la carpeta en la que se debe crear la carpeta del proyecto.

* Ejecute los comandos siguientes para crear un proyecto de Razor Pages y `cd` para acceder a la nueva carpeta del proyecto:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Configurar el estilo del sitio

Configure el encabezado, el pie de página y el menú del sitio mediante la actualización de *Pages/Shared/_Layout.cshtml*:

* Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University". Hay tres repeticiones.

* Elimine las entradas de menú **Home** y **Privacy**, y agregue entradas para **About**, **Students**, **Courses**, **Instructors** y **Departments**.

Los cambios aparecen resaltados.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

En *Pages/Index.cshtml*, reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET Core con texto sobre esta aplicación:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Ejecute la aplicación para comprobar que aparece la página principal.

## <a name="the-data-model"></a>El modelo de datos

En las secciones siguientes se crea un modelo de datos:

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Un alumno se puede inscribir en cualquier número de cursos y un curso puede tener cualquier número de alumnos inscritos.

## <a name="the-student-entity"></a>La entidad Student

![Diagrama de la entidad Student](intro/_static/student-entity.png)

* Cree una carpeta *Models* en la carpeta del proyecto. 

* Cree *Models/Student.cs* con el código siguiente:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos que se corresponde a esta clase. De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`. Por tanto, el nombre que se reconoce de forma automática para la clave principal de la clase `Student` es `StudentID`. Para más información, consulte [EF Core: claves](/ef/core/modeling/keys?tabs=data-annotations).

La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships). Las propiedades de navegación contienen otras entidades relacionadas con esta entidad. En este caso, la propiedad `Enrollments` de una entidad `Student` contiene todas las entidades `Enrollment` que están relacionadas con esa instancia de Student. Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades Enrollment. 

En la base de datos, una fila Enrollment se relaciona con una fila Student si su columna StudentID contiene el valor de identificador del alumno. Por ejemplo, imagine que una fila Student tiene el identificador 1. Las filas Enrollment relacionadas tendrán StudentID = 1. StudentID es una *clave externa* de la tabla Enrollment. 

La propiedad `Enrollments` se define como `ICollection<Enrollment>` porque puede haber varias entidades Enrollment relacionadas. Puede usar otros tipos de colección, como `List<Enrollment>` o `HashSet<Enrollment>`. Cuando se usa `ICollection<Enrollment>`, EF Core crea una colección `HashSet<Enrollment>` de forma predeterminada.

## <a name="the-enrollment-entity"></a>La entidad Enrollment

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

Cree *Models/Enrollment.cs* con el código siguiente:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

La propiedad `EnrollmentID` es la clave principal; esta entidad usa el patrón `classnameID` en lugar de `ID` por sí solo. Para un modelo de datos de producción, elija un patrón y úselo de forma coherente. En este tutorial se usan los dos simplemente para ilustrar el trabajo. El uso de `ID` sin `classname` facilita la implementación de algunos tipos de cambios del modelo de datos.

La propiedad `Grade` es una `enum`. El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade`[acepta valores NULL](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/). Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.

La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`. Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.

La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`. Una entidad `Enrollment` está asociada con una entidad `Course`.

EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`. Por ejemplo, `StudentID` es la clave externa para la propiedad de navegación `Student`, ya que la clave principal de la entidad `Student` es `ID`. Las propiedades de clave externa también se pueden denominar `<primary key property name>`. Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.

## <a name="the-course-entity"></a>La entidad Course

![Diagrama de la entidad Course](intro/_static/course-entity.png)

Cree *Models/Course.cs* con el código siguiente:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

La propiedad `Enrollments` es una propiedad de navegación. Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.

El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.

Compile el proyecto para comprobar que no hay errores de compilación.

## <a name="scaffold-student-pages"></a>Scaffolding de las páginas Student

En esta sección, se usa la herramienta de scaffolding de ASP.NET Core para generar lo siguiente:

* Una clase de *contexto* de EF Core. El contexto es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos determinado. Se deriva de la clase `Microsoft.EntityFrameworkCore.DbContext`.
* Razor Pages que controlan las operaciones de creación, lectura, actualización y eliminación (CRUD) de la entidad `Student`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cree una carpeta *Students* en la carpeta *Pages*.
* En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Pages/Students* y seleccione **Agregar** > **Nuevo elemento con scaffold**.
* En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **AGREGAR**.
* En el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :
  * En la lista desplegable **Clase de modelo**, seleccione **Student (ContosoUniversity.Models)** .
  * En la fila **Clase de contexto de datos**, seleccione el signo **+** (más).
  * Cambie el nombre del contexto de datos *ContosoUniversity.Models.ContosoUniversityContext* por *ContosoUniversity.Data.SchoolContext*.
  * Seleccione **Agregar**.

Los paquetes siguientes se instalan de forma automática:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ejecute los siguientes comandos de la CLI de .NET Core para instalar los paquetes NuGet necesarios:
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  El paquete Microsoft.VisualStudio.Web.CodeGeneration.Design es necesario para scaffolding. Aunque en la aplicación no se usará SQL Server, la herramienta de scaffolding necesita el paquete de SQL Server.

* Cree una carpeta *Pages/Students*.

* Ejecute el comando siguiente para instalar la [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Ejecute el comando siguiente para aplicar scaffolding a las páginas Student.

  **En Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **En macOS o Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Si tiene un problema con el paso anterior, compile el proyecto y vuelva a intentar el paso de scaffolding.

El proceso de scaffolding:

* Crea Razor Pages en la carpeta *Pages/Students*:
  * *Create.cshtml* y *Create.cshtml.cs*
  * *Delete.cshtml* y *Delete.cshtml.cs*
  * *Details.cshtml* y *Details.cshtml.cs*
  * *Edit.cshtml* y *Edit.cshtml.cs*
  * *Index.cshtml* e *Index.cshtml.cs*
* Crea *Data/SchoolContext.cs*.
* Agrega el contexto a la inserción de dependencias en *Startup.cs*.
* Agrega una cadena de conexión de base de datos a *appsettings.json*.

## <a name="database-connection-string"></a>Cadena de conexión de base de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción. De forma predeterminada, LocalDB crea archivos *.mdf* en el directorio `C:/Users/<user>`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Cambie la cadena de conexión para que apunte a un archivo de base de datos de SQLite denominado *CU.db*:

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Actualización de la clase de contexto de base de datos

La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos. El contexto se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). En el contexto se especifica qué entidades se incluyen en el modelo de datos. En este proyecto, la clase se denomina `SchoolContext`.

Actualice *SchoolContext.cs* con el código siguiente:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

El código resaltado crea una propiedad [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades. En la terminología de EF Core:

* Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.
* Una entidad se corresponde con una fila de la tabla.

Puesto que un conjunto de entidades contiene varias entidades, las propiedades DBSet deben ser nombres en plural. Como la herramienta de scaffolding ha creado una instancia `Student` de DBSet, en este paso se cambia a `Students` en plural. 

Para que el código de Razor Pages coincida con el nuevo nombre de DBSet, realice un cambio global de `_context.Student` a `_context.Students` en todo el proyecto.  Hay ocho repeticiones.

Compile el proyecto para comprobar que no haya errores del compilador.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection). Los servicios (como el contexto de base de datos de EF Core) se registran con la inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor. El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.

La herramienta de scaffolding ha registrado de forma automática la clase de contexto con el contenedor de inserción de dependencias.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En `ConfigureServices`, el proveedor de scaffolding ha agregado la línea resaltada:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* En `ConfigureServices`, asegúrese de que el código agregado por el proveedor de scaffolding llama a `UseSqlite`.

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json*.

## <a name="create-the-database"></a>Creación de la base de datos

Actualice *Program.cs* para crear la base de datos si no existe:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

El método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) no realiza ninguna acción si existe una base de datos para el contexto. Si no existe ninguna base de datos, se crean la base de datos y el esquema. `EnsureCreated` habilita el flujo de trabajo siguiente para controlar los cambios del modelo de datos:

* Se elimina la base de datos. Se pierden los datos existentes.
* Se cambia el modelo de datos. Por ejemplo, se agrega un campo `EmailAddress`.
* Ejecutar la aplicación.
* `EnsureCreated` crea una base de datos con el esquema nuevo.

Este flujo de trabajo funciona bien al principio de la fase de desarrollo cuando el esquema evoluciona rápidamente, siempre y cuando no sea necesario conservar los datos. La situación es distinta cuando es necesario conservar los datos introducidos en la base de datos. En ese caso, use las migraciones.

Más adelante en la serie de tutoriales, eliminará la base de datos creada por `EnsureCreated` y, en su lugar, usará las migraciones. Una base de datos creada por `EnsureCreated` no se puede actualizar mediante migraciones.

### <a name="test-the-app"></a>Prueba de la aplicación

* Ejecutar la aplicación.
* Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.
* Pruebe los vínculos Edit, Details y Delete.

## <a name="seed-the-database"></a>Inicializar la base de datos

El método `EnsureCreated` crea una base de datos vacía. En esta sección se agrega código que rellena la base de datos con datos de prueba.

Cree *Data/DbInitializer.cs* con el código siguiente:
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  El código comprueba si hay alumnos en la base de datos. Si no hay ningún alumno, agrega datos de prueba a la base de datos. Crea los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.

* En *Program.cs*, reemplace la llamada a `EnsureCreated` con una llamada a `DbInitializer.Initialize`:

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.

---

* Reinicie la aplicación.

* Seleccione la página Students para ver los datos inicializados.

## <a name="view-the-database"></a>Consulta la base de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.
* En SSOX, seleccione **(localdb)\MSSQLLocalDB > Bases de datos > SchoolContext-{GUID}** . El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.
* Expanda el nodo **Tablas**.
* Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.
* Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver código** para ver cómo el modelo `Student` se asigna al esquema de tabla `Student`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Use la herramienta SQLite para ver el esquema de la base de datos y los datos inicializados. El archivo de base de datos se denomina *CU.db* y se encuentra en la carpeta del proyecto.

---

## <a name="asynchronous-code"></a>Código asincrónico

La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.

Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso. Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen. Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S. Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes. Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz y el servidor pueda administrar más tráfico sin retrasos.

El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución. En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.

En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* La palabra clave `async` indica al compilador que:
  * Genere devoluciones de llamada para partes del cuerpo del método.
  * Cree el objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que se devuelve.
* El tipo devuelto `Task<T>` representa el trabajo en curso.
* La palabra clave `await` hace que el compilador divida el método en dos partes. La primera parte termina con la operación que se inició de forma asincrónica. La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.
* `ToListAsync` es la versión asincrónica del método de extensión `ToList`.

Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:

* Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos. Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`. No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.
* Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para la paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.

Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="step-by-step"]
> [Siguiente tutorial](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En la aplicación web de ejemplo Contoso University se muestra cómo crear una aplicación web de Razor Pages de ASP.NET Core con Entity Framework (EF) Core.

La aplicación de ejemplo es un sitio web de una universidad ficticia, Contoso University. Incluye funciones como la admisión de estudiantes, la creación de cursos y asignaciones de instructores. Esta página es la primera de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo Contoso University.

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instrucciones de descarga](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Familiaridad con [Razor Pages](xref:razor-pages/index). Los programadores nuevos deben completar [Introducción a Razor Pages en ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start) antes de empezar esta serie.

## <a name="troubleshooting"></a>Solución de problemas

Si experimenta un problema que no puede resolver, por lo general podrá encontrar la solución si compara el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Una buena forma de obtener ayuda consiste en publicar una pregunta en [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) para [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>La aplicación web Contoso University

La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.

Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores. Estas son algunas de las pantallas que se crean en el tutorial.

![Página de índice de Students](intro/_static/students-index.png)

![Página de edición de estudiantes](intro/_static/student-edit.png)

El estilo de la interfaz de usuario de este sitio se mantiene fiel a lo que generan las plantillas integradas. El tutorial se centra en EF Core con Razor Pages, no en la interfaz de usuario.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Creación de la aplicación web de Razor Pages ContosoUniversity

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.
* Cree una aplicación web de ASP.NET Core. Asigne el nombre **ContosoUniversity** al proyecto. Es importante que el nombre del proyecto sea *ContosoUniversity* para que coincidan los espacios de nombres al copiar y pegar el código.
* Seleccione **ASP.NET Core 2.1** en la lista desplegable y, luego, **Aplicación web**.

Para ver las imágenes de los pasos anteriores, consulte [Creación de una aplicación web de Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Ejecutar la aplicación.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Configurar el estilo del sitio

Con algunos cambios se configura el menú del sitio, el diseño y la página principal. Actualice *Pages/Shared/_Layout.cshtml* con los cambios siguientes:

* Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University". Hay tres repeticiones.

* Agregue entradas de menú para **Students**, **Courses**, **Instructors** y **Departments**, y elimine la entrada de menú **Contact**.

Los cambios aparecen resaltados. (*No* se muestra todo el marcado).

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

En *Pages/Index.cshtml*, reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET y MVC con texto sobre esta aplicación:

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Crear el modelo de datos

Cree las clases de entidad para la aplicación Contoso University. Comience con las tres entidades siguientes:

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Hay una relación uno a varios entre las entidades `Student` y `Enrollment`. Hay una relación uno a varios entre las entidades `Course` y `Enrollment`. Un estudiante se puede inscribir en cualquier número de cursos. Un curso puede tener cualquier número de alumnos inscritos.

En las secciones siguientes, se crea una clase para cada una de estas entidades.

### <a name="the-student-entity"></a>La entidad Student

![Diagrama de la entidad Student](intro/_static/student-entity.png)

Cree una carpeta *Models*. En la carpeta *Models*, cree un archivo de clase denominado *Student.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos (DB) que corresponde a esta clase. De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`. En `classnameID`, `classname` es el nombre de la clase. En el ejemplo anterior, la clave principal alternativa que se reconoce de forma automática es `StudentID`.

La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships). Las propiedades de navegación se vinculan a otras entidades relacionadas con esta entidad. En este caso, la propiedad `Enrollments` de una `Student entity` contiene todas las entidades `Enrollment` que están relacionadas con esa entidad `Student`. Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades `Enrollment`. Una fila `Enrollment` relacionada es la que contiene el valor de clave principal de ese estudiante en la columna `StudentID`. Por ejemplo, suponga que el estudiante con ID=1 tiene dos filas en la tabla `Enrollment`. La tabla `Enrollment` tiene dos filas con `StudentID` = 1. `StudentID` es una clave externa en la tabla `Enrollment` que especifica el estudiante en la tabla `Student`.

Si una propiedad de navegación puede contener varias entidades, la propiedad de navegación debe ser un tipo de lista, como `ICollection<T>`. Se puede especificar `ICollection<T>`, o bien un tipo como `List<T>` o `HashSet<T>`. Cuando se usa `ICollection<T>`, EF Core crea una colección `HashSet<T>` de forma predeterminada. Las propiedades de navegación que contienen varias entidades proceden de relaciones de varios a varios y uno a varios.

### <a name="the-enrollment-entity"></a>La entidad Enrollment

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

En la carpeta *Models*, cree *Enrollment.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

La propiedad `EnrollmentID` es la clave principal. En esta entidad se usa el patrón `classnameID` en lugar de `ID` como en la entidad `Student`. Normalmente, los desarrolladores eligen un patrón y lo usan en todo el modelo de datos. En un tutorial posterior, se muestra el uso de ID sin un nombre de clase para facilitar la implementación de la herencia en el modelo de datos.

La propiedad `Grade` es una `enum`. El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade` acepta valores NULL. Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.

La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`. Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`. La entidad `Student` difiere de la propiedad de navegación `Student.Enrollments`, que contiene varias entidades `Enrollment`.

La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`. Una entidad `Enrollment` está asociada con una entidad `Course`.

EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`. Por ejemplo, `StudentID` para la propiedad de navegación `Student`, puesto que la clave principal de la entidad `Student` es `ID`. Las propiedades de clave externa también se pueden denominar `<primary key property name>`. Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.

### <a name="the-course-entity"></a>La entidad Course

![Diagrama de la entidad Course](intro/_static/course-entity.png)

En la carpeta *Models*, cree *Course.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

La propiedad `Enrollments` es una propiedad de navegación. Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.

El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.

## <a name="scaffold-the-student-model"></a>Aplicación de scaffolding al modelo de alumnos

En esta sección, se aplica scaffolding al modelo de alumnos. Es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de alumnos.

* Compile el proyecto.
* Cree la carpeta *Pages/Students*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Pages/Students* > **Agregar** > **Nuevo elemento con scaffold**.
* En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **AGREGAR**.

Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :

* En la lista desplegable **Clase de modelo**, seleccione **Student (ContosoUniversity.Models)** .
* En la fila **Clase de contexto de datos**, haga clic en el signo **+** (más) y cambie el nombre generado por **ContosoUniversity.Models.SchoolContext**.
* En la lista desplegable **Clase de contexto de datos**, seleccione **ContosoUniversity.Models.SchoolContext**
* Seleccione **Agregar**.

![Cuadro de diálogo CRUD](intro/_static/s1.png)

Si tiene algún problema con el paso anterior, consulte [Aplicar scaffolding al modelo de película](xref:tutorials/razor-pages/model#scaffold-the-movie-model).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ejecute los comandos siguientes para aplicar scaffolding al modelo de alumnos.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

El proceso de scaffolding ha creado y cambiado los archivos siguientes:

### <a name="files-created"></a>Archivos creados

* *Pages/Students* Create, Delete, Details, Edit, Index.
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>Actualizaciones de archivos

* *Startup.cs*: en la sección siguiente se detallan los cambios realizados en este archivo.
* *appsettings.json*: se agrega la cadena de conexión que se usa para conectarse a una base de datos local.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar el contexto registrado con la inserción de dependencias

ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection). Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor. El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.

La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.

Examine el método `ConfigureServices` de *Startup.cs*. El proveedor de scaffolding ha agregado la línea resaltada:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json*.

## <a name="update-main"></a>Actualización de main

En *Program.cs*, modifique el método `Main` para que haga lo siguiente:

* Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.
* Llame a [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Elimine el contexto cuando finalice el método `EnsureCreated`.

En el código siguiente se muestra el archivo *Program.cs* actualizado.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` garantiza la existencia de la base de datos para el contexto. Si existe, no se realiza ninguna acción. Si no existe, se crean la base de datos y todo su esquema. En `EnsureCreated` no se usan migraciones para crear la base de datos. Una base de datos que se cree con `EnsureCreated` no se podrá actualizar más adelante mediante las migraciones.

`EnsureCreated` se llama durante el inicio de la aplicación, lo que permite el flujo de trabajo siguiente:

* Se elimina la base de datos.
* Se cambia el esquema de base de datos (por ejemplo, se agrega un campo `EmailAddress`).
* Ejecute la aplicación.
* `EnsureCreated` crea una base de datos con la columna `EmailAddress`.

`EnsureCreated` es útil al principio del desarrollo, cuando el esquema evoluciona rápidamente. Más adelante, en el tutorial se elimina la base de datos y se usan las migraciones.

### <a name="test-the-app"></a>Prueba de la aplicación

Ejecute la aplicación y acepte la directiva de cookies. Esta aplicación no conserva información de carácter personal. Puede obtener más información sobre la directiva de cookies en [Compatibilidad con el Reglamento general de protección de datos (RGPD) de la UE](xref:security/gdpr).

* Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.
* Pruebe los vínculos Edit, Details y Delete.

## <a name="examine-the-schoolcontext-db-context"></a>Examinar el contexto de base de datos SchoolContext

La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos. El contexto de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos. En este proyecto, la clase se denomina `SchoolContext`.

Actualice *SchoolContext.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

El código resaltado crea una propiedad [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades. En la terminología de EF Core:

* Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.
* Una entidad se corresponde con una fila de la tabla.

`DbSet<Enrollment>` y `DbSet<Course>` se pueden omitir. EF Core las incluye implícitamente porque la entidad `Student` hace referencia a la entidad `Enrollment` y la entidad `Enrollment` hace referencia a la entidad `Course`. Para este tutorial, conserve `DbSet<Enrollment>` y `DbSet<Course>` en el `SchoolContext`.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción. LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja. De forma predeterminada, LocalDB crea archivos de base de datos *.mdf* en el directorio `C:/Users/<user>`.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Agregar código para inicializar la base de datos con datos de prueba

EF Core crea una base de datos vacía. En esta sección, se escribe un método `Initialize` para rellenarlo con datos de prueba.

En la carpeta *Data*, cree un archivo de clase denominado *DbInitializer.cs* y agregue el código siguiente:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Nota: El código anterior usa `Models` para el espacio de nombres (`namespace ContosoUniversity.Models`) en lugar de `Data`. `Models` es coherente con el código generado por el proveedor de scaffolding. Para obtener más información, consulte [este problema de scaffolding de GitHub](https://github.com/aspnet/Scaffolding/issues/822).

El código comprueba si hay estudiantes en la base de datos. Si no hay alumnos en la base de datos, se inicializa con datos de prueba. Carga los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.

El método `EnsureCreated` crea automáticamente la base de datos para el contexto de base de datos. Si la base de datos existe, `EnsureCreated` vuelve sin modificarla.

En *Program.cs*, modifique el método `Main` para que llame a `Initialize`:

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.

---

## <a name="view-the-db"></a>Ver la base de datos

El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID. Por lo tanto, el nombre de la base de datos será "SchoolContext-{GUID}". El GUID será diferente para cada usuario.
Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.
En SSOX, haga clic en **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .

Expanda el nodo **Tablas**.

Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.

## <a name="asynchronous-code"></a>Código asincrónico

La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.

Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso. Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen. Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S. Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes. Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz, y el servidor está habilitado para administrar más tráfico sin retrasos.

El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución. En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.

En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* La palabra clave `async` indica al compilador que:
  * Genere devoluciones de llamada para partes del cuerpo del método.
  * Cree automáticamente el objeto [Task](/dotnet/api/system.threading.tasks.task) que se devuelve. Para más información, vea [Tipo de valor devuelto Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* El tipo devuelto implícito `Task` representa el trabajo en curso.
* La palabra clave `await` hace que el compilador divida el método en dos partes. La primera parte termina con la operación que se inició de forma asincrónica. La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.
* `ToListAsync` es la versión asincrónica del método de extensión `ToList`.

Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:

* Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos. Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`. No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.
* Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.

Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).

En el siguiente tutorial, se examinan las operaciones CRUD (crear, leer, actualizar y eliminar) básicas.



## <a name="additional-resources"></a>Recursos adicionales

* [Versión en YouTube de este tutorial](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Siguiente](xref:data/ef-rp/crud)

::: moniker-end
