---
title: 'Tutorial: Introducción a EF Core en una aplicación web de ASP.NET Core MVC'
description: Este es el primero de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo Contoso University desde el principio.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
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
uid: data/ef-mvc/intro
ms.openlocfilehash: e081c13f9ffb33c1ff137cb0989e747d51571ea7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629202"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Tutorial: Introducción a EF Core en una aplicación web de ASP.NET Core MVC

Este tutorial **no** se ha actualizado para ASP.NET Core 3.0. La [versión de Razor Pages](xref:data/ef-rp/intro) se ha actualizado. La mayoría de los cambios de código para ASP.NET Core 3.0 y la versión posterior de este tutorial:

* Están en los archivos *Startup.cs* y *Program.cs*.
* Se pueden encontrar en la [versión de Razor Pages](xref:data/ef-rp/intro). 

Para información sobre cuándo podría actualizarse, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/13920).

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

En la aplicación web de ejemplo Contoso University se muestra cómo crear aplicaciones web de ASP.NET Core 2.2 MVC con Entity Framework (EF) Core 2.2 y Visual Studio 2017 o 2019.

La aplicación de ejemplo es un sitio web de una universidad ficticia, Contoso University. Incluye funciones como la admisión de estudiantes, la creación de cursos y asignaciones de instructores. Este es el primero de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo Contoso University desde el principio.

En este tutorial ha:

> [!div class="checklist"]
> * Creación de una aplicación web de ASP.NET Core MVC
> * Configurar el estilo del sitio
> * Obtiene información sobre los paquetes NuGet de EF Core
> * Crear el modelo de datos
> * Crear el contexto de base de datos
> * Registrar el contexto para la inserción de dependencias
> * Inicializar la base de datos con datos de prueba
> * Crear un controlador y vistas
> * Consulta la base de datos

## <a name="prerequisites"></a>Requisitos previos

* [SDK de .NET Core 2.2](https://dotnet.microsoft.com/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con las cargas de trabajo siguientes:
  * Carga de trabajo de **ASP.NET y desarrollo web**
  * Carga de trabajo **Desarrollo multiplataforma de .NET Core**

## <a name="troubleshooting"></a>Solución de problemas

Si experimenta un problema que no puede resolver, por lo general podrá encontrar la solución si compara el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final). Para obtener una lista de errores comunes y cómo resolverlos, vea [la sección de solución de problemas del último tutorial de la serie](advanced.md#common-errors). Si ahí no encuentra lo que necesita, puede publicar una pregunta en StackOverflow.com para [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

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

La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships). Las propiedades de navegación contienen otras entidades relacionadas con esta entidad. En este caso, la propiedad `Enrollments` de una `Student entity` contendrá todas las entidades `Enrollment` que estén relacionadas con esa entidad `Student`. En otras palabras, si una fila Student determinada en la base de datos tiene dos filas Enrollment relacionadas (filas que contienen el valor de clave principal de ese estudiante en la columna de clave externa StudentID), la propiedad de navegación `Enrollments` de esa entidad `Student` contendrá esas dos entidades `Enrollment`.

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

## <a name="register-the-schoolcontext"></a>Registra SchoolContext

ASP.NET Core implementa la [inserción de dependencias](../../fundamentals/dependency-injection.md) de forma predeterminada. Los servicios (como el contexto de base de datos de EF) se registran con inserción de dependencias durante el inicio de la aplicación. Estos servicios se proporcionan a los componentes que los necesitan (como los controladores MVC) a través de parámetros de constructor. Más adelante en este tutorial verá el código de constructor de controlador que obtiene una instancia de contexto.

Para registrar `SchoolContext` como servicio, abra *Startup.cs* y agregue las líneas resaltadas al método `ConfigureServices`.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto `DbContextOptionsBuilder`. Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json*.

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

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Agregue instrucciones `using`:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

En los tutoriales anteriores, es posible que vea código similar en el método `Configure` de *Startup.cs*. Se recomienda usar el método `Configure` solo para configurar la canalización de solicitudes. El código de inicio de la aplicación pertenece al método `Main`.

Ahora, la primera vez que ejecute la aplicación, se creará la base de datos y se inicializará con datos de prueba. Cada vez que cambie el modelo de datos, puede eliminar la base de datos, actualizar el método de inicialización y comenzar desde cero con una base de datos nueva del mismo modo. En los tutoriales posteriores, verá cómo modificar la base de datos cuando cambie el modelo de datos, sin tener que eliminarla y volver a crearla.

## <a name="create-controller-and-views"></a>Crea un controlador y vistas

A continuación, usará el motor de scaffolding de Visual Studio para agregar un controlador y vistas de MVC que usarán EF para consultar y guardar los datos.

La creación automática de vistas y métodos de acción CRUD se conoce como scaffolding. El scaffolding difiere de la generación de código en que el código con scaffolding es un punto de partida que se puede modificar para satisfacer sus propias necesidades, mientras que el código generado normalmente no se modifica. Cuando tenga que personalizar código generado, use clases parciales o regenere el código cuando se produzcan cambios.

* Haga clic con el botón derecho en la carpeta **Controladores** en el **Explorador de soluciones** y seleccione **Agregar > Nuevo elemento con scaffold**.

* En el cuadro de diálogo **Agregar scaffold**:

  * Seleccione **Controlador de MVC con vistas que usan Entity Framework**.

  * Haga clic en **Agregar**. Aparece el cuadro de diálogo **Agregar un controlador de MVC con vistas que usan Entity Framework**.

    ![Scaffolding de Student](intro/_static/scaffold-student2.png)

  * En **Clase de modelo** seleccione **Student**.

  * En **Clase de contexto de datos** seleccione **SchoolContext**.

  * Acepte el valor predeterminado **StudentsController** como el nombre.

  * Haga clic en **Agregar**.

  Al hacer clic en **Agregar**, el motor de scaffolding de Visual Studio crea un archivo *StudentsController.cs* y un conjunto de vistas (archivos *.cshtml*) que funcionan con el controlador.

(El motor de scaffolding también puede crear el contexto de base de datos de forma automática si no lo crea primero manualmente como se hizo antes en este tutorial. Puede especificar una clase de contexto nueva en el cuadro **Agregar controlador** si hace clic en el signo más situado a la derecha de **Clase del contexto de datos**.  Después, Visual Studio creará la clase `DbContext`, así como el controlador y las vistas).

Observará que el controlador toma un `SchoolContext` como parámetro de constructor.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

La inserción de dependencias de ASP.NET Core se encarga de pasar una instancia de `SchoolContext` al controlador. Lo configuró anteriormente en el archivo *Startup.cs*.

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

En SSOX, haga clic en **(localdb)\MSSQLLocalDB > Databases** y después en la entrada del nombre de base de datos que se encuentra en la cadena de conexión del archivo *appsettings.json*.

Expanda el nodo **Tablas** para ver las tablas de la base de datos.

![Tablas en SSOX](intro/_static/ssox-tables.png)

Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.

![Tabla de estudiantes en SSOX](intro/_static/ssox-student-table.png)

Los archivos de base de datos *.mdf* y *.ldf* se encuentran en la carpeta *C:\Usuarios\\\<yourusername>* .

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

## <a name="get-the-code"></a>Obtención del código

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

> [!div class="checklist"]
> * Creado una aplicación web de ASP.NET Core MVC
> * Configurar el estilo del sitio
> * Obtenido información sobre los paquetes NuGet de EF Core
> * Creado el modelo de datos
> * Creado el contexto de la base de datos
> * Registrado SchoolContext
> * Inicializado la base de datos con datos de prueba
> * Creado un controlador y vistas
> * Consultado la base de datos

En el tutorial siguiente, obtendrá información sobre cómo realizar operaciones CRUD (crear, leer, actualizar y eliminar) básicas.

Pase al tutorial siguiente para obtener información sobre cómo realizar operaciones CRUD (crear, leer, actualizar y eliminar) básicas.

> [!div class="nextstepaction"]
> [Implementación de la funcionalidad CRUD básica](crud.md)

