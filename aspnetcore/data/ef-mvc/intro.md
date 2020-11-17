---
title: 'Tutorial: Introducción a EF Core en una aplicación web de ASP.NET Core MVC'
description: Esta página es la primera de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo EF/MVC de Contoso University.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
ms.topic: tutorial
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 428320f9d706b0dd16ced68d183ec4b331451965
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550652"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Tutorial: Introducción a EF Core en una aplicación web de ASP.NET Core MVC

Por [Tom Dykstra](https://github.com/tdykstra) y [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

En la aplicación web de ejemplo de Contoso University se muestra cómo crear una aplicación web ASP.NET Core MVC con Entity Framework (EF) Core y Visual Studio.

La aplicación de ejemplo es un sitio web de una universidad ficticia, Contoso University. Incluye funciones como la admisión de estudiantes, la creación de cursos y asignaciones de instructores. Esta página es la primera de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo de Contoso University.

## <a name="prerequisites"></a>Requisitos previos

* Si no está familiarizado con ASP.NET Core MVC, consulte la serie de tutoriales [Introducción a ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) antes de iniciarlo.

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a>Motores de bases de datos

En las instrucciones de Visual Studio se usa [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versión de SQL Server Express que solo se ejecuta en Windows.

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a>Solución de problemas

Si experimenta un problema que no puede resolver, por lo general podrá encontrar la solución si compara el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples). Para obtener una lista de errores comunes y cómo resolverlos, vea [la sección de solución de problemas del último tutorial de la serie](advanced.md#common-errors). Si ahí no encuentra lo que necesita, puede publicar una pregunta en StackOverflow.com para [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Esta es una serie de 10 tutoriales y cada uno se basa en lo que se realiza en los anteriores. Considere la posibilidad de guardar una copia del proyecto después de completar correctamente cada tutorial. Después, si experimenta problemas, puede empezar desde el tutorial anterior en lugar de volver al principio de la serie completa.

## <a name="contoso-university-web-app"></a>Aplicación web Contoso University

La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.

Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores. Estas son algunas de las pantallas de la aplicación:

![Página de índice de Students](intro/_static/students-index.png)

![Página de edición de estudiantes](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Creación de una aplicación web

1. Inicie Visual Studio y seleccione **Crear un proyecto**.
1. En el cuadro de diálogo **Crear un proyecto**, seleccione **Aplicación web ASP.NET Core** > **Siguiente**.
1. En el cuadro de diálogo **Configurar su nuevo proyecto**, escriba `ContosoUniversity` en **Nombre del proyecto**. Es importante usar este nombre exacto, incluido el uso de mayúsculas, para que cada `namespace` coincida cuando se copie el código.
1. Seleccione **Crear**.
1. En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione:
    1. **.NET Core** y **ASP.NET Core 5.0** en los menús desplegables.
    1. **Aplicación web de ASP.NET Core (Modelo-Vista-Controlador)**
    1. **Crear**
      ![Cuadro de diálogo Nuevo proyecto de ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)

## <a name="set-up-the-site-style"></a>Configurar el estilo del sitio

Con algunos cambios básicos se configura el menú del sitio, el diseño y la página principal.

Abra *Views/Shared/_Layout.cshtml* y realice los cambios siguientes:

* Cambie cada aparición de `ContosoUniversity` a `Contoso University`. Hay tres repeticiones.
* Agregue entradas de menú para **About**, **Students**, **Courses**, **Instructors** y **Departments**, y elimine la entrada de menú **Privacy**.

Los cambios anteriores se resaltan en el código siguiente:

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

En *Views/Home/Index.cshtml*, reemplace el contenido del archivo por el marcado siguiente:

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

Presione CTRL+F5 para ejecutar el proyecto o seleccione **Depurar > Iniciar sin depurar** en el menú. La página principal se muestra con las pestañas de las páginas creadas en este tutorial.

![Página de inicio de Contoso University](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a>Paquetes NuGet de EF Core

En este tutorial se usa SQL Server y el paquete de proveedor es [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).

Este paquete de SQL Server de EF y sus dependencias (`Microsoft.EntityFrameworkCore` y `Microsoft.EntityFrameworkCore.Relational`) proporcionan compatibilidad en tiempo de ejecución para EF.

Agregue los paquetes NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) y [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore). En la consola del administrador de programas (PMC), escriba los siguientes comandos para agregar los paquetes NuGet:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

El paquete NuGet `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` proporciona middleware de ASP.NET Core para páginas de error de EF Core. Este middleware ayuda a detectar y diagnosticar errores con migraciones de EF Core.

Para obtener información sobre otros proveedores de base de datos disponibles para EF Core, vea [Proveedores de bases de datos](/ef/core/providers/).

## <a name="create-the-data-model"></a>Creación del modelo de datos

Se crean las siguientes clases de entidad para esta aplicación:

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Las entidades anteriores tienen las siguientes relaciones:

* Una relación uno a varios entre las entidades `Student` y `Enrollment`. Un alumno se puede inscribir en cualquier número de cursos.
* Una relación uno a varios entre las entidades `Course` y `Enrollment`. Un curso puede tener cualquier número de alumnos inscritos.

En las secciones siguientes, se crea una clase para cada una de estas entidades.

### <a name="the-student-entity"></a>La entidad Student

![Diagrama de la entidad Student](intro/_static/student-entity.png)

En la carpeta *Models*, cree la clase `Student` con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

La propiedad `ID` es la columna de clave principal (**PK**) de la tabla de base de datos que se corresponde a esta clase. De forma predeterminada, EF interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`. Por ejemplo, la clave principal podría tener el nombre `StudentID` en lugar de `ID`.

La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships). Las propiedades de navegación contienen otras entidades relacionadas con esta entidad. La propiedad `Enrollments` de una entidad `Student`:

* Contiene todas las entidades `Enrollment` que están relacionadas con esa entidad `Student`.
* Si una fila de `Student` específica en la base de datos tiene dos filas `Enrollment` relacionadas:
  * La propiedad de navegación `Enrollments` de esa entidad `Student` contiene esas dos entidades `Enrollment`.
  
Las filas `Enrollment` contienen el valor de clave principal del alumno en la columna de clave externa (**FK**) `StudentID`.

Si una propiedad de navegación puede contener varias entidades:

 * El tipo debe ser una lista, como `ICollection<T>`, `List<T>` o `HashSet<T>`.
 * Las entidades se pueden agregar, eliminar y actualizar.

Las relaciones de navegación de varios a varios y de uno a varios pueden contener varias entidades. Cuando se usa `ICollection<T>`, EF crea una colección `HashSet<T>` de forma predeterminada.

### <a name="the-enrollment-entity"></a>La entidad Enrollment

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

En la carpeta *Models*, cree la clase `Enrollment` con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

La propiedad `EnrollmentID` es la clave principal. Esta entidad usa el patrón `classnameID` en lugar de `ID` por sí misma. La entidad `Student` usó el patrón `ID`. Algunos desarrolladores prefieren usar un patrón en todo el modelo de datos. En este tutorial, la variación muestra que se puede usar cualquiera de los patrones. En un [tutorial posterior](inheritance.md), verá cómo el uso de `ID` sin un nombre de clase facilita la implementación de la herencia en el modelo de datos.

La propiedad `Grade` es una `enum`. El signo `?` después de la declaración de tipo `Grade` indica que la propiedad `Grade` [acepta valores NULL](/dotnet/csharp/language-reference/builtin-types/nullable-value-types). Un curso que sea `null` es diferente de un curso cero. `null` significa que no se conoce un curso o que todavía no se ha asignado.

La propiedad `StudentID` es una clave externa (FK) y la propiedad de navegación correspondiente es `Student`. Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad solo puede contener una entidad `Student`. Esto difiere de la propiedad de navegación `Student.Enrollments`, que contiene varias entidades `Enrollment`.

La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`. Una entidad `Enrollment` está asociada con una entidad `Course`.

Entity Framework interpreta una propiedad como una propiedad de clave externa si lleva por nombre `<`nombre de propiedad de navegación`><`nombre de propiedad de clave principal`>`. Por ejemplo, `StudentID` para la propiedad de navegación `Student`, puesto que la clave principal de la entidad `Student` es `ID`. Las propiedades de clave externa también pueden llevar por nombre `<`nombre de propiedad de clave principal`>`. Por ejemplo, `CourseID` porque la clave principal de la entidad `Course` es `CourseID`.

### <a name="the-course-entity"></a>La entidad Course

![Diagrama de la entidad Course](intro/_static/course-entity.png)

En la carpeta *Models*, cree la clase `Course` con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

La propiedad `Enrollments` es una propiedad de navegación. Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.

El atributo [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) se explica en un [tutorial posterior](complex-data-model.md). Este atributo permite especificar la clave principal para el curso en lugar de hacer que la base de datos la genere.

## <a name="create-the-database-context"></a>Crear el contexto de base de datos

La clase principal que coordina la funcionalidad de EF para un modelo de datos determinado es la clase de contexto de base de datos <xref:Microsoft.EntityFrameworkCore.DbContext>. Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`. En la clase derivada `DbContext` se especifica qué entidades se incluyen en el modelo de datos. Algunos comportamientos de EF se pueden personalizar. En este proyecto, la clase se denomina `SchoolContext`.

En la carpeta del proyecto, cree una carpeta denominada `Data`.

En la carpeta *Data*, cree una clase `SchoolContext` con el código siguiente:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

El código anterior crea una propiedad `DbSet` para el conjunto de entidades. En la terminología de EF:

* Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.
* Una entidad se corresponde con una fila de la tabla.

Se podrían haber omitido las instrucciones `DbSet<Enrollment>` y `DbSet<Course>`, y el funcionamiento sería el mismo. EF las incluiría implícitamente porque:

* La entidad `Student` hace referencia a la entidad `Enrollment`.
* La entidad `Enrollment` hace referencia a la entidad `Course`.

Cuando se crea la base de datos, EF crea las tablas con los mismos nombres que los nombres de propiedad `DbSet`. Los nombres de propiedad para las colecciones suelen ser plurales. Por ejemplo, `Students` en lugar de `Student`. Los desarrolladores están en desacuerdo sobre si los nombres de tabla deben ser plurales o no. Para estos tutoriales, se invalida el comportamiento predeterminado mediante la especificación de nombres de tabla en singular en `DbContext`. Para ello, agregue el código resaltado siguiente después de la última propiedad DbSet.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Registro de `SchoolContext`

ASP.NET Core incluye la [inserción de dependencias](../../fundamentals/dependency-injection.md). Los servicios (como el contexto de base de datos de EF) se registran con inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como los controladores MVC) a través de parámetros de constructor. Más adelante en este tutorial verá el código de constructor de controlador que obtiene una instancia de contexto.

Para registrar `SchoolContext` como servicio, abra *Startup.cs* y agregue las líneas resaltadas al método `ConfigureServices`.

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto `DbContextOptionsBuilder`. Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .

Abra el archivo *appsettings.json* y agregue una cadena de conexión como se muestra en el marcado siguiente:

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a>Incorporación del filtro de excepción de base de datos

Agregue <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> a `ConfigureServices`, tal como se muestra en el código siguiente:

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

`AddDatabaseDeveloperPageExceptionFilter` proporciona información de error útil en el [entorno de desarrollo](xref:fundamentals/environments).

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción. LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja. De forma predeterminada, LocalDB crea archivos de base de datos *.mdf* en el directorio `C:/Users/<user>`.

## <a name="initialize-db-with-test-data"></a>Inicializa la base de datos con datos de prueba

EF crea una base de datos vacía. En esta sección, se agrega un método al que se llama después de crear la base de datos para rellenarla con datos de prueba.

El método `EnsureCreated` se usa para crear automáticamente la base de datos. En un [tutorial posterior](migrations.md), verá cómo controlar los cambios en el modelo mediante Migraciones de Code First para cambiar el esquema de base de datos en lugar de quitar y volver a crear la base de datos.

En la carpeta *Data*, cree una nueva clase denominada `DbInitializer` con el código siguiente:

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

El código anterior comprueba si existe la base de datos:

* Si no se encuentra la base de datos,
  * se crea y se carga con datos de prueba. Carga los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.
* Si la base de datos se encuentra, no se realiza ninguna acción.

Actualice *Program.cs* con el siguiente código:

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

*Program.cs* hace lo siguiente en el inicio de la aplicación:

* Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.
* Llame al método `DbInitializer.Initialize`.
* Elimine el contexto cuando se complete el método `Initialize` como se muestra en el código siguiente:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

La primera vez que se ejecuta la aplicación, se crea la base de datos y se carga con datos de prueba. Cada vez que los datos del modelo cambian:

* Se elimina la base de datos.
* Actualice el método de inicialización e inicie desde cero con una base de datos nueva.

 En los tutoriales posteriores, la base de datos se modifica cuando cambia el modelo de datos, sin tener que eliminarla y volver a crearla. No se pierden datos cuando cambia el modelo de datos.

## <a name="create-controller-and-views"></a>Crea un controlador y vistas

Use el motor de scaffolding de Visual Studio para agregar un controlador y vistas de MVC que usará EF para consultar y guardar los datos.

La creación automática de vistas y métodos de acción [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) se conoce como scaffolding.

* En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta `Controllers` y seleccione **Agregar > Nuevo elemento con scaffold**.
* En el cuadro de diálogo **Agregar scaffold**:
  * Seleccione **Controlador de MVC con vistas que usan Entity Framework**.
  * Haga clic en **Agregar**. Aparece el cuadro de diálogo **Agregar un controlador de MVC con vistas que usan Entity Framework**: ![Scaffolding de Student](intro/_static/scaffold-student2.png)
  * En **Clase de modelo** seleccione **Student**.
  * En **Clase de contexto de datos** seleccione **SchoolContext**.
  * Acepte el valor predeterminado **StudentsController** como el nombre.
  * Haga clic en **Agregar**.

El motor de scaffolding de Visual Studio crea un archivo `StudentsController.cs` y un conjunto de vistas (archivos `*.cshtml`) que funcionan con el controlador.

Observe que el controlador toma `SchoolContext` como parámetro de constructor.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

La inserción de dependencias de ASP.NET Core se encarga de pasar una instancia de `SchoolContext` al controlador. Lo configuró en la clase `Startup`.

El controlador contiene un método de acción `Index`, que muestra todos los alumnos en la base de datos. El método obtiene una lista de estudiantes de la entidad Students, que se establece leyendo la propiedad `Students` de la instancia del contexto de base de datos:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Más adelante en el tutorial obtendrá información sobre los elementos de programación asincrónicos de este código.

En la vista *Views/Students/Index.cshtml* se muestra esta lista en una tabla:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Presione CTRL+F5 para ejecutar el proyecto o seleccione **Depurar > Iniciar sin depurar** en el menú.

Haga clic en la pestaña Students para ver los datos de prueba insertados por el método `DbInitializer.Initialize`. En función del ancho de la ventana del explorador, verá el vínculo de la pestaña `Students` en la parte superior de la página o tendrá que hacer clic en el icono de navegación en la esquina superior derecha para verlo.

![Página de inicio estrecha de Contoso University](intro/_static/home-page-narrow.png)

![Página de índice de Students](intro/_static/students-index.png)

## <a name="view-the-database"></a>Consulta la base de datos

Cuando se inicia la aplicación, el método `DbInitializer.Initialize` llama a `EnsureCreated`. EF observó que no había ninguna base de datos:

* Así que creó una base de datos.
* El código del método `Initialize` ha rellenado la base de datos con datos.

Use el **Explorador de objetos de SQL Server** (SSOX) para ver la base de datos en Visual Studio:

* Seleccione el **Explorador de objetos de SQL Server** desde el menú **Vista** en Visual Studio.
* En SSOX, seleccione **(localdb)\MSSQLLocalDB > Bases de datos**.
* Seleccione `ContosoUniversity1`, la entrada del nombre de la base de datos que se encuentra en la cadena de conexión en el archivo *appsettings.json* .
* Expanda el nodo **Tablas** para ver las tablas de la base de datos.

![Tablas en SSOX](intro/_static/ssox-tables.png)

Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver los datos de la tabla.

![Tabla de estudiantes en SSOX](intro/_static/ssox-student-table.png)

Los archivos de base de datos `*.mdf` y `*.ldf` se encuentran en la carpeta *C:\Usuarios\\\<username>* .

Dado que se llama a `EnsureCreated` en el método de inicializador que se ejecuta en el inicio de la aplicación, puede:

* Hacer un cambio en la clase `Student`.
* Se elimina la base de datos.
* Detenga e inicie la aplicación. La base de datos se vuelve a crear automáticamente para coincidir con el cambio.

Por ejemplo, si se agrega una propiedad `EmailAddress` a la clase `Student`, una nueva columna `EmailAddress` en la tabla que se ha vuelto a crear. La vista con clase no mostrará la nueva propiedad `EmailAddress`.

## <a name="conventions"></a>Convenciones

La cantidad de código que se escribe para que EF cree una base de datos completa es mínima debido al uso que hace EF de las convenciones:

* Los nombres de las propiedades `DbSet` se usan como nombres de tabla. Para las entidades a las que no se hace referencia con una propiedad `DbSet`, los nombres de clase de entidad se usan como nombres de tabla.
* Los nombres de propiedad de entidad se usan para los nombres de columna.
* Las propiedades de entidad que se denominan `ID` o `classnameID` se reconocen como propiedades de clave principal.
* Una propiedad se interpreta como una propiedad de clave externa si lleva por nombre `<`nombre de propiedad de navegación`><`nombre de propiedad de clave principal`>`. Por ejemplo, `StudentID` para la propiedad de navegación `Student`, puesto que la clave principal de la entidad `Student` es `ID`. Las propiedades clave externa también pueden llevar por nombre `<`nombre de propiedad de clave principal`>`. Por ejemplo `EnrollmentID`, dado que la clave principal de la entidad `Enrollment` es `EnrollmentID`.

El comportamiento de las convenciones se puede reemplazar. Por ejemplo, los nombres de tabla se pueden especificar explícitamente, como se mostró anteriormente en este tutorial. Los nombres de columna y cualquier propiedad se pueden establecer como clave principal o clave externa.

## <a name="asynchronous-code"></a>Código asincrónico

La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.

Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso. Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen. Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S. Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes. Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz, y el servidor está habilitado para administrar más tráfico sin retrasos.

El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución, pero para situaciones de poco tráfico la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.

En el código siguiente, `async`, `Task<T>`, `await` y `ToListAsync` hacen que el código se ejecute de forma asincrónica.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* La palabra clave `async` indica al compilador que genere devoluciones de llamada para partes del cuerpo del método y que cree automáticamente el objeto `Task<IActionResult>` que se devuelve.
* El tipo de valor devuelto `Task<IActionResult>` representa el trabajo en curso con un resultado de tipo `IActionResult`.
* La palabra clave `await` hace que el compilador divida el método en dos partes. La primera parte termina con la operación que se inició de forma asincrónica. La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.
* `ToListAsync` es la versión asincrónica del método de extensión `ToList`.

Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF son los siguientes:

* Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos. Eso incluye, por ejemplo, `ToListAsync`, `SingleOrDefaultAsync` y `SaveChangesAsync`. No incluye, por ejemplo, instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contexto de EF no es seguro para subprocesos: no intente realizar varias operaciones en paralelo. Cuando llame a cualquier método asincrónico de EF, use siempre la palabra clave `await`.
* Para aprovechar las ventajas de rendimiento del código asincrónico, asegúrese de que en los paquetes de biblioteca que use también se usa async si llaman a cualquier método de EF que haga que las consultas se envíen a la base de datos.

Para obtener más información sobre la programación asincrónica en .NET, vea [Información general de Async](/dotnet/articles/standard/async).

## <a name="limit-entities-fetched"></a>Limitación de las entidades capturadas

Consulte [Consideraciones de rendimiento](xref:data/ef-rp/intro) para obtener información sobre cómo limitar el número o las entidades devueltas por una consulta.

Pase al tutorial siguiente para obtener información sobre cómo realizar operaciones CRUD (crear, leer, actualizar y eliminar) básicas.

> [!div class="nextstepaction"]
> [Implementación de la funcionalidad CRUD básica](crud.md)

::: moniker-end

::: moniker range="<= aspnetcore-3.1"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

En la aplicación web de ejemplo Contoso University se muestra cómo crear aplicaciones web de ASP.NET Core 2.2 MVC con Entity Framework (EF) Core 2.2 y Visual Studio 2017 o 2019.

Este tutorial no se ha actualizado para ASP.NET Core 3.1. Se ha actualizado para [ASP.NET Core 5.0](xref:data/ef-mvc/intro?view=aspnetcore-5.0).

La aplicación de ejemplo es un sitio web de una universidad ficticia, Contoso University. Incluye funciones como la admisión de estudiantes, la creación de cursos y asignaciones de instructores. Este es el primero de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo Contoso University desde el principio.

## <a name="prerequisites"></a>Requisitos previos

* [SDK de .NET Core 2.2](https://dotnet.microsoft.com/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con las cargas de trabajo siguientes:
  * Carga de trabajo de **ASP.NET y desarrollo web**
  * Carga de trabajo **Desarrollo multiplataforma de .NET Core**

## <a name="troubleshooting"></a>Solución de problemas

Si experimenta un problema que no puede resolver, por lo general podrá encontrar la solución si compara el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples). Para obtener una lista de errores comunes y cómo resolverlos, vea [la sección de solución de problemas del último tutorial de la serie](advanced.md#common-errors). Si ahí no encuentra lo que necesita, puede publicar una pregunta en StackOverflow.com para [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Esta es una serie de 10 tutoriales y cada uno se basa en lo que se realiza en los anteriores. Considere la posibilidad de guardar una copia del proyecto después de completar correctamente cada tutorial. Después, si experimenta problemas, puede empezar desde el tutorial anterior en lugar de volver al principio de la serie completa.

## <a name="contoso-university-web-app"></a>Aplicación web Contoso University

La aplicación que se va a compilar en estos tutoriales es un sitio web sencillo de una universidad.

Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores. A continuación se muestran algunas de las pantallas que se van a crear.

![Página de índice de Students](intro/_static/students-index.png)

![Página de edición de estudiantes](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Creación de una aplicación web

* Abra Visual Studio.

* En el menú **Archivo**, seleccione **Nuevo > Proyecto**.

* En el panel de la izquierda, seleccione **Instalado > Visual C# > Web**.

* Seleccione la plantilla de proyecto **Aplicación web ASP.NET Core**.

* Escriba **ContosoUniversity** como el nombre y haga clic en **Aceptar**.

  ![Cuadro de diálogo Nuevo proyecto](intro/_static/new-project2.png)

* Espere que aparezca el cuadro de diálogo **Nueva aplicación web ASP.NET Core**.

* Seleccione **.NET Core**, **ASP.NET Core 2.2** y la plantilla **Aplicación web (controlador de vista de modelos)** .

* Asegúrese de que **Autenticación** esté establecida en **Sin autenticación**.

* Seleccione **Aceptar**.

  ![Cuadro de diálogo Nuevo proyecto ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Configurar el estilo del sitio

Con algunos cambios sencillos se configura el menú del sitio, el diseño y la página principal.

Abra *Views/Shared/_Layout.cshtml* y realice los cambios siguientes:

* Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University". Hay tres repeticiones.

* Agregue entradas de menú para **About**, **Students**, **Courses**, **Instructors** y **Departments**, y elimine la entrada de menú **Privacy**.

Los cambios aparecen resaltados.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

En *Views/Home/Index.cshtml*, reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET y MVC con texto sobre esta aplicación:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Presione CTRL+F5 para ejecutar el proyecto o seleccione **Depurar > Iniciar sin depurar** en el menú. Verá la página principal con pestañas para las páginas que se crearán en estos tutoriales.

![Página de inicio de Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>Acerca de los paquetes NuGet de EF Core

Para agregar compatibilidad con EF Core a un proyecto, instale el proveedor de base de datos que quiera tener como destino. En este tutorial se usa SQL Server y el paquete de proveedor es [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Este paquete se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), por lo que no es necesario hacer referencia al paquete.

Este paquete de SQL Server de EF y sus dependencias (`Microsoft.EntityFrameworkCore` y `Microsoft.EntityFrameworkCore.Relational`) proporcionan compatibilidad en tiempo de ejecución para EF. Más adelante, en el tutorial [Migraciones](migrations.md), agregará un paquete de herramientas.

Para obtener información sobre otros proveedores de base de datos disponibles para Entity Framework Core, vea [Proveedores de bases de datos](/ef/core/providers/).

## <a name="create-the-data-model"></a>Crear el modelo de datos

A continuación podrá crear las clases de entidad para la aplicación Contoso University. Empezará por las tres siguientes entidades.

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Hay una relación uno a varios entre las entidades `Student` y `Enrollment`, y también entre las entidades `Course` y `Enrollment`. En otras palabras, un estudiante se puede inscribir en cualquier número de cursos y un curso puede tener cualquier número de alumnos inscritos.

En las secciones siguientes creará una clase para cada una de estas entidades.

### <a name="the-student-entity"></a>La entidad Student

![Diagrama de la entidad Student](intro/_static/student-entity.png)

En la carpeta *Models*, cree un archivo de clase denominado *Student.cs* y reemplace el código de plantilla con el código siguiente.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

La propiedad `ID` se convertirá en la columna de clave principal de la tabla de base de datos que corresponde a esta clase. De forma predeterminada, Entity Framework interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.

La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships). Las propiedades de navegación contienen otras entidades relacionadas con esta entidad. En este caso, la propiedad `Enrollments` de una `Student entity` contendrá todas las entidades `Enrollment` que estén relacionadas con esa entidad `Student`. En otras palabras, si una fila `Student` determinada en la base de datos tiene dos filas `Enrollment` relacionadas (filas que contienen el valor de clave principal de ese estudiante en la columna de clave externa StudentID), la propiedad de navegación `Student` de esa entidad `Enrollments` contendrá esas dos entidades `Enrollment`.

Si una propiedad de navegación puede contener varias entidades (como en las relaciones de varios a varios o uno a varios), su tipo debe ser una lista a la que se puedan agregar las entradas, eliminarlas y actualizarlas, como `ICollection<T>`. Puede especificar `ICollection<T>` o un tipo como `List<T>` o `HashSet<T>`. Si especifica `ICollection<T>`, EF crea una colección `HashSet<T>` de forma predeterminada.

### <a name="the-enrollment-entity"></a>La entidad Enrollment

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

En la carpeta *Models*, cree *Enrollment.cs* y reemplace el código existente con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

La propiedad `EnrollmentID` será la clave principal; esta entidad usa el patrón `classnameID` en lugar de `ID` por sí solo, como se vio en la entidad `Student`. Normalmente debería elegir un patrón y usarlo en todo el modelo de datos. En este caso, la variación muestra que se puede usar cualquiera de los patrones. En un [tutorial posterior](inheritance.md), verá cómo el uso de ID sin un nombre de clase facilita la implementación de la herencia en el modelo de datos.

La propiedad `Grade` es una `enum`. El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade` acepta valores NULL. Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.

La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`. Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad solo puede contener un única entidad `Student` (a diferencia de la propiedad de navegación `Student.Enrollments` que se vio anteriormente, que puede contener varias entidades `Enrollment`).

La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`. Una entidad `Enrollment` está asociada con una entidad `Course`.

Entity Framework interpreta una propiedad como propiedad de clave externa si se denomina `<navigation property name><primary key property name>` (por ejemplo `StudentID` para la propiedad de navegación `Student`, dado que la clave principal de la entidad `Student` es `ID`). Las propiedades de clave externa también se pueden denominar simplemente `<primary key property name>` (por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`).

### <a name="the-course-entity"></a>La entidad Course

![Diagrama de la entidad Course](intro/_static/course-entity.png)

En la carpeta *Models*, cree *Course.cs* y reemplace el código existente con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

La propiedad `Enrollments` es una propiedad de navegación. Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.

En un [tutorial posterior](complex-data-model.md) de esta serie se incluirá más información sobre el atributo `DatabaseGenerated`. Básicamente, este atributo permite escribir la clave principal para el curso en lugar de hacer que la base de datos lo genere.

## <a name="create-the-database-context"></a>Crear el contexto de base de datos

La clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos determinado es la clase de contexto de base de datos. Esta clase se crea al derivar de la clase `Microsoft.EntityFrameworkCore.DbContext`. En el código se especifica qué entidades se incluyen en el modelo de datos. También se puede personalizar determinado comportamiento de Entity Framework. En este proyecto, la clase se denomina `SchoolContext`.

En la carpeta del proyecto, cree una carpeta denominada *Data*.

En la carpeta *Data*, cree un archivo de clase denominado *SchoolContext.cs* y reemplace el código de plantilla con el código siguiente:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Este código crea una propiedad `DbSet` para cada conjunto de entidades. En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.

Se podrían haber omitido las instrucciones `DbSet<Enrollment>` y `DbSet<Course>`, y el funcionamiento sería el mismo. Entity Framework las incluiría implícitamente porque la entidad `Student` hace referencia a la entidad `Enrollment` y la entidad `Enrollment` hace referencia a la entidad `Course`.

Cuando se crea la base de datos, EF crea las tablas con los mismos nombres que los nombres de propiedad `DbSet`. Los nombres de propiedad para las colecciones normalmente están en plural (Students en lugar de Student), pero los desarrolladores no están de acuerdo sobre si los nombres de tabla deben estar en plural o no. Para estos tutoriales, se invalidará el comportamiento predeterminado mediante la especificación de nombres de tabla en singular en DbContext. Para ello, agregue el código resaltado siguiente después de la última propiedad DbSet.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

Compile el proyecto para comprobar si hay errores del compilador.

## <a name="register-the-schoolcontext"></a>Registra SchoolContext

ASP.NET Core implementa la [inserción de dependencias](../../fundamentals/dependency-injection.md) de forma predeterminada. Los servicios (como el contexto de base de datos de EF) se registran con inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como los controladores MVC) a través de parámetros de constructor. Más adelante en este tutorial verá el código de constructor de controlador que obtiene una instancia de contexto.

Para registrar `SchoolContext` como servicio, abra *Startup.cs* y agregue las líneas resaltadas al método `ConfigureServices`.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto `DbContextOptionsBuilder`. Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .

Agregue instrucciones `using` para los espacios de nombres `ContosoUniversity.Data` y `Microsoft.EntityFrameworkCore`, y después compile el proyecto.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Abra el archivo *appsettings.json* y agregue una cadena de conexión como se muestra en el ejemplo siguiente.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

La cadena de conexión especifica una base de datos de SQL Server LocalDB. LocalDB es una versión ligera del motor de base de datos de SQL Server Express que está dirigida al desarrollo de aplicaciones, no al uso en producción. LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja. De forma predeterminada, LocalDB crea archivos de base de datos *.mdf* en el directorio `C:/Users/<user>`.

## <a name="initialize-db-with-test-data"></a>Inicializa la base de datos con datos de prueba

Entity Framework creará una base de datos vacía por usted. En esta sección, escribirá un método que se llama después de crear la base de datos para rellenarla con datos de prueba.

Aquí usará el método `EnsureCreated` para crear automáticamente la base de datos. En un [tutorial posterior](migrations.md), verá cómo controlar los cambios en el modelo mediante Migraciones de Code First para cambiar el esquema de base de datos en lugar de quitar y volver a crear la base de datos.

En la carpeta *Data*, cree un archivo de clase denominado *DbInitializer.cs* y reemplace el código de plantilla con el código siguiente, que hace que se cree una base de datos cuando es necesario y carga datos de prueba en la nueva base de datos.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

El código comprueba si hay estudiantes en la base de datos, y si no es así, asume que la base de datos es nueva y debe inicializarse con datos de prueba. Carga los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.

En *Program.cs*, modifique el método `Main` para que haga lo siguiente al iniciar la aplicación:

* Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.
* Llamar al método de inicialización, pasándolo al contexto.
* Eliminar el contexto cuando el método de inicialización haya finalizado.

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

La primera vez que ejecute la aplicación, se creará la base de datos y se inicializará con datos de prueba. Siempre que cambie el modelo de datos:

 * Se elimina la base de datos.
 * Actualice el método de inicialización e inicie desde cero con una base de datos nueva del mismo modo.
 
En los tutoriales posteriores, verá cómo modificar la base de datos cuando cambie el modelo de datos, sin tener que eliminarla y volver a crearla.

## <a name="create-controller-and-views"></a>Crea un controlador y vistas

En esta sección, se usa el motor de scaffolding de Visual Studio para agregar un controlador y vistas de MVC que usarán EF para consultar y guardar los datos.

La creación automática de vistas y métodos de acción CRUD se conoce como scaffolding. El scaffolding difiere de la generación de código en que el código con scaffolding es un punto de partida que se puede modificar para satisfacer sus propias necesidades, mientras que el código generado normalmente no se modifica. Cuando tenga que personalizar código generado, use clases parciales o regenere el código cuando se produzcan cambios.

* Haga clic con el botón derecho en la carpeta **Controladores** en el **Explorador de soluciones** y seleccione **Agregar > Nuevo elemento con scaffold**.
* En el cuadro de diálogo **Agregar scaffold**:
  * Seleccione **Controlador de MVC con vistas que usan Entity Framework**.
  * Haga clic en **Agregar**. Aparece el cuadro de diálogo **Agregar un controlador de MVC con vistas que usan Entity Framework**: ![Scaffolding de Student](intro/_static/scaffold-student2.png)
  * En **Clase de modelo** seleccione **Student**.
  * En **Clase de contexto de datos** seleccione **SchoolContext**.
  * Acepte el valor predeterminado **StudentsController** como el nombre.
  * Haga clic en **Agregar**.

El motor de scaffolding de Visual Studio crea un archivo *StudentsController.cs* y un conjunto de vistas (archivos *.cshtml*) que funcionan con el controlador.

Observe que el controlador toma `SchoolContext` como parámetro de constructor.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

La inserción de dependencias de ASP.NET Core se encarga de pasar una instancia de `SchoolContext` al controlador. Eso se configuró en el archivo *Startup.cs*.

El controlador contiene un método de acción `Index`, que muestra todos los alumnos en la base de datos. El método obtiene una lista de estudiantes de la entidad Students, que se establece leyendo la propiedad `Students` de la instancia del contexto de base de datos:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Más adelante en el tutorial obtendrá información sobre los elementos de programación asincrónicos de este código.

En la vista *Views/Students/Index.cshtml* se muestra esta lista en una tabla:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Presione CTRL+F5 para ejecutar el proyecto o seleccione **Depurar > Iniciar sin depurar** en el menú.

Haga clic en la pestaña Students para ver los datos de prueba insertados por el método `DbInitializer.Initialize`. En función del ancho de la ventana del explorador, verá el vínculo de la pestaña `Students` en la parte superior de la página o tendrá que hacer clic en el icono de navegación en la esquina superior derecha para verlo.

![Página de inicio estrecha de Contoso University](intro/_static/home-page-narrow.png)

![Página de índice de Students](intro/_static/students-index.png)

## <a name="view-the-database"></a>Consulta la base de datos

Al iniciar la aplicación, el método `DbInitializer.Initialize` llama a `EnsureCreated`. EF comprobó que no había ninguna base de datos y creó una, y después el resto del código del método `Initialize` la rellenó con datos. Puede usar el **Explorador de objetos de SQL Server** (SSOX) para ver la base de datos en Visual Studio.

Cierre el explorador.

Si la ventana de SSOX no está abierta, selecciónela en el menú **Vista** de Visual Studio.

En SSOX, haga clic en **(localdb)\MSSQLLocalDB > Databases** y después en la entrada del nombre de base de datos que se encuentra en la cadena de conexión del archivo *appsettings.json* .

Expanda el nodo **Tablas** para ver las tablas de la base de datos.

![Tablas en SSOX](intro/_static/ssox-tables.png)

Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.

![Tabla de estudiantes en SSOX](intro/_static/ssox-student-table.png)

Los archivos de base de datos *.mdf* y *.ldf* se encuentran en la carpeta *C:\Usuarios\\\<username>* .

Como se está llamando a `EnsureCreated` en el método de inicializador que se ejecuta al iniciar la aplicación, ahora podría realizar un cambio en la clase `Student`, eliminar la base de datos, volver a ejecutar la aplicación y la base de datos se volvería a crear de forma automática para que coincida con el cambio. Por ejemplo, si agrega una propiedad `EmailAddress` a la clase `Student`, verá una columna `EmailAddress` nueva en la tabla que se ha vuelto a crear.

## <a name="conventions"></a>Convenciones

La cantidad de código que tendría que escribir para que Entity Framework pudiera crear una base de datos completa para usted es mínima debido al uso de convenciones o las suposiciones que hace Entity Framework.

* Los nombres de las propiedades `DbSet` se usan como nombres de tabla. Para las entidades a las que no se hace referencia con una propiedad `DbSet`, los nombres de clase de entidad se usan como nombres de tabla.
* Los nombres de propiedad de entidad se usan para los nombres de columna.
* Las propiedades de entidad que se denominan ID o classnameID se reconocen como propiedades de clave principal.
* Una propiedad se interpreta como propiedad de clave externa si se denomina *\<navigation property name>\<primary key property name>* (por ejemplo, `StudentID` para la propiedad de navegación `Student`, dado que la clave principal de la entidad `Student` es `ID`). Las propiedades de clave externa también se pueden denominar simplemente *\<primary key property name>* (por ejemplo `EnrollmentID`, dado que la clave principal de la entidad `Enrollment` es `EnrollmentID`).

El comportamiento de las convenciones se puede reemplazar. Por ejemplo, puede especificar explícitamente los nombres de tabla, como se vio anteriormente en este tutorial. Y puede establecer los nombres de columna y cualquier propiedad como clave principal o clave externa, como verá en un [tutorial posterior](complex-data-model.md) de esta serie.

## <a name="asynchronous-code"></a>Código asincrónico

La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.

Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso. Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen. Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S. Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes. Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz, y el servidor está habilitado para administrar más tráfico sin retrasos.

El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución, pero para situaciones de poco tráfico la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.

En el código siguiente, la palabra clave `async`, el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* La palabra clave `async` indica al compilador que genere devoluciones de llamada para partes del cuerpo del método y que cree automáticamente el objeto `Task<IActionResult>` que se devuelve.
* El tipo de valor devuelto `Task<IActionResult>` representa el trabajo en curso con un resultado de tipo `IActionResult`.
* La palabra clave `await` hace que el compilador divida el método en dos partes. La primera parte termina con la operación que se inició de forma asincrónica. La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.
* `ToListAsync` es la versión asincrónica del método de extensión `ToList`.

Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa Entity Framework son los siguientes:

* Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos. Eso incluye, por ejemplo, `ToListAsync`, `SingleOrDefaultAsync` y `SaveChangesAsync`. No incluye, por ejemplo, instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contexto de EF no es seguro para subprocesos: no intente realizar varias operaciones en paralelo. Cuando llame a cualquier método asincrónico de EF, use siempre la palabra clave `await`.
* Si quiere aprovechar las ventajas de rendimiento del código asincrónico, asegúrese de que en los paquetes de biblioteca que use (por ejemplo para paginación), también se usa async si llaman a cualquier método de Entity Framework que haga que las consultas se envíen a la base de datos.

Para obtener más información sobre la programación asincrónica en .NET, vea [Información general de Async](/dotnet/articles/standard/async).

## <a name="next-steps"></a>Pasos siguientes

Pase al tutorial siguiente para obtener información sobre cómo realizar operaciones CRUD (crear, leer, actualizar y eliminar) básicas.

> [!div class="nextstepaction"]
> [Implementación de la funcionalidad CRUD básica](crud.md)

::: moniker-end
