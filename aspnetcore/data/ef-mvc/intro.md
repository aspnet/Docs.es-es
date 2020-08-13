---
title: 'Tutorial: Introducción a EF Core en una aplicación web de ASP.NET Core MVC'
description: Este es el primero de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo Contoso University desde el principio.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
no-loc:
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
ms.openlocfilehash: 36b0c913db3c2b6c2c834d33b0ea8665f3e25814
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012974"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="77b78-103">Tutorial: Introducción a EF Core en una aplicación web de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="77b78-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="77b78-104">Este tutorial **no** se ha actualizado para ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="77b78-104">This tutorial has **not** been updated to ASP.NET Core 3.0.</span></span> <span data-ttu-id="77b78-105">La [versión de Razor Pages](xref:data/ef-rp/intro) se ha actualizado.</span><span class="sxs-lookup"><span data-stu-id="77b78-105">The [Razor Pages version](xref:data/ef-rp/intro) has been updated.</span></span> <span data-ttu-id="77b78-106">La mayoría de los cambios de código para ASP.NET Core 3.0 y la versión posterior de este tutorial:</span><span class="sxs-lookup"><span data-stu-id="77b78-106">Most of the code changes for the ASP.NET Core 3.0 and later version of this tutorial:</span></span>

* <span data-ttu-id="77b78-107">Están en los archivos *Startup.cs* y *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="77b78-107">Are in the *Startup.cs* and *Program.cs* files.</span></span>
* <span data-ttu-id="77b78-108">Se pueden encontrar en la [versión de Razor Pages](xref:data/ef-rp/intro).</span><span class="sxs-lookup"><span data-stu-id="77b78-108">Can be found in the [Razor Pages version](xref:data/ef-rp/intro).</span></span> 

<span data-ttu-id="77b78-109">Para información sobre cuándo podría actualizarse, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/13920).</span><span class="sxs-lookup"><span data-stu-id="77b78-109">For information on when this might be updated, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/13920).</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="77b78-110">En la aplicación web de ejemplo Contoso University se muestra cómo crear aplicaciones web de ASP.NET Core 2.2 MVC con Entity Framework (EF) Core 2.2 y Visual Studio 2017 o 2019.</span><span class="sxs-lookup"><span data-stu-id="77b78-110">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="77b78-111">La aplicación de ejemplo es un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="77b78-111">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="77b78-112">Incluye funciones como la admisión de estudiantes, la creación de cursos y asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="77b78-112">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="77b78-113">Este es el primero de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo Contoso University desde el principio.</span><span class="sxs-lookup"><span data-stu-id="77b78-113">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="77b78-114">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="77b78-114">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="77b78-115">Creación de una aplicación web de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="77b78-115">Create an ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="77b78-116">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="77b78-116">Set up the site style</span></span>
> * <span data-ttu-id="77b78-117">Obtiene información sobre los paquetes NuGet de EF Core</span><span class="sxs-lookup"><span data-stu-id="77b78-117">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="77b78-118">Crear el modelo de datos</span><span class="sxs-lookup"><span data-stu-id="77b78-118">Create the data model</span></span>
> * <span data-ttu-id="77b78-119">Crear el contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="77b78-119">Create the database context</span></span>
> * <span data-ttu-id="77b78-120">Registrar el contexto para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="77b78-120">Register the context for dependency injection</span></span>
> * <span data-ttu-id="77b78-121">Inicializar la base de datos con datos de prueba</span><span class="sxs-lookup"><span data-stu-id="77b78-121">Initialize the database with test data</span></span>
> * <span data-ttu-id="77b78-122">Crear un controlador y vistas</span><span class="sxs-lookup"><span data-stu-id="77b78-122">Create a controller and views</span></span>
> * <span data-ttu-id="77b78-123">Consulta la base de datos</span><span class="sxs-lookup"><span data-stu-id="77b78-123">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="77b78-124">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="77b78-124">Prerequisites</span></span>

* [<span data-ttu-id="77b78-125">SDK de .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="77b78-125">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="77b78-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con las cargas de trabajo siguientes:</span><span class="sxs-lookup"><span data-stu-id="77b78-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="77b78-127">Carga de trabajo de **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="77b78-127">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="77b78-128">Carga de trabajo **Desarrollo multiplataforma de .NET Core**</span><span class="sxs-lookup"><span data-stu-id="77b78-128">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="77b78-129">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="77b78-129">Troubleshooting</span></span>

<span data-ttu-id="77b78-130">Si experimenta un problema que no puede resolver, por lo general podrá encontrar la solución si compara el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span><span class="sxs-lookup"><span data-stu-id="77b78-130">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="77b78-131">Para obtener una lista de errores comunes y cómo resolverlos, vea [la sección de solución de problemas del último tutorial de la serie](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="77b78-131">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="77b78-132">Si ahí no encuentra lo que necesita, puede publicar una pregunta en StackOverflow.com para [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="77b78-132">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="77b78-133">Esta es una serie de 10 tutoriales y cada uno se basa en lo que se realiza en los anteriores.</span><span class="sxs-lookup"><span data-stu-id="77b78-133">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="77b78-134">Considere la posibilidad de guardar una copia del proyecto después de completar correctamente cada tutorial.</span><span class="sxs-lookup"><span data-stu-id="77b78-134">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="77b78-135">Después, si experimenta problemas, puede empezar desde el tutorial anterior en lugar de volver al principio de la serie completa.</span><span class="sxs-lookup"><span data-stu-id="77b78-135">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="77b78-136">Aplicación web Contoso University</span><span class="sxs-lookup"><span data-stu-id="77b78-136">Contoso University web app</span></span>

<span data-ttu-id="77b78-137">La aplicación que se va a compilar en estos tutoriales es un sitio web sencillo de una universidad.</span><span class="sxs-lookup"><span data-stu-id="77b78-137">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="77b78-138">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="77b78-138">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="77b78-139">A continuación se muestran algunas de las pantallas que se van a crear.</span><span class="sxs-lookup"><span data-stu-id="77b78-139">Here are a few of the screens you'll create.</span></span>

![Página de índice de Students](intro/_static/students-index.png)

![Página de edición de estudiantes](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="77b78-142">Creación de una aplicación web</span><span class="sxs-lookup"><span data-stu-id="77b78-142">Create web app</span></span>

* <span data-ttu-id="77b78-143">Abra Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="77b78-143">Open Visual Studio.</span></span>

* <span data-ttu-id="77b78-144">En el menú **Archivo**, seleccione **Nuevo > Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="77b78-144">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="77b78-145">En el panel de la izquierda, seleccione **Instalado > Visual C# > Web**.</span><span class="sxs-lookup"><span data-stu-id="77b78-145">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="77b78-146">Seleccione la plantilla de proyecto **Aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="77b78-146">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="77b78-147">Escriba **ContosoUniversity** como el nombre y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="77b78-147">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Cuadro de diálogo Nuevo proyecto](intro/_static/new-project2.png)

* <span data-ttu-id="77b78-149">Espere que aparezca el cuadro de diálogo **Nueva aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="77b78-149">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="77b78-150">Seleccione **.NET Core**, **ASP.NET Core 2.2** y la plantilla **Aplicación web (controlador de vista de modelos)** .</span><span class="sxs-lookup"><span data-stu-id="77b78-150">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="77b78-151">Asegúrese de que **Autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="77b78-151">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="77b78-152">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="77b78-152">Select **OK**</span></span>

  ![Cuadro de diálogo Nuevo proyecto ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="77b78-154">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="77b78-154">Set up the site style</span></span>

<span data-ttu-id="77b78-155">Con algunos cambios sencillos se configura el menú del sitio, el diseño y la página principal.</span><span class="sxs-lookup"><span data-stu-id="77b78-155">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="77b78-156">Abra *Views/Shared/_Layout.cshtml* y realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="77b78-156">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="77b78-157">Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="77b78-157">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="77b78-158">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="77b78-158">There are three occurrences.</span></span>

* <span data-ttu-id="77b78-159">Agregue entradas de menú para **About**, **Students**, **Courses**, **Instructors** y **Departments**, y elimine la entrada de menú **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="77b78-159">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="77b78-160">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="77b78-160">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="77b78-161">En *Views/Home/Index.cshtml*, reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET y MVC con texto sobre esta aplicación:</span><span class="sxs-lookup"><span data-stu-id="77b78-161">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="77b78-162">Presione CTRL+F5 para ejecutar el proyecto o seleccione **Depurar > Iniciar sin depurar** en el menú.</span><span class="sxs-lookup"><span data-stu-id="77b78-162">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="77b78-163">Verá la página principal con pestañas para las páginas que se crearán en estos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="77b78-163">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Página de inicio de Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="77b78-165">Acerca de los paquetes NuGet de EF Core</span><span class="sxs-lookup"><span data-stu-id="77b78-165">About EF Core NuGet packages</span></span>

<span data-ttu-id="77b78-166">Para agregar compatibilidad con EF Core a un proyecto, instale el proveedor de base de datos que quiera tener como destino.</span><span class="sxs-lookup"><span data-stu-id="77b78-166">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="77b78-167">En este tutorial se usa SQL Server y el paquete de proveedor es [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="77b78-167">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="77b78-168">Este paquete se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), por lo que no es necesario hacer referencia al paquete.</span><span class="sxs-lookup"><span data-stu-id="77b78-168">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="77b78-169">Este paquete de SQL Server de EF y sus dependencias (`Microsoft.EntityFrameworkCore` y `Microsoft.EntityFrameworkCore.Relational`) proporcionan compatibilidad en tiempo de ejecución para EF.</span><span class="sxs-lookup"><span data-stu-id="77b78-169">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="77b78-170">Más adelante, en el tutorial [Migraciones](migrations.md), agregará un paquete de herramientas.</span><span class="sxs-lookup"><span data-stu-id="77b78-170">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="77b78-171">Para obtener información sobre otros proveedores de base de datos disponibles para Entity Framework Core, vea [Proveedores de bases de datos](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="77b78-171">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="77b78-172">Crear el modelo de datos</span><span class="sxs-lookup"><span data-stu-id="77b78-172">Create the data model</span></span>

<span data-ttu-id="77b78-173">A continuación podrá crear las clases de entidad para la aplicación Contoso University.</span><span class="sxs-lookup"><span data-stu-id="77b78-173">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="77b78-174">Empezará por las tres siguientes entidades.</span><span class="sxs-lookup"><span data-stu-id="77b78-174">You'll start with the following three entities.</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="77b78-176">Hay una relación uno a varios entre las entidades `Student` y `Enrollment`, y también entre las entidades `Course` y `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="77b78-176">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="77b78-177">En otras palabras, un estudiante se puede inscribir en cualquier número de cursos y un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="77b78-177">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="77b78-178">En las secciones siguientes creará una clase para cada una de estas entidades.</span><span class="sxs-lookup"><span data-stu-id="77b78-178">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="77b78-179">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="77b78-179">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

<span data-ttu-id="77b78-181">En la carpeta *Models*, cree un archivo de clase denominado *Student.cs* y reemplace el código de plantilla con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="77b78-181">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="77b78-182">La propiedad `ID` se convertirá en la columna de clave principal de la tabla de base de datos que corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="77b78-182">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="77b78-183">De forma predeterminada, Entity Framework interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="77b78-183">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="77b78-184">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="77b78-184">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="77b78-185">Las propiedades de navegación contienen otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="77b78-185">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="77b78-186">En este caso, la propiedad `Enrollments` de una `Student entity` contendrá todas las entidades `Enrollment` que estén relacionadas con esa entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="77b78-186">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="77b78-187">En otras palabras, si una fila Student determinada en la base de datos tiene dos filas Enrollment relacionadas (filas que contienen el valor de clave principal de ese estudiante en la columna de clave externa StudentID), la propiedad de navegación `Enrollments` de esa entidad `Student` contendrá esas dos entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="77b78-187">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="77b78-188">Si una propiedad de navegación puede contener varias entidades (como en las relaciones de varios a varios o uno a varios), su tipo debe ser una lista a la que se puedan agregar las entradas, eliminarlas y actualizarlas, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="77b78-188">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="77b78-189">Puede especificar `ICollection<T>` o un tipo como `List<T>` o `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="77b78-189">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="77b78-190">Si especifica `ICollection<T>`, EF crea una colección `HashSet<T>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="77b78-190">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="77b78-191">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="77b78-191">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="77b78-193">En la carpeta *Models*, cree *Enrollment.cs* y reemplace el código existente con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="77b78-193">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="77b78-194">La propiedad `EnrollmentID` será la clave principal; esta entidad usa el patrón `classnameID` en lugar de `ID` por sí solo, como se vio en la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="77b78-194">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="77b78-195">Normalmente debería elegir un patrón y usarlo en todo el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-195">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="77b78-196">En este caso, la variación muestra que se puede usar cualquiera de los patrones.</span><span class="sxs-lookup"><span data-stu-id="77b78-196">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="77b78-197">En un [tutorial posterior](inheritance.md), verá cómo el uso de ID sin un nombre de clase facilita la implementación de la herencia en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-197">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="77b78-198">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="77b78-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="77b78-199">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade` acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="77b78-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="77b78-200">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="77b78-200">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="77b78-201">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="77b78-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="77b78-202">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad solo puede contener un única entidad `Student` (a diferencia de la propiedad de navegación `Student.Enrollments` que se vio anteriormente, que puede contener varias entidades `Enrollment`).</span><span class="sxs-lookup"><span data-stu-id="77b78-202">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="77b78-203">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="77b78-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="77b78-204">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="77b78-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="77b78-205">Entity Framework interpreta una propiedad como propiedad de clave externa si se denomina `<navigation property name><primary key property name>` (por ejemplo `StudentID` para la propiedad de navegación `Student`, dado que la clave principal de la entidad `Student` es `ID`).</span><span class="sxs-lookup"><span data-stu-id="77b78-205">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="77b78-206">Las propiedades de clave externa también se pueden denominar simplemente `<primary key property name>` (por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`).</span><span class="sxs-lookup"><span data-stu-id="77b78-206">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="77b78-207">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="77b78-207">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="77b78-209">En la carpeta *Models*, cree *Course.cs* y reemplace el código existente con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="77b78-209">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="77b78-210">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="77b78-210">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="77b78-211">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="77b78-211">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="77b78-212">En un [tutorial posterior](complex-data-model.md) de esta serie se incluirá más información sobre el atributo `DatabaseGenerated`.</span><span class="sxs-lookup"><span data-stu-id="77b78-212">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="77b78-213">Básicamente, este atributo permite escribir la clave principal para el curso en lugar de hacer que la base de datos lo genere.</span><span class="sxs-lookup"><span data-stu-id="77b78-213">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="77b78-214">Crear el contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="77b78-214">Create the database context</span></span>

<span data-ttu-id="77b78-215">La clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-215">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="77b78-216">Esta clase se crea al derivar de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="77b78-216">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="77b78-217">En el código se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-217">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="77b78-218">También se puede personalizar determinado comportamiento de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="77b78-218">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="77b78-219">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="77b78-219">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="77b78-220">En la carpeta del proyecto, cree una carpeta denominada *Data*.</span><span class="sxs-lookup"><span data-stu-id="77b78-220">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="77b78-221">En la carpeta *Data*, cree un archivo de clase denominado *SchoolContext.cs* y reemplace el código de plantilla con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="77b78-221">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="77b78-222">Este código crea una propiedad `DbSet` para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="77b78-222">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="77b78-223">En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="77b78-223">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="77b78-224">Se podrían haber omitido las instrucciones `DbSet<Enrollment>` y `DbSet<Course>`, y el funcionamiento sería el mismo.</span><span class="sxs-lookup"><span data-stu-id="77b78-224">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="77b78-225">Entity Framework las incluiría implícitamente porque la entidad `Student` hace referencia a la entidad `Enrollment` y la entidad `Enrollment` hace referencia a la entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="77b78-225">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="77b78-226">Cuando se crea la base de datos, EF crea las tablas con los mismos nombres que los nombres de propiedad `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="77b78-226">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="77b78-227">Los nombres de propiedad para las colecciones normalmente están en plural (Students en lugar de Student), pero los desarrolladores no están de acuerdo sobre si los nombres de tabla deben estar en plural o no.</span><span class="sxs-lookup"><span data-stu-id="77b78-227">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="77b78-228">Para estos tutoriales, se invalidará el comportamiento predeterminado mediante la especificación de nombres de tabla en singular en DbContext.</span><span class="sxs-lookup"><span data-stu-id="77b78-228">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="77b78-229">Para ello, agregue el código resaltado siguiente después de la última propiedad DbSet.</span><span class="sxs-lookup"><span data-stu-id="77b78-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="77b78-230">Registra SchoolContext</span><span class="sxs-lookup"><span data-stu-id="77b78-230">Register the SchoolContext</span></span>

<span data-ttu-id="77b78-231">ASP.NET Core implementa la [inserción de dependencias](../../fundamentals/dependency-injection.md) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="77b78-231">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="77b78-232">Los servicios (como el contexto de base de datos de EF) se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="77b78-232">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="77b78-233">Estos servicios se proporcionan a los componentes que los necesitan (como los controladores MVC) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="77b78-233">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="77b78-234">Más adelante en este tutorial verá el código de constructor de controlador que obtiene una instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="77b78-234">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="77b78-235">Para registrar `SchoolContext` como servicio, abra *Startup.cs* y agregue las líneas resaltadas al método `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="77b78-235">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="77b78-236">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="77b78-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="77b78-237">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="77b78-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="77b78-238">Agregue instrucciones `using` para los espacios de nombres `ContosoUniversity.Data` y `Microsoft.EntityFrameworkCore`, y después compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="77b78-238">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="77b78-239">Abra el archivo *appsettings.json* y agregue una cadena de conexión como se muestra en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="77b78-239">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="77b78-240">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="77b78-240">SQL Server Express LocalDB</span></span>

<span data-ttu-id="77b78-241">La cadena de conexión especifica una base de datos de SQL Server LocalDB.</span><span class="sxs-lookup"><span data-stu-id="77b78-241">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="77b78-242">LocalDB es una versión ligera del motor de base de datos de SQL Server Express que está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="77b78-242">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="77b78-243">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="77b78-243">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="77b78-244">De forma predeterminada, LocalDB crea archivos de base de datos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="77b78-244">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="77b78-245">Inicializa la base de datos con datos de prueba</span><span class="sxs-lookup"><span data-stu-id="77b78-245">Initialize DB with test data</span></span>

<span data-ttu-id="77b78-246">Entity Framework creará una base de datos vacía por usted.</span><span class="sxs-lookup"><span data-stu-id="77b78-246">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="77b78-247">En esta sección, escribirá un método que se llama después de crear la base de datos para rellenarla con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="77b78-247">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="77b78-248">Aquí usará el método `EnsureCreated` para crear automáticamente la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-248">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="77b78-249">En un [tutorial posterior](migrations.md), verá cómo controlar los cambios en el modelo mediante Migraciones de Code First para cambiar el esquema de base de datos en lugar de quitar y volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-249">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="77b78-250">En la carpeta *Data*, cree un archivo de clase denominado *DbInitializer.cs* y reemplace el código de plantilla con el código siguiente, que hace que se cree una base de datos cuando es necesario y carga datos de prueba en la nueva base de datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-250">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="77b78-251">El código comprueba si hay estudiantes en la base de datos, y si no es así, asume que la base de datos es nueva y debe inicializarse con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="77b78-251">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="77b78-252">Carga los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="77b78-252">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="77b78-253">En *Program.cs*, modifique el método `Main` para que haga lo siguiente al iniciar la aplicación:</span><span class="sxs-lookup"><span data-stu-id="77b78-253">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="77b78-254">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="77b78-254">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="77b78-255">Llamar al método de inicialización, pasándolo al contexto.</span><span class="sxs-lookup"><span data-stu-id="77b78-255">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="77b78-256">Eliminar el contexto cuando el método de inicialización haya finalizado.</span><span class="sxs-lookup"><span data-stu-id="77b78-256">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="77b78-257">Agregue instrucciones `using`:</span><span class="sxs-lookup"><span data-stu-id="77b78-257">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="77b78-258">En los tutoriales anteriores, es posible que vea código similar en el método `Configure` de *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="77b78-258">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="77b78-259">Se recomienda usar el método `Configure` solo para configurar la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="77b78-259">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="77b78-260">El código de inicio de la aplicación pertenece al método `Main`.</span><span class="sxs-lookup"><span data-stu-id="77b78-260">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="77b78-261">Ahora, la primera vez que ejecute la aplicación, se creará la base de datos y se inicializará con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="77b78-261">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="77b78-262">Cada vez que cambie el modelo de datos, puede eliminar la base de datos, actualizar el método de inicialización y comenzar desde cero con una base de datos nueva del mismo modo.</span><span class="sxs-lookup"><span data-stu-id="77b78-262">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="77b78-263">En los tutoriales posteriores, verá cómo modificar la base de datos cuando cambie el modelo de datos, sin tener que eliminarla y volver a crearla.</span><span class="sxs-lookup"><span data-stu-id="77b78-263">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="77b78-264">Crea un controlador y vistas</span><span class="sxs-lookup"><span data-stu-id="77b78-264">Create controller and views</span></span>

<span data-ttu-id="77b78-265">A continuación, usará el motor de scaffolding de Visual Studio para agregar un controlador y vistas de MVC que usarán EF para consultar y guardar los datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-265">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="77b78-266">La creación automática de vistas y métodos de acción CRUD se conoce como scaffolding.</span><span class="sxs-lookup"><span data-stu-id="77b78-266">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="77b78-267">El scaffolding difiere de la generación de código en que el código con scaffolding es un punto de partida que se puede modificar para satisfacer sus propias necesidades, mientras que el código generado normalmente no se modifica.</span><span class="sxs-lookup"><span data-stu-id="77b78-267">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="77b78-268">Cuando tenga que personalizar código generado, use clases parciales o regenere el código cuando se produzcan cambios.</span><span class="sxs-lookup"><span data-stu-id="77b78-268">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="77b78-269">Haga clic con el botón derecho en la carpeta **Controladores** en el **Explorador de soluciones** y seleccione **Agregar > Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="77b78-269">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

* <span data-ttu-id="77b78-270">En el cuadro de diálogo **Agregar scaffold**:</span><span class="sxs-lookup"><span data-stu-id="77b78-270">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="77b78-271">Seleccione **Controlador de MVC con vistas que usan Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="77b78-271">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="77b78-272">Haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="77b78-272">Click **Add**.</span></span> <span data-ttu-id="77b78-273">Aparece el cuadro de diálogo **Agregar un controlador de MVC con vistas que usan Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="77b78-273">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![Scaffolding de Student](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="77b78-275">En **Clase de modelo** seleccione **Student**.</span><span class="sxs-lookup"><span data-stu-id="77b78-275">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="77b78-276">En **Clase de contexto de datos** seleccione **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="77b78-276">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="77b78-277">Acepte el valor predeterminado **StudentsController** como el nombre.</span><span class="sxs-lookup"><span data-stu-id="77b78-277">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="77b78-278">Haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="77b78-278">Click **Add**.</span></span>

  <span data-ttu-id="77b78-279">Al hacer clic en **Agregar**, el motor de scaffolding de Visual Studio crea un archivo *StudentsController.cs* y un conjunto de vistas (archivos *.cshtml*) que funcionan con el controlador.</span><span class="sxs-lookup"><span data-stu-id="77b78-279">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="77b78-280">(El motor de scaffolding también puede crear el contexto de base de datos de forma automática si no lo crea primero manualmente como se hizo antes en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="77b78-280">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="77b78-281">Puede especificar una clase de contexto nueva en el cuadro **Agregar controlador** si hace clic en el signo más situado a la derecha de **Clase del contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="77b78-281">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="77b78-282">Después, Visual Studio creará la clase `DbContext`, así como el controlador y las vistas).</span><span class="sxs-lookup"><span data-stu-id="77b78-282">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="77b78-283">Observará que el controlador toma un `SchoolContext` como parámetro de constructor.</span><span class="sxs-lookup"><span data-stu-id="77b78-283">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="77b78-284">La inserción de dependencias de ASP.NET Core se encarga de pasar una instancia de `SchoolContext` al controlador.</span><span class="sxs-lookup"><span data-stu-id="77b78-284">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="77b78-285">Lo configuró anteriormente en el archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="77b78-285">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="77b78-286">El controlador contiene un método de acción `Index`, que muestra todos los alumnos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-286">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="77b78-287">El método obtiene una lista de estudiantes de la entidad Students, que se establece leyendo la propiedad `Students` de la instancia del contexto de base de datos:</span><span class="sxs-lookup"><span data-stu-id="77b78-287">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="77b78-288">Más adelante en el tutorial obtendrá información sobre los elementos de programación asincrónicos de este código.</span><span class="sxs-lookup"><span data-stu-id="77b78-288">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="77b78-289">En la vista *Views/Students/Index.cshtml* se muestra esta lista en una tabla:</span><span class="sxs-lookup"><span data-stu-id="77b78-289">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="77b78-290">Presione CTRL+F5 para ejecutar el proyecto o seleccione **Depurar > Iniciar sin depurar** en el menú.</span><span class="sxs-lookup"><span data-stu-id="77b78-290">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="77b78-291">Haga clic en la pestaña Students para ver los datos de prueba insertados por el método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="77b78-291">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="77b78-292">En función del ancho de la ventana del explorador, verá el vínculo de la pestaña `Students` en la parte superior de la página o tendrá que hacer clic en el icono de navegación en la esquina superior derecha para verlo.</span><span class="sxs-lookup"><span data-stu-id="77b78-292">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Página de inicio estrecha de Contoso University](intro/_static/home-page-narrow.png)

![Página de índice de Students](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="77b78-295">Consulta la base de datos</span><span class="sxs-lookup"><span data-stu-id="77b78-295">View the database</span></span>

<span data-ttu-id="77b78-296">Al iniciar la aplicación, el método `DbInitializer.Initialize` llama a `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="77b78-296">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="77b78-297">EF comprobó que no había ninguna base de datos y creó una, y después el resto del código del método `Initialize` la rellenó con datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-297">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="77b78-298">Puede usar el **Explorador de objetos de SQL Server** (SSOX) para ver la base de datos en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="77b78-298">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="77b78-299">Cierre el explorador.</span><span class="sxs-lookup"><span data-stu-id="77b78-299">Close the browser.</span></span>

<span data-ttu-id="77b78-300">Si la ventana de SSOX no está abierta, selecciónela en el menú **Vista** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="77b78-300">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="77b78-301">En SSOX, haga clic en **(localdb)\MSSQLLocalDB > Databases** y después en la entrada del nombre de base de datos que se encuentra en la cadena de conexión del archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="77b78-301">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="77b78-302">Expanda el nodo **Tablas** para ver las tablas de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-302">Expand the **Tables** node to see the tables in your database.</span></span>

![Tablas en SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="77b78-304">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="77b78-304">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Tabla de estudiantes en SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="77b78-306">Los archivos de base de datos *.mdf* y *.ldf* se encuentran en la carpeta *C:\Usuarios\\\<yourusername>* .</span><span class="sxs-lookup"><span data-stu-id="77b78-306">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<yourusername>* folder.</span></span>

<span data-ttu-id="77b78-307">Como se está llamando a `EnsureCreated` en el método de inicializador que se ejecuta al iniciar la aplicación, ahora podría realizar un cambio en la clase `Student`, eliminar la base de datos, volver a ejecutar la aplicación y la base de datos se volvería a crear de forma automática para que coincida con el cambio.</span><span class="sxs-lookup"><span data-stu-id="77b78-307">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="77b78-308">Por ejemplo, si agrega una propiedad `EmailAddress` a la clase `Student`, verá una columna `EmailAddress` nueva en la tabla que se ha vuelto a crear.</span><span class="sxs-lookup"><span data-stu-id="77b78-308">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="77b78-309">Convenciones</span><span class="sxs-lookup"><span data-stu-id="77b78-309">Conventions</span></span>

<span data-ttu-id="77b78-310">La cantidad de código que tendría que escribir para que Entity Framework pudiera crear una base de datos completa para usted es mínima debido al uso de convenciones o las suposiciones que hace Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="77b78-310">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="77b78-311">Los nombres de las propiedades `DbSet` se usan como nombres de tabla.</span><span class="sxs-lookup"><span data-stu-id="77b78-311">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="77b78-312">Para las entidades a las que no se hace referencia con una propiedad `DbSet`, los nombres de clase de entidad se usan como nombres de tabla.</span><span class="sxs-lookup"><span data-stu-id="77b78-312">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="77b78-313">Los nombres de propiedad de entidad se usan para los nombres de columna.</span><span class="sxs-lookup"><span data-stu-id="77b78-313">Entity property names are used for column names.</span></span>

* <span data-ttu-id="77b78-314">Las propiedades de entidad que se denominan ID o classnameID se reconocen como propiedades de clave principal.</span><span class="sxs-lookup"><span data-stu-id="77b78-314">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="77b78-315">Una propiedad se interpreta como propiedad de clave externa si se denomina *\<navigation property name>\<primary key property name>* (por ejemplo, `StudentID` para la propiedad de navegación `Student`, dado que la clave principal de la entidad `Student` es `ID`).</span><span class="sxs-lookup"><span data-stu-id="77b78-315">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="77b78-316">Las propiedades de clave externa también se pueden denominar simplemente *\<primary key property name>* (por ejemplo `EnrollmentID`, dado que la clave principal de la entidad `Enrollment` es `EnrollmentID`).</span><span class="sxs-lookup"><span data-stu-id="77b78-316">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="77b78-317">El comportamiento de las convenciones se puede reemplazar.</span><span class="sxs-lookup"><span data-stu-id="77b78-317">Conventional behavior can be overridden.</span></span> <span data-ttu-id="77b78-318">Por ejemplo, puede especificar explícitamente los nombres de tabla, como se vio anteriormente en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="77b78-318">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="77b78-319">Y puede establecer los nombres de columna y cualquier propiedad como clave principal o clave externa, como verá en un [tutorial posterior](complex-data-model.md) de esta serie.</span><span class="sxs-lookup"><span data-stu-id="77b78-319">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="77b78-320">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="77b78-320">Asynchronous code</span></span>

<span data-ttu-id="77b78-321">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="77b78-321">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="77b78-322">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="77b78-322">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="77b78-323">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="77b78-323">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="77b78-324">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="77b78-324">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="77b78-325">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="77b78-325">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="77b78-326">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz, y el servidor está habilitado para administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="77b78-326">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="77b78-327">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución, pero para situaciones de poco tráfico la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="77b78-327">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="77b78-328">En el código siguiente, la palabra clave `async`, el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="77b78-328">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="77b78-329">La palabra clave `async` indica al compilador que genere devoluciones de llamada para partes del cuerpo del método y que cree automáticamente el objeto `Task<IActionResult>` que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="77b78-329">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="77b78-330">El tipo de valor devuelto `Task<IActionResult>` representa el trabajo en curso con un resultado de tipo `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="77b78-330">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="77b78-331">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="77b78-331">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="77b78-332">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="77b78-332">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="77b78-333">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="77b78-333">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="77b78-334">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="77b78-334">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="77b78-335">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa Entity Framework son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="77b78-335">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="77b78-336">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-336">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="77b78-337">Eso incluye, por ejemplo, `ToListAsync`, `SingleOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="77b78-337">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="77b78-338">No incluye, por ejemplo, instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="77b78-338">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="77b78-339">Un contexto de EF no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="77b78-339">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="77b78-340">Cuando llame a cualquier método asincrónico de EF, use siempre la palabra clave `await`.</span><span class="sxs-lookup"><span data-stu-id="77b78-340">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="77b78-341">Si quiere aprovechar las ventajas de rendimiento del código asincrónico, asegúrese de que en los paquetes de biblioteca que use (por ejemplo para paginación), también se usa async si llaman a cualquier método de Entity Framework que haga que las consultas se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77b78-341">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="77b78-342">Para obtener más información sobre la programación asincrónica en .NET, vea [Información general de Async](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="77b78-342">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="77b78-343">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="77b78-343">Get the code</span></span>

[<span data-ttu-id="77b78-344">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="77b78-344">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="77b78-345">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="77b78-345">Next steps</span></span>

<span data-ttu-id="77b78-346">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="77b78-346">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="77b78-347">Creado una aplicación web de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="77b78-347">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="77b78-348">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="77b78-348">Set up the site style</span></span>
> * <span data-ttu-id="77b78-349">Obtenido información sobre los paquetes NuGet de EF Core</span><span class="sxs-lookup"><span data-stu-id="77b78-349">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="77b78-350">Creado el modelo de datos</span><span class="sxs-lookup"><span data-stu-id="77b78-350">Created the data model</span></span>
> * <span data-ttu-id="77b78-351">Creado el contexto de la base de datos</span><span class="sxs-lookup"><span data-stu-id="77b78-351">Created the database context</span></span>
> * <span data-ttu-id="77b78-352">Registrado SchoolContext</span><span class="sxs-lookup"><span data-stu-id="77b78-352">Registered the SchoolContext</span></span>
> * <span data-ttu-id="77b78-353">Inicializado la base de datos con datos de prueba</span><span class="sxs-lookup"><span data-stu-id="77b78-353">Initialized DB with test data</span></span>
> * <span data-ttu-id="77b78-354">Creado un controlador y vistas</span><span class="sxs-lookup"><span data-stu-id="77b78-354">Created controller and views</span></span>
> * <span data-ttu-id="77b78-355">Consultado la base de datos</span><span class="sxs-lookup"><span data-stu-id="77b78-355">Viewed the database</span></span>

<span data-ttu-id="77b78-356">En el tutorial siguiente, obtendrá información sobre cómo realizar operaciones CRUD (crear, leer, actualizar y eliminar) básicas.</span><span class="sxs-lookup"><span data-stu-id="77b78-356">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="77b78-357">Pase al tutorial siguiente para obtener información sobre cómo realizar operaciones CRUD (crear, leer, actualizar y eliminar) básicas.</span><span class="sxs-lookup"><span data-stu-id="77b78-357">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="77b78-358">Implementación de la funcionalidad CRUD básica</span><span class="sxs-lookup"><span data-stu-id="77b78-358">Implement basic CRUD functionality</span></span>](crud.md)

