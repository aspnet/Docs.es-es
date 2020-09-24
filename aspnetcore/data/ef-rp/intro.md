---
title: 'Razor Pages con Entity Framework Core en ASP.NET Core: Tutorial 1 de 8'
author: rick-anderson
description: Se muestra cómo crear una aplicación de Razor Pages mediante Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 9dd8d293e189eebe6b61f6f0b35aee71977d2f77
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722558"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="07d97-103">Razor Pages con Entity Framework Core en ASP.NET Core: Tutorial 1 de 8</span><span class="sxs-lookup"><span data-stu-id="07d97-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="07d97-104">Por [Tom Dykstra](https://github.com/tdykstra) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="07d97-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="07d97-105">Este es el primero de una serie de tutoriales en los que se muestra cómo usar Entity Framework (EF) Core en una [aplicación Razor Pages](xref:razor-pages/index) en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07d97-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="07d97-106">En el tutorial se crea un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="07d97-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="07d97-107">El sitio incluye funciones como la admisión de alumnos, la creación de cursos y las asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="07d97-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="07d97-108">En el tutorial se usa el enfoque de Code First.</span><span class="sxs-lookup"><span data-stu-id="07d97-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="07d97-109">Para obtener información sobre cómo seguir este tutorial mediante el enfoque de Database First, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="07d97-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="07d97-110">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="07d97-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="07d97-111">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="07d97-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="07d97-112">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="07d97-112">Prerequisites</span></span>

* <span data-ttu-id="07d97-113">Si no está familiarizado con Razor Pages, consulte la serie de tutoriales [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) antes de empezar este.</span><span class="sxs-lookup"><span data-stu-id="07d97-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="07d97-116">Motores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-116">Database engines</span></span>

<span data-ttu-id="07d97-117">En las instrucciones de Visual Studio se usa [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versión de SQL Server Express que solo se ejecuta en Windows.</span><span class="sxs-lookup"><span data-stu-id="07d97-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="07d97-118">En las instrucciones de Visual Studio Code se usa [SQLite](https://www.sqlite.org/), un motor de base de datos multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="07d97-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="07d97-119">Si decide usar SQLite, descargue e instale una herramienta de terceros para administrar y ver una base de datos de SQLite, como [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="07d97-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="07d97-120">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="07d97-120">Troubleshooting</span></span>

<span data-ttu-id="07d97-121">Si experimenta un problema que no puede resolver, compare el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="07d97-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="07d97-122">Una buena forma de obtener ayuda consiste en publicar una pregunta en StackOverflow.com con la [etiqueta ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o la [etiqueta EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="07d97-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="07d97-123">La aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="07d97-123">The sample app</span></span>

<span data-ttu-id="07d97-124">La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.</span><span class="sxs-lookup"><span data-stu-id="07d97-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="07d97-125">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="07d97-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="07d97-126">Estas son algunas de las pantallas que se crean en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="07d97-126">Here are a few of the screens created in the tutorial.</span></span>

![Página de índice de Students](intro/_static/students-index30.png)

![Página de edición de estudiantes](intro/_static/student-edit30.png)

<span data-ttu-id="07d97-129">El estilo de la interfaz de usuario de este sitio se basa en las plantillas de proyecto integradas.</span><span class="sxs-lookup"><span data-stu-id="07d97-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="07d97-130">El enfoque del tutorial es cómo usar EF Core, no cómo personalizar la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="07d97-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="07d97-131">Siga el vínculo de la parte superior de la página para obtener el código fuente para el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="07d97-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="07d97-132">La carpeta *cu30* contiene el código para la versión ASP.NET Core 3.0 del tutorial.</span><span class="sxs-lookup"><span data-stu-id="07d97-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="07d97-133">Los archivos que reflejan el estado del código para los tutoriales 1-7 se pueden encontrar en la carpeta *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="07d97-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07d97-135">Para ejecutar la aplicación después de descargar el proyecto completado:</span><span class="sxs-lookup"><span data-stu-id="07d97-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="07d97-136">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-136">Build the project.</span></span>
* <span data-ttu-id="07d97-137">En la Consola del administrador de paquetes (PMC), ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-137">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="07d97-138">Ejecute el proyecto para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-138">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="07d97-140">Para ejecutar la aplicación después de descargar el proyecto completado:</span><span class="sxs-lookup"><span data-stu-id="07d97-140">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="07d97-141">Elimine *ContosoUniversity.csproj* y cambie el nombre de *ContosoUniversitySQLite.csproj* por *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="07d97-141">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="07d97-142">En *Program.cs*, convierta `#define Startup` en comentario para que se use `StartupSQLite`.</span><span class="sxs-lookup"><span data-stu-id="07d97-142">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="07d97-143">Elimine *appSettings.json* y cambie el nombre de *appSettingsSQLite.json* por *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="07d97-143">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="07d97-144">Elimine la carpeta *Migrations* y cambie el nombre de *MigrationsSQL* por *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="07d97-144">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="07d97-145">Realice una búsqueda global `#if SQLiteVersion` y quite `#if SQLiteVersion` y la instrucción `#endif` asociada.</span><span class="sxs-lookup"><span data-stu-id="07d97-145">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="07d97-146">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-146">Build the project.</span></span>
* <span data-ttu-id="07d97-147">En un símbolo del sistema en la carpeta del proyecto, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="07d97-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="07d97-148">En la herramienta SQLite, ejecute la siguiente instrucción SQL:</span><span class="sxs-lookup"><span data-stu-id="07d97-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="07d97-149">Ejecute el proyecto para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="07d97-150">Creación del proyecto de aplicación web</span><span class="sxs-lookup"><span data-stu-id="07d97-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d97-152">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="07d97-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="07d97-153">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="07d97-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="07d97-154">Asigne el nombre *ContosoUniversity* al proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="07d97-155">Es importante usar este nombre exacto incluido el uso de mayúsculas, para que los espacios de nombres coincidan cuando se copie y pegue el código.</span><span class="sxs-lookup"><span data-stu-id="07d97-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="07d97-156">Seleccione **.NET Core** y **ASP.NET Core 3.0** en las listas desplegables y, luego, **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="07d97-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d97-158">En un terminal, vaya a la carpeta en la que se debe crear la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="07d97-159">Ejecute los comandos siguientes para crear un proyecto de Razor Pages y `cd` para acceder a la nueva carpeta del proyecto:</span><span class="sxs-lookup"><span data-stu-id="07d97-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="07d97-160">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="07d97-160">Set up the site style</span></span>

<span data-ttu-id="07d97-161">Configure el encabezado, el pie de página y el menú del sitio mediante la actualización de *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="07d97-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="07d97-162">Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="07d97-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="07d97-163">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="07d97-163">There are three occurrences.</span></span>

* <span data-ttu-id="07d97-164">Elimine las entradas de menú **Home** y **Privacy**, y agregue entradas para **About**, **Students**, **Courses**, **Instructors** y **Departments**.</span><span class="sxs-lookup"><span data-stu-id="07d97-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="07d97-165">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="07d97-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="07d97-166">En *Pages/Index.cshtml*, reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET Core con texto sobre esta aplicación:</span><span class="sxs-lookup"><span data-stu-id="07d97-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="07d97-167">Ejecute la aplicación para comprobar que aparece la página principal.</span><span class="sxs-lookup"><span data-stu-id="07d97-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="07d97-168">El modelo de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-168">The data model</span></span>

<span data-ttu-id="07d97-169">En las secciones siguientes se crea un modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="07d97-169">The following sections create a data model:</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="07d97-171">Un alumno se puede inscribir en cualquier número de cursos y un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="07d97-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="07d97-172">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="07d97-172">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

* <span data-ttu-id="07d97-174">Cree una carpeta *Models* en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="07d97-175">Cree *Models/Student.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="07d97-176">La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos que se corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="07d97-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="07d97-177">De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="07d97-178">Por tanto, el nombre que se reconoce de forma automática para la clave principal de la clase `Student` es `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="07d97-179">Para más información, consulte [EF Core: claves](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="07d97-179">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="07d97-180">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="07d97-180">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="07d97-181">Las propiedades de navegación contienen otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="07d97-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="07d97-182">En este caso, la propiedad `Enrollments` de una entidad `Student` contiene todas las entidades `Enrollment` que están relacionadas con esa instancia de Student.</span><span class="sxs-lookup"><span data-stu-id="07d97-182">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="07d97-183">Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades Enrollment.</span><span class="sxs-lookup"><span data-stu-id="07d97-183">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="07d97-184">En la base de datos, una fila Enrollment se relaciona con una fila Student si su columna StudentID contiene el valor de identificador del alumno.</span><span class="sxs-lookup"><span data-stu-id="07d97-184">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="07d97-185">Por ejemplo, imagine que una fila Student tiene el identificador 1.</span><span class="sxs-lookup"><span data-stu-id="07d97-185">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="07d97-186">Las filas Enrollment relacionadas tendrán StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="07d97-186">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="07d97-187">StudentID es una *clave externa* de la tabla Enrollment.</span><span class="sxs-lookup"><span data-stu-id="07d97-187">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="07d97-188">La propiedad `Enrollments` se define como `ICollection<Enrollment>` porque puede haber varias entidades Enrollment relacionadas.</span><span class="sxs-lookup"><span data-stu-id="07d97-188">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="07d97-189">Puede usar otros tipos de colección, como `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-189">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="07d97-190">Cuando se usa `ICollection<Enrollment>`, EF Core crea una colección `HashSet<Enrollment>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="07d97-190">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="07d97-191">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="07d97-191">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="07d97-193">Cree *Models/Enrollment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-193">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="07d97-194">La propiedad `EnrollmentID` es la clave principal; esta entidad usa el patrón `classnameID` en lugar de `ID` por sí solo.</span><span class="sxs-lookup"><span data-stu-id="07d97-194">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="07d97-195">Para un modelo de datos de producción, elija un patrón y úselo de forma coherente.</span><span class="sxs-lookup"><span data-stu-id="07d97-195">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="07d97-196">En este tutorial se usan los dos simplemente para ilustrar el trabajo.</span><span class="sxs-lookup"><span data-stu-id="07d97-196">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="07d97-197">El uso de `ID` sin `classname` facilita la implementación de algunos tipos de cambios del modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-197">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="07d97-198">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="07d97-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="07d97-199">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade`[acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="07d97-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="07d97-200">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="07d97-200">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="07d97-201">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="07d97-202">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-202">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="07d97-203">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="07d97-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="07d97-204">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="07d97-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="07d97-205">EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-205">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="07d97-206">Por ejemplo, `StudentID` es la clave externa para la propiedad de navegación `Student`, ya que la clave principal de la entidad `Student` es `ID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-206">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="07d97-207">Las propiedades de clave externa también se pueden denominar `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-207">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="07d97-208">Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-208">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="07d97-209">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="07d97-209">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="07d97-211">Cree *Models/Course.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-211">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="07d97-212">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="07d97-212">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="07d97-213">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="07d97-213">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="07d97-214">El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.</span><span class="sxs-lookup"><span data-stu-id="07d97-214">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="07d97-215">Compile el proyecto para comprobar que no hay errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="07d97-215">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="07d97-216">Scaffolding de las páginas Student</span><span class="sxs-lookup"><span data-stu-id="07d97-216">Scaffold Student pages</span></span>

<span data-ttu-id="07d97-217">En esta sección, se usa la herramienta de scaffolding de ASP.NET Core para generar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-217">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="07d97-218">Una clase de *contexto* de EF Core.</span><span class="sxs-lookup"><span data-stu-id="07d97-218">An EF Core *context* class.</span></span> <span data-ttu-id="07d97-219">El contexto es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos determinado.</span><span class="sxs-lookup"><span data-stu-id="07d97-219">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="07d97-220">Se deriva de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="07d97-220">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="07d97-221">Razor Pages que controlan las operaciones de creación, lectura, actualización y eliminación (CRUD) de la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-221">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-222">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d97-223">Cree una carpeta *Students* en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="07d97-223">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="07d97-224">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Pages/Students* y seleccione **Agregar** > **Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="07d97-224">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="07d97-225">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **AGREGAR**.</span><span class="sxs-lookup"><span data-stu-id="07d97-225">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="07d97-226">En el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="07d97-226">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="07d97-227">En la lista desplegable **Clase de modelo**, seleccione **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="07d97-227">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="07d97-228">En la fila **Clase de contexto de datos**, seleccione el signo **+** (más).</span><span class="sxs-lookup"><span data-stu-id="07d97-228">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="07d97-229">Cambie el nombre del contexto de datos *ContosoUniversity.Models.ContosoUniversityContext* por *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="07d97-229">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="07d97-230">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="07d97-230">Select **Add**.</span></span>

<span data-ttu-id="07d97-231">Los paquetes siguientes se instalan de forma automática:</span><span class="sxs-lookup"><span data-stu-id="07d97-231">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-232">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-232">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d97-233">Ejecute los siguientes comandos de la CLI de .NET Core para instalar los paquetes NuGet necesarios:</span><span class="sxs-lookup"><span data-stu-id="07d97-233">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="07d97-234">El paquete Microsoft.VisualStudio.Web.CodeGeneration.Design es necesario para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="07d97-234">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="07d97-235">Aunque en la aplicación no se usará SQL Server, la herramienta de scaffolding necesita el paquete de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="07d97-235">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="07d97-236">Cree una carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="07d97-236">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="07d97-237">Ejecute el comando siguiente para instalar la [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="07d97-237">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="07d97-238">Ejecute el comando siguiente para aplicar scaffolding a las páginas Student.</span><span class="sxs-lookup"><span data-stu-id="07d97-238">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="07d97-239">**En Windows**</span><span class="sxs-lookup"><span data-stu-id="07d97-239">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="07d97-240">**En macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="07d97-240">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="07d97-241">Si tiene un problema con el paso anterior, compile el proyecto y vuelva a intentar el paso de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="07d97-241">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="07d97-242">El proceso de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="07d97-242">The scaffolding process:</span></span>

* <span data-ttu-id="07d97-243">Crea Razor Pages en la carpeta *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="07d97-243">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="07d97-244">*Create.cshtml* y *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="07d97-244">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="07d97-245">*Delete.cshtml* y *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="07d97-245">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="07d97-246">*Details.cshtml* y *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="07d97-246">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="07d97-247">*Edit.cshtml* y *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="07d97-247">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="07d97-248">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="07d97-248">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="07d97-249">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="07d97-249">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="07d97-250">Agrega el contexto a la inserción de dependencias en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="07d97-250">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="07d97-251">Agrega una cadena de conexión de base de datos a *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="07d97-251">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="07d97-252">Cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-252">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07d97-254">La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="07d97-254">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="07d97-255">LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="07d97-255">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="07d97-256">De forma predeterminada, LocalDB crea archivos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-256">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="07d97-258">Cambie la cadena de conexión para que apunte a un archivo de base de datos de SQLite denominado *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="07d97-258">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="07d97-259">Actualización de la clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-259">Update the database context class</span></span>

<span data-ttu-id="07d97-260">La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-260">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="07d97-261">El contexto se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="07d97-261">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="07d97-262">En el contexto se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-262">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="07d97-263">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="07d97-263">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="07d97-264">Actualice *SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-264">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="07d97-265">El código resaltado crea una propiedad [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="07d97-265">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="07d97-266">En la terminología de EF Core:</span><span class="sxs-lookup"><span data-stu-id="07d97-266">In EF Core terminology:</span></span>

* <span data-ttu-id="07d97-267">Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-267">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="07d97-268">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="07d97-268">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="07d97-269">Puesto que un conjunto de entidades contiene varias entidades, las propiedades DBSet deben ser nombres en plural.</span><span class="sxs-lookup"><span data-stu-id="07d97-269">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="07d97-270">Como la herramienta de scaffolding ha creado una instancia `Student` de DBSet, en este paso se cambia a `Students` en plural.</span><span class="sxs-lookup"><span data-stu-id="07d97-270">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="07d97-271">Para que el código de Razor Pages coincida con el nuevo nombre de DBSet, realice un cambio global de `_context.Student` a `_context.Students` en todo el proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-271">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="07d97-272">Hay ocho repeticiones.</span><span class="sxs-lookup"><span data-stu-id="07d97-272">There are 8 occurrences.</span></span>

<span data-ttu-id="07d97-273">Compile el proyecto para comprobar que no haya errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="07d97-273">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="07d97-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="07d97-274">Startup.cs</span></span>

<span data-ttu-id="07d97-275">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="07d97-275">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="07d97-276">Los servicios (como el contexto de base de datos de EF Core) se registran con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07d97-276">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="07d97-277">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="07d97-277">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="07d97-278">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="07d97-278">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="07d97-279">La herramienta de scaffolding ha registrado de forma automática la clase de contexto con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="07d97-279">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-280">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-280">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d97-281">En `ConfigureServices`, el proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="07d97-281">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d97-283">En `ConfigureServices`, asegúrese de que el código agregado por el proveedor de scaffolding llama a `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="07d97-283">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="07d97-284">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="07d97-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="07d97-285">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="07d97-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="07d97-286">Creación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-286">Create the database</span></span>

<span data-ttu-id="07d97-287">Actualice *Program.cs* para crear la base de datos si no existe:</span><span class="sxs-lookup"><span data-stu-id="07d97-287">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="07d97-288">El método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) no realiza ninguna acción si existe una base de datos para el contexto.</span><span class="sxs-lookup"><span data-stu-id="07d97-288">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="07d97-289">Si no existe ninguna base de datos, se crean la base de datos y el esquema.</span><span class="sxs-lookup"><span data-stu-id="07d97-289">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="07d97-290">`EnsureCreated` habilita el flujo de trabajo siguiente para controlar los cambios del modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="07d97-290">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="07d97-291">Se elimina la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-291">Delete the database.</span></span> <span data-ttu-id="07d97-292">Se pierden los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="07d97-292">Any existing data is lost.</span></span>
* <span data-ttu-id="07d97-293">Se cambia el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-293">Change the data model.</span></span> <span data-ttu-id="07d97-294">Por ejemplo, se agrega un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="07d97-294">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="07d97-295">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07d97-295">Run the app.</span></span>
* <span data-ttu-id="07d97-296">`EnsureCreated` crea una base de datos con el esquema nuevo.</span><span class="sxs-lookup"><span data-stu-id="07d97-296">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="07d97-297">Este flujo de trabajo funciona bien al principio de la fase de desarrollo cuando el esquema evoluciona rápidamente, siempre y cuando no sea necesario conservar los datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-297">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="07d97-298">La situación es distinta cuando es necesario conservar los datos introducidos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-298">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="07d97-299">En ese caso, use las migraciones.</span><span class="sxs-lookup"><span data-stu-id="07d97-299">When that is the case, use migrations.</span></span>

<span data-ttu-id="07d97-300">Más adelante en la serie de tutoriales, eliminará la base de datos creada por `EnsureCreated` y, en su lugar, usará las migraciones.</span><span class="sxs-lookup"><span data-stu-id="07d97-300">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="07d97-301">Una base de datos creada por `EnsureCreated` no se puede actualizar mediante migraciones.</span><span class="sxs-lookup"><span data-stu-id="07d97-301">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="07d97-302">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="07d97-302">Test the app</span></span>

* <span data-ttu-id="07d97-303">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07d97-303">Run the app.</span></span>
* <span data-ttu-id="07d97-304">Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="07d97-304">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="07d97-305">Pruebe los vínculos Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="07d97-305">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="07d97-306">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-306">Seed the database</span></span>

<span data-ttu-id="07d97-307">El método `EnsureCreated` crea una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="07d97-307">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="07d97-308">En esta sección se agrega código que rellena la base de datos con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="07d97-308">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="07d97-309">Cree *Data/DbInitializer.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-309">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="07d97-310">El código comprueba si hay alumnos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-310">The code checks if there are any students in the database.</span></span> <span data-ttu-id="07d97-311">Si no hay ningún alumno, agrega datos de prueba a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-311">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="07d97-312">Crea los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="07d97-312">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="07d97-313">En *Program.cs*, reemplace la llamada a `EnsureCreated` con una llamada a `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="07d97-313">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07d97-315">Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="07d97-315">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d97-317">Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="07d97-317">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="07d97-318">Reinicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07d97-318">Restart the app.</span></span>

* <span data-ttu-id="07d97-319">Seleccione la página Students para ver los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="07d97-319">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="07d97-320">Consulta la base de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-320">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-321">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d97-322">Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="07d97-322">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="07d97-323">En SSOX, seleccione **(localdb)\MSSQLLocalDB > Bases de datos > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="07d97-323">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="07d97-324">El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.</span><span class="sxs-lookup"><span data-stu-id="07d97-324">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="07d97-325">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="07d97-325">Expand the **Tables** node.</span></span>
* <span data-ttu-id="07d97-326">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="07d97-326">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="07d97-327">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver código** para ver cómo el modelo `Student` se asigna al esquema de tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-327">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="07d97-329">Use la herramienta SQLite para ver el esquema de la base de datos y los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="07d97-329">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="07d97-330">El archivo de base de datos se denomina *CU.db* y se encuentra en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-330">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="07d97-331">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="07d97-331">Asynchronous code</span></span>

<span data-ttu-id="07d97-332">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="07d97-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="07d97-333">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="07d97-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="07d97-334">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="07d97-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="07d97-335">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="07d97-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="07d97-336">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="07d97-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="07d97-337">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz y el servidor pueda administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="07d97-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="07d97-338">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="07d97-338">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="07d97-339">En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="07d97-339">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="07d97-340">En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="07d97-340">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="07d97-341">La palabra clave `async` indica al compilador que:</span><span class="sxs-lookup"><span data-stu-id="07d97-341">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="07d97-342">Genere devoluciones de llamada para partes del cuerpo del método.</span><span class="sxs-lookup"><span data-stu-id="07d97-342">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="07d97-343">Cree el objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="07d97-343">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="07d97-344">El tipo devuelto `Task<T>` representa el trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="07d97-344">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="07d97-345">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="07d97-345">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="07d97-346">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="07d97-346">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="07d97-347">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="07d97-347">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="07d97-348">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="07d97-348">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="07d97-349">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="07d97-349">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="07d97-350">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-350">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="07d97-351">Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="07d97-351">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="07d97-352">No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="07d97-352">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="07d97-353">Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="07d97-353">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="07d97-354">Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para la paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-354">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="07d97-355">Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="07d97-355">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="07d97-356">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="07d97-356">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="07d97-357">Siguiente tutorial</span><span class="sxs-lookup"><span data-stu-id="07d97-357">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="07d97-358">Este es el primero de una serie de tutoriales en los que se muestra cómo usar Entity Framework (EF) Core en una [aplicación Razor Pages](xref:razor-pages/index) en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07d97-358">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="07d97-359">En el tutorial se crea un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="07d97-359">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="07d97-360">El sitio incluye funciones como la admisión de alumnos, la creación de cursos y las asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="07d97-360">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="07d97-361">En el tutorial se usa el enfoque de Code First.</span><span class="sxs-lookup"><span data-stu-id="07d97-361">The tutorial uses the code first approach.</span></span> <span data-ttu-id="07d97-362">Para obtener información sobre cómo seguir este tutorial mediante el enfoque de Database First, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="07d97-362">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="07d97-363">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="07d97-363">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="07d97-364">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="07d97-364">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="07d97-365">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="07d97-365">Prerequisites</span></span>

* <span data-ttu-id="07d97-366">Si no está familiarizado con Razor Pages, consulte la serie de tutoriales [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) antes de empezar este.</span><span class="sxs-lookup"><span data-stu-id="07d97-366">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-367">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-367">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-368">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-368">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="07d97-369">Motores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-369">Database engines</span></span>

<span data-ttu-id="07d97-370">En las instrucciones de Visual Studio se usa [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versión de SQL Server Express que solo se ejecuta en Windows.</span><span class="sxs-lookup"><span data-stu-id="07d97-370">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="07d97-371">En las instrucciones de Visual Studio Code se usa [SQLite](https://www.sqlite.org/), un motor de base de datos multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="07d97-371">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="07d97-372">Si decide usar SQLite, descargue e instale una herramienta de terceros para administrar y ver una base de datos de SQLite, como [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="07d97-372">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="07d97-373">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="07d97-373">Troubleshooting</span></span>

<span data-ttu-id="07d97-374">Si experimenta un problema que no puede resolver, compare el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="07d97-374">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="07d97-375">Una buena forma de obtener ayuda consiste en publicar una pregunta en StackOverflow.com con la [etiqueta ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o la [etiqueta EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="07d97-375">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="07d97-376">La aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="07d97-376">The sample app</span></span>

<span data-ttu-id="07d97-377">La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.</span><span class="sxs-lookup"><span data-stu-id="07d97-377">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="07d97-378">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="07d97-378">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="07d97-379">Estas son algunas de las pantallas que se crean en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="07d97-379">Here are a few of the screens created in the tutorial.</span></span>

![Página de índice de Students](intro/_static/students-index30.png)

![Página de edición de estudiantes](intro/_static/student-edit30.png)

<span data-ttu-id="07d97-382">El estilo de la interfaz de usuario de este sitio se basa en las plantillas de proyecto integradas.</span><span class="sxs-lookup"><span data-stu-id="07d97-382">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="07d97-383">El enfoque del tutorial es cómo usar EF Core, no cómo personalizar la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="07d97-383">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="07d97-384">Siga el vínculo de la parte superior de la página para obtener el código fuente para el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="07d97-384">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="07d97-385">La carpeta *cu30* contiene el código para la versión ASP.NET Core 3.0 del tutorial.</span><span class="sxs-lookup"><span data-stu-id="07d97-385">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="07d97-386">Los archivos que reflejan el estado del código para los tutoriales 1-7 se pueden encontrar en la carpeta *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="07d97-386">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-387">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-387">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07d97-388">Para ejecutar la aplicación después de descargar el proyecto completado:</span><span class="sxs-lookup"><span data-stu-id="07d97-388">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="07d97-389">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-389">Build the project.</span></span>
* <span data-ttu-id="07d97-390">En la Consola del administrador de paquetes (PMC), ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-390">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="07d97-391">Ejecute el proyecto para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-391">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-392">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-392">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="07d97-393">Para ejecutar la aplicación después de descargar el proyecto completado:</span><span class="sxs-lookup"><span data-stu-id="07d97-393">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="07d97-394">Elimine *ContosoUniversity.csproj* y cambie el nombre de *ContosoUniversitySQLite.csproj* por *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="07d97-394">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="07d97-395">En *Program.cs*, convierta `#define Startup` en comentario para que se use `StartupSQLite`.</span><span class="sxs-lookup"><span data-stu-id="07d97-395">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="07d97-396">Elimine *appSettings.json* y cambie el nombre de *appSettingsSQLite.json* por *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="07d97-396">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="07d97-397">Elimine la carpeta *Migrations* y cambie el nombre de *MigrationsSQL* por *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="07d97-397">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="07d97-398">Realice una búsqueda global `#if SQLiteVersion` y quite `#if SQLiteVersion` y la instrucción `#endif` asociada.</span><span class="sxs-lookup"><span data-stu-id="07d97-398">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="07d97-399">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-399">Build the project.</span></span>
* <span data-ttu-id="07d97-400">En un símbolo del sistema en la carpeta del proyecto, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="07d97-400">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="07d97-401">En la herramienta SQLite, ejecute la siguiente instrucción SQL:</span><span class="sxs-lookup"><span data-stu-id="07d97-401">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="07d97-402">Ejecute el proyecto para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-402">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="07d97-403">Creación del proyecto de aplicación web</span><span class="sxs-lookup"><span data-stu-id="07d97-403">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-404">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d97-405">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="07d97-405">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="07d97-406">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="07d97-406">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="07d97-407">Asigne el nombre *ContosoUniversity* al proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-407">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="07d97-408">Es importante usar este nombre exacto incluido el uso de mayúsculas, para que los espacios de nombres coincidan cuando se copie y pegue el código.</span><span class="sxs-lookup"><span data-stu-id="07d97-408">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="07d97-409">Seleccione **.NET Core** y **ASP.NET Core 3.0** en las listas desplegables y, luego, **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="07d97-409">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d97-411">En un terminal, vaya a la carpeta en la que se debe crear la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-411">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="07d97-412">Ejecute los comandos siguientes para crear un proyecto de Razor Pages y `cd` para acceder a la nueva carpeta del proyecto:</span><span class="sxs-lookup"><span data-stu-id="07d97-412">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="07d97-413">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="07d97-413">Set up the site style</span></span>

<span data-ttu-id="07d97-414">Configure el encabezado, el pie de página y el menú del sitio mediante la actualización de *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="07d97-414">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="07d97-415">Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="07d97-415">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="07d97-416">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="07d97-416">There are three occurrences.</span></span>

* <span data-ttu-id="07d97-417">Elimine las entradas de menú **Home** y **Privacy**, y agregue entradas para **About**, **Students**, **Courses**, **Instructors** y **Departments**.</span><span class="sxs-lookup"><span data-stu-id="07d97-417">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="07d97-418">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="07d97-418">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="07d97-419">En *Pages/Index.cshtml*, reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET Core con texto sobre esta aplicación:</span><span class="sxs-lookup"><span data-stu-id="07d97-419">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="07d97-420">Ejecute la aplicación para comprobar que aparece la página principal.</span><span class="sxs-lookup"><span data-stu-id="07d97-420">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="07d97-421">El modelo de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-421">The data model</span></span>

<span data-ttu-id="07d97-422">En las secciones siguientes se crea un modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="07d97-422">The following sections create a data model:</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="07d97-424">Un alumno se puede inscribir en cualquier número de cursos y un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="07d97-424">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="07d97-425">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="07d97-425">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

* <span data-ttu-id="07d97-427">Cree una carpeta *Models* en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-427">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="07d97-428">Cree *Models/Student.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-428">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="07d97-429">La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos que se corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="07d97-429">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="07d97-430">De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-430">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="07d97-431">Por tanto, el nombre que se reconoce de forma automática para la clave principal de la clase `Student` es `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-431">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="07d97-432">Para más información, consulte [EF Core: claves](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="07d97-432">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="07d97-433">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="07d97-433">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="07d97-434">Las propiedades de navegación contienen otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="07d97-434">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="07d97-435">En este caso, la propiedad `Enrollments` de una entidad `Student` contiene todas las entidades `Enrollment` que están relacionadas con esa instancia de Student.</span><span class="sxs-lookup"><span data-stu-id="07d97-435">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="07d97-436">Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades Enrollment.</span><span class="sxs-lookup"><span data-stu-id="07d97-436">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="07d97-437">En la base de datos, una fila Enrollment se relaciona con una fila Student si su columna StudentID contiene el valor de identificador del alumno.</span><span class="sxs-lookup"><span data-stu-id="07d97-437">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="07d97-438">Por ejemplo, imagine que una fila Student tiene el identificador 1.</span><span class="sxs-lookup"><span data-stu-id="07d97-438">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="07d97-439">Las filas Enrollment relacionadas tendrán StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="07d97-439">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="07d97-440">StudentID es una *clave externa* de la tabla Enrollment.</span><span class="sxs-lookup"><span data-stu-id="07d97-440">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="07d97-441">La propiedad `Enrollments` se define como `ICollection<Enrollment>` porque puede haber varias entidades Enrollment relacionadas.</span><span class="sxs-lookup"><span data-stu-id="07d97-441">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="07d97-442">Puede usar otros tipos de colección, como `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-442">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="07d97-443">Cuando se usa `ICollection<Enrollment>`, EF Core crea una colección `HashSet<Enrollment>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="07d97-443">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="07d97-444">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="07d97-444">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="07d97-446">Cree *Models/Enrollment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-446">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="07d97-447">La propiedad `EnrollmentID` es la clave principal; esta entidad usa el patrón `classnameID` en lugar de `ID` por sí solo.</span><span class="sxs-lookup"><span data-stu-id="07d97-447">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="07d97-448">Para un modelo de datos de producción, elija un patrón y úselo de forma coherente.</span><span class="sxs-lookup"><span data-stu-id="07d97-448">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="07d97-449">En este tutorial se usan los dos simplemente para ilustrar el trabajo.</span><span class="sxs-lookup"><span data-stu-id="07d97-449">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="07d97-450">El uso de `ID` sin `classname` facilita la implementación de algunos tipos de cambios del modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-450">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="07d97-451">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="07d97-451">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="07d97-452">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade`[acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="07d97-452">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="07d97-453">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="07d97-453">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="07d97-454">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-454">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="07d97-455">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-455">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="07d97-456">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="07d97-456">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="07d97-457">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="07d97-457">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="07d97-458">EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-458">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="07d97-459">Por ejemplo, `StudentID` es la clave externa para la propiedad de navegación `Student`, ya que la clave principal de la entidad `Student` es `ID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-459">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="07d97-460">Las propiedades de clave externa también se pueden denominar `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-460">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="07d97-461">Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-461">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="07d97-462">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="07d97-462">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="07d97-464">Cree *Models/Course.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-464">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="07d97-465">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="07d97-465">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="07d97-466">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="07d97-466">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="07d97-467">El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.</span><span class="sxs-lookup"><span data-stu-id="07d97-467">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="07d97-468">Compile el proyecto para comprobar que no hay errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="07d97-468">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="07d97-469">Scaffolding de las páginas Student</span><span class="sxs-lookup"><span data-stu-id="07d97-469">Scaffold Student pages</span></span>

<span data-ttu-id="07d97-470">En esta sección, se usa la herramienta de scaffolding de ASP.NET Core para generar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-470">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="07d97-471">Una clase de *contexto* de EF Core.</span><span class="sxs-lookup"><span data-stu-id="07d97-471">An EF Core *context* class.</span></span> <span data-ttu-id="07d97-472">El contexto es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos determinado.</span><span class="sxs-lookup"><span data-stu-id="07d97-472">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="07d97-473">Se deriva de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="07d97-473">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="07d97-474">Razor Pages que controlan las operaciones de creación, lectura, actualización y eliminación (CRUD) de la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-474">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-475">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-475">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d97-476">Cree una carpeta *Students* en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="07d97-476">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="07d97-477">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Pages/Students* y seleccione **Agregar** > **Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="07d97-477">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="07d97-478">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **AGREGAR**.</span><span class="sxs-lookup"><span data-stu-id="07d97-478">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="07d97-479">En el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="07d97-479">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="07d97-480">En la lista desplegable **Clase de modelo**, seleccione **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="07d97-480">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="07d97-481">En la fila **Clase de contexto de datos**, seleccione el signo **+** (más).</span><span class="sxs-lookup"><span data-stu-id="07d97-481">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="07d97-482">Cambie el nombre del contexto de datos *ContosoUniversity.Models.ContosoUniversityContext* por *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="07d97-482">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="07d97-483">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="07d97-483">Select **Add**.</span></span>

<span data-ttu-id="07d97-484">Los paquetes siguientes se instalan de forma automática:</span><span class="sxs-lookup"><span data-stu-id="07d97-484">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-485">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-485">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d97-486">Ejecute los siguientes comandos de la CLI de .NET Core para instalar los paquetes NuGet necesarios:</span><span class="sxs-lookup"><span data-stu-id="07d97-486">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="07d97-487">El paquete Microsoft.VisualStudio.Web.CodeGeneration.Design es necesario para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="07d97-487">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="07d97-488">Aunque en la aplicación no se usará SQL Server, la herramienta de scaffolding necesita el paquete de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="07d97-488">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="07d97-489">Cree una carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="07d97-489">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="07d97-490">Ejecute el comando siguiente para instalar la [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="07d97-490">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="07d97-491">Ejecute el comando siguiente para aplicar scaffolding a las páginas Student.</span><span class="sxs-lookup"><span data-stu-id="07d97-491">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="07d97-492">**En Windows**</span><span class="sxs-lookup"><span data-stu-id="07d97-492">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="07d97-493">**En macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="07d97-493">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="07d97-494">Si tiene un problema con el paso anterior, compile el proyecto y vuelva a intentar el paso de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="07d97-494">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="07d97-495">El proceso de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="07d97-495">The scaffolding process:</span></span>

* <span data-ttu-id="07d97-496">Crea Razor Pages en la carpeta *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="07d97-496">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="07d97-497">*Create.cshtml* y *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="07d97-497">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="07d97-498">*Delete.cshtml* y *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="07d97-498">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="07d97-499">*Details.cshtml* y *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="07d97-499">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="07d97-500">*Edit.cshtml* y *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="07d97-500">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="07d97-501">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="07d97-501">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="07d97-502">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="07d97-502">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="07d97-503">Agrega el contexto a la inserción de dependencias en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="07d97-503">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="07d97-504">Agrega una cadena de conexión de base de datos a *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="07d97-504">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="07d97-505">Cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-505">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-506">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07d97-507">La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="07d97-507">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="07d97-508">LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="07d97-508">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="07d97-509">De forma predeterminada, LocalDB crea archivos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-509">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-510">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-510">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="07d97-511">Cambie la cadena de conexión para que apunte a un archivo de base de datos de SQLite denominado *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="07d97-511">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="07d97-512">Actualización de la clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-512">Update the database context class</span></span>

<span data-ttu-id="07d97-513">La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-513">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="07d97-514">El contexto se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="07d97-514">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="07d97-515">En el contexto se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-515">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="07d97-516">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="07d97-516">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="07d97-517">Actualice *SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-517">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="07d97-518">El código resaltado crea una propiedad [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="07d97-518">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="07d97-519">En la terminología de EF Core:</span><span class="sxs-lookup"><span data-stu-id="07d97-519">In EF Core terminology:</span></span>

* <span data-ttu-id="07d97-520">Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-520">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="07d97-521">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="07d97-521">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="07d97-522">Puesto que un conjunto de entidades contiene varias entidades, las propiedades DBSet deben ser nombres en plural.</span><span class="sxs-lookup"><span data-stu-id="07d97-522">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="07d97-523">Como la herramienta de scaffolding ha creado una instancia `Student` de DBSet, en este paso se cambia a `Students` en plural.</span><span class="sxs-lookup"><span data-stu-id="07d97-523">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="07d97-524">Para que el código de Razor Pages coincida con el nuevo nombre de DBSet, realice un cambio global de `_context.Student` a `_context.Students` en todo el proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-524">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="07d97-525">Hay ocho repeticiones.</span><span class="sxs-lookup"><span data-stu-id="07d97-525">There are 8 occurrences.</span></span>

<span data-ttu-id="07d97-526">Compile el proyecto para comprobar que no haya errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="07d97-526">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="07d97-527">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="07d97-527">Startup.cs</span></span>

<span data-ttu-id="07d97-528">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="07d97-528">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="07d97-529">Los servicios (como el contexto de base de datos de EF Core) se registran con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07d97-529">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="07d97-530">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="07d97-530">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="07d97-531">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="07d97-531">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="07d97-532">La herramienta de scaffolding ha registrado de forma automática la clase de contexto con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="07d97-532">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-533">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-533">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d97-534">En `ConfigureServices`, el proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="07d97-534">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-535">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-535">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d97-536">En `ConfigureServices`, asegúrese de que el código agregado por el proveedor de scaffolding llama a `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="07d97-536">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="07d97-537">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="07d97-537">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="07d97-538">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="07d97-538">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="07d97-539">Creación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-539">Create the database</span></span>

<span data-ttu-id="07d97-540">Actualice *Program.cs* para crear la base de datos si no existe:</span><span class="sxs-lookup"><span data-stu-id="07d97-540">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="07d97-541">El método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) no realiza ninguna acción si existe una base de datos para el contexto.</span><span class="sxs-lookup"><span data-stu-id="07d97-541">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="07d97-542">Si no existe ninguna base de datos, se crean la base de datos y el esquema.</span><span class="sxs-lookup"><span data-stu-id="07d97-542">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="07d97-543">`EnsureCreated` habilita el flujo de trabajo siguiente para controlar los cambios del modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="07d97-543">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="07d97-544">Se elimina la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-544">Delete the database.</span></span> <span data-ttu-id="07d97-545">Se pierden los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="07d97-545">Any existing data is lost.</span></span>
* <span data-ttu-id="07d97-546">Se cambia el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-546">Change the data model.</span></span> <span data-ttu-id="07d97-547">Por ejemplo, se agrega un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="07d97-547">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="07d97-548">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07d97-548">Run the app.</span></span>
* <span data-ttu-id="07d97-549">`EnsureCreated` crea una base de datos con el esquema nuevo.</span><span class="sxs-lookup"><span data-stu-id="07d97-549">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="07d97-550">Este flujo de trabajo funciona bien al principio de la fase de desarrollo cuando el esquema evoluciona rápidamente, siempre y cuando no sea necesario conservar los datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-550">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="07d97-551">La situación es distinta cuando es necesario conservar los datos introducidos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-551">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="07d97-552">En ese caso, use las migraciones.</span><span class="sxs-lookup"><span data-stu-id="07d97-552">When that is the case, use migrations.</span></span>

<span data-ttu-id="07d97-553">Más adelante en la serie de tutoriales, eliminará la base de datos creada por `EnsureCreated` y, en su lugar, usará las migraciones.</span><span class="sxs-lookup"><span data-stu-id="07d97-553">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="07d97-554">Una base de datos creada por `EnsureCreated` no se puede actualizar mediante migraciones.</span><span class="sxs-lookup"><span data-stu-id="07d97-554">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="07d97-555">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="07d97-555">Test the app</span></span>

* <span data-ttu-id="07d97-556">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07d97-556">Run the app.</span></span>
* <span data-ttu-id="07d97-557">Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="07d97-557">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="07d97-558">Pruebe los vínculos Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="07d97-558">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="07d97-559">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-559">Seed the database</span></span>

<span data-ttu-id="07d97-560">El método `EnsureCreated` crea una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="07d97-560">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="07d97-561">En esta sección se agrega código que rellena la base de datos con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="07d97-561">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="07d97-562">Cree *Data/DbInitializer.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-562">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="07d97-563">El código comprueba si hay alumnos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-563">The code checks if there are any students in the database.</span></span> <span data-ttu-id="07d97-564">Si no hay ningún alumno, agrega datos de prueba a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-564">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="07d97-565">Crea los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="07d97-565">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="07d97-566">En *Program.cs*, reemplace la llamada a `EnsureCreated` con una llamada a `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="07d97-566">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-567">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-567">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07d97-568">Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="07d97-568">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-569">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-569">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d97-570">Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="07d97-570">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="07d97-571">Reinicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07d97-571">Restart the app.</span></span>

* <span data-ttu-id="07d97-572">Seleccione la página Students para ver los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="07d97-572">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="07d97-573">Consulta la base de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-573">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-574">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-574">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d97-575">Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="07d97-575">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="07d97-576">En SSOX, seleccione **(localdb)\MSSQLLocalDB > Bases de datos > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="07d97-576">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="07d97-577">El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.</span><span class="sxs-lookup"><span data-stu-id="07d97-577">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="07d97-578">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="07d97-578">Expand the **Tables** node.</span></span>
* <span data-ttu-id="07d97-579">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="07d97-579">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="07d97-580">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver código** para ver cómo el modelo `Student` se asigna al esquema de tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-580">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="07d97-582">Use la herramienta SQLite para ver el esquema de la base de datos y los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="07d97-582">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="07d97-583">El archivo de base de datos se denomina *CU.db* y se encuentra en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-583">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="07d97-584">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="07d97-584">Asynchronous code</span></span>

<span data-ttu-id="07d97-585">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="07d97-585">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="07d97-586">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="07d97-586">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="07d97-587">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="07d97-587">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="07d97-588">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="07d97-588">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="07d97-589">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="07d97-589">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="07d97-590">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz y el servidor pueda administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="07d97-590">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="07d97-591">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="07d97-591">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="07d97-592">En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="07d97-592">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="07d97-593">En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="07d97-593">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="07d97-594">La palabra clave `async` indica al compilador que:</span><span class="sxs-lookup"><span data-stu-id="07d97-594">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="07d97-595">Genere devoluciones de llamada para partes del cuerpo del método.</span><span class="sxs-lookup"><span data-stu-id="07d97-595">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="07d97-596">Cree el objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="07d97-596">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="07d97-597">El tipo devuelto `Task<T>` representa el trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="07d97-597">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="07d97-598">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="07d97-598">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="07d97-599">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="07d97-599">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="07d97-600">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="07d97-600">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="07d97-601">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="07d97-601">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="07d97-602">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="07d97-602">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="07d97-603">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-603">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="07d97-604">Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="07d97-604">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="07d97-605">No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="07d97-605">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="07d97-606">Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="07d97-606">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="07d97-607">Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para la paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-607">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="07d97-608">Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="07d97-608">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="07d97-609">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="07d97-609">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="07d97-610">Siguiente tutorial</span><span class="sxs-lookup"><span data-stu-id="07d97-610">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07d97-611">En la aplicación web de ejemplo Contoso University se muestra cómo crear una aplicación web de Razor Pages de ASP.NET Core con Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="07d97-611">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="07d97-612">La aplicación de ejemplo es un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="07d97-612">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="07d97-613">Incluye funciones como la admisión de estudiantes, la creación de cursos y asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="07d97-613">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="07d97-614">Esta página es la primera de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="07d97-614">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="07d97-615">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="07d97-615">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="07d97-616">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="07d97-616">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="07d97-617">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="07d97-617">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-618">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-618">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-619">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-619">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="07d97-620">Familiaridad con [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="07d97-620">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="07d97-621">Los programadores nuevos deben completar [Introducción a Razor Pages en ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start) antes de empezar esta serie.</span><span class="sxs-lookup"><span data-stu-id="07d97-621">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="07d97-622">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="07d97-622">Troubleshooting</span></span>

<span data-ttu-id="07d97-623">Si experimenta un problema que no puede resolver, por lo general podrá encontrar la solución si compara el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="07d97-623">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="07d97-624">Una buena forma de obtener ayuda consiste en publicar una pregunta en [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) para [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="07d97-624">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="07d97-625">La aplicación web Contoso University</span><span class="sxs-lookup"><span data-stu-id="07d97-625">The Contoso University web app</span></span>

<span data-ttu-id="07d97-626">La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.</span><span class="sxs-lookup"><span data-stu-id="07d97-626">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="07d97-627">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="07d97-627">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="07d97-628">Estas son algunas de las pantallas que se crean en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="07d97-628">Here are a few of the screens created in the tutorial.</span></span>

![Página de índice de Students](intro/_static/students-index.png)

![Página de edición de estudiantes](intro/_static/student-edit.png)

<span data-ttu-id="07d97-631">El estilo de la interfaz de usuario de este sitio se mantiene fiel a lo que generan las plantillas integradas.</span><span class="sxs-lookup"><span data-stu-id="07d97-631">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="07d97-632">El tutorial se centra en EF Core con Razor Pages, no en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="07d97-632">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="07d97-633">Creación de la aplicación web de Razor Pages ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="07d97-633">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-634">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-634">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d97-635">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="07d97-635">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="07d97-636">Cree una aplicación web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07d97-636">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="07d97-637">Asigne el nombre **ContosoUniversity** al proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-637">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="07d97-638">Es importante que el nombre del proyecto sea *ContosoUniversity* para que coincidan los espacios de nombres al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="07d97-638">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="07d97-639">Seleccione **ASP.NET Core 2.1** en la lista desplegable y, luego, **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="07d97-639">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="07d97-640">Para ver las imágenes de los pasos anteriores, consulte [Creación de una aplicación web de Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="07d97-640">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="07d97-641">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07d97-641">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-642">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-642">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="07d97-643">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="07d97-643">Set up the site style</span></span>

<span data-ttu-id="07d97-644">Con algunos cambios se configura el menú del sitio, el diseño y la página principal.</span><span class="sxs-lookup"><span data-stu-id="07d97-644">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="07d97-645">Actualice *Pages/Shared/_Layout.cshtml* con los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="07d97-645">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="07d97-646">Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="07d97-646">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="07d97-647">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="07d97-647">There are three occurrences.</span></span>

* <span data-ttu-id="07d97-648">Agregue entradas de menú para **Students**, **Courses**, **Instructors** y **Departments**, y elimine la entrada de menú **Contact**.</span><span class="sxs-lookup"><span data-stu-id="07d97-648">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="07d97-649">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="07d97-649">The changes are highlighted.</span></span> <span data-ttu-id="07d97-650">(*No* se muestra todo el marcado).</span><span class="sxs-lookup"><span data-stu-id="07d97-650">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="07d97-651">En *Pages/Index.cshtml*, reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET y MVC con texto sobre esta aplicación:</span><span class="sxs-lookup"><span data-stu-id="07d97-651">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="07d97-652">Crear el modelo de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-652">Create the data model</span></span>

<span data-ttu-id="07d97-653">Cree las clases de entidad para la aplicación Contoso University.</span><span class="sxs-lookup"><span data-stu-id="07d97-653">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="07d97-654">Comience con las tres entidades siguientes:</span><span class="sxs-lookup"><span data-stu-id="07d97-654">Start with the following three entities:</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="07d97-656">Hay una relación uno a varios entre las entidades `Student` y `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="07d97-656">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="07d97-657">Hay una relación uno a varios entre las entidades `Course` y `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="07d97-657">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="07d97-658">Un estudiante se puede inscribir en cualquier número de cursos.</span><span class="sxs-lookup"><span data-stu-id="07d97-658">A student can enroll in any number of courses.</span></span> <span data-ttu-id="07d97-659">Un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="07d97-659">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="07d97-660">En las secciones siguientes, se crea una clase para cada una de estas entidades.</span><span class="sxs-lookup"><span data-stu-id="07d97-660">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="07d97-661">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="07d97-661">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

<span data-ttu-id="07d97-663">Cree una carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="07d97-663">Create a *Models* folder.</span></span> <span data-ttu-id="07d97-664">En la carpeta *Models*, cree un archivo de clase denominado *Student.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-664">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="07d97-665">La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos (DB) que corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="07d97-665">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="07d97-666">De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-666">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="07d97-667">En `classnameID`, `classname` es el nombre de la clase.</span><span class="sxs-lookup"><span data-stu-id="07d97-667">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="07d97-668">En el ejemplo anterior, la clave principal alternativa que se reconoce de forma automática es `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-668">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="07d97-669">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="07d97-669">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="07d97-670">Las propiedades de navegación se vinculan a otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="07d97-670">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="07d97-671">En este caso, la propiedad `Enrollments` de una `Student entity` contiene todas las entidades `Enrollment` que están relacionadas con esa entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-671">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="07d97-672">Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="07d97-672">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="07d97-673">Una fila `Enrollment` relacionada es la que contiene el valor de clave principal de ese estudiante en la columna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-673">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="07d97-674">Por ejemplo, suponga que el estudiante con ID=1 tiene dos filas en la tabla `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="07d97-674">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="07d97-675">La tabla `Enrollment` tiene dos filas con `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="07d97-675">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="07d97-676">`StudentID` es una clave externa en la tabla `Enrollment` que especifica el estudiante en la tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-676">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="07d97-677">Si una propiedad de navegación puede contener varias entidades, la propiedad de navegación debe ser un tipo de lista, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-677">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="07d97-678">Se puede especificar `ICollection<T>`, o bien un tipo como `List<T>` o `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-678">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="07d97-679">Cuando se usa `ICollection<T>`, EF Core crea una colección `HashSet<T>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="07d97-679">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="07d97-680">Las propiedades de navegación que contienen varias entidades proceden de relaciones de varios a varios y uno a varios.</span><span class="sxs-lookup"><span data-stu-id="07d97-680">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="07d97-681">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="07d97-681">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="07d97-683">En la carpeta *Models*, cree *Enrollment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-683">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="07d97-684">La propiedad `EnrollmentID` es la clave principal.</span><span class="sxs-lookup"><span data-stu-id="07d97-684">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="07d97-685">En esta entidad se usa el patrón `classnameID` en lugar de `ID` como en la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-685">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="07d97-686">Normalmente, los desarrolladores eligen un patrón y lo usan en todo el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-686">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="07d97-687">En un tutorial posterior, se muestra el uso de ID sin un nombre de clase para facilitar la implementación de la herencia en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-687">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="07d97-688">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="07d97-688">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="07d97-689">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade` acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="07d97-689">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="07d97-690">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="07d97-690">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="07d97-691">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-691">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="07d97-692">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="07d97-692">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="07d97-693">La entidad `Student` difiere de la propiedad de navegación `Student.Enrollments`, que contiene varias entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="07d97-693">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="07d97-694">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="07d97-694">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="07d97-695">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="07d97-695">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="07d97-696">EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-696">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="07d97-697">Por ejemplo, `StudentID` para la propiedad de navegación `Student`, puesto que la clave principal de la entidad `Student` es `ID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-697">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="07d97-698">Las propiedades de clave externa también se pueden denominar `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-698">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="07d97-699">Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="07d97-699">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="07d97-700">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="07d97-700">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="07d97-702">En la carpeta *Models*, cree *Course.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-702">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="07d97-703">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="07d97-703">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="07d97-704">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="07d97-704">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="07d97-705">El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.</span><span class="sxs-lookup"><span data-stu-id="07d97-705">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="07d97-706">Aplicación de scaffolding al modelo de alumnos</span><span class="sxs-lookup"><span data-stu-id="07d97-706">Scaffold the student model</span></span>

<span data-ttu-id="07d97-707">En esta sección, se aplica scaffolding al modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="07d97-707">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="07d97-708">Es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="07d97-708">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="07d97-709">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="07d97-709">Build the project.</span></span>
* <span data-ttu-id="07d97-710">Cree la carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="07d97-710">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-711">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-711">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d97-712">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Pages/Students* > **Agregar** > **Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="07d97-712">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="07d97-713">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **AGREGAR**.</span><span class="sxs-lookup"><span data-stu-id="07d97-713">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="07d97-714">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="07d97-714">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="07d97-715">En la lista desplegable **Clase de modelo**, seleccione **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="07d97-715">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="07d97-716">En la fila **Clase de contexto de datos**, haga clic en el signo **+** (más) y cambie el nombre generado por **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="07d97-716">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="07d97-717">En la lista desplegable **Clase de contexto de datos**, seleccione **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="07d97-717">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="07d97-718">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="07d97-718">Select **Add**.</span></span>

![Cuadro de diálogo CRUD](intro/_static/s1.png)

<span data-ttu-id="07d97-720">Si tiene algún problema con el paso anterior, consulte [Aplicar scaffolding al modelo de película](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="07d97-720">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-721">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-721">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="07d97-722">Ejecute los comandos siguientes para aplicar scaffolding al modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="07d97-722">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="07d97-723">El proceso de scaffolding ha creado y cambiado los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="07d97-723">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="07d97-724">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="07d97-724">Files created</span></span>

* <span data-ttu-id="07d97-725">*Pages/Students* Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="07d97-725">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="07d97-726">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="07d97-726">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="07d97-727">Actualizaciones de archivos</span><span class="sxs-lookup"><span data-stu-id="07d97-727">File updates</span></span>

* <span data-ttu-id="07d97-728">*Startup.cs*: en la sección siguiente se detallan los cambios realizados en este archivo.</span><span class="sxs-lookup"><span data-stu-id="07d97-728">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="07d97-729">*appsettings.json*: se agrega la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="07d97-729">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="07d97-730">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="07d97-730">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="07d97-731">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="07d97-731">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="07d97-732">Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07d97-732">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="07d97-733">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="07d97-733">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="07d97-734">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="07d97-734">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="07d97-735">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="07d97-735">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="07d97-736">Examine el método `ConfigureServices` de *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="07d97-736">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="07d97-737">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="07d97-737">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="07d97-738">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="07d97-738">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="07d97-739">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="07d97-739">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="07d97-740">Actualización de main</span><span class="sxs-lookup"><span data-stu-id="07d97-740">Update main</span></span>

<span data-ttu-id="07d97-741">En *Program.cs*, modifique el método `Main` para que haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-741">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="07d97-742">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="07d97-742">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="07d97-743">Llame a [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="07d97-743">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="07d97-744">Elimine el contexto cuando finalice el método `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="07d97-744">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="07d97-745">En el código siguiente se muestra el archivo *Program.cs* actualizado.</span><span class="sxs-lookup"><span data-stu-id="07d97-745">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="07d97-746">`EnsureCreated` garantiza la existencia de la base de datos para el contexto.</span><span class="sxs-lookup"><span data-stu-id="07d97-746">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="07d97-747">Si existe, no se realiza ninguna acción.</span><span class="sxs-lookup"><span data-stu-id="07d97-747">If it exists, no action is taken.</span></span> <span data-ttu-id="07d97-748">Si no existe, se crean la base de datos y todo su esquema.</span><span class="sxs-lookup"><span data-stu-id="07d97-748">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="07d97-749">En `EnsureCreated` no se usan migraciones para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-749">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="07d97-750">Una base de datos que se cree con `EnsureCreated` no se podrá actualizar más adelante mediante las migraciones.</span><span class="sxs-lookup"><span data-stu-id="07d97-750">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="07d97-751">`EnsureCreated` se llama durante el inicio de la aplicación, lo que permite el flujo de trabajo siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-751">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="07d97-752">Se elimina la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-752">Delete the DB.</span></span>
* <span data-ttu-id="07d97-753">Se cambia el esquema de base de datos (por ejemplo, se agrega un campo `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="07d97-753">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="07d97-754">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="07d97-754">Run the app.</span></span>
* <span data-ttu-id="07d97-755">`EnsureCreated` crea una base de datos con la columna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="07d97-755">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="07d97-756">`EnsureCreated` es útil al principio del desarrollo, cuando el esquema evoluciona rápidamente.</span><span class="sxs-lookup"><span data-stu-id="07d97-756">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="07d97-757">Más adelante, en el tutorial se elimina la base de datos y se usan las migraciones.</span><span class="sxs-lookup"><span data-stu-id="07d97-757">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="07d97-758">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="07d97-758">Test the app</span></span>

<span data-ttu-id="07d97-759">Ejecute la aplicación y acepte la directiva de cookies.</span><span class="sxs-lookup"><span data-stu-id="07d97-759">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="07d97-760">Esta aplicación no conserva información de carácter personal.</span><span class="sxs-lookup"><span data-stu-id="07d97-760">This app doesn't keep personal information.</span></span> <span data-ttu-id="07d97-761">Puede obtener más información sobre la directiva de cookies en [Compatibilidad con el Reglamento general de protección de datos (RGPD) de la UE](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="07d97-761">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="07d97-762">Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="07d97-762">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="07d97-763">Pruebe los vínculos Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="07d97-763">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="07d97-764">Examinar el contexto de base de datos SchoolContext</span><span class="sxs-lookup"><span data-stu-id="07d97-764">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="07d97-765">La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-765">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="07d97-766">El contexto de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="07d97-766">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="07d97-767">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-767">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="07d97-768">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="07d97-768">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="07d97-769">Actualice *SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-769">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="07d97-770">El código resaltado crea una propiedad [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="07d97-770">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="07d97-771">En la terminología de EF Core:</span><span class="sxs-lookup"><span data-stu-id="07d97-771">In EF Core terminology:</span></span>

* <span data-ttu-id="07d97-772">Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-772">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="07d97-773">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="07d97-773">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="07d97-774">`DbSet<Enrollment>` y `DbSet<Course>` se pueden omitir.</span><span class="sxs-lookup"><span data-stu-id="07d97-774">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="07d97-775">EF Core las incluye implícitamente porque la entidad `Student` hace referencia a la entidad `Enrollment` y la entidad `Enrollment` hace referencia a la entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="07d97-775">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="07d97-776">Para este tutorial, conserve `DbSet<Enrollment>` y `DbSet<Course>` en el `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="07d97-776">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="07d97-777">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="07d97-777">SQL Server Express LocalDB</span></span>

<span data-ttu-id="07d97-778">La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="07d97-778">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="07d97-779">LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="07d97-779">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="07d97-780">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="07d97-780">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="07d97-781">De forma predeterminada, LocalDB crea archivos de base de datos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="07d97-781">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="07d97-782">Agregar código para inicializar la base de datos con datos de prueba</span><span class="sxs-lookup"><span data-stu-id="07d97-782">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="07d97-783">EF Core crea una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="07d97-783">EF Core creates an empty DB.</span></span> <span data-ttu-id="07d97-784">En esta sección, se escribe un método `Initialize` para rellenarlo con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="07d97-784">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="07d97-785">En la carpeta *Data*, cree un archivo de clase denominado *DbInitializer.cs* y agregue el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07d97-785">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="07d97-786">Nota: El código anterior usa `Models` para el espacio de nombres (`namespace ContosoUniversity.Models`) en lugar de `Data`.</span><span class="sxs-lookup"><span data-stu-id="07d97-786">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="07d97-787">`Models` es coherente con el código generado por el proveedor de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="07d97-787">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="07d97-788">Para obtener más información, consulte [este problema de scaffolding de GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="07d97-788">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="07d97-789">El código comprueba si hay estudiantes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-789">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="07d97-790">Si no hay alumnos en la base de datos, se inicializa con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="07d97-790">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="07d97-791">Carga los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="07d97-791">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="07d97-792">El método `EnsureCreated` crea automáticamente la base de datos para el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-792">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="07d97-793">Si la base de datos existe, `EnsureCreated` vuelve sin modificarla.</span><span class="sxs-lookup"><span data-stu-id="07d97-793">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="07d97-794">En *Program.cs*, modifique el método `Main` para que llame a `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="07d97-794">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="07d97-795">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d97-795">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07d97-796">Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="07d97-796">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="07d97-797">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d97-797">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d97-798">Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="07d97-798">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="07d97-799">Ver la base de datos</span><span class="sxs-lookup"><span data-stu-id="07d97-799">View the DB</span></span>

<span data-ttu-id="07d97-800">El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.</span><span class="sxs-lookup"><span data-stu-id="07d97-800">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="07d97-801">Por lo tanto, el nombre de la base de datos será "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="07d97-801">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="07d97-802">El GUID será diferente para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="07d97-802">The GUID will be different for each user.</span></span>
<span data-ttu-id="07d97-803">Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="07d97-803">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="07d97-804">En SSOX, haga clic en **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="07d97-804">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="07d97-805">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="07d97-805">Expand the **Tables** node.</span></span>

<span data-ttu-id="07d97-806">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="07d97-806">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="07d97-807">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="07d97-807">Asynchronous code</span></span>

<span data-ttu-id="07d97-808">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="07d97-808">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="07d97-809">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="07d97-809">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="07d97-810">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="07d97-810">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="07d97-811">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="07d97-811">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="07d97-812">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="07d97-812">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="07d97-813">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz, y el servidor está habilitado para administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="07d97-813">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="07d97-814">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="07d97-814">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="07d97-815">En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="07d97-815">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="07d97-816">En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="07d97-816">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="07d97-817">La palabra clave `async` indica al compilador que:</span><span class="sxs-lookup"><span data-stu-id="07d97-817">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="07d97-818">Genere devoluciones de llamada para partes del cuerpo del método.</span><span class="sxs-lookup"><span data-stu-id="07d97-818">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="07d97-819">Cree automáticamente el objeto [Task](/dotnet/api/system.threading.tasks.task) que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="07d97-819">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="07d97-820">Para más información, vea [Tipo de valor devuelto Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="07d97-820">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="07d97-821">El tipo devuelto implícito `Task` representa el trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="07d97-821">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="07d97-822">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="07d97-822">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="07d97-823">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="07d97-823">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="07d97-824">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="07d97-824">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="07d97-825">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="07d97-825">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="07d97-826">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="07d97-826">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="07d97-827">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-827">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="07d97-828">Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="07d97-828">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="07d97-829">No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="07d97-829">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="07d97-830">Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="07d97-830">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="07d97-831">Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="07d97-831">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="07d97-832">Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="07d97-832">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="07d97-833">En el siguiente tutorial, se examinan las operaciones CRUD (crear, leer, actualizar y eliminar) básicas.</span><span class="sxs-lookup"><span data-stu-id="07d97-833">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="07d97-834">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="07d97-834">Additional resources</span></span>

* [<span data-ttu-id="07d97-835">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="07d97-835">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="07d97-836">Siguiente</span><span class="sxs-lookup"><span data-stu-id="07d97-836">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end