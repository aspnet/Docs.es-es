---
title: 'Razor Pages con Entity Framework Core en ASP.NET Core: Tutorial 1 de 8'
author: rick-anderson
description: Se muestra cómo crear una aplicación de Razor Pages mediante Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: e797228d498ec9b269e718c099a0532bbb4504d3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530247"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="7c542-103">Razor Pages con Entity Framework Core en ASP.NET Core: Tutorial 1 de 8</span><span class="sxs-lookup"><span data-stu-id="7c542-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="7c542-104">Por [Tom Dykstra](https://github.com/tdykstra) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7c542-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7c542-105">Este es el primero de una serie de tutoriales en los que se muestra cómo usar Entity Framework (EF) Core en una [aplicación Razor Pages](xref:razor-pages/index) en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c542-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="7c542-106">En el tutorial se crea un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="7c542-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="7c542-107">El sitio incluye funciones como la admisión de alumnos, la creación de cursos y las asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="7c542-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="7c542-108">En el tutorial se usa el enfoque de Code First.</span><span class="sxs-lookup"><span data-stu-id="7c542-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="7c542-109">Para obtener información sobre cómo seguir este tutorial mediante el enfoque de Database First, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="7c542-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="7c542-110">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="7c542-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="7c542-111">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7c542-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7c542-112">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="7c542-112">Prerequisites</span></span>

* <span data-ttu-id="7c542-113">Si no está familiarizado con Razor Pages, consulte la serie de tutoriales [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) antes de empezar este.</span><span class="sxs-lookup"><span data-stu-id="7c542-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="7c542-116">Motores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-116">Database engines</span></span>

<span data-ttu-id="7c542-117">En las instrucciones de Visual Studio se usa [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versión de SQL Server Express que solo se ejecuta en Windows.</span><span class="sxs-lookup"><span data-stu-id="7c542-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="7c542-118">En las instrucciones de Visual Studio Code se usa [SQLite](https://www.sqlite.org/), un motor de base de datos multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="7c542-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="7c542-119">Si decide usar SQLite, descargue e instale una herramienta de terceros para administrar y ver una base de datos de SQLite, como [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="7c542-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7c542-120">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="7c542-120">Troubleshooting</span></span>

<span data-ttu-id="7c542-121">Si experimenta un problema que no puede resolver, compare el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="7c542-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="7c542-122">Una buena forma de obtener ayuda consiste en publicar una pregunta en StackOverflow.com con la [etiqueta ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o la [etiqueta EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="7c542-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="7c542-123">La aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="7c542-123">The sample app</span></span>

<span data-ttu-id="7c542-124">La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.</span><span class="sxs-lookup"><span data-stu-id="7c542-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="7c542-125">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="7c542-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="7c542-126">Estas son algunas de las pantallas que se crean en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="7c542-126">Here are a few of the screens created in the tutorial.</span></span>

![Página de índice de Students](intro/_static/students-index30.png)

![Página de edición de estudiantes](intro/_static/student-edit30.png)

<span data-ttu-id="7c542-129">El estilo de la interfaz de usuario de este sitio se basa en las plantillas de proyecto integradas.</span><span class="sxs-lookup"><span data-stu-id="7c542-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="7c542-130">El enfoque del tutorial es cómo usar EF Core con ASP.NET Core, no cómo personalizar la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="7c542-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="7c542-131">Creación del proyecto de aplicación web</span><span class="sxs-lookup"><span data-stu-id="7c542-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7c542-133">Inicie Visual Studio y seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="7c542-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="7c542-134">En el cuadro de diálogo **Crear un proyecto**, seleccione **Aplicación web ASP.NET Core** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7c542-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="7c542-135">En el cuadro de diálogo **Configurar su nuevo proyecto**, escriba `ContosoUniversity` en **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="7c542-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="7c542-136">Es importante usar este nombre exacto, incluido el uso de mayúsculas, para que cada `namespace` coincida cuando se copie el código.</span><span class="sxs-lookup"><span data-stu-id="7c542-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="7c542-137">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7c542-137">Select **Create**.</span></span>
1. <span data-ttu-id="7c542-138">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione:</span><span class="sxs-lookup"><span data-stu-id="7c542-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="7c542-139">**.NET Core** y **ASP.NET Core 5.0** en los menús desplegables.</span><span class="sxs-lookup"><span data-stu-id="7c542-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="7c542-140">**Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="7c542-140">**ASP.NET Core Web App**.</span></span>
    1. <span data-ttu-id="7c542-141">**Crear**
      ![Cuadro de diálogo Nuevo proyecto de ASP.NET Core](~/data/ef-rp/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="7c542-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-rp/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c542-143">En un terminal, vaya a la carpeta en la que se debe crear la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="7c542-144">Ejecute los comandos siguientes para crear un proyecto de Razor Pages y `cd` para acceder a la nueva carpeta del proyecto:</span><span class="sxs-lookup"><span data-stu-id="7c542-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="7c542-145">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="7c542-145">Set up the site style</span></span>

<span data-ttu-id="7c542-146">Copie el código siguiente y péguelo en el archivo *Pages/Shared/_Layout.cshtml*: .</span><span class="sxs-lookup"><span data-stu-id="7c542-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="7c542-147">El archivo de diseño establece el encabezado, el pie de página y el menú del sitio.</span><span class="sxs-lookup"><span data-stu-id="7c542-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="7c542-148">En el código anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="7c542-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="7c542-149">Todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="7c542-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="7c542-150">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="7c542-150">There are three occurrences.</span></span>
* <span data-ttu-id="7c542-151">Se eliminan las entradas del menú de **Inicio** y **Privacidad**.</span><span class="sxs-lookup"><span data-stu-id="7c542-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="7c542-152">Se han agregado entradas para **Acerca de**, **Estudiantes**, **Cursos**, **Instructores** y **Departamentos**.</span><span class="sxs-lookup"><span data-stu-id="7c542-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="7c542-153">En *Pages/Index.cshtml*, reemplace el contenido del archivo por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="7c542-154">El código anterior reemplaza el texto sobre ASP.NET Core con texto sobre esta aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="7c542-155">Ejecute la aplicación para comprobar que aparece la página principal.</span><span class="sxs-lookup"><span data-stu-id="7c542-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="7c542-156">El modelo de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-156">The data model</span></span>

<span data-ttu-id="7c542-157">En las secciones siguientes se crea un modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="7c542-157">The following sections create a data model:</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="7c542-159">Un alumno se puede inscribir en cualquier número de cursos y un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="7c542-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="7c542-160">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="7c542-160">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

* <span data-ttu-id="7c542-162">Cree una carpeta *Models* en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="7c542-163">Cree *Models/Student.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="7c542-164">La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos que se corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="7c542-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="7c542-165">De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="7c542-166">Por tanto, el nombre que se reconoce de forma automática para la clave principal de la clase `Student` es `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="7c542-167">Para más información, consulte [EF Core: claves](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="7c542-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="7c542-168">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7c542-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="7c542-169">Las propiedades de navegación contienen otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="7c542-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="7c542-170">En este caso, la propiedad `Enrollments` de una entidad `Student` contiene todas las entidades `Enrollment` que están relacionadas con esa instancia de Student.</span><span class="sxs-lookup"><span data-stu-id="7c542-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="7c542-171">Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades Enrollment.</span><span class="sxs-lookup"><span data-stu-id="7c542-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="7c542-172">En la base de datos, una fila Enrollment se relaciona con una fila Student si su columna StudentID contiene el valor de identificador del alumno.</span><span class="sxs-lookup"><span data-stu-id="7c542-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="7c542-173">Por ejemplo, imagine que una fila Student tiene el identificador 1.</span><span class="sxs-lookup"><span data-stu-id="7c542-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="7c542-174">Las filas Enrollment relacionadas tendrán StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="7c542-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="7c542-175">StudentID es una *clave externa* de la tabla Enrollment.</span><span class="sxs-lookup"><span data-stu-id="7c542-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="7c542-176">La propiedad `Enrollments` se define como `ICollection<Enrollment>` porque puede haber varias entidades Enrollment relacionadas.</span><span class="sxs-lookup"><span data-stu-id="7c542-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="7c542-177">Puede usar otros tipos de colección, como `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="7c542-178">Cuando se usa `ICollection<Enrollment>`, EF Core crea una colección `HashSet<Enrollment>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="7c542-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="7c542-179">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="7c542-179">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="7c542-181">Cree *Models/Enrollment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="7c542-182">La propiedad `EnrollmentID` es la clave principal; esta entidad usa el patrón `classnameID` en lugar de `ID` por sí solo.</span><span class="sxs-lookup"><span data-stu-id="7c542-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="7c542-183">Para un modelo de datos de producción, elija un patrón y úselo de forma coherente.</span><span class="sxs-lookup"><span data-stu-id="7c542-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="7c542-184">En este tutorial se usan los dos simplemente para ilustrar el trabajo.</span><span class="sxs-lookup"><span data-stu-id="7c542-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="7c542-185">El uso de `ID` sin `classname` facilita la implementación de algunos tipos de cambios del modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="7c542-186">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="7c542-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="7c542-187">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade`[acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="7c542-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="7c542-188">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="7c542-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="7c542-189">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="7c542-190">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="7c542-191">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="7c542-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="7c542-192">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="7c542-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="7c542-193">EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="7c542-194">Por ejemplo, `StudentID` es la clave externa para la propiedad de navegación `Student`, ya que la clave principal de la entidad `Student` es `ID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="7c542-195">Las propiedades de clave externa también se pueden denominar `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="7c542-196">Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="7c542-197">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="7c542-197">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="7c542-199">Cree *Models/Course.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="7c542-200">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="7c542-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="7c542-201">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7c542-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="7c542-202">El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.</span><span class="sxs-lookup"><span data-stu-id="7c542-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="7c542-203">Compile el proyecto para comprobar que no hay errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="7c542-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="7c542-204">Scaffolding de las páginas Student</span><span class="sxs-lookup"><span data-stu-id="7c542-204">Scaffold Student pages</span></span>

<span data-ttu-id="7c542-205">En esta sección, se usa la herramienta de scaffolding de ASP.NET Core para generar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="7c542-206">Una clase de `DbContext` de EF Core.</span><span class="sxs-lookup"><span data-stu-id="7c542-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="7c542-207">El contexto es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos determinado.</span><span class="sxs-lookup"><span data-stu-id="7c542-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="7c542-208">Se deriva de la clase <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="7c542-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="7c542-209">Razor Pages que controlan las operaciones de creación, lectura, actualización y eliminación (CRUD) de la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c542-211">Cree una carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="7c542-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="7c542-212">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Pages/Students* y seleccione **Agregar** > **Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="7c542-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7c542-213">En el cuadro de diálogo **Agregar nuevo elemento scaffold**:</span><span class="sxs-lookup"><span data-stu-id="7c542-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="7c542-214">En la pestaña de la izquierda, seleccione **Instalado > Común > Páginas Razor** .</span><span class="sxs-lookup"><span data-stu-id="7c542-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="7c542-215">Seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="7c542-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="7c542-216">En el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="7c542-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="7c542-217">En la lista desplegable **Clase de modelo**, seleccione **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="7c542-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="7c542-218">En la fila **Clase de contexto de datos**, seleccione el signo **+** (más).</span><span class="sxs-lookup"><span data-stu-id="7c542-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="7c542-219">Cambie el nombre del contexto de datos para que acabe en `SchoolContext` en lugar de `ContosoUniversityContext`.</span><span class="sxs-lookup"><span data-stu-id="7c542-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="7c542-220">Nombre del contexto actualizado: `ContosoUniversity.Data.SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7c542-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
    * <span data-ttu-id="7c542-221">Seleccione **Agregar** para terminar de agregar la clase de contexto de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-221">Select **Add** to finish adding the data context class.</span></span>
   * <span data-ttu-id="7c542-222">Seleccione **Agregar** para finalizar el cuadro de diálogo **Agregar Razor Pages**.</span><span class="sxs-lookup"><span data-stu-id="7c542-222">Select **Add** to finish the **Add Razor Pages** dialog.</span></span>

<span data-ttu-id="7c542-223">Los paquetes siguientes se instalan de forma automática:</span><span class="sxs-lookup"><span data-stu-id="7c542-223">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c542-225">Ejecute los siguientes comandos de la CLI de .NET Core para instalar los paquetes NuGet necesarios:</span><span class="sxs-lookup"><span data-stu-id="7c542-225">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="7c542-226">El paquete Microsoft.VisualStudio.Web.CodeGeneration.Design es necesario para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="7c542-226">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="7c542-227">Aunque en la aplicación no se usará SQL Server, la herramienta de scaffolding necesita el paquete de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7c542-227">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="7c542-228">Cree una carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="7c542-228">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="7c542-229">Ejecute el comando siguiente para instalar la [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="7c542-229">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="7c542-230">Ejecute el comando siguiente para aplicar scaffolding a las páginas Student.</span><span class="sxs-lookup"><span data-stu-id="7c542-230">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="7c542-231">**En Windows**</span><span class="sxs-lookup"><span data-stu-id="7c542-231">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="7c542-232">**En macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="7c542-232">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="7c542-233">Si el paso anterior falla, compile el proyecto y vuelva a intentar el paso de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="7c542-233">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="7c542-234">El proceso de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="7c542-234">The scaffolding process:</span></span>

* <span data-ttu-id="7c542-235">Crea Razor Pages en la carpeta *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="7c542-235">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="7c542-236">*Create.cshtml* y *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7c542-236">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="7c542-237">*Delete.cshtml* y *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7c542-237">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="7c542-238">*Details.cshtml* y *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7c542-238">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="7c542-239">*Edit.cshtml* y *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7c542-239">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="7c542-240">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7c542-240">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="7c542-241">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="7c542-241">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="7c542-242">Agrega el contexto a la inserción de dependencias en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7c542-242">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="7c542-243">Agrega una cadena de conexión de la base de datos a *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7c542-243">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="7c542-244">Cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-244">Database connection string</span></span>

<span data-ttu-id="7c542-245">La herramienta de scaffolding genera una cadena de conexión en el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7c542-245">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c542-247">La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="7c542-247">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="7c542-248">LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="7c542-248">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="7c542-249">De forma predeterminada, LocalDB crea archivos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-249">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c542-251">Acorte la cadena de conexión de SQLite a *CU.dB*:</span><span class="sxs-lookup"><span data-stu-id="7c542-251">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="7c542-252">Actualización de la clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-252">Update the database context class</span></span>

<span data-ttu-id="7c542-253">La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-253">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="7c542-254">El contexto se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7c542-254">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7c542-255">En el contexto se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-255">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="7c542-256">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7c542-256">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="7c542-257">Actualice *Data/SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-257">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="7c542-258">El código anterior cambia del elemento `DbSet<Student> Student` singular al `DbSet<Student> Students` plural.</span><span class="sxs-lookup"><span data-stu-id="7c542-258">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="7c542-259">Para que el código de las páginas Razor coincida con el nombre nuevo de `DBSet`, realice un cambio global de: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="7c542-259">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="7c542-260">a: `_context.Students.`.</span><span class="sxs-lookup"><span data-stu-id="7c542-260">to: `_context.Students.`</span></span>

<span data-ttu-id="7c542-261">Hay ocho repeticiones.</span><span class="sxs-lookup"><span data-stu-id="7c542-261">There are 8 occurrences.</span></span>

<span data-ttu-id="7c542-262">Dado que un conjunto de entidades contiene varias entidades, muchos desarrolladores prefieren que los nombres de la propiedad `DBSet` sean plurales.</span><span class="sxs-lookup"><span data-stu-id="7c542-262">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="7c542-263">El código resaltado:</span><span class="sxs-lookup"><span data-stu-id="7c542-263">The highlighted code:</span></span>

* <span data-ttu-id="7c542-264">Crea una propiedad [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="7c542-264">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="7c542-265">En la terminología de EF Core:</span><span class="sxs-lookup"><span data-stu-id="7c542-265">In EF Core terminology:</span></span>
  * <span data-ttu-id="7c542-266">Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-266">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="7c542-267">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="7c542-267">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="7c542-268">Llama a <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="7c542-268">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="7c542-269">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="7c542-269">`OnModelCreating`:</span></span>
  * <span data-ttu-id="7c542-270">se llama cuando `SchoolContext` se ha inicializado, pero antes de que el modelo se haya bloqueado y utilizado para inicializar el contexto.</span><span class="sxs-lookup"><span data-stu-id="7c542-270">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="7c542-271">Es necesario porque, más adelante en el tutorial, la entidad `Student` tendrá referencias a las demás entidades.</span><span class="sxs-lookup"><span data-stu-id="7c542-271">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="7c542-272">Compile el proyecto para comprobar que no haya errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="7c542-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="7c542-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="7c542-273">Startup.cs</span></span>

<span data-ttu-id="7c542-274">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7c542-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7c542-275">Los servicios como `SchoolContext` se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-275">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="7c542-276">Estos servicios se proporcionan a los componentes que los necesitan, como páginas Razor, a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="7c542-276">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="7c542-277">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="7c542-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7c542-278">La herramienta de scaffolding ha registrado de forma automática la clase de contexto con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="7c542-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-279">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c542-280">El proveedor de scaffolding ha agregado las líneas resaltadas siguientes:</span><span class="sxs-lookup"><span data-stu-id="7c542-280">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a><span data-ttu-id="7c542-281">Tener [Visual Studio Code](#tab/visual-studio-code)</span><span class="sxs-lookup"><span data-stu-id="7c542-281">[Visual Studio Code](#tab/visual-studio-code)</span></span>

<span data-ttu-id="7c542-282">Compruebe que el código que agrega el proveedor de scaffolding llama a `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="7c542-282">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="7c542-283">Para obtener información sobre el uso de una base de datos de producción, vea [Uso de SQLite para desarrollo, SQL Server para producción](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production).</span><span class="sxs-lookup"><span data-stu-id="7c542-283">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="7c542-284">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="7c542-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7c542-285">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7c542-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="7c542-286">Incorporación del filtro de excepción de base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-286">Add the database exception filter</span></span>

<span data-ttu-id="7c542-287">Agregue <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> a `ConfigureServices`, tal como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-287">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-288">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="7c542-289">Agregue el paquete NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="7c542-289">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="7c542-290">En la PMC, escriba lo siguiente para agregar el paquete NuGet:</span><span class="sxs-lookup"><span data-stu-id="7c542-290">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
```

# <a name="visual-studio-code"></a><span data-ttu-id="7c542-291">Tener [Visual Studio Code](#tab/visual-studio-code)</span><span class="sxs-lookup"><span data-stu-id="7c542-291">[Visual Studio Code](#tab/visual-studio-code)</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="7c542-292">El paquete NuGet de `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` proporciona middleware de ASP.NET Core para páginas de error de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7c542-292">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="7c542-293">Este middleware ayuda a detectar y diagnosticar errores con migraciones de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7c542-293">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="7c542-294">`AddDatabaseDeveloperPageExceptionFilter` proporciona información de error útil en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="7c542-294">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="7c542-295">Creación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-295">Create the database</span></span>

<span data-ttu-id="7c542-296">Actualice *Program.cs* para crear la base de datos si no existe:</span><span class="sxs-lookup"><span data-stu-id="7c542-296">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="7c542-297">El método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) no realiza ninguna acción si existe una base de datos para el contexto.</span><span class="sxs-lookup"><span data-stu-id="7c542-297">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="7c542-298">Si no existe ninguna base de datos, se crean la base de datos y el esquema.</span><span class="sxs-lookup"><span data-stu-id="7c542-298">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="7c542-299">`EnsureCreated` habilita el flujo de trabajo siguiente para controlar los cambios del modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="7c542-299">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="7c542-300">Se elimina la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-300">Delete the database.</span></span> <span data-ttu-id="7c542-301">Se pierden los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="7c542-301">Any existing data is lost.</span></span>
* <span data-ttu-id="7c542-302">Se cambia el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-302">Change the data model.</span></span> <span data-ttu-id="7c542-303">Por ejemplo, se agrega un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="7c542-303">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="7c542-304">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-304">Run the app.</span></span>
* <span data-ttu-id="7c542-305">`EnsureCreated` crea una base de datos con el esquema nuevo.</span><span class="sxs-lookup"><span data-stu-id="7c542-305">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="7c542-306">Este flujo de trabajo funciona bien al principio de la fase de desarrollo cuando el esquema evoluciona rápidamente, siempre y cuando no sea necesario conservar los datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-306">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="7c542-307">La situación es distinta cuando es necesario conservar los datos introducidos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-307">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="7c542-308">En ese caso, use las migraciones.</span><span class="sxs-lookup"><span data-stu-id="7c542-308">When that is the case, use migrations.</span></span>

<span data-ttu-id="7c542-309">Más adelante en la serie de tutoriales, eliminará la base de datos creada por `EnsureCreated` y, en su lugar, usará las migraciones.</span><span class="sxs-lookup"><span data-stu-id="7c542-309">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="7c542-310">Una base de datos creada por `EnsureCreated` no se puede actualizar mediante migraciones.</span><span class="sxs-lookup"><span data-stu-id="7c542-310">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7c542-311">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="7c542-311">Test the app</span></span>

* <span data-ttu-id="7c542-312">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-312">Run the app.</span></span>
* <span data-ttu-id="7c542-313">Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="7c542-313">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="7c542-314">Pruebe los vínculos Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="7c542-314">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="7c542-315">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-315">Seed the database</span></span>

<span data-ttu-id="7c542-316">El método `EnsureCreated` crea una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="7c542-316">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="7c542-317">En esta sección se agrega código que rellena la base de datos con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="7c542-317">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="7c542-318">Cree *Data/DbInitializer.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-318">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="7c542-319">El código comprueba si hay alumnos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-319">The code checks if there are any students in the database.</span></span> <span data-ttu-id="7c542-320">Si no hay ningún alumno, agrega datos de prueba a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-320">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="7c542-321">Crea los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="7c542-321">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="7c542-322">En *Program.cs*, reemplace la llamada a `EnsureCreated` con una llamada a `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="7c542-322">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-323">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-323">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c542-324">Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="7c542-324">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="7c542-325">Responda con `Y` para eliminar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-325">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-326">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-326">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c542-327">Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="7c542-327">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="7c542-328">Reinicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-328">Restart the app.</span></span>
* <span data-ttu-id="7c542-329">Seleccione la página Students para ver los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="7c542-329">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="7c542-330">Consulta la base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-330">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-331">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c542-332">Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7c542-332">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="7c542-333">En SSOX, seleccione **(localdb)\MSSQLLocalDB > Bases de datos > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="7c542-333">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="7c542-334">El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.</span><span class="sxs-lookup"><span data-stu-id="7c542-334">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="7c542-335">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="7c542-335">Expand the **Tables** node.</span></span>
* <span data-ttu-id="7c542-336">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="7c542-336">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="7c542-337">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver código** para ver cómo el modelo `Student` se asigna al esquema de tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-337">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-338">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-338">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c542-339">Use la herramienta SQLite para ver el esquema de la base de datos y los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="7c542-339">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="7c542-340">El archivo de base de datos se denomina *CU.db* y se encuentra en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-340">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="7c542-341">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="7c542-341">Asynchronous code</span></span>

<span data-ttu-id="7c542-342">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="7c542-342">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="7c542-343">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="7c542-343">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="7c542-344">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="7c542-344">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="7c542-345">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="7c542-345">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="7c542-346">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="7c542-346">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="7c542-347">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz y el servidor pueda administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="7c542-347">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="7c542-348">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="7c542-348">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="7c542-349">En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="7c542-349">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="7c542-350">En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="7c542-350">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="7c542-351">La palabra clave `async` indica al compilador que:</span><span class="sxs-lookup"><span data-stu-id="7c542-351">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="7c542-352">Genere devoluciones de llamada para partes del cuerpo del método.</span><span class="sxs-lookup"><span data-stu-id="7c542-352">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="7c542-353">Cree el objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="7c542-353">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="7c542-354">El tipo devuelto `Task` representa el trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="7c542-354">The `Task` return type represents ongoing work.</span></span>
* <span data-ttu-id="7c542-355">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="7c542-355">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="7c542-356">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="7c542-356">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="7c542-357">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="7c542-357">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="7c542-358">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="7c542-358">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="7c542-359">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="7c542-359">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="7c542-360">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-360">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="7c542-361">Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="7c542-361">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="7c542-362">No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="7c542-362">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="7c542-363">Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="7c542-363">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="7c542-364">Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para la paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-364">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="7c542-365">Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="7c542-365">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="7c542-366">Consideraciones de rendimiento</span><span class="sxs-lookup"><span data-stu-id="7c542-366">Performance considerations</span></span>

<span data-ttu-id="7c542-367">En general, una página web no debe cargar un número arbitrario de filas.</span><span class="sxs-lookup"><span data-stu-id="7c542-367">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="7c542-368">Una consulta debe utilizar la paginación o un enfoque de limitación.</span><span class="sxs-lookup"><span data-stu-id="7c542-368">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="7c542-369">Por ejemplo, la consulta anterior podría usar `Take` para limitar las filas devueltas:</span><span class="sxs-lookup"><span data-stu-id="7c542-369">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="7c542-370">Enumerar una tabla grande en una vista podría devolver una respuesta HTTP 200 parcialmente construida si se produce una excepción de base de datos en mitad de la enumeración.</span><span class="sxs-lookup"><span data-stu-id="7c542-370">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="7c542-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> tiene como valor predeterminado 1024.</span><span class="sxs-lookup"><span data-stu-id="7c542-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="7c542-372">El código siguiente establece `MaxModelBindingCollectionSize`:</span><span class="sxs-lookup"><span data-stu-id="7c542-372">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7c542-373">La paginación se trata más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="7c542-373">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7c542-374">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="7c542-374">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="7c542-375">Siguiente tutorial</span><span class="sxs-lookup"><span data-stu-id="7c542-375">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="7c542-376">Este es el primero de una serie de tutoriales en los que se muestra cómo usar Entity Framework (EF) Core en una [aplicación Razor Pages](xref:razor-pages/index) en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c542-376">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="7c542-377">En el tutorial se crea un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="7c542-377">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="7c542-378">El sitio incluye funciones como la admisión de alumnos, la creación de cursos y las asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="7c542-378">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="7c542-379">En el tutorial se usa el enfoque de Code First.</span><span class="sxs-lookup"><span data-stu-id="7c542-379">The tutorial uses the code first approach.</span></span> <span data-ttu-id="7c542-380">Para obtener información sobre cómo seguir este tutorial mediante el enfoque de Database First, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="7c542-380">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="7c542-381">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="7c542-381">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="7c542-382">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7c542-382">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7c542-383">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="7c542-383">Prerequisites</span></span>

* <span data-ttu-id="7c542-384">Si no está familiarizado con Razor Pages, consulte la serie de tutoriales [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) antes de empezar este.</span><span class="sxs-lookup"><span data-stu-id="7c542-384">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-385">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-385">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="7c542-387">Motores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-387">Database engines</span></span>

<span data-ttu-id="7c542-388">En las instrucciones de Visual Studio se usa [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versión de SQL Server Express que solo se ejecuta en Windows.</span><span class="sxs-lookup"><span data-stu-id="7c542-388">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="7c542-389">En las instrucciones de Visual Studio Code se usa [SQLite](https://www.sqlite.org/), un motor de base de datos multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="7c542-389">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="7c542-390">Si decide usar SQLite, descargue e instale una herramienta de terceros para administrar y ver una base de datos de SQLite, como [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="7c542-390">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7c542-391">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="7c542-391">Troubleshooting</span></span>

<span data-ttu-id="7c542-392">Si experimenta un problema que no puede resolver, compare el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="7c542-392">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="7c542-393">Una buena forma de obtener ayuda consiste en publicar una pregunta en StackOverflow.com con la [etiqueta ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o la [etiqueta EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="7c542-393">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="7c542-394">La aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="7c542-394">The sample app</span></span>

<span data-ttu-id="7c542-395">La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.</span><span class="sxs-lookup"><span data-stu-id="7c542-395">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="7c542-396">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="7c542-396">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="7c542-397">Estas son algunas de las pantallas que se crean en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="7c542-397">Here are a few of the screens created in the tutorial.</span></span>

![Página de índice de Students](intro/_static/students-index30.png)

![Página de edición de estudiantes](intro/_static/student-edit30.png)

<span data-ttu-id="7c542-400">El estilo de la interfaz de usuario de este sitio se basa en las plantillas de proyecto integradas.</span><span class="sxs-lookup"><span data-stu-id="7c542-400">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="7c542-401">El enfoque del tutorial es cómo usar EF Core, no cómo personalizar la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="7c542-401">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="7c542-402">Siga el vínculo de la parte superior de la página para obtener el código fuente para el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="7c542-402">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="7c542-403">La carpeta *cu30* contiene el código para la versión ASP.NET Core 3.0 del tutorial.</span><span class="sxs-lookup"><span data-stu-id="7c542-403">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="7c542-404">Los archivos que reflejan el estado del código para los tutoriales 1-7 se pueden encontrar en la carpeta *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="7c542-404">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-405">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c542-406">Para ejecutar la aplicación después de descargar el proyecto completado:</span><span class="sxs-lookup"><span data-stu-id="7c542-406">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="7c542-407">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-407">Build the project.</span></span>
* <span data-ttu-id="7c542-408">En la Consola del administrador de paquetes (PMC), ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-408">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="7c542-409">Ejecute el proyecto para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-409">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c542-411">Para ejecutar la aplicación después de descargar el proyecto completado:</span><span class="sxs-lookup"><span data-stu-id="7c542-411">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="7c542-412">Elimine *ContosoUniversity.csproj* y cambie el nombre de *ContosoUniversitySQLite.csproj* por *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="7c542-412">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="7c542-413">En *Program.cs*, convierta `#define Startup` en comentario para que se use `StartupSQLite`.</span><span class="sxs-lookup"><span data-stu-id="7c542-413">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="7c542-414">Elimine *appSettings.json* y cambie el nombre de *appSettingsSQLite.json* por *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7c542-414">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="7c542-415">Elimine la carpeta *Migrations* y cambie el nombre de *MigrationsSQL* por *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="7c542-415">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="7c542-416">Realice una búsqueda global `#if SQLiteVersion` y quite `#if SQLiteVersion` y la instrucción `#endif` asociada.</span><span class="sxs-lookup"><span data-stu-id="7c542-416">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="7c542-417">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-417">Build the project.</span></span>
* <span data-ttu-id="7c542-418">En un símbolo del sistema en la carpeta del proyecto, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="7c542-418">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="7c542-419">En la herramienta SQLite, ejecute la siguiente instrucción SQL:</span><span class="sxs-lookup"><span data-stu-id="7c542-419">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="7c542-420">Ejecute el proyecto para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-420">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="7c542-421">Creación del proyecto de aplicación web</span><span class="sxs-lookup"><span data-stu-id="7c542-421">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c542-423">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="7c542-423">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7c542-424">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="7c542-424">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="7c542-425">Asigne el nombre *ContosoUniversity* al proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-425">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="7c542-426">Es importante usar este nombre exacto incluido el uso de mayúsculas, para que los espacios de nombres coincidan cuando se copie y pegue el código.</span><span class="sxs-lookup"><span data-stu-id="7c542-426">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="7c542-427">Seleccione **.NET Core** y **ASP.NET Core 3.0** en las listas desplegables y, luego, **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="7c542-427">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-428">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-428">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c542-429">En un terminal, vaya a la carpeta en la que se debe crear la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-429">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="7c542-430">Ejecute los comandos siguientes para crear un proyecto de Razor Pages y `cd` para acceder a la nueva carpeta del proyecto:</span><span class="sxs-lookup"><span data-stu-id="7c542-430">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="7c542-431">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="7c542-431">Set up the site style</span></span>

<span data-ttu-id="7c542-432">Configure el encabezado, el pie de página y el menú del sitio mediante la actualización de *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7c542-432">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="7c542-433">Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="7c542-433">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="7c542-434">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="7c542-434">There are three occurrences.</span></span>

* <span data-ttu-id="7c542-435">Elimine las entradas de menú **Home** y **Privacy**, y agregue entradas para **About**, **Students**, **Courses**, **Instructors** y **Departments**.</span><span class="sxs-lookup"><span data-stu-id="7c542-435">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="7c542-436">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="7c542-436">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="7c542-437">En *Pages/Index.cshtml*, reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET Core con texto sobre esta aplicación:</span><span class="sxs-lookup"><span data-stu-id="7c542-437">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="7c542-438">Ejecute la aplicación para comprobar que aparece la página principal.</span><span class="sxs-lookup"><span data-stu-id="7c542-438">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="7c542-439">El modelo de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-439">The data model</span></span>

<span data-ttu-id="7c542-440">En las secciones siguientes se crea un modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="7c542-440">The following sections create a data model:</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="7c542-442">Un alumno se puede inscribir en cualquier número de cursos y un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="7c542-442">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="7c542-443">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="7c542-443">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

* <span data-ttu-id="7c542-445">Cree una carpeta *Models* en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-445">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="7c542-446">Cree *Models/Student.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-446">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="7c542-447">La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos que se corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="7c542-447">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="7c542-448">De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-448">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="7c542-449">Por tanto, el nombre que se reconoce de forma automática para la clave principal de la clase `Student` es `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-449">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="7c542-450">Para más información, consulte [EF Core: claves](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="7c542-450">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="7c542-451">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7c542-451">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="7c542-452">Las propiedades de navegación contienen otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="7c542-452">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="7c542-453">En este caso, la propiedad `Enrollments` de una entidad `Student` contiene todas las entidades `Enrollment` que están relacionadas con esa instancia de Student.</span><span class="sxs-lookup"><span data-stu-id="7c542-453">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="7c542-454">Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades Enrollment.</span><span class="sxs-lookup"><span data-stu-id="7c542-454">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="7c542-455">En la base de datos, una fila Enrollment se relaciona con una fila Student si su columna StudentID contiene el valor de identificador del alumno.</span><span class="sxs-lookup"><span data-stu-id="7c542-455">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="7c542-456">Por ejemplo, imagine que una fila Student tiene el identificador 1.</span><span class="sxs-lookup"><span data-stu-id="7c542-456">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="7c542-457">Las filas Enrollment relacionadas tendrán StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="7c542-457">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="7c542-458">StudentID es una *clave externa* de la tabla Enrollment.</span><span class="sxs-lookup"><span data-stu-id="7c542-458">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="7c542-459">La propiedad `Enrollments` se define como `ICollection<Enrollment>` porque puede haber varias entidades Enrollment relacionadas.</span><span class="sxs-lookup"><span data-stu-id="7c542-459">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="7c542-460">Puede usar otros tipos de colección, como `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-460">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="7c542-461">Cuando se usa `ICollection<Enrollment>`, EF Core crea una colección `HashSet<Enrollment>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="7c542-461">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="7c542-462">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="7c542-462">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="7c542-464">Cree *Models/Enrollment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-464">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="7c542-465">La propiedad `EnrollmentID` es la clave principal; esta entidad usa el patrón `classnameID` en lugar de `ID` por sí solo.</span><span class="sxs-lookup"><span data-stu-id="7c542-465">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="7c542-466">Para un modelo de datos de producción, elija un patrón y úselo de forma coherente.</span><span class="sxs-lookup"><span data-stu-id="7c542-466">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="7c542-467">En este tutorial se usan los dos simplemente para ilustrar el trabajo.</span><span class="sxs-lookup"><span data-stu-id="7c542-467">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="7c542-468">El uso de `ID` sin `classname` facilita la implementación de algunos tipos de cambios del modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-468">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="7c542-469">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="7c542-469">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="7c542-470">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade`[acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="7c542-470">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="7c542-471">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="7c542-471">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="7c542-472">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-472">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="7c542-473">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-473">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="7c542-474">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="7c542-474">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="7c542-475">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="7c542-475">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="7c542-476">EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-476">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="7c542-477">Por ejemplo, `StudentID` es la clave externa para la propiedad de navegación `Student`, ya que la clave principal de la entidad `Student` es `ID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-477">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="7c542-478">Las propiedades de clave externa también se pueden denominar `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-478">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="7c542-479">Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-479">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="7c542-480">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="7c542-480">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="7c542-482">Cree *Models/Course.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-482">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="7c542-483">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="7c542-483">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="7c542-484">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7c542-484">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="7c542-485">El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.</span><span class="sxs-lookup"><span data-stu-id="7c542-485">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="7c542-486">Compile el proyecto para comprobar que no hay errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="7c542-486">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="7c542-487">Scaffolding de las páginas Student</span><span class="sxs-lookup"><span data-stu-id="7c542-487">Scaffold Student pages</span></span>

<span data-ttu-id="7c542-488">En esta sección, se usa la herramienta de scaffolding de ASP.NET Core para generar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-488">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="7c542-489">Una clase de *contexto* de EF Core.</span><span class="sxs-lookup"><span data-stu-id="7c542-489">An EF Core *context* class.</span></span> <span data-ttu-id="7c542-490">El contexto es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos determinado.</span><span class="sxs-lookup"><span data-stu-id="7c542-490">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="7c542-491">Se deriva de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7c542-491">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="7c542-492">Razor Pages que controlan las operaciones de creación, lectura, actualización y eliminación (CRUD) de la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-492">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-493">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c542-494">Cree una carpeta *Students* en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="7c542-494">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="7c542-495">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Pages/Students* y seleccione **Agregar** > **Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="7c542-495">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7c542-496">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **AGREGAR**.</span><span class="sxs-lookup"><span data-stu-id="7c542-496">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="7c542-497">En el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="7c542-497">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="7c542-498">En la lista desplegable **Clase de modelo**, seleccione **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="7c542-498">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="7c542-499">En la fila **Clase de contexto de datos**, seleccione el signo **+** (más).</span><span class="sxs-lookup"><span data-stu-id="7c542-499">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="7c542-500">Cambie el nombre del contexto de datos *ContosoUniversity.Models.ContosoUniversityContext* por *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="7c542-500">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="7c542-501">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="7c542-501">Select **Add**.</span></span>

<span data-ttu-id="7c542-502">Los paquetes siguientes se instalan de forma automática:</span><span class="sxs-lookup"><span data-stu-id="7c542-502">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-503">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-503">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c542-504">Ejecute los siguientes comandos de la CLI de .NET Core para instalar los paquetes NuGet necesarios:</span><span class="sxs-lookup"><span data-stu-id="7c542-504">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="7c542-505">El paquete Microsoft.VisualStudio.Web.CodeGeneration.Design es necesario para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="7c542-505">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="7c542-506">Aunque en la aplicación no se usará SQL Server, la herramienta de scaffolding necesita el paquete de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7c542-506">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="7c542-507">Cree una carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="7c542-507">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="7c542-508">Ejecute el comando siguiente para instalar la [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="7c542-508">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="7c542-509">Ejecute el comando siguiente para aplicar scaffolding a las páginas Student.</span><span class="sxs-lookup"><span data-stu-id="7c542-509">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="7c542-510">**En Windows**</span><span class="sxs-lookup"><span data-stu-id="7c542-510">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="7c542-511">**En macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="7c542-511">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="7c542-512">Si tiene un problema con el paso anterior, compile el proyecto y vuelva a intentar el paso de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="7c542-512">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="7c542-513">El proceso de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="7c542-513">The scaffolding process:</span></span>

* <span data-ttu-id="7c542-514">Crea Razor Pages en la carpeta *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="7c542-514">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="7c542-515">*Create.cshtml* y *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7c542-515">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="7c542-516">*Delete.cshtml* y *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7c542-516">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="7c542-517">*Details.cshtml* y *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7c542-517">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="7c542-518">*Edit.cshtml* y *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7c542-518">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="7c542-519">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7c542-519">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="7c542-520">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="7c542-520">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="7c542-521">Agrega el contexto a la inserción de dependencias en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7c542-521">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="7c542-522">Agrega una cadena de conexión de la base de datos a *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7c542-522">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="7c542-523">Cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-523">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-524">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-524">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c542-525">El archivo *appsettings.json* especifica la cadena de conexión [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="7c542-525">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="7c542-526">LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="7c542-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="7c542-527">De forma predeterminada, LocalDB crea archivos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-527">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-528">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-528">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c542-529">Cambie la cadena de conexión para que apunte a un archivo de base de datos de SQLite denominado *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="7c542-529">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="7c542-530">Actualización de la clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-530">Update the database context class</span></span>

<span data-ttu-id="7c542-531">La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-531">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="7c542-532">El contexto se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7c542-532">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7c542-533">En el contexto se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-533">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="7c542-534">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7c542-534">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="7c542-535">Actualice *Data/SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-535">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="7c542-536">El código resaltado crea una propiedad [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="7c542-536">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="7c542-537">En la terminología de EF Core:</span><span class="sxs-lookup"><span data-stu-id="7c542-537">In EF Core terminology:</span></span>

* <span data-ttu-id="7c542-538">Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-538">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="7c542-539">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="7c542-539">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7c542-540">Puesto que un conjunto de entidades contiene varias entidades, las propiedades DBSet deben ser nombres en plural.</span><span class="sxs-lookup"><span data-stu-id="7c542-540">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="7c542-541">Como la herramienta de scaffolding ha creado una instancia `Student` de DBSet, en este paso se cambia a `Students` en plural.</span><span class="sxs-lookup"><span data-stu-id="7c542-541">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="7c542-542">Para que el código de Razor Pages coincida con el nuevo nombre de DBSet, realice un cambio global de `_context.Student` a `_context.Students` en todo el proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-542">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="7c542-543">Hay ocho repeticiones.</span><span class="sxs-lookup"><span data-stu-id="7c542-543">There are 8 occurrences.</span></span>

<span data-ttu-id="7c542-544">Compile el proyecto para comprobar que no haya errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="7c542-544">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="7c542-545">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="7c542-545">Startup.cs</span></span>

<span data-ttu-id="7c542-546">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7c542-546">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7c542-547">Los servicios (como el contexto de base de datos de EF Core) se registran con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-547">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7c542-548">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="7c542-548">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7c542-549">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="7c542-549">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7c542-550">La herramienta de scaffolding ha registrado de forma automática la clase de contexto con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="7c542-550">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-551">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-551">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c542-552">En `ConfigureServices`, el proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="7c542-552">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-553">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-553">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c542-554">En `ConfigureServices`, asegúrese de que el código agregado por el proveedor de scaffolding llama a `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="7c542-554">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="7c542-555">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="7c542-555">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7c542-556">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7c542-556">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="7c542-557">Creación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-557">Create the database</span></span>

<span data-ttu-id="7c542-558">Actualice *Program.cs* para crear la base de datos si no existe:</span><span class="sxs-lookup"><span data-stu-id="7c542-558">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="7c542-559">El método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) no realiza ninguna acción si existe una base de datos para el contexto.</span><span class="sxs-lookup"><span data-stu-id="7c542-559">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="7c542-560">Si no existe ninguna base de datos, se crean la base de datos y el esquema.</span><span class="sxs-lookup"><span data-stu-id="7c542-560">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="7c542-561">`EnsureCreated` habilita el flujo de trabajo siguiente para controlar los cambios del modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="7c542-561">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="7c542-562">Se elimina la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-562">Delete the database.</span></span> <span data-ttu-id="7c542-563">Se pierden los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="7c542-563">Any existing data is lost.</span></span>
* <span data-ttu-id="7c542-564">Se cambia el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-564">Change the data model.</span></span> <span data-ttu-id="7c542-565">Por ejemplo, se agrega un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="7c542-565">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="7c542-566">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-566">Run the app.</span></span>
* <span data-ttu-id="7c542-567">`EnsureCreated` crea una base de datos con el esquema nuevo.</span><span class="sxs-lookup"><span data-stu-id="7c542-567">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="7c542-568">Este flujo de trabajo funciona bien al principio de la fase de desarrollo cuando el esquema evoluciona rápidamente, siempre y cuando no sea necesario conservar los datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-568">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="7c542-569">La situación es distinta cuando es necesario conservar los datos introducidos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-569">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="7c542-570">En ese caso, use las migraciones.</span><span class="sxs-lookup"><span data-stu-id="7c542-570">When that is the case, use migrations.</span></span>

<span data-ttu-id="7c542-571">Más adelante en la serie de tutoriales, eliminará la base de datos creada por `EnsureCreated` y, en su lugar, usará las migraciones.</span><span class="sxs-lookup"><span data-stu-id="7c542-571">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="7c542-572">Una base de datos creada por `EnsureCreated` no se puede actualizar mediante migraciones.</span><span class="sxs-lookup"><span data-stu-id="7c542-572">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7c542-573">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="7c542-573">Test the app</span></span>

* <span data-ttu-id="7c542-574">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-574">Run the app.</span></span>
* <span data-ttu-id="7c542-575">Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="7c542-575">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="7c542-576">Pruebe los vínculos Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="7c542-576">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="7c542-577">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-577">Seed the database</span></span>

<span data-ttu-id="7c542-578">El método `EnsureCreated` crea una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="7c542-578">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="7c542-579">En esta sección se agrega código que rellena la base de datos con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="7c542-579">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="7c542-580">Cree *Data/DbInitializer.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-580">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="7c542-581">El código comprueba si hay alumnos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-581">The code checks if there are any students in the database.</span></span> <span data-ttu-id="7c542-582">Si no hay ningún alumno, agrega datos de prueba a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-582">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="7c542-583">Crea los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="7c542-583">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="7c542-584">En *Program.cs*, reemplace la llamada a `EnsureCreated` con una llamada a `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="7c542-584">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-585">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-585">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c542-586">Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="7c542-586">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-587">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-587">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c542-588">Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="7c542-588">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="7c542-589">Reinicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-589">Restart the app.</span></span>

* <span data-ttu-id="7c542-590">Seleccione la página Students para ver los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="7c542-590">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="7c542-591">Consulta la base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-591">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-592">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-592">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c542-593">Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7c542-593">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="7c542-594">En SSOX, seleccione **(localdb)\MSSQLLocalDB > Bases de datos > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="7c542-594">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="7c542-595">El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.</span><span class="sxs-lookup"><span data-stu-id="7c542-595">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="7c542-596">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="7c542-596">Expand the **Tables** node.</span></span>
* <span data-ttu-id="7c542-597">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="7c542-597">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="7c542-598">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver código** para ver cómo el modelo `Student` se asigna al esquema de tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-598">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-599">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-599">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c542-600">Use la herramienta SQLite para ver el esquema de la base de datos y los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="7c542-600">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="7c542-601">El archivo de base de datos se denomina *CU.db* y se encuentra en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-601">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="7c542-602">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="7c542-602">Asynchronous code</span></span>

<span data-ttu-id="7c542-603">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="7c542-603">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="7c542-604">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="7c542-604">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="7c542-605">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="7c542-605">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="7c542-606">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="7c542-606">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="7c542-607">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="7c542-607">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="7c542-608">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz y el servidor pueda administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="7c542-608">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="7c542-609">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="7c542-609">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="7c542-610">En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="7c542-610">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="7c542-611">En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="7c542-611">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="7c542-612">La palabra clave `async` indica al compilador que:</span><span class="sxs-lookup"><span data-stu-id="7c542-612">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="7c542-613">Genere devoluciones de llamada para partes del cuerpo del método.</span><span class="sxs-lookup"><span data-stu-id="7c542-613">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="7c542-614">Cree el objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="7c542-614">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="7c542-615">El tipo devuelto `Task<T>` representa el trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="7c542-615">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="7c542-616">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="7c542-616">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="7c542-617">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="7c542-617">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="7c542-618">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="7c542-618">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="7c542-619">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="7c542-619">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="7c542-620">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="7c542-620">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="7c542-621">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-621">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="7c542-622">Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="7c542-622">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="7c542-623">No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="7c542-623">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="7c542-624">Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="7c542-624">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="7c542-625">Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para la paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-625">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="7c542-626">Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="7c542-626">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="7c542-627">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="7c542-627">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="7c542-628">Siguiente tutorial</span><span class="sxs-lookup"><span data-stu-id="7c542-628">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7c542-629">En la aplicación web de ejemplo Contoso University se muestra cómo crear una aplicación web de Razor Pages de ASP.NET Core con Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="7c542-629">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="7c542-630">La aplicación de ejemplo es un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="7c542-630">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="7c542-631">Incluye funciones como la admisión de estudiantes, la creación de cursos y asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="7c542-631">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="7c542-632">Esta página es la primera de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="7c542-632">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="7c542-633">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="7c542-633">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="7c542-634">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7c542-634">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7c542-635">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="7c542-635">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-636">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-636">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-637">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-637">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="7c542-638">Familiaridad con [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="7c542-638">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="7c542-639">Los programadores nuevos deben completar [Introducción a Razor Pages en ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start) antes de empezar esta serie.</span><span class="sxs-lookup"><span data-stu-id="7c542-639">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7c542-640">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="7c542-640">Troubleshooting</span></span>

<span data-ttu-id="7c542-641">Si experimenta un problema que no puede resolver, por lo general podrá encontrar la solución si compara el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="7c542-641">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="7c542-642">Una buena forma de obtener ayuda consiste en publicar una pregunta en [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) para [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="7c542-642">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="7c542-643">La aplicación web Contoso University</span><span class="sxs-lookup"><span data-stu-id="7c542-643">The Contoso University web app</span></span>

<span data-ttu-id="7c542-644">La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.</span><span class="sxs-lookup"><span data-stu-id="7c542-644">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="7c542-645">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="7c542-645">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="7c542-646">Estas son algunas de las pantallas que se crean en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="7c542-646">Here are a few of the screens created in the tutorial.</span></span>

![Página de índice de Students](intro/_static/students-index.png)

![Página de edición de estudiantes](intro/_static/student-edit.png)

<span data-ttu-id="7c542-649">El estilo de la interfaz de usuario de este sitio se mantiene fiel a lo que generan las plantillas integradas.</span><span class="sxs-lookup"><span data-stu-id="7c542-649">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="7c542-650">El tutorial se centra en EF Core con Razor Pages, no en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="7c542-650">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="7c542-651">Creación de la aplicación web de Razor Pages ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="7c542-651">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-652">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-652">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c542-653">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="7c542-653">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7c542-654">Cree una aplicación web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c542-654">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="7c542-655">Asigne el nombre **ContosoUniversity** al proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-655">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="7c542-656">Es importante que el nombre del proyecto sea *ContosoUniversity* para que coincidan los espacios de nombres al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="7c542-656">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="7c542-657">Seleccione **ASP.NET Core 2.1** en la lista desplegable y, luego, **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="7c542-657">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="7c542-658">Para ver las imágenes de los pasos anteriores, consulte [Creación de una aplicación web de Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="7c542-658">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="7c542-659">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-659">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-660">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-660">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="7c542-661">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="7c542-661">Set up the site style</span></span>

<span data-ttu-id="7c542-662">Con algunos cambios se configura el menú del sitio, el diseño y la página principal.</span><span class="sxs-lookup"><span data-stu-id="7c542-662">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="7c542-663">Actualice *Pages/Shared/_Layout.cshtml* con los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="7c542-663">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="7c542-664">Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="7c542-664">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="7c542-665">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="7c542-665">There are three occurrences.</span></span>

* <span data-ttu-id="7c542-666">Agregue entradas de menú para **Students**, **Courses**, **Instructors** y **Departments**, y elimine la entrada de menú **Contact**.</span><span class="sxs-lookup"><span data-stu-id="7c542-666">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="7c542-667">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="7c542-667">The changes are highlighted.</span></span> <span data-ttu-id="7c542-668">(*No* se muestra todo el marcado).</span><span class="sxs-lookup"><span data-stu-id="7c542-668">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="7c542-669">En *Pages/Index.cshtml*, reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET y MVC con texto sobre esta aplicación:</span><span class="sxs-lookup"><span data-stu-id="7c542-669">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="7c542-670">Crear el modelo de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-670">Create the data model</span></span>

<span data-ttu-id="7c542-671">Cree las clases de entidad para la aplicación Contoso University.</span><span class="sxs-lookup"><span data-stu-id="7c542-671">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="7c542-672">Comience con las tres entidades siguientes:</span><span class="sxs-lookup"><span data-stu-id="7c542-672">Start with the following three entities:</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="7c542-674">Hay una relación uno a varios entre las entidades `Student` y `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7c542-674">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="7c542-675">Hay una relación uno a varios entre las entidades `Course` y `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7c542-675">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="7c542-676">Un estudiante se puede inscribir en cualquier número de cursos.</span><span class="sxs-lookup"><span data-stu-id="7c542-676">A student can enroll in any number of courses.</span></span> <span data-ttu-id="7c542-677">Un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="7c542-677">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="7c542-678">En las secciones siguientes, se crea una clase para cada una de estas entidades.</span><span class="sxs-lookup"><span data-stu-id="7c542-678">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="7c542-679">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="7c542-679">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

<span data-ttu-id="7c542-681">Cree una carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="7c542-681">Create a *Models* folder.</span></span> <span data-ttu-id="7c542-682">En la carpeta *Models*, cree un archivo de clase denominado *Student.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-682">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="7c542-683">La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos (DB) que corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="7c542-683">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="7c542-684">De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-684">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="7c542-685">En `classnameID`, `classname` es el nombre de la clase.</span><span class="sxs-lookup"><span data-stu-id="7c542-685">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="7c542-686">En el ejemplo anterior, la clave principal alternativa que se reconoce de forma automática es `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-686">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="7c542-687">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7c542-687">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="7c542-688">Las propiedades de navegación se vinculan a otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="7c542-688">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="7c542-689">En este caso, la propiedad `Enrollments` de una `Student entity` contiene todas las entidades `Enrollment` que están relacionadas con esa entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-689">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="7c542-690">Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7c542-690">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="7c542-691">Una fila `Enrollment` relacionada es la que contiene el valor de clave principal de ese estudiante en la columna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-691">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="7c542-692">Por ejemplo, suponga que el estudiante con ID=1 tiene dos filas en la tabla `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7c542-692">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="7c542-693">La tabla `Enrollment` tiene dos filas con `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="7c542-693">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="7c542-694">`StudentID` es una clave externa en la tabla `Enrollment` que especifica el estudiante en la tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-694">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="7c542-695">Si una propiedad de navegación puede contener varias entidades, la propiedad de navegación debe ser un tipo de lista, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-695">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="7c542-696">Se puede especificar `ICollection<T>`, o bien un tipo como `List<T>` o `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-696">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="7c542-697">Cuando se usa `ICollection<T>`, EF Core crea una colección `HashSet<T>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="7c542-697">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="7c542-698">Las propiedades de navegación que contienen varias entidades proceden de relaciones de varios a varios y uno a varios.</span><span class="sxs-lookup"><span data-stu-id="7c542-698">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="7c542-699">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="7c542-699">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="7c542-701">En la carpeta *Models*, cree *Enrollment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-701">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="7c542-702">La propiedad `EnrollmentID` es la clave principal.</span><span class="sxs-lookup"><span data-stu-id="7c542-702">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="7c542-703">En esta entidad se usa el patrón `classnameID` en lugar de `ID` como en la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-703">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="7c542-704">Normalmente, los desarrolladores eligen un patrón y lo usan en todo el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-704">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="7c542-705">En un tutorial posterior, se muestra el uso de ID sin un nombre de clase para facilitar la implementación de la herencia en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-705">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="7c542-706">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="7c542-706">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="7c542-707">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade` acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="7c542-707">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="7c542-708">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="7c542-708">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="7c542-709">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-709">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="7c542-710">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="7c542-710">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="7c542-711">La entidad `Student` difiere de la propiedad de navegación `Student.Enrollments`, que contiene varias entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7c542-711">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="7c542-712">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="7c542-712">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="7c542-713">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="7c542-713">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="7c542-714">EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-714">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="7c542-715">Por ejemplo, `StudentID` para la propiedad de navegación `Student`, puesto que la clave principal de la entidad `Student` es `ID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-715">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="7c542-716">Las propiedades de clave externa también se pueden denominar `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-716">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="7c542-717">Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="7c542-717">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="7c542-718">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="7c542-718">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="7c542-720">En la carpeta *Models*, cree *Course.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-720">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="7c542-721">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="7c542-721">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="7c542-722">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="7c542-722">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="7c542-723">El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.</span><span class="sxs-lookup"><span data-stu-id="7c542-723">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="7c542-724">Aplicación de scaffolding al modelo de alumnos</span><span class="sxs-lookup"><span data-stu-id="7c542-724">Scaffold the student model</span></span>

<span data-ttu-id="7c542-725">En esta sección, se aplica scaffolding al modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="7c542-725">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="7c542-726">Es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="7c542-726">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="7c542-727">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="7c542-727">Build the project.</span></span>
* <span data-ttu-id="7c542-728">Cree la carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="7c542-728">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-729">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-729">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c542-730">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Pages/Students* > **Agregar** > **Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="7c542-730">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7c542-731">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **AGREGAR**.</span><span class="sxs-lookup"><span data-stu-id="7c542-731">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="7c542-732">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="7c542-732">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7c542-733">En la lista desplegable **Clase de modelo**, seleccione **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="7c542-733">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="7c542-734">En la fila **Clase de contexto de datos**, haga clic en el signo **+** (más) y cambie el nombre generado por **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="7c542-734">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="7c542-735">En la lista desplegable **Clase de contexto de datos**, seleccione **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="7c542-735">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="7c542-736">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="7c542-736">Select **Add**.</span></span>

![Cuadro de diálogo CRUD](intro/_static/s1.png)

<span data-ttu-id="7c542-738">Si tiene algún problema con el paso anterior, consulte [Aplicar scaffolding al modelo de película](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="7c542-738">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-739">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-739">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c542-740">Ejecute los comandos siguientes para aplicar scaffolding al modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="7c542-740">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="7c542-741">El proceso de scaffolding ha creado y cambiado los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="7c542-741">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="7c542-742">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="7c542-742">Files created</span></span>

* <span data-ttu-id="7c542-743">*Pages/Students* Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="7c542-743">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="7c542-744">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="7c542-744">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="7c542-745">Actualizaciones de archivos</span><span class="sxs-lookup"><span data-stu-id="7c542-745">File updates</span></span>

* <span data-ttu-id="7c542-746">*Startup.cs*: en la sección siguiente se detallan los cambios realizados en este archivo.</span><span class="sxs-lookup"><span data-stu-id="7c542-746">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="7c542-747">*appsettings.json* : se agrega la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="7c542-747">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7c542-748">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="7c542-748">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7c542-749">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7c542-749">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7c542-750">Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-750">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7c542-751">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="7c542-751">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7c542-752">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="7c542-752">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7c542-753">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="7c542-753">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7c542-754">Examine el método `ConfigureServices` de *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7c542-754">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="7c542-755">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="7c542-755">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="7c542-756">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="7c542-756">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7c542-757">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7c542-757">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="7c542-758">Actualización de main</span><span class="sxs-lookup"><span data-stu-id="7c542-758">Update main</span></span>

<span data-ttu-id="7c542-759">En *Program.cs*, modifique el método `Main` para que haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-759">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="7c542-760">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="7c542-760">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="7c542-761">Llame a [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="7c542-761">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="7c542-762">Elimine el contexto cuando finalice el método `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="7c542-762">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="7c542-763">En el código siguiente se muestra el archivo *Program.cs* actualizado.</span><span class="sxs-lookup"><span data-stu-id="7c542-763">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="7c542-764">`EnsureCreated` garantiza la existencia de la base de datos para el contexto.</span><span class="sxs-lookup"><span data-stu-id="7c542-764">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="7c542-765">Si existe, no se realiza ninguna acción.</span><span class="sxs-lookup"><span data-stu-id="7c542-765">If it exists, no action is taken.</span></span> <span data-ttu-id="7c542-766">Si no existe, se crean la base de datos y todo su esquema.</span><span class="sxs-lookup"><span data-stu-id="7c542-766">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="7c542-767">En `EnsureCreated` no se usan migraciones para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-767">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="7c542-768">Una base de datos que se cree con `EnsureCreated` no se podrá actualizar más adelante mediante las migraciones.</span><span class="sxs-lookup"><span data-stu-id="7c542-768">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="7c542-769">`EnsureCreated` se llama durante el inicio de la aplicación, lo que permite el flujo de trabajo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-769">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="7c542-770">Se elimina la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-770">Delete the DB.</span></span>
* <span data-ttu-id="7c542-771">Se cambia el esquema de base de datos (por ejemplo, se agrega un campo `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="7c542-771">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="7c542-772">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c542-772">Run the app.</span></span>
* <span data-ttu-id="7c542-773">`EnsureCreated` crea una base de datos con la columna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="7c542-773">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="7c542-774">`EnsureCreated` es útil al principio del desarrollo, cuando el esquema evoluciona rápidamente.</span><span class="sxs-lookup"><span data-stu-id="7c542-774">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="7c542-775">Más adelante, en el tutorial se elimina la base de datos y se usan las migraciones.</span><span class="sxs-lookup"><span data-stu-id="7c542-775">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7c542-776">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="7c542-776">Test the app</span></span>

<span data-ttu-id="7c542-777">Ejecute la aplicación y acepte la directiva de cookies.</span><span class="sxs-lookup"><span data-stu-id="7c542-777">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="7c542-778">Esta aplicación no conserva información de carácter personal.</span><span class="sxs-lookup"><span data-stu-id="7c542-778">This app doesn't keep personal information.</span></span> <span data-ttu-id="7c542-779">Puede obtener más información sobre la directiva de cookies en [Compatibilidad con el Reglamento general de protección de datos (RGPD) de la UE](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="7c542-779">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="7c542-780">Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="7c542-780">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="7c542-781">Pruebe los vínculos Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="7c542-781">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="7c542-782">Examinar el contexto de base de datos SchoolContext</span><span class="sxs-lookup"><span data-stu-id="7c542-782">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="7c542-783">La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-783">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="7c542-784">El contexto de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7c542-784">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7c542-785">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-785">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="7c542-786">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7c542-786">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="7c542-787">Actualice *SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-787">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="7c542-788">El código resaltado crea una propiedad [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="7c542-788">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="7c542-789">En la terminología de EF Core:</span><span class="sxs-lookup"><span data-stu-id="7c542-789">In EF Core terminology:</span></span>

* <span data-ttu-id="7c542-790">Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-790">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="7c542-791">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="7c542-791">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7c542-792">`DbSet<Enrollment>` y `DbSet<Course>` se pueden omitir.</span><span class="sxs-lookup"><span data-stu-id="7c542-792">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="7c542-793">EF Core las incluye implícitamente porque la entidad `Student` hace referencia a la entidad `Enrollment` y la entidad `Enrollment` hace referencia a la entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="7c542-793">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="7c542-794">Para este tutorial, conserve `DbSet<Enrollment>` y `DbSet<Course>` en el `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="7c542-794">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="7c542-795">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="7c542-795">SQL Server Express LocalDB</span></span>

<span data-ttu-id="7c542-796">La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="7c542-796">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="7c542-797">LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="7c542-797">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="7c542-798">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="7c542-798">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="7c542-799">De forma predeterminada, LocalDB crea archivos de base de datos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="7c542-799">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="7c542-800">Agregar código para inicializar la base de datos con datos de prueba</span><span class="sxs-lookup"><span data-stu-id="7c542-800">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="7c542-801">EF Core crea una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="7c542-801">EF Core creates an empty DB.</span></span> <span data-ttu-id="7c542-802">En esta sección, se escribe un método `Initialize` para rellenarlo con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="7c542-802">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="7c542-803">En la carpeta *Data*, cree un archivo de clase denominado *DbInitializer.cs* y agregue el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7c542-803">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="7c542-804">Nota: El código anterior usa `Models` para el espacio de nombres (`namespace ContosoUniversity.Models`) en lugar de `Data`.</span><span class="sxs-lookup"><span data-stu-id="7c542-804">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="7c542-805">`Models` es coherente con el código generado por el proveedor de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="7c542-805">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="7c542-806">Para obtener más información, consulte [este problema de scaffolding de GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="7c542-806">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="7c542-807">El código comprueba si hay estudiantes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-807">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="7c542-808">Si no hay alumnos en la base de datos, se inicializa con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="7c542-808">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="7c542-809">Carga los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="7c542-809">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="7c542-810">El método `EnsureCreated` crea automáticamente la base de datos para el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-810">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="7c542-811">Si la base de datos existe, `EnsureCreated` vuelve sin modificarla.</span><span class="sxs-lookup"><span data-stu-id="7c542-811">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="7c542-812">En *Program.cs*, modifique el método `Main` para que llame a `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="7c542-812">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="7c542-813">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c542-813">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c542-814">Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="7c542-814">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c542-815">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c542-815">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c542-816">Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="7c542-816">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="7c542-817">Ver la base de datos</span><span class="sxs-lookup"><span data-stu-id="7c542-817">View the DB</span></span>

<span data-ttu-id="7c542-818">El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.</span><span class="sxs-lookup"><span data-stu-id="7c542-818">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="7c542-819">Por lo tanto, el nombre de la base de datos será "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="7c542-819">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="7c542-820">El GUID será diferente para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="7c542-820">The GUID will be different for each user.</span></span>
<span data-ttu-id="7c542-821">Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7c542-821">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="7c542-822">En SSOX, haga clic en **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="7c542-822">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="7c542-823">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="7c542-823">Expand the **Tables** node.</span></span>

<span data-ttu-id="7c542-824">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="7c542-824">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="7c542-825">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="7c542-825">Asynchronous code</span></span>

<span data-ttu-id="7c542-826">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="7c542-826">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="7c542-827">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="7c542-827">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="7c542-828">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="7c542-828">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="7c542-829">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="7c542-829">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="7c542-830">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="7c542-830">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="7c542-831">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz, y el servidor está habilitado para administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="7c542-831">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="7c542-832">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="7c542-832">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="7c542-833">En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="7c542-833">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="7c542-834">En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="7c542-834">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="7c542-835">La palabra clave `async` indica al compilador que:</span><span class="sxs-lookup"><span data-stu-id="7c542-835">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="7c542-836">Genere devoluciones de llamada para partes del cuerpo del método.</span><span class="sxs-lookup"><span data-stu-id="7c542-836">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="7c542-837">Cree automáticamente el objeto [Task](/dotnet/api/system.threading.tasks.task) que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="7c542-837">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="7c542-838">Para más información, vea [Tipo de valor devuelto Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="7c542-838">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="7c542-839">El tipo devuelto implícito `Task` representa el trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="7c542-839">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="7c542-840">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="7c542-840">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="7c542-841">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="7c542-841">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="7c542-842">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="7c542-842">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="7c542-843">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="7c542-843">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="7c542-844">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="7c542-844">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="7c542-845">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-845">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="7c542-846">Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="7c542-846">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="7c542-847">No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="7c542-847">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="7c542-848">Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="7c542-848">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="7c542-849">Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7c542-849">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="7c542-850">Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="7c542-850">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="7c542-851">En el siguiente tutorial, se examinan las operaciones CRUD (crear, leer, actualizar y eliminar) básicas.</span><span class="sxs-lookup"><span data-stu-id="7c542-851">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="7c542-852">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7c542-852">Additional resources</span></span>

* [<span data-ttu-id="7c542-853">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="7c542-853">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="7c542-854">Siguiente</span><span class="sxs-lookup"><span data-stu-id="7c542-854">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
