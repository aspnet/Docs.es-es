---
title: 'Razor Pages con Entity Framework Core en ASP.NET Core: Tutorial 1 de 8'
author: rick-anderson
description: 'Se muestra cómo crear una aplicación de Razor Pages mediante Entity Framework Core'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 74f65b916c2d5b7de61ec29f4259a51584ee5989
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365423"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="beb71-103">Razor Pages con Entity Framework Core en ASP.NET Core: Tutorial 1 de 8</span><span class="sxs-lookup"><span data-stu-id="beb71-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="beb71-104">Por [Tom Dykstra](https://github.com/tdykstra) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="beb71-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="beb71-105">Este es el primero de una serie de tutoriales en los que se muestra cómo usar Entity Framework (EF) Core en una [aplicación Razor Pages](xref:razor-pages/index) en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="beb71-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="beb71-106">En el tutorial se crea un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="beb71-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="beb71-107">El sitio incluye funciones como la admisión de alumnos, la creación de cursos y las asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="beb71-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="beb71-108">En el tutorial se usa el enfoque de Code First.</span><span class="sxs-lookup"><span data-stu-id="beb71-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="beb71-109">Para obtener información sobre cómo seguir este tutorial mediante el enfoque de Database First, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="beb71-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="beb71-110">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="beb71-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="beb71-111">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="beb71-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="beb71-112">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="beb71-112">Prerequisites</span></span>

* <span data-ttu-id="beb71-113">Si no está familiarizado con Razor Pages, consulte la serie de tutoriales [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) antes de empezar este.</span><span class="sxs-lookup"><span data-stu-id="beb71-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="beb71-116">Motores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-116">Database engines</span></span>

<span data-ttu-id="beb71-117">En las instrucciones de Visual Studio se usa [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versión de SQL Server Express que solo se ejecuta en Windows.</span><span class="sxs-lookup"><span data-stu-id="beb71-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="beb71-118">En las instrucciones de Visual Studio Code se usa [SQLite](https://www.sqlite.org/), un motor de base de datos multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="beb71-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="beb71-119">Si decide usar SQLite, descargue e instale una herramienta de terceros para administrar y ver una base de datos de SQLite, como [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="beb71-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="beb71-120">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="beb71-120">Troubleshooting</span></span>

<span data-ttu-id="beb71-121">Si experimenta un problema que no puede resolver, compare el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="beb71-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="beb71-122">Una buena forma de obtener ayuda consiste en publicar una pregunta en StackOverflow.com con la [etiqueta ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o la [etiqueta EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="beb71-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="beb71-123">La aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="beb71-123">The sample app</span></span>

<span data-ttu-id="beb71-124">La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.</span><span class="sxs-lookup"><span data-stu-id="beb71-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="beb71-125">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="beb71-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="beb71-126">Estas son algunas de las pantallas que se crean en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="beb71-126">Here are a few of the screens created in the tutorial.</span></span>

![Página de índice de Students](intro/_static/students-index30.png)

![Página de edición de estudiantes](intro/_static/student-edit30.png)

<span data-ttu-id="beb71-129">El estilo de la interfaz de usuario de este sitio se basa en las plantillas de proyecto integradas.</span><span class="sxs-lookup"><span data-stu-id="beb71-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="beb71-130">El enfoque del tutorial es cómo usar EF Core con ASP.NET Core, no cómo personalizar la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="beb71-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="beb71-131">Creación del proyecto de aplicación web</span><span class="sxs-lookup"><span data-stu-id="beb71-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="beb71-133">Inicie Visual Studio y seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="beb71-133">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="beb71-134">Seleccione **Aplicación web de ASP.NET Core** > **SIGUIENTE**.</span><span class="sxs-lookup"><span data-stu-id="beb71-134">Select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="beb71-135">Asigne el nombre *ContosoUniversity* al proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="beb71-136">Es importante usar este nombre exacto incluido el uso de mayúsculas, para que los espacios de nombres coincidan cuando se copie y pegue el código.</span><span class="sxs-lookup"><span data-stu-id="beb71-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="beb71-137">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="beb71-137">Select **Create**.</span></span>
* <span data-ttu-id="beb71-138">Seleccione **.NET Core** y **ASP.NET Core 5.0** en las listas desplegables y, luego, **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="beb71-138">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="beb71-140">En un terminal, vaya a la carpeta en la que se debe crear la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-140">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="beb71-141">Ejecute los comandos siguientes para crear un proyecto de Razor Pages y `cd` para acceder a la nueva carpeta del proyecto:</span><span class="sxs-lookup"><span data-stu-id="beb71-141">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="beb71-142">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="beb71-142">Set up the site style</span></span>

<span data-ttu-id="beb71-143">Copie el código siguiente y péguelo en el archivo *Pages/Shared/_Layout.cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)].</span><span class="sxs-lookup"><span data-stu-id="beb71-143">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="beb71-144">El archivo de diseño establece el encabezado, el pie de página y el menú del sitio.</span><span class="sxs-lookup"><span data-stu-id="beb71-144">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="beb71-145">En el código anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="beb71-145">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="beb71-146">Todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="beb71-146">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="beb71-147">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="beb71-147">There are three occurrences.</span></span>
* <span data-ttu-id="beb71-148">Se eliminan las entradas del menú de **Inicio** y **Privacidad**.</span><span class="sxs-lookup"><span data-stu-id="beb71-148">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="beb71-149">Se han agregado entradas para **Acerca de** , **Estudiantes** , **Cursos** , **Instructores** y **Departamentos**.</span><span class="sxs-lookup"><span data-stu-id="beb71-149">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="beb71-150">En *Pages/Index.cshtml* , reemplace el contenido del archivo por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-150">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="beb71-151">El código anterior reemplaza el texto sobre ASP.NET Core con texto sobre esta aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-151">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="beb71-152">Ejecute la aplicación para comprobar que aparece la página principal.</span><span class="sxs-lookup"><span data-stu-id="beb71-152">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="beb71-153">El modelo de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-153">The data model</span></span>

<span data-ttu-id="beb71-154">En las secciones siguientes se crea un modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="beb71-154">The following sections create a data model:</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="beb71-156">Un alumno se puede inscribir en cualquier número de cursos y un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="beb71-156">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="beb71-157">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="beb71-157">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

* <span data-ttu-id="beb71-159">Cree una carpeta *Models* en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-159">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="beb71-160">Cree *Models/Student.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-160">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="beb71-161">La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos que se corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="beb71-161">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="beb71-162">De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-162">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="beb71-163">Por tanto, el nombre que se reconoce de forma automática para la clave principal de la clase `Student` es `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-163">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="beb71-164">Para más información, consulte [EF Core: claves](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="beb71-164">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="beb71-165">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="beb71-165">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="beb71-166">Las propiedades de navegación contienen otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="beb71-166">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="beb71-167">En este caso, la propiedad `Enrollments` de una entidad `Student` contiene todas las entidades `Enrollment` que están relacionadas con esa instancia de Student.</span><span class="sxs-lookup"><span data-stu-id="beb71-167">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="beb71-168">Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades Enrollment.</span><span class="sxs-lookup"><span data-stu-id="beb71-168">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="beb71-169">En la base de datos, una fila Enrollment se relaciona con una fila Student si su columna StudentID contiene el valor de identificador del alumno.</span><span class="sxs-lookup"><span data-stu-id="beb71-169">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="beb71-170">Por ejemplo, imagine que una fila Student tiene el identificador 1.</span><span class="sxs-lookup"><span data-stu-id="beb71-170">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="beb71-171">Las filas Enrollment relacionadas tendrán StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="beb71-171">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="beb71-172">StudentID es una *clave externa* de la tabla Enrollment.</span><span class="sxs-lookup"><span data-stu-id="beb71-172">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="beb71-173">La propiedad `Enrollments` se define como `ICollection<Enrollment>` porque puede haber varias entidades Enrollment relacionadas.</span><span class="sxs-lookup"><span data-stu-id="beb71-173">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="beb71-174">Puede usar otros tipos de colección, como `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-174">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="beb71-175">Cuando se usa `ICollection<Enrollment>`, EF Core crea una colección `HashSet<Enrollment>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="beb71-175">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="beb71-176">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="beb71-176">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="beb71-178">Cree *Models/Enrollment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-178">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="beb71-179">La propiedad `EnrollmentID` es la clave principal; esta entidad usa el patrón `classnameID` en lugar de `ID` por sí solo.</span><span class="sxs-lookup"><span data-stu-id="beb71-179">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="beb71-180">Para un modelo de datos de producción, elija un patrón y úselo de forma coherente.</span><span class="sxs-lookup"><span data-stu-id="beb71-180">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="beb71-181">En este tutorial se usan los dos simplemente para ilustrar el trabajo.</span><span class="sxs-lookup"><span data-stu-id="beb71-181">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="beb71-182">El uso de `ID` sin `classname` facilita la implementación de algunos tipos de cambios del modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-182">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="beb71-183">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="beb71-183">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="beb71-184">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade`[acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="beb71-184">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="beb71-185">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="beb71-185">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="beb71-186">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-186">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="beb71-187">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-187">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="beb71-188">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="beb71-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="beb71-189">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="beb71-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="beb71-190">EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="beb71-191">Por ejemplo, `StudentID` es la clave externa para la propiedad de navegación `Student`, ya que la clave principal de la entidad `Student` es `ID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-191">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="beb71-192">Las propiedades de clave externa también se pueden denominar `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="beb71-193">Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="beb71-194">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="beb71-194">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="beb71-196">Cree *Models/Course.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-196">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="beb71-197">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="beb71-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="beb71-198">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="beb71-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="beb71-199">El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.</span><span class="sxs-lookup"><span data-stu-id="beb71-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="beb71-200">Compile el proyecto para comprobar que no hay errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="beb71-200">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="beb71-201">Scaffolding de las páginas Student</span><span class="sxs-lookup"><span data-stu-id="beb71-201">Scaffold Student pages</span></span>

<span data-ttu-id="beb71-202">En esta sección, se usa la herramienta de scaffolding de ASP.NET Core para generar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-202">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="beb71-203">Una clase de `DbContext` de EF Core.</span><span class="sxs-lookup"><span data-stu-id="beb71-203">An EF Core `DbContext` class.</span></span> <span data-ttu-id="beb71-204">El contexto es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos determinado.</span><span class="sxs-lookup"><span data-stu-id="beb71-204">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="beb71-205">Se deriva de la clase <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="beb71-205">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="beb71-206">Razor Pages que controlan las operaciones de creación, lectura, actualización y eliminación (CRUD) de la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-206">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-207">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="beb71-208">Cree una carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="beb71-208">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="beb71-209">En el **Explorador de soluciones** , haga clic con el botón derecho en la carpeta *Pages/Students* y seleccione **Agregar** > **Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="beb71-209">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="beb71-210">En el cuadro de diálogo **Agregar nuevo elemento scaffold** :</span><span class="sxs-lookup"><span data-stu-id="beb71-210">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="beb71-211">En la pestaña de la izquierda, seleccione **Instalado > Común > Páginas Razor** .</span><span class="sxs-lookup"><span data-stu-id="beb71-211">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="beb71-212">Seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="beb71-212">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="beb71-213">En el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="beb71-213">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="beb71-214">En la lista desplegable **Clase de modelo** , seleccione **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="beb71-214">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="beb71-215">En la fila **Clase de contexto de datos** , seleccione el signo **+** (más).</span><span class="sxs-lookup"><span data-stu-id="beb71-215">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="beb71-216">Cambie el nombre del contexto de datos para que acabe en `SchoolContext` en lugar de `ContosoUniversityContext`.</span><span class="sxs-lookup"><span data-stu-id="beb71-216">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="beb71-217">Nombre del contexto actualizado: `ContosoUniversity.Data.SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="beb71-217">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="beb71-218">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="beb71-218">Select **Add**.</span></span>

<span data-ttu-id="beb71-219">Los paquetes siguientes se instalan de forma automática:</span><span class="sxs-lookup"><span data-stu-id="beb71-219">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-220">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-220">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="beb71-221">Ejecute los siguientes comandos de la CLI de .NET Core para instalar los paquetes NuGet necesarios:</span><span class="sxs-lookup"><span data-stu-id="beb71-221">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="beb71-222">El paquete Microsoft.VisualStudio.Web.CodeGeneration.Design es necesario para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="beb71-222">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="beb71-223">Aunque en la aplicación no se usará SQL Server, la herramienta de scaffolding necesita el paquete de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="beb71-223">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="beb71-224">Cree una carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="beb71-224">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="beb71-225">Ejecute el comando siguiente para instalar la [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="beb71-225">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="beb71-226">Ejecute el comando siguiente para aplicar scaffolding a las páginas Student.</span><span class="sxs-lookup"><span data-stu-id="beb71-226">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="beb71-227">**En Windows**</span><span class="sxs-lookup"><span data-stu-id="beb71-227">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="beb71-228">**En macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="beb71-228">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="beb71-229">Si el paso anterior falla, compile el proyecto y vuelva a intentar el paso de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="beb71-229">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="beb71-230">El proceso de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="beb71-230">The scaffolding process:</span></span>

* <span data-ttu-id="beb71-231">Crea Razor Pages en la carpeta *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="beb71-231">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="beb71-232">*Create.cshtml* y *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="beb71-232">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="beb71-233">*Delete.cshtml* y *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="beb71-233">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="beb71-234">*Details.cshtml* y *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="beb71-234">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="beb71-235">*Edit.cshtml* y *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="beb71-235">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="beb71-236">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="beb71-236">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="beb71-237">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="beb71-237">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="beb71-238">Agrega el contexto a la inserción de dependencias en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="beb71-238">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="beb71-239">Agrega una cadena de conexión de la base de datos a *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="beb71-239">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="beb71-240">Cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-240">Database connection string</span></span>

<span data-ttu-id="beb71-241">La herramienta de scaffolding genera una cadena de conexión en el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="beb71-241">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="beb71-243">La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="beb71-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="beb71-244">LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="beb71-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="beb71-245">De forma predeterminada, LocalDB crea archivos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-245">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="beb71-247">Acorte la cadena de conexión de SQLite a *CU.dB* :</span><span class="sxs-lookup"><span data-stu-id="beb71-247">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="beb71-248">Actualización de la clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-248">Update the database context class</span></span>

<span data-ttu-id="beb71-249">La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-249">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="beb71-250">El contexto se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="beb71-250">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="beb71-251">En el contexto se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-251">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="beb71-252">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="beb71-252">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="beb71-253">Actualice *Data/SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-253">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="beb71-254">El código anterior cambia del elemento `DbSet<Student> Student` singular al `DbSet<Student> Students` plural.</span><span class="sxs-lookup"><span data-stu-id="beb71-254">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="beb71-255">Para que el código de las páginas Razor coincida con el nombre nuevo de `DBSet`, realice un cambio global de: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="beb71-255">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="beb71-256">a: `_context.Students.`.</span><span class="sxs-lookup"><span data-stu-id="beb71-256">to: `_context.Students.`</span></span>

<span data-ttu-id="beb71-257">Hay ocho repeticiones.</span><span class="sxs-lookup"><span data-stu-id="beb71-257">There are 8 occurrences.</span></span>

<span data-ttu-id="beb71-258">Dado que un conjunto de entidades contiene varias entidades, muchos desarrolladores prefieren que los nombres de la propiedad `DBSet` sean plurales.</span><span class="sxs-lookup"><span data-stu-id="beb71-258">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="beb71-259">El código resaltado:</span><span class="sxs-lookup"><span data-stu-id="beb71-259">The highlighted code:</span></span>

* <span data-ttu-id="beb71-260">Crea una propiedad [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="beb71-260">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="beb71-261">En la terminología de EF Core:</span><span class="sxs-lookup"><span data-stu-id="beb71-261">In EF Core terminology:</span></span>
  * <span data-ttu-id="beb71-262">Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-262">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="beb71-263">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="beb71-263">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="beb71-264">Llama a <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="beb71-264">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="beb71-265">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="beb71-265">`OnModelCreating`:</span></span>
  * <span data-ttu-id="beb71-266">se llama cuando `SchoolContext` se ha inicializado, pero antes de que el modelo se haya bloqueado y utilizado para inicializar el contexto.</span><span class="sxs-lookup"><span data-stu-id="beb71-266">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="beb71-267">Es necesario porque, más adelante en el tutorial, la entidad `Student` tendrá referencias a las demás entidades.</span><span class="sxs-lookup"><span data-stu-id="beb71-267">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="beb71-268">Compile el proyecto para comprobar que no haya errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="beb71-268">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="beb71-269">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="beb71-269">Startup.cs</span></span>

<span data-ttu-id="beb71-270">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="beb71-270">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="beb71-271">Los servicios como `SchoolContext` se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-271">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="beb71-272">Estos servicios se proporcionan a los componentes que los necesitan, como páginas Razor, a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="beb71-272">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="beb71-273">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="beb71-273">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="beb71-274">La herramienta de scaffolding ha registrado de forma automática la clase de contexto con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="beb71-274">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-275">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="beb71-276">El proveedor de scaffolding ha agregado las líneas resaltadas siguientes:</span><span class="sxs-lookup"><span data-stu-id="beb71-276">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a><span data-ttu-id="beb71-277">Tener [Visual Studio Code](#tab/visual-studio-code)</span><span class="sxs-lookup"><span data-stu-id="beb71-277">[Visual Studio Code](#tab/visual-studio-code)</span></span>

<span data-ttu-id="beb71-278">Compruebe que el código que agrega el proveedor de scaffolding llama a `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="beb71-278">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="beb71-279">Para obtener información sobre el uso de una base de datos de producción, vea [Uso de SQLite para desarrollo, SQL Server para producción](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production).</span><span class="sxs-lookup"><span data-stu-id="beb71-279">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="beb71-280">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="beb71-280">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="beb71-281">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="beb71-281">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="beb71-282">Incorporación del filtro de excepción de base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-282">Add the database exception filter</span></span>

<span data-ttu-id="beb71-283">Agregue <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> a `ConfigureServices`, tal como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-283">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-284">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="beb71-285">Agregue el paquete NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="beb71-285">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="beb71-286">En la PMC, escriba lo siguiente para agregar el paquete NuGet:</span><span class="sxs-lookup"><span data-stu-id="beb71-286">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a><span data-ttu-id="beb71-287">Tener [Visual Studio Code](#tab/visual-studio-code)</span><span class="sxs-lookup"><span data-stu-id="beb71-287">[Visual Studio Code](#tab/visual-studio-code)</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="beb71-288">El paquete NuGet de `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` proporciona middleware de ASP.NET Core para páginas de error de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="beb71-288">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="beb71-289">Este middleware ayuda a detectar y diagnosticar errores con migraciones de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="beb71-289">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="beb71-290">`AddDatabaseDeveloperPageExceptionFilter` proporciona información de error útil en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="beb71-290">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="beb71-291">Creación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-291">Create the database</span></span>

<span data-ttu-id="beb71-292">Actualice *Program.cs* para crear la base de datos si no existe:</span><span class="sxs-lookup"><span data-stu-id="beb71-292">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="beb71-293">El método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) no realiza ninguna acción si existe una base de datos para el contexto.</span><span class="sxs-lookup"><span data-stu-id="beb71-293">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="beb71-294">Si no existe ninguna base de datos, se crean la base de datos y el esquema.</span><span class="sxs-lookup"><span data-stu-id="beb71-294">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="beb71-295">`EnsureCreated` habilita el flujo de trabajo siguiente para controlar los cambios del modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="beb71-295">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="beb71-296">Se elimina la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-296">Delete the database.</span></span> <span data-ttu-id="beb71-297">Se pierden los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="beb71-297">Any existing data is lost.</span></span>
* <span data-ttu-id="beb71-298">Se cambia el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-298">Change the data model.</span></span> <span data-ttu-id="beb71-299">Por ejemplo, se agrega un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="beb71-299">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="beb71-300">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-300">Run the app.</span></span>
* <span data-ttu-id="beb71-301">`EnsureCreated` crea una base de datos con el esquema nuevo.</span><span class="sxs-lookup"><span data-stu-id="beb71-301">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="beb71-302">Este flujo de trabajo funciona bien al principio de la fase de desarrollo cuando el esquema evoluciona rápidamente, siempre y cuando no sea necesario conservar los datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-302">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="beb71-303">La situación es distinta cuando es necesario conservar los datos introducidos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-303">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="beb71-304">En ese caso, use las migraciones.</span><span class="sxs-lookup"><span data-stu-id="beb71-304">When that is the case, use migrations.</span></span>

<span data-ttu-id="beb71-305">Más adelante en la serie de tutoriales, eliminará la base de datos creada por `EnsureCreated` y, en su lugar, usará las migraciones.</span><span class="sxs-lookup"><span data-stu-id="beb71-305">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="beb71-306">Una base de datos creada por `EnsureCreated` no se puede actualizar mediante migraciones.</span><span class="sxs-lookup"><span data-stu-id="beb71-306">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="beb71-307">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="beb71-307">Test the app</span></span>

* <span data-ttu-id="beb71-308">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-308">Run the app.</span></span>
* <span data-ttu-id="beb71-309">Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="beb71-309">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="beb71-310">Pruebe los vínculos Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="beb71-310">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="beb71-311">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-311">Seed the database</span></span>

<span data-ttu-id="beb71-312">El método `EnsureCreated` crea una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="beb71-312">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="beb71-313">En esta sección se agrega código que rellena la base de datos con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="beb71-313">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="beb71-314">Cree *Data/DbInitializer.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-314">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="beb71-315">El código comprueba si hay alumnos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-315">The code checks if there are any students in the database.</span></span> <span data-ttu-id="beb71-316">Si no hay ningún alumno, agrega datos de prueba a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-316">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="beb71-317">Crea los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="beb71-317">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="beb71-318">En *Program.cs* , reemplace la llamada a `EnsureCreated` con una llamada a `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="beb71-318">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-319">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="beb71-320">Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="beb71-320">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="beb71-321">Responda con `Y` para eliminar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-321">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-322">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-322">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="beb71-323">Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="beb71-323">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="beb71-324">Reinicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-324">Restart the app.</span></span>
* <span data-ttu-id="beb71-325">Seleccione la página Students para ver los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="beb71-325">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="beb71-326">Consulta la base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-326">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-327">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-327">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="beb71-328">Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="beb71-328">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="beb71-329">En SSOX, seleccione **(localdb)\MSSQLLocalDB > Bases de datos > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="beb71-329">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="beb71-330">El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.</span><span class="sxs-lookup"><span data-stu-id="beb71-330">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="beb71-331">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="beb71-331">Expand the **Tables** node.</span></span>
* <span data-ttu-id="beb71-332">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="beb71-332">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="beb71-333">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver código** para ver cómo el modelo `Student` se asigna al esquema de tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-333">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="beb71-335">Use la herramienta SQLite para ver el esquema de la base de datos y los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="beb71-335">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="beb71-336">El archivo de base de datos se denomina *CU.db* y se encuentra en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-336">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="beb71-337">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="beb71-337">Asynchronous code</span></span>

<span data-ttu-id="beb71-338">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="beb71-338">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="beb71-339">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="beb71-339">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="beb71-340">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="beb71-340">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="beb71-341">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="beb71-341">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="beb71-342">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="beb71-342">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="beb71-343">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz y el servidor pueda administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="beb71-343">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="beb71-344">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="beb71-344">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="beb71-345">En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="beb71-345">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="beb71-346">En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="beb71-346">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="beb71-347">La palabra clave `async` indica al compilador que:</span><span class="sxs-lookup"><span data-stu-id="beb71-347">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="beb71-348">Genere devoluciones de llamada para partes del cuerpo del método.</span><span class="sxs-lookup"><span data-stu-id="beb71-348">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="beb71-349">Cree el objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="beb71-349">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="beb71-350">El tipo devuelto `Task<T>` representa el trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="beb71-350">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="beb71-351">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="beb71-351">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="beb71-352">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="beb71-352">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="beb71-353">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="beb71-353">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="beb71-354">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="beb71-354">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="beb71-355">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="beb71-355">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="beb71-356">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-356">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="beb71-357">Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="beb71-357">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="beb71-358">No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="beb71-358">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="beb71-359">Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="beb71-359">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="beb71-360">Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para la paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-360">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="beb71-361">Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="beb71-361">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="beb71-362">Consideraciones de rendimiento</span><span class="sxs-lookup"><span data-stu-id="beb71-362">Performance considerations</span></span>

<span data-ttu-id="beb71-363">En general, una página web no debe cargar un número arbitrario de filas.</span><span class="sxs-lookup"><span data-stu-id="beb71-363">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="beb71-364">Una consulta debe utilizar la paginación o un enfoque de limitación.</span><span class="sxs-lookup"><span data-stu-id="beb71-364">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="beb71-365">Por ejemplo, la consulta anterior podría usar `Take` para limitar las filas devueltas:</span><span class="sxs-lookup"><span data-stu-id="beb71-365">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="beb71-366">Enumerar una tabla grande en una vista podría devolver una respuesta HTTP 200 parcialmente construida si se produce una excepción de base de datos en mitad de la enumeración.</span><span class="sxs-lookup"><span data-stu-id="beb71-366">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="beb71-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> tiene como valor predeterminado 1024.</span><span class="sxs-lookup"><span data-stu-id="beb71-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="beb71-368">El código siguiente establece `MaxModelBindingCollectionSize`:</span><span class="sxs-lookup"><span data-stu-id="beb71-368">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="beb71-369">La paginación se trata más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="beb71-369">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="beb71-370">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="beb71-370">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="beb71-371">Siguiente tutorial</span><span class="sxs-lookup"><span data-stu-id="beb71-371">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="beb71-372">Este es el primero de una serie de tutoriales en los que se muestra cómo usar Entity Framework (EF) Core en una [aplicación Razor Pages](xref:razor-pages/index) en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="beb71-372">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="beb71-373">En el tutorial se crea un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="beb71-373">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="beb71-374">El sitio incluye funciones como la admisión de alumnos, la creación de cursos y las asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="beb71-374">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="beb71-375">En el tutorial se usa el enfoque de Code First.</span><span class="sxs-lookup"><span data-stu-id="beb71-375">The tutorial uses the code first approach.</span></span> <span data-ttu-id="beb71-376">Para obtener información sobre cómo seguir este tutorial mediante el enfoque de Database First, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="beb71-376">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="beb71-377">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="beb71-377">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="beb71-378">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="beb71-378">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="beb71-379">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="beb71-379">Prerequisites</span></span>

* <span data-ttu-id="beb71-380">Si no está familiarizado con Razor Pages, consulte la serie de tutoriales [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) antes de empezar este.</span><span class="sxs-lookup"><span data-stu-id="beb71-380">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-381">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="beb71-383">Motores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-383">Database engines</span></span>

<span data-ttu-id="beb71-384">En las instrucciones de Visual Studio se usa [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versión de SQL Server Express que solo se ejecuta en Windows.</span><span class="sxs-lookup"><span data-stu-id="beb71-384">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="beb71-385">En las instrucciones de Visual Studio Code se usa [SQLite](https://www.sqlite.org/), un motor de base de datos multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="beb71-385">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="beb71-386">Si decide usar SQLite, descargue e instale una herramienta de terceros para administrar y ver una base de datos de SQLite, como [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="beb71-386">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="beb71-387">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="beb71-387">Troubleshooting</span></span>

<span data-ttu-id="beb71-388">Si experimenta un problema que no puede resolver, compare el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="beb71-388">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="beb71-389">Una buena forma de obtener ayuda consiste en publicar una pregunta en StackOverflow.com con la [etiqueta ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o la [etiqueta EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="beb71-389">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="beb71-390">La aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="beb71-390">The sample app</span></span>

<span data-ttu-id="beb71-391">La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.</span><span class="sxs-lookup"><span data-stu-id="beb71-391">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="beb71-392">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="beb71-392">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="beb71-393">Estas son algunas de las pantallas que se crean en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="beb71-393">Here are a few of the screens created in the tutorial.</span></span>

![Página de índice de Students](intro/_static/students-index30.png)

![Página de edición de estudiantes](intro/_static/student-edit30.png)

<span data-ttu-id="beb71-396">El estilo de la interfaz de usuario de este sitio se basa en las plantillas de proyecto integradas.</span><span class="sxs-lookup"><span data-stu-id="beb71-396">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="beb71-397">El enfoque del tutorial es cómo usar EF Core, no cómo personalizar la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="beb71-397">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="beb71-398">Siga el vínculo de la parte superior de la página para obtener el código fuente para el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="beb71-398">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="beb71-399">La carpeta *cu30* contiene el código para la versión ASP.NET Core 3.0 del tutorial.</span><span class="sxs-lookup"><span data-stu-id="beb71-399">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="beb71-400">Los archivos que reflejan el estado del código para los tutoriales 1-7 se pueden encontrar en la carpeta *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="beb71-400">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-401">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="beb71-402">Para ejecutar la aplicación después de descargar el proyecto completado:</span><span class="sxs-lookup"><span data-stu-id="beb71-402">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="beb71-403">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-403">Build the project.</span></span>
* <span data-ttu-id="beb71-404">En la Consola del administrador de paquetes (PMC), ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-404">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="beb71-405">Ejecute el proyecto para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-405">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-406">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-406">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="beb71-407">Para ejecutar la aplicación después de descargar el proyecto completado:</span><span class="sxs-lookup"><span data-stu-id="beb71-407">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="beb71-408">Elimine *ContosoUniversity.csproj* y cambie el nombre de *ContosoUniversitySQLite.csproj* por *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="beb71-408">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="beb71-409">En *Program.cs* , convierta `#define Startup` en comentario para que se use `StartupSQLite`.</span><span class="sxs-lookup"><span data-stu-id="beb71-409">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="beb71-410">Elimine *appSettings.json* y cambie el nombre de *appSettingsSQLite.json* por *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="beb71-410">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="beb71-411">Elimine la carpeta *Migrations* y cambie el nombre de *MigrationsSQL* por *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="beb71-411">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="beb71-412">Realice una búsqueda global `#if SQLiteVersion` y quite `#if SQLiteVersion` y la instrucción `#endif` asociada.</span><span class="sxs-lookup"><span data-stu-id="beb71-412">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="beb71-413">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-413">Build the project.</span></span>
* <span data-ttu-id="beb71-414">En un símbolo del sistema en la carpeta del proyecto, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="beb71-414">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="beb71-415">En la herramienta SQLite, ejecute la siguiente instrucción SQL:</span><span class="sxs-lookup"><span data-stu-id="beb71-415">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="beb71-416">Ejecute el proyecto para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-416">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="beb71-417">Creación del proyecto de aplicación web</span><span class="sxs-lookup"><span data-stu-id="beb71-417">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-418">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="beb71-419">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="beb71-419">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="beb71-420">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="beb71-420">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="beb71-421">Asigne el nombre *ContosoUniversity* al proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-421">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="beb71-422">Es importante usar este nombre exacto incluido el uso de mayúsculas, para que los espacios de nombres coincidan cuando se copie y pegue el código.</span><span class="sxs-lookup"><span data-stu-id="beb71-422">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="beb71-423">Seleccione **.NET Core** y **ASP.NET Core 3.0** en las listas desplegables y, luego, **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="beb71-423">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-424">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-424">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="beb71-425">En un terminal, vaya a la carpeta en la que se debe crear la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-425">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="beb71-426">Ejecute los comandos siguientes para crear un proyecto de Razor Pages y `cd` para acceder a la nueva carpeta del proyecto:</span><span class="sxs-lookup"><span data-stu-id="beb71-426">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="beb71-427">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="beb71-427">Set up the site style</span></span>

<span data-ttu-id="beb71-428">Configure el encabezado, el pie de página y el menú del sitio mediante la actualización de *Pages/Shared/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="beb71-428">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="beb71-429">Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="beb71-429">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="beb71-430">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="beb71-430">There are three occurrences.</span></span>

* <span data-ttu-id="beb71-431">Elimine las entradas de menú **Home** y **Privacy** , y agregue entradas para **About** , **Students** , **Courses** , **Instructors** y **Departments**.</span><span class="sxs-lookup"><span data-stu-id="beb71-431">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="beb71-432">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="beb71-432">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="beb71-433">En *Pages/Index.cshtml* , reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET Core con texto sobre esta aplicación:</span><span class="sxs-lookup"><span data-stu-id="beb71-433">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="beb71-434">Ejecute la aplicación para comprobar que aparece la página principal.</span><span class="sxs-lookup"><span data-stu-id="beb71-434">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="beb71-435">El modelo de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-435">The data model</span></span>

<span data-ttu-id="beb71-436">En las secciones siguientes se crea un modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="beb71-436">The following sections create a data model:</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="beb71-438">Un alumno se puede inscribir en cualquier número de cursos y un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="beb71-438">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="beb71-439">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="beb71-439">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

* <span data-ttu-id="beb71-441">Cree una carpeta *Models* en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-441">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="beb71-442">Cree *Models/Student.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-442">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="beb71-443">La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos que se corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="beb71-443">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="beb71-444">De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-444">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="beb71-445">Por tanto, el nombre que se reconoce de forma automática para la clave principal de la clase `Student` es `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-445">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="beb71-446">Para más información, consulte [EF Core: claves](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="beb71-446">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="beb71-447">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="beb71-447">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="beb71-448">Las propiedades de navegación contienen otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="beb71-448">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="beb71-449">En este caso, la propiedad `Enrollments` de una entidad `Student` contiene todas las entidades `Enrollment` que están relacionadas con esa instancia de Student.</span><span class="sxs-lookup"><span data-stu-id="beb71-449">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="beb71-450">Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades Enrollment.</span><span class="sxs-lookup"><span data-stu-id="beb71-450">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="beb71-451">En la base de datos, una fila Enrollment se relaciona con una fila Student si su columna StudentID contiene el valor de identificador del alumno.</span><span class="sxs-lookup"><span data-stu-id="beb71-451">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="beb71-452">Por ejemplo, imagine que una fila Student tiene el identificador 1.</span><span class="sxs-lookup"><span data-stu-id="beb71-452">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="beb71-453">Las filas Enrollment relacionadas tendrán StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="beb71-453">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="beb71-454">StudentID es una *clave externa* de la tabla Enrollment.</span><span class="sxs-lookup"><span data-stu-id="beb71-454">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="beb71-455">La propiedad `Enrollments` se define como `ICollection<Enrollment>` porque puede haber varias entidades Enrollment relacionadas.</span><span class="sxs-lookup"><span data-stu-id="beb71-455">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="beb71-456">Puede usar otros tipos de colección, como `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-456">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="beb71-457">Cuando se usa `ICollection<Enrollment>`, EF Core crea una colección `HashSet<Enrollment>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="beb71-457">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="beb71-458">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="beb71-458">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="beb71-460">Cree *Models/Enrollment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-460">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="beb71-461">La propiedad `EnrollmentID` es la clave principal; esta entidad usa el patrón `classnameID` en lugar de `ID` por sí solo.</span><span class="sxs-lookup"><span data-stu-id="beb71-461">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="beb71-462">Para un modelo de datos de producción, elija un patrón y úselo de forma coherente.</span><span class="sxs-lookup"><span data-stu-id="beb71-462">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="beb71-463">En este tutorial se usan los dos simplemente para ilustrar el trabajo.</span><span class="sxs-lookup"><span data-stu-id="beb71-463">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="beb71-464">El uso de `ID` sin `classname` facilita la implementación de algunos tipos de cambios del modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-464">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="beb71-465">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="beb71-465">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="beb71-466">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade`[acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="beb71-466">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="beb71-467">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="beb71-467">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="beb71-468">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-468">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="beb71-469">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-469">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="beb71-470">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="beb71-470">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="beb71-471">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="beb71-471">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="beb71-472">EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-472">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="beb71-473">Por ejemplo, `StudentID` es la clave externa para la propiedad de navegación `Student`, ya que la clave principal de la entidad `Student` es `ID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-473">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="beb71-474">Las propiedades de clave externa también se pueden denominar `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-474">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="beb71-475">Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-475">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="beb71-476">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="beb71-476">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="beb71-478">Cree *Models/Course.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-478">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="beb71-479">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="beb71-479">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="beb71-480">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="beb71-480">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="beb71-481">El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.</span><span class="sxs-lookup"><span data-stu-id="beb71-481">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="beb71-482">Compile el proyecto para comprobar que no hay errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="beb71-482">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="beb71-483">Scaffolding de las páginas Student</span><span class="sxs-lookup"><span data-stu-id="beb71-483">Scaffold Student pages</span></span>

<span data-ttu-id="beb71-484">En esta sección, se usa la herramienta de scaffolding de ASP.NET Core para generar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-484">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="beb71-485">Una clase de *contexto* de EF Core.</span><span class="sxs-lookup"><span data-stu-id="beb71-485">An EF Core *context* class.</span></span> <span data-ttu-id="beb71-486">El contexto es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos determinado.</span><span class="sxs-lookup"><span data-stu-id="beb71-486">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="beb71-487">Se deriva de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="beb71-487">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="beb71-488">Razor Pages que controlan las operaciones de creación, lectura, actualización y eliminación (CRUD) de la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-488">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-489">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-489">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="beb71-490">Cree una carpeta *Students* en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="beb71-490">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="beb71-491">En el **Explorador de soluciones** , haga clic con el botón derecho en la carpeta *Pages/Students* y seleccione **Agregar** > **Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="beb71-491">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="beb71-492">En el cuadro de diálogo **Agregar scaffold** , seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **AGREGAR**.</span><span class="sxs-lookup"><span data-stu-id="beb71-492">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="beb71-493">En el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="beb71-493">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="beb71-494">En la lista desplegable **Clase de modelo** , seleccione **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="beb71-494">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="beb71-495">En la fila **Clase de contexto de datos** , seleccione el signo **+** (más).</span><span class="sxs-lookup"><span data-stu-id="beb71-495">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="beb71-496">Cambie el nombre del contexto de datos *ContosoUniversity.Models.ContosoUniversityContext* por *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="beb71-496">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="beb71-497">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="beb71-497">Select **Add**.</span></span>

<span data-ttu-id="beb71-498">Los paquetes siguientes se instalan de forma automática:</span><span class="sxs-lookup"><span data-stu-id="beb71-498">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-499">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-499">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="beb71-500">Ejecute los siguientes comandos de la CLI de .NET Core para instalar los paquetes NuGet necesarios:</span><span class="sxs-lookup"><span data-stu-id="beb71-500">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="beb71-501">El paquete Microsoft.VisualStudio.Web.CodeGeneration.Design es necesario para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="beb71-501">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="beb71-502">Aunque en la aplicación no se usará SQL Server, la herramienta de scaffolding necesita el paquete de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="beb71-502">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="beb71-503">Cree una carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="beb71-503">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="beb71-504">Ejecute el comando siguiente para instalar la [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="beb71-504">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="beb71-505">Ejecute el comando siguiente para aplicar scaffolding a las páginas Student.</span><span class="sxs-lookup"><span data-stu-id="beb71-505">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="beb71-506">**En Windows**</span><span class="sxs-lookup"><span data-stu-id="beb71-506">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="beb71-507">**En macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="beb71-507">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="beb71-508">Si tiene un problema con el paso anterior, compile el proyecto y vuelva a intentar el paso de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="beb71-508">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="beb71-509">El proceso de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="beb71-509">The scaffolding process:</span></span>

* <span data-ttu-id="beb71-510">Crea Razor Pages en la carpeta *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="beb71-510">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="beb71-511">*Create.cshtml* y *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="beb71-511">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="beb71-512">*Delete.cshtml* y *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="beb71-512">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="beb71-513">*Details.cshtml* y *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="beb71-513">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="beb71-514">*Edit.cshtml* y *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="beb71-514">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="beb71-515">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="beb71-515">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="beb71-516">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="beb71-516">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="beb71-517">Agrega el contexto a la inserción de dependencias en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="beb71-517">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="beb71-518">Agrega una cadena de conexión de la base de datos a *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="beb71-518">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="beb71-519">Cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-519">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-520">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-520">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="beb71-521">El archivo *appsettings.json* especifica la cadena de conexión [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="beb71-521">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="beb71-522">LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="beb71-522">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="beb71-523">De forma predeterminada, LocalDB crea archivos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-523">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-524">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-524">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="beb71-525">Cambie la cadena de conexión para que apunte a un archivo de base de datos de SQLite denominado *CU.db* :</span><span class="sxs-lookup"><span data-stu-id="beb71-525">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="beb71-526">Actualización de la clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-526">Update the database context class</span></span>

<span data-ttu-id="beb71-527">La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-527">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="beb71-528">El contexto se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="beb71-528">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="beb71-529">En el contexto se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-529">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="beb71-530">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="beb71-530">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="beb71-531">Actualice *Data/SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-531">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="beb71-532">El código resaltado crea una propiedad [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="beb71-532">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="beb71-533">En la terminología de EF Core:</span><span class="sxs-lookup"><span data-stu-id="beb71-533">In EF Core terminology:</span></span>

* <span data-ttu-id="beb71-534">Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-534">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="beb71-535">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="beb71-535">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="beb71-536">Puesto que un conjunto de entidades contiene varias entidades, las propiedades DBSet deben ser nombres en plural.</span><span class="sxs-lookup"><span data-stu-id="beb71-536">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="beb71-537">Como la herramienta de scaffolding ha creado una instancia `Student` de DBSet, en este paso se cambia a `Students` en plural.</span><span class="sxs-lookup"><span data-stu-id="beb71-537">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="beb71-538">Para que el código de Razor Pages coincida con el nuevo nombre de DBSet, realice un cambio global de `_context.Student` a `_context.Students` en todo el proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-538">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="beb71-539">Hay ocho repeticiones.</span><span class="sxs-lookup"><span data-stu-id="beb71-539">There are 8 occurrences.</span></span>

<span data-ttu-id="beb71-540">Compile el proyecto para comprobar que no haya errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="beb71-540">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="beb71-541">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="beb71-541">Startup.cs</span></span>

<span data-ttu-id="beb71-542">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="beb71-542">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="beb71-543">Los servicios (como el contexto de base de datos de EF Core) se registran con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-543">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="beb71-544">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="beb71-544">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="beb71-545">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="beb71-545">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="beb71-546">La herramienta de scaffolding ha registrado de forma automática la clase de contexto con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="beb71-546">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-547">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-547">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="beb71-548">En `ConfigureServices`, el proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="beb71-548">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="beb71-550">En `ConfigureServices`, asegúrese de que el código agregado por el proveedor de scaffolding llama a `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="beb71-550">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="beb71-551">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="beb71-551">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="beb71-552">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="beb71-552">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="beb71-553">Creación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-553">Create the database</span></span>

<span data-ttu-id="beb71-554">Actualice *Program.cs* para crear la base de datos si no existe:</span><span class="sxs-lookup"><span data-stu-id="beb71-554">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="beb71-555">El método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) no realiza ninguna acción si existe una base de datos para el contexto.</span><span class="sxs-lookup"><span data-stu-id="beb71-555">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="beb71-556">Si no existe ninguna base de datos, se crean la base de datos y el esquema.</span><span class="sxs-lookup"><span data-stu-id="beb71-556">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="beb71-557">`EnsureCreated` habilita el flujo de trabajo siguiente para controlar los cambios del modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="beb71-557">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="beb71-558">Se elimina la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-558">Delete the database.</span></span> <span data-ttu-id="beb71-559">Se pierden los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="beb71-559">Any existing data is lost.</span></span>
* <span data-ttu-id="beb71-560">Se cambia el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-560">Change the data model.</span></span> <span data-ttu-id="beb71-561">Por ejemplo, se agrega un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="beb71-561">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="beb71-562">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-562">Run the app.</span></span>
* <span data-ttu-id="beb71-563">`EnsureCreated` crea una base de datos con el esquema nuevo.</span><span class="sxs-lookup"><span data-stu-id="beb71-563">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="beb71-564">Este flujo de trabajo funciona bien al principio de la fase de desarrollo cuando el esquema evoluciona rápidamente, siempre y cuando no sea necesario conservar los datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-564">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="beb71-565">La situación es distinta cuando es necesario conservar los datos introducidos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-565">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="beb71-566">En ese caso, use las migraciones.</span><span class="sxs-lookup"><span data-stu-id="beb71-566">When that is the case, use migrations.</span></span>

<span data-ttu-id="beb71-567">Más adelante en la serie de tutoriales, eliminará la base de datos creada por `EnsureCreated` y, en su lugar, usará las migraciones.</span><span class="sxs-lookup"><span data-stu-id="beb71-567">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="beb71-568">Una base de datos creada por `EnsureCreated` no se puede actualizar mediante migraciones.</span><span class="sxs-lookup"><span data-stu-id="beb71-568">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="beb71-569">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="beb71-569">Test the app</span></span>

* <span data-ttu-id="beb71-570">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-570">Run the app.</span></span>
* <span data-ttu-id="beb71-571">Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="beb71-571">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="beb71-572">Pruebe los vínculos Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="beb71-572">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="beb71-573">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-573">Seed the database</span></span>

<span data-ttu-id="beb71-574">El método `EnsureCreated` crea una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="beb71-574">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="beb71-575">En esta sección se agrega código que rellena la base de datos con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="beb71-575">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="beb71-576">Cree *Data/DbInitializer.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-576">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="beb71-577">El código comprueba si hay alumnos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-577">The code checks if there are any students in the database.</span></span> <span data-ttu-id="beb71-578">Si no hay ningún alumno, agrega datos de prueba a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-578">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="beb71-579">Crea los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="beb71-579">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="beb71-580">En *Program.cs* , reemplace la llamada a `EnsureCreated` con una llamada a `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="beb71-580">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-581">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-581">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="beb71-582">Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="beb71-582">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-583">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-583">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="beb71-584">Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="beb71-584">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="beb71-585">Reinicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-585">Restart the app.</span></span>

* <span data-ttu-id="beb71-586">Seleccione la página Students para ver los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="beb71-586">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="beb71-587">Consulta la base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-587">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-588">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-588">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="beb71-589">Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="beb71-589">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="beb71-590">En SSOX, seleccione **(localdb)\MSSQLLocalDB > Bases de datos > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="beb71-590">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="beb71-591">El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.</span><span class="sxs-lookup"><span data-stu-id="beb71-591">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="beb71-592">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="beb71-592">Expand the **Tables** node.</span></span>
* <span data-ttu-id="beb71-593">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="beb71-593">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="beb71-594">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver código** para ver cómo el modelo `Student` se asigna al esquema de tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-594">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-595">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-595">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="beb71-596">Use la herramienta SQLite para ver el esquema de la base de datos y los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="beb71-596">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="beb71-597">El archivo de base de datos se denomina *CU.db* y se encuentra en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-597">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="beb71-598">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="beb71-598">Asynchronous code</span></span>

<span data-ttu-id="beb71-599">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="beb71-599">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="beb71-600">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="beb71-600">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="beb71-601">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="beb71-601">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="beb71-602">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="beb71-602">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="beb71-603">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="beb71-603">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="beb71-604">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz y el servidor pueda administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="beb71-604">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="beb71-605">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="beb71-605">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="beb71-606">En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="beb71-606">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="beb71-607">En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="beb71-607">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="beb71-608">La palabra clave `async` indica al compilador que:</span><span class="sxs-lookup"><span data-stu-id="beb71-608">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="beb71-609">Genere devoluciones de llamada para partes del cuerpo del método.</span><span class="sxs-lookup"><span data-stu-id="beb71-609">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="beb71-610">Cree el objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="beb71-610">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="beb71-611">El tipo devuelto `Task<T>` representa el trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="beb71-611">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="beb71-612">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="beb71-612">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="beb71-613">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="beb71-613">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="beb71-614">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="beb71-614">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="beb71-615">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="beb71-615">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="beb71-616">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="beb71-616">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="beb71-617">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-617">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="beb71-618">Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="beb71-618">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="beb71-619">No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="beb71-619">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="beb71-620">Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="beb71-620">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="beb71-621">Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para la paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-621">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="beb71-622">Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="beb71-622">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="beb71-623">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="beb71-623">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="beb71-624">Siguiente tutorial</span><span class="sxs-lookup"><span data-stu-id="beb71-624">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="beb71-625">En la aplicación web de ejemplo Contoso University se muestra cómo crear una aplicación web de Razor Pages de ASP.NET Core con Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="beb71-625">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="beb71-626">La aplicación de ejemplo es un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="beb71-626">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="beb71-627">Incluye funciones como la admisión de estudiantes, la creación de cursos y asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="beb71-627">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="beb71-628">Esta página es la primera de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="beb71-628">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="beb71-629">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="beb71-629">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="beb71-630">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="beb71-630">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="beb71-631">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="beb71-631">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-632">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-632">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-633">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-633">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="beb71-634">Familiaridad con [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="beb71-634">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="beb71-635">Los programadores nuevos deben completar [Introducción a Razor Pages en ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start) antes de empezar esta serie.</span><span class="sxs-lookup"><span data-stu-id="beb71-635">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="beb71-636">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="beb71-636">Troubleshooting</span></span>

<span data-ttu-id="beb71-637">Si experimenta un problema que no puede resolver, por lo general podrá encontrar la solución si compara el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="beb71-637">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="beb71-638">Una buena forma de obtener ayuda consiste en publicar una pregunta en [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) para [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="beb71-638">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="beb71-639">La aplicación web Contoso University</span><span class="sxs-lookup"><span data-stu-id="beb71-639">The Contoso University web app</span></span>

<span data-ttu-id="beb71-640">La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.</span><span class="sxs-lookup"><span data-stu-id="beb71-640">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="beb71-641">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="beb71-641">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="beb71-642">Estas son algunas de las pantallas que se crean en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="beb71-642">Here are a few of the screens created in the tutorial.</span></span>

![Página de índice de Students](intro/_static/students-index.png)

![Página de edición de estudiantes](intro/_static/student-edit.png)

<span data-ttu-id="beb71-645">El estilo de la interfaz de usuario de este sitio se mantiene fiel a lo que generan las plantillas integradas.</span><span class="sxs-lookup"><span data-stu-id="beb71-645">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="beb71-646">El tutorial se centra en EF Core con Razor Pages, no en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="beb71-646">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="beb71-647">Creación de la aplicación web de Razor Pages ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="beb71-647">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-648">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-648">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="beb71-649">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="beb71-649">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="beb71-650">Cree una aplicación web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="beb71-650">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="beb71-651">Asigne el nombre **ContosoUniversity** al proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-651">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="beb71-652">Es importante que el nombre del proyecto sea *ContosoUniversity* para que coincidan los espacios de nombres al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="beb71-652">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="beb71-653">Seleccione **ASP.NET Core 2.1** en la lista desplegable y, luego, **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="beb71-653">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="beb71-654">Para ver las imágenes de los pasos anteriores, consulte [Creación de una aplicación web de Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="beb71-654">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="beb71-655">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-655">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-656">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-656">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="beb71-657">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="beb71-657">Set up the site style</span></span>

<span data-ttu-id="beb71-658">Con algunos cambios se configura el menú del sitio, el diseño y la página principal.</span><span class="sxs-lookup"><span data-stu-id="beb71-658">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="beb71-659">Actualice *Pages/Shared/_Layout.cshtml* con los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="beb71-659">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="beb71-660">Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="beb71-660">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="beb71-661">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="beb71-661">There are three occurrences.</span></span>

* <span data-ttu-id="beb71-662">Agregue entradas de menú para **Students** , **Courses** , **Instructors** y **Departments** , y elimine la entrada de menú **Contact**.</span><span class="sxs-lookup"><span data-stu-id="beb71-662">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="beb71-663">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="beb71-663">The changes are highlighted.</span></span> <span data-ttu-id="beb71-664">( *No* se muestra todo el marcado).</span><span class="sxs-lookup"><span data-stu-id="beb71-664">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="beb71-665">En *Pages/Index.cshtml* , reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET y MVC con texto sobre esta aplicación:</span><span class="sxs-lookup"><span data-stu-id="beb71-665">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="beb71-666">Crear el modelo de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-666">Create the data model</span></span>

<span data-ttu-id="beb71-667">Cree las clases de entidad para la aplicación Contoso University.</span><span class="sxs-lookup"><span data-stu-id="beb71-667">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="beb71-668">Comience con las tres entidades siguientes:</span><span class="sxs-lookup"><span data-stu-id="beb71-668">Start with the following three entities:</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="beb71-670">Hay una relación uno a varios entre las entidades `Student` y `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="beb71-670">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="beb71-671">Hay una relación uno a varios entre las entidades `Course` y `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="beb71-671">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="beb71-672">Un estudiante se puede inscribir en cualquier número de cursos.</span><span class="sxs-lookup"><span data-stu-id="beb71-672">A student can enroll in any number of courses.</span></span> <span data-ttu-id="beb71-673">Un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="beb71-673">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="beb71-674">En las secciones siguientes, se crea una clase para cada una de estas entidades.</span><span class="sxs-lookup"><span data-stu-id="beb71-674">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="beb71-675">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="beb71-675">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

<span data-ttu-id="beb71-677">Cree una carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="beb71-677">Create a *Models* folder.</span></span> <span data-ttu-id="beb71-678">En la carpeta *Models* , cree un archivo de clase denominado *Student.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-678">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="beb71-679">La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos (DB) que corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="beb71-679">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="beb71-680">De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-680">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="beb71-681">En `classnameID`, `classname` es el nombre de la clase.</span><span class="sxs-lookup"><span data-stu-id="beb71-681">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="beb71-682">En el ejemplo anterior, la clave principal alternativa que se reconoce de forma automática es `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-682">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="beb71-683">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="beb71-683">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="beb71-684">Las propiedades de navegación se vinculan a otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="beb71-684">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="beb71-685">En este caso, la propiedad `Enrollments` de una `Student entity` contiene todas las entidades `Enrollment` que están relacionadas con esa entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-685">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="beb71-686">Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="beb71-686">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="beb71-687">Una fila `Enrollment` relacionada es la que contiene el valor de clave principal de ese estudiante en la columna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-687">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="beb71-688">Por ejemplo, suponga que el estudiante con ID=1 tiene dos filas en la tabla `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="beb71-688">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="beb71-689">La tabla `Enrollment` tiene dos filas con `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="beb71-689">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="beb71-690">`StudentID` es una clave externa en la tabla `Enrollment` que especifica el estudiante en la tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-690">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="beb71-691">Si una propiedad de navegación puede contener varias entidades, la propiedad de navegación debe ser un tipo de lista, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-691">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="beb71-692">Se puede especificar `ICollection<T>`, o bien un tipo como `List<T>` o `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-692">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="beb71-693">Cuando se usa `ICollection<T>`, EF Core crea una colección `HashSet<T>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="beb71-693">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="beb71-694">Las propiedades de navegación que contienen varias entidades proceden de relaciones de varios a varios y uno a varios.</span><span class="sxs-lookup"><span data-stu-id="beb71-694">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="beb71-695">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="beb71-695">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="beb71-697">En la carpeta *Models* , cree *Enrollment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-697">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="beb71-698">La propiedad `EnrollmentID` es la clave principal.</span><span class="sxs-lookup"><span data-stu-id="beb71-698">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="beb71-699">En esta entidad se usa el patrón `classnameID` en lugar de `ID` como en la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-699">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="beb71-700">Normalmente, los desarrolladores eligen un patrón y lo usan en todo el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-700">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="beb71-701">En un tutorial posterior, se muestra el uso de ID sin un nombre de clase para facilitar la implementación de la herencia en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-701">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="beb71-702">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="beb71-702">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="beb71-703">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade` acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="beb71-703">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="beb71-704">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="beb71-704">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="beb71-705">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-705">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="beb71-706">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="beb71-706">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="beb71-707">La entidad `Student` difiere de la propiedad de navegación `Student.Enrollments`, que contiene varias entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="beb71-707">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="beb71-708">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="beb71-708">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="beb71-709">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="beb71-709">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="beb71-710">EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-710">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="beb71-711">Por ejemplo, `StudentID` para la propiedad de navegación `Student`, puesto que la clave principal de la entidad `Student` es `ID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-711">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="beb71-712">Las propiedades de clave externa también se pueden denominar `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-712">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="beb71-713">Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="beb71-713">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="beb71-714">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="beb71-714">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="beb71-716">En la carpeta *Models* , cree *Course.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-716">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="beb71-717">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="beb71-717">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="beb71-718">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="beb71-718">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="beb71-719">El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.</span><span class="sxs-lookup"><span data-stu-id="beb71-719">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="beb71-720">Aplicación de scaffolding al modelo de alumnos</span><span class="sxs-lookup"><span data-stu-id="beb71-720">Scaffold the student model</span></span>

<span data-ttu-id="beb71-721">En esta sección, se aplica scaffolding al modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="beb71-721">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="beb71-722">Es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="beb71-722">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="beb71-723">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="beb71-723">Build the project.</span></span>
* <span data-ttu-id="beb71-724">Cree la carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="beb71-724">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-725">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-725">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="beb71-726">En el **Explorador de soluciones** , haga clic con el botón derecho en la carpeta *Pages/Students* > **Agregar** > **Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="beb71-726">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="beb71-727">En el cuadro de diálogo **Agregar scaffold** , seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **AGREGAR**.</span><span class="sxs-lookup"><span data-stu-id="beb71-727">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="beb71-728">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="beb71-728">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="beb71-729">En la lista desplegable **Clase de modelo** , seleccione **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="beb71-729">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="beb71-730">En la fila **Clase de contexto de datos** , haga clic en el signo **+** (más) y cambie el nombre generado por **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="beb71-730">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="beb71-731">En la lista desplegable **Clase de contexto de datos** , seleccione **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="beb71-731">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="beb71-732">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="beb71-732">Select **Add**.</span></span>

![Cuadro de diálogo CRUD](intro/_static/s1.png)

<span data-ttu-id="beb71-734">Si tiene algún problema con el paso anterior, consulte [Aplicar scaffolding al modelo de película](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="beb71-734">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-735">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-735">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="beb71-736">Ejecute los comandos siguientes para aplicar scaffolding al modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="beb71-736">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="beb71-737">El proceso de scaffolding ha creado y cambiado los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="beb71-737">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="beb71-738">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="beb71-738">Files created</span></span>

* <span data-ttu-id="beb71-739">*Pages/Students* Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="beb71-739">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="beb71-740">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="beb71-740">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="beb71-741">Actualizaciones de archivos</span><span class="sxs-lookup"><span data-stu-id="beb71-741">File updates</span></span>

* <span data-ttu-id="beb71-742">*Startup.cs* : en la sección siguiente se detallan los cambios realizados en este archivo.</span><span class="sxs-lookup"><span data-stu-id="beb71-742">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="beb71-743">*appsettings.json* : se agrega la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="beb71-743">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="beb71-744">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="beb71-744">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="beb71-745">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="beb71-745">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="beb71-746">Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-746">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="beb71-747">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="beb71-747">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="beb71-748">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="beb71-748">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="beb71-749">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="beb71-749">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="beb71-750">Examine el método `ConfigureServices` de *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="beb71-750">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="beb71-751">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="beb71-751">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="beb71-752">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="beb71-752">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="beb71-753">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="beb71-753">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="beb71-754">Actualización de main</span><span class="sxs-lookup"><span data-stu-id="beb71-754">Update main</span></span>

<span data-ttu-id="beb71-755">En *Program.cs* , modifique el método `Main` para que haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-755">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="beb71-756">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="beb71-756">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="beb71-757">Llame a [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="beb71-757">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="beb71-758">Elimine el contexto cuando finalice el método `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="beb71-758">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="beb71-759">En el código siguiente se muestra el archivo *Program.cs* actualizado.</span><span class="sxs-lookup"><span data-stu-id="beb71-759">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="beb71-760">`EnsureCreated` garantiza la existencia de la base de datos para el contexto.</span><span class="sxs-lookup"><span data-stu-id="beb71-760">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="beb71-761">Si existe, no se realiza ninguna acción.</span><span class="sxs-lookup"><span data-stu-id="beb71-761">If it exists, no action is taken.</span></span> <span data-ttu-id="beb71-762">Si no existe, se crean la base de datos y todo su esquema.</span><span class="sxs-lookup"><span data-stu-id="beb71-762">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="beb71-763">En `EnsureCreated` no se usan migraciones para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-763">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="beb71-764">Una base de datos que se cree con `EnsureCreated` no se podrá actualizar más adelante mediante las migraciones.</span><span class="sxs-lookup"><span data-stu-id="beb71-764">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="beb71-765">`EnsureCreated` se llama durante el inicio de la aplicación, lo que permite el flujo de trabajo siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-765">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="beb71-766">Se elimina la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-766">Delete the DB.</span></span>
* <span data-ttu-id="beb71-767">Se cambia el esquema de base de datos (por ejemplo, se agrega un campo `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="beb71-767">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="beb71-768">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="beb71-768">Run the app.</span></span>
* <span data-ttu-id="beb71-769">`EnsureCreated` crea una base de datos con la columna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="beb71-769">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="beb71-770">`EnsureCreated` es útil al principio del desarrollo, cuando el esquema evoluciona rápidamente.</span><span class="sxs-lookup"><span data-stu-id="beb71-770">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="beb71-771">Más adelante, en el tutorial se elimina la base de datos y se usan las migraciones.</span><span class="sxs-lookup"><span data-stu-id="beb71-771">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="beb71-772">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="beb71-772">Test the app</span></span>

<span data-ttu-id="beb71-773">Ejecute la aplicación y acepte la directiva de cookies.</span><span class="sxs-lookup"><span data-stu-id="beb71-773">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="beb71-774">Esta aplicación no conserva información de carácter personal.</span><span class="sxs-lookup"><span data-stu-id="beb71-774">This app doesn't keep personal information.</span></span> <span data-ttu-id="beb71-775">Puede obtener más información sobre la directiva de cookies en [Compatibilidad con el Reglamento general de protección de datos (RGPD) de la UE](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="beb71-775">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="beb71-776">Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="beb71-776">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="beb71-777">Pruebe los vínculos Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="beb71-777">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="beb71-778">Examinar el contexto de base de datos SchoolContext</span><span class="sxs-lookup"><span data-stu-id="beb71-778">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="beb71-779">La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-779">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="beb71-780">El contexto de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="beb71-780">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="beb71-781">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-781">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="beb71-782">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="beb71-782">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="beb71-783">Actualice *SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-783">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="beb71-784">El código resaltado crea una propiedad [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="beb71-784">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="beb71-785">En la terminología de EF Core:</span><span class="sxs-lookup"><span data-stu-id="beb71-785">In EF Core terminology:</span></span>

* <span data-ttu-id="beb71-786">Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-786">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="beb71-787">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="beb71-787">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="beb71-788">`DbSet<Enrollment>` y `DbSet<Course>` se pueden omitir.</span><span class="sxs-lookup"><span data-stu-id="beb71-788">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="beb71-789">EF Core las incluye implícitamente porque la entidad `Student` hace referencia a la entidad `Enrollment` y la entidad `Enrollment` hace referencia a la entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="beb71-789">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="beb71-790">Para este tutorial, conserve `DbSet<Enrollment>` y `DbSet<Course>` en el `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="beb71-790">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="beb71-791">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="beb71-791">SQL Server Express LocalDB</span></span>

<span data-ttu-id="beb71-792">La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="beb71-792">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="beb71-793">LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="beb71-793">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="beb71-794">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="beb71-794">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="beb71-795">De forma predeterminada, LocalDB crea archivos de base de datos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="beb71-795">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="beb71-796">Agregar código para inicializar la base de datos con datos de prueba</span><span class="sxs-lookup"><span data-stu-id="beb71-796">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="beb71-797">EF Core crea una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="beb71-797">EF Core creates an empty DB.</span></span> <span data-ttu-id="beb71-798">En esta sección, se escribe un método `Initialize` para rellenarlo con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="beb71-798">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="beb71-799">En la carpeta *Data* , cree un archivo de clase denominado *DbInitializer.cs* y agregue el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="beb71-799">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="beb71-800">Nota: El código anterior usa `Models` para el espacio de nombres (`namespace ContosoUniversity.Models`) en lugar de `Data`.</span><span class="sxs-lookup"><span data-stu-id="beb71-800">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="beb71-801">`Models` es coherente con el código generado por el proveedor de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="beb71-801">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="beb71-802">Para obtener más información, consulte [este problema de scaffolding de GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="beb71-802">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="beb71-803">El código comprueba si hay estudiantes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-803">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="beb71-804">Si no hay alumnos en la base de datos, se inicializa con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="beb71-804">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="beb71-805">Carga los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="beb71-805">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="beb71-806">El método `EnsureCreated` crea automáticamente la base de datos para el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-806">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="beb71-807">Si la base de datos existe, `EnsureCreated` vuelve sin modificarla.</span><span class="sxs-lookup"><span data-stu-id="beb71-807">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="beb71-808">En *Program.cs* , modifique el método `Main` para que llame a `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="beb71-808">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="beb71-809">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="beb71-809">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="beb71-810">Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="beb71-810">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="beb71-811">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="beb71-811">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="beb71-812">Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="beb71-812">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="beb71-813">Ver la base de datos</span><span class="sxs-lookup"><span data-stu-id="beb71-813">View the DB</span></span>

<span data-ttu-id="beb71-814">El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.</span><span class="sxs-lookup"><span data-stu-id="beb71-814">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="beb71-815">Por lo tanto, el nombre de la base de datos será "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="beb71-815">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="beb71-816">El GUID será diferente para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="beb71-816">The GUID will be different for each user.</span></span>
<span data-ttu-id="beb71-817">Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="beb71-817">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="beb71-818">En SSOX, haga clic en **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="beb71-818">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="beb71-819">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="beb71-819">Expand the **Tables** node.</span></span>

<span data-ttu-id="beb71-820">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="beb71-820">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="beb71-821">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="beb71-821">Asynchronous code</span></span>

<span data-ttu-id="beb71-822">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="beb71-822">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="beb71-823">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="beb71-823">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="beb71-824">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="beb71-824">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="beb71-825">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="beb71-825">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="beb71-826">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="beb71-826">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="beb71-827">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz, y el servidor está habilitado para administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="beb71-827">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="beb71-828">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="beb71-828">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="beb71-829">En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="beb71-829">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="beb71-830">En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="beb71-830">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="beb71-831">La palabra clave `async` indica al compilador que:</span><span class="sxs-lookup"><span data-stu-id="beb71-831">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="beb71-832">Genere devoluciones de llamada para partes del cuerpo del método.</span><span class="sxs-lookup"><span data-stu-id="beb71-832">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="beb71-833">Cree automáticamente el objeto [Task](/dotnet/api/system.threading.tasks.task) que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="beb71-833">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="beb71-834">Para más información, vea [Tipo de valor devuelto Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="beb71-834">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="beb71-835">El tipo devuelto implícito `Task` representa el trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="beb71-835">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="beb71-836">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="beb71-836">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="beb71-837">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="beb71-837">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="beb71-838">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="beb71-838">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="beb71-839">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="beb71-839">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="beb71-840">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="beb71-840">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="beb71-841">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-841">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="beb71-842">Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="beb71-842">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="beb71-843">No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="beb71-843">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="beb71-844">Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="beb71-844">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="beb71-845">Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="beb71-845">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="beb71-846">Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="beb71-846">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="beb71-847">En el siguiente tutorial, se examinan las operaciones CRUD (crear, leer, actualizar y eliminar) básicas.</span><span class="sxs-lookup"><span data-stu-id="beb71-847">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="beb71-848">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="beb71-848">Additional resources</span></span>

* [<span data-ttu-id="beb71-849">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="beb71-849">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="beb71-850">Siguiente</span><span class="sxs-lookup"><span data-stu-id="beb71-850">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
