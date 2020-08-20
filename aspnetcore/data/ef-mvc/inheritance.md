---
title: 'Tutorial: Implementación de la herencia: ASP.NET MVC con EF Core'
description: En este tutorial se explica cómo implementar la herencia en el modelo de datos con Entity Framework Core en una aplicación de ASP.NET Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
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
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 9a17a2ddbdac6b4fc6ba90804a31915d3b96d707
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629189"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="c9181-103">Tutorial: Implementación de la herencia: ASP.NET MVC con EF Core</span><span class="sxs-lookup"><span data-stu-id="c9181-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="c9181-104">En el tutorial anterior, se trataron las excepciones de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="c9181-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="c9181-105">En este tutorial se muestra cómo implementar la herencia en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="c9181-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="c9181-106">En la programación orientada a objetos, puede usar la herencia para facilitar la reutilización del código.</span><span class="sxs-lookup"><span data-stu-id="c9181-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="c9181-107">En este tutorial, cambiará las clases `Instructor` y `Student` para que deriven de una clase base `Person` que contenga propiedades como `LastName`, que son comunes tanto para los instructores como para los alumnos.</span><span class="sxs-lookup"><span data-stu-id="c9181-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="c9181-108">No tendrá que agregar ni cambiar ninguna página web, sino que cambiará parte del código y esos cambios se reflejarán automáticamente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c9181-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="c9181-109">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="c9181-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c9181-110">Asigna la herencia a la base de datos</span><span class="sxs-lookup"><span data-stu-id="c9181-110">Map inheritance to database</span></span>
> * <span data-ttu-id="c9181-111">Creación de la clase Person</span><span class="sxs-lookup"><span data-stu-id="c9181-111">Create the Person class</span></span>
> * <span data-ttu-id="c9181-112">Actualiza Instructor y Student</span><span class="sxs-lookup"><span data-stu-id="c9181-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="c9181-113">Agrega Person al modelo</span><span class="sxs-lookup"><span data-stu-id="c9181-113">Add Person to the model</span></span>
> * <span data-ttu-id="c9181-114">Crea y actualiza migraciones</span><span class="sxs-lookup"><span data-stu-id="c9181-114">Create and update migrations</span></span>
> * <span data-ttu-id="c9181-115">Prueba la implementación</span><span class="sxs-lookup"><span data-stu-id="c9181-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c9181-116">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="c9181-116">Prerequisites</span></span>

* [<span data-ttu-id="c9181-117">Control de la simultaneidad</span><span class="sxs-lookup"><span data-stu-id="c9181-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="c9181-118">Asigna la herencia a la base de datos</span><span class="sxs-lookup"><span data-stu-id="c9181-118">Map inheritance to database</span></span>

<span data-ttu-id="c9181-119">Las clases `Instructor` y `Student` del modelo de datos School tienen varias propiedades idénticas:</span><span class="sxs-lookup"><span data-stu-id="c9181-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Clases Student e Instructor](inheritance/_static/no-inheritance.png)

<span data-ttu-id="c9181-121">Imagine que quiere eliminar el código redundante de las propiedades que comparten las entidades `Instructor` y `Student`.</span><span class="sxs-lookup"><span data-stu-id="c9181-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="c9181-122">O que quiere escribir un servicio que pueda dar formato a los nombres sin preocuparse de si el nombre es de un instructor o de un alumno.</span><span class="sxs-lookup"><span data-stu-id="c9181-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="c9181-123">Puede crear una clase base `Person` que solo contenga las propiedades compartidas y después hacer que las clases `Instructor` y `Student` hereden de esa clase base, como se muestra en la siguiente ilustración:</span><span class="sxs-lookup"><span data-stu-id="c9181-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Clases Student e Instructor derivadas de la clase Person](inheritance/_static/inheritance.png)

<span data-ttu-id="c9181-125">Esta estructura de herencia se puede representar de varias formas en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c9181-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="c9181-126">Puede tener una sola tabla Person que incluya información sobre los alumnos y los instructores.</span><span class="sxs-lookup"><span data-stu-id="c9181-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="c9181-127">Algunas de las columnas podrían aplicarse solo a los instructores (HireDate), otras solo a los alumnos (EnrollmentDate) y otras a ambos (LastName, FirstName).</span><span class="sxs-lookup"><span data-stu-id="c9181-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="c9181-128">Lo más común sería que tuviera una columna discriminadora para indicar qué tipo representa cada fila.</span><span class="sxs-lookup"><span data-stu-id="c9181-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="c9181-129">Por ejemplo, en la columna discriminadora podría aparecer "Instructor" para los instructores y "Student" para los alumnos.</span><span class="sxs-lookup"><span data-stu-id="c9181-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Ejemplo de tabla por jerarquía](inheritance/_static/tph.png)

<span data-ttu-id="c9181-131">Este patrón, que consiste en generar una estructura de herencia de la entidad a partir de una tabla de base de datos única, se denomina herencia de tabla por jerarquía (TPH).</span><span class="sxs-lookup"><span data-stu-id="c9181-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="c9181-132">Una alternativa consiste en hacer que la base de datos se parezca más a la estructura de herencia.</span><span class="sxs-lookup"><span data-stu-id="c9181-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="c9181-133">Por ejemplo, podría tener solo los campos de nombre en la tabla Person y tener tablas Instructor y Student independientes con los campos de fecha.</span><span class="sxs-lookup"><span data-stu-id="c9181-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Herencia de tabla por tipo](inheritance/_static/tpt.png)

<span data-ttu-id="c9181-135">Este patrón, que consiste en crear una tabla de base de datos para cada clase de entidad, se denomina herencia de tabla por tipo (TPT).</span><span class="sxs-lookup"><span data-stu-id="c9181-135">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="c9181-136">Y todavía otra opción pasa por asignar todos los tipos no abstractos a tablas individuales.</span><span class="sxs-lookup"><span data-stu-id="c9181-136">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="c9181-137">Todas las propiedades de una clase, incluidas las propiedades heredadas, se asignan a columnas de la tabla correspondiente.</span><span class="sxs-lookup"><span data-stu-id="c9181-137">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="c9181-138">Este patrón se denomina herencia de tabla por clase concreta (TPC).</span><span class="sxs-lookup"><span data-stu-id="c9181-138">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="c9181-139">Si implementara la herencia de TPC en las clases Person, Student e Instructor tal como se ha explicado anteriormente, las tablas Student e Instructor se verían igual antes que después de implementar la herencia.</span><span class="sxs-lookup"><span data-stu-id="c9181-139">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="c9181-140">Los patrones de herencia de TPC y TPH acostumbran a tener un mejor rendimiento que los patrones de herencia de TPT, porque estos pueden provocar consultas de combinaciones complejas.</span><span class="sxs-lookup"><span data-stu-id="c9181-140">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="c9181-141">Este tutorial muestra cómo implementar la herencia de TPH.</span><span class="sxs-lookup"><span data-stu-id="c9181-141">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="c9181-142">TPH es el único patrón de herencia que Entity Framework Core admite.</span><span class="sxs-lookup"><span data-stu-id="c9181-142">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="c9181-143">Lo que tiene que hacer es crear una clase `Person`, cambiar las clases `Instructor` y `Student` que deriven de `Person`, agregar la nueva clase a `DbContext` y crear una migración.</span><span class="sxs-lookup"><span data-stu-id="c9181-143">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="c9181-144">Considere la posibilidad de guardar una copia del proyecto antes de realizar los siguientes cambios.</span><span class="sxs-lookup"><span data-stu-id="c9181-144">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="c9181-145">De este modo, si tiene problemas y necesita volver a empezar, le resultará más fácil comenzar desde el proyecto guardado que tener que revertir los pasos realizados en este tutorial o volver al principio de toda la serie.</span><span class="sxs-lookup"><span data-stu-id="c9181-145">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="c9181-146">Creación de la clase Person</span><span class="sxs-lookup"><span data-stu-id="c9181-146">Create the Person class</span></span>

<span data-ttu-id="c9181-147">En la carpeta Models, cree Person.cs y reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c9181-147">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="c9181-148">Actualiza Instructor y Student</span><span class="sxs-lookup"><span data-stu-id="c9181-148">Update Instructor and Student</span></span>

<span data-ttu-id="c9181-149">En *Instructor.cs*, derive la clase Instructor de la clase Person y quite los campos de clave y nombre.</span><span class="sxs-lookup"><span data-stu-id="c9181-149">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="c9181-150">El código tendrá un aspecto similar al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c9181-150">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="c9181-151">Realice los mismos cambios en *Student.cs*.</span><span class="sxs-lookup"><span data-stu-id="c9181-151">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="c9181-152">Agrega Person al modelo</span><span class="sxs-lookup"><span data-stu-id="c9181-152">Add Person to the model</span></span>

<span data-ttu-id="c9181-153">Agregue el tipo de entidad Person a *SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="c9181-153">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="c9181-154">Se resaltan las líneas nuevas.</span><span class="sxs-lookup"><span data-stu-id="c9181-154">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="c9181-155">Esto es todo lo que Entity Framework necesita para configurar la herencia de tabla por jerarquía.</span><span class="sxs-lookup"><span data-stu-id="c9181-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="c9181-156">Como verá, cuando la base de datos esté actualizada, tendrá una tabla Person en lugar de las tablas Student e Instructor.</span><span class="sxs-lookup"><span data-stu-id="c9181-156">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="c9181-157">Crea y actualiza migraciones</span><span class="sxs-lookup"><span data-stu-id="c9181-157">Create and update migrations</span></span>

<span data-ttu-id="c9181-158">Guarde los cambios y compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="c9181-158">Save your changes and build the project.</span></span> <span data-ttu-id="c9181-159">A continuación, abra la ventana de comandos en la carpeta de proyecto y escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="c9181-159">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="c9181-160">No ejecute el comando `database update` todavía.</span><span class="sxs-lookup"><span data-stu-id="c9181-160">Don't run the `database update` command yet.</span></span> <span data-ttu-id="c9181-161">Este comando provocará la pérdida de datos porque colocará la tabla Instructor y cambiará el nombre de la tabla Student por Person.</span><span class="sxs-lookup"><span data-stu-id="c9181-161">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="c9181-162">Deberá proporcionar código personalizado para conservar los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="c9181-162">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="c9181-163">Abra *Migrations/\<timestamp>_Inheritance.cs* y reemplace el método `Up` por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c9181-163">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="c9181-164">Este código se encarga de las siguientes tareas de actualización de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="c9181-164">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="c9181-165">Quita las restricciones de la clave externa y los índices que apuntan a la tabla Student.</span><span class="sxs-lookup"><span data-stu-id="c9181-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="c9181-166">Cambia el nombre de la tabla Instructor por Person y realiza los cambios necesarios para que pueda almacenar datos de los alumnos:</span><span class="sxs-lookup"><span data-stu-id="c9181-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="c9181-167">Agrega EnrollmentDate que acepta valores NULL para alumnos.</span><span class="sxs-lookup"><span data-stu-id="c9181-167">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="c9181-168">Agrega la columna discriminadora para indicar si una fila es para un alumno o para un instructor.</span><span class="sxs-lookup"><span data-stu-id="c9181-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="c9181-169">Hace que HireDate admita un valor NULL, puesto que las filas de alumnos no dispondrán de fechas de contratación.</span><span class="sxs-lookup"><span data-stu-id="c9181-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="c9181-170">Agrega un campo temporal que se usará para actualizar las claves externas que apuntan a los alumnos.</span><span class="sxs-lookup"><span data-stu-id="c9181-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="c9181-171">Cuando copie alumnos en la tabla Person, obtendrán nuevos valores de clave principal.</span><span class="sxs-lookup"><span data-stu-id="c9181-171">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="c9181-172">Copia datos de la tabla Student a la tabla Person.</span><span class="sxs-lookup"><span data-stu-id="c9181-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="c9181-173">Esto hace que los alumnos se asignen a nuevos valores de clave principal.</span><span class="sxs-lookup"><span data-stu-id="c9181-173">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="c9181-174">Corrige los valores de clave externa correspondientes a los alumnos.</span><span class="sxs-lookup"><span data-stu-id="c9181-174">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="c9181-175">Vuelve a crear las restricciones y los índices de la clave externa, pero ahora los dirige a la tabla Person.</span><span class="sxs-lookup"><span data-stu-id="c9181-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="c9181-176">(Si hubiera usado el GUID en lugar de un número entero como tipo de clave principal, los valores de la clave principal de alumno no tendrían que cambiar y algunos de estos pasos se podrían haber omitido).</span><span class="sxs-lookup"><span data-stu-id="c9181-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="c9181-177">Ejecute el comando `database update`:</span><span class="sxs-lookup"><span data-stu-id="c9181-177">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="c9181-178">(En un sistema de producción haría los cambios correspondientes en el método `Down` por si alguna vez tuviera que usarlo para volver a la versión anterior de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c9181-178">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="c9181-179">Para este tutorial, no se usará el método `Down`).</span><span class="sxs-lookup"><span data-stu-id="c9181-179">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="c9181-180">Al hacer cambios en el esquema, se pueden generar otros errores en una base de datos que contenga los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="c9181-180">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="c9181-181">Si se producen errores de migración que no se pueden resolver, puede cambiar el nombre de la base de datos en la cadena de conexión o eliminar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c9181-181">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="c9181-182">Con una nueva base de datos, no habrá ningún dato para migrar y es más probable que el comando de actualización de base de datos se complete sin errores.</span><span class="sxs-lookup"><span data-stu-id="c9181-182">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="c9181-183">Para eliminar la base de datos, use SSOX o ejecute el comando `database drop` de la CLI.</span><span class="sxs-lookup"><span data-stu-id="c9181-183">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="c9181-184">Prueba la implementación</span><span class="sxs-lookup"><span data-stu-id="c9181-184">Test the implementation</span></span>

<span data-ttu-id="c9181-185">Ejecute la aplicación y haga la prueba en distintas páginas.</span><span class="sxs-lookup"><span data-stu-id="c9181-185">Run the app and try various pages.</span></span> <span data-ttu-id="c9181-186">Todo funciona igual que antes.</span><span class="sxs-lookup"><span data-stu-id="c9181-186">Everything works the same as it did before.</span></span>

<span data-ttu-id="c9181-187">En **Explorador de objetos de SQL Server**, expanda **Conexiones de datos/SchoolContext** y después **Tables**, y verá que las tablas Student e Instructor se han reemplazado por una tabla Person.</span><span class="sxs-lookup"><span data-stu-id="c9181-187">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="c9181-188">Abra el diseñador de la tabla Person y verá que contiene todas las columnas que solía haber en las tablas Student e Instructor.</span><span class="sxs-lookup"><span data-stu-id="c9181-188">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Tabla Person en SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="c9181-190">Haga clic con el botón derecho en la tabla Person y después haga clic en **Mostrar datos de tabla** para ver la columna discriminadora.</span><span class="sxs-lookup"><span data-stu-id="c9181-190">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Tabla Person en SSOX: datos de la tabla](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="c9181-192">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="c9181-192">Get the code</span></span>

[<span data-ttu-id="c9181-193">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="c9181-193">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="c9181-194">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c9181-194">Additional resources</span></span>

<span data-ttu-id="c9181-195">Para obtener más información sobre la herencia en Entity Framework Core, consulte [Herencia](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="c9181-195">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="c9181-196">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="c9181-196">Next steps</span></span>

<span data-ttu-id="c9181-197">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="c9181-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c9181-198">Asignado la herencia a la base de datos</span><span class="sxs-lookup"><span data-stu-id="c9181-198">Mapped inheritance to database</span></span>
> * <span data-ttu-id="c9181-199">Creado la clase Person</span><span class="sxs-lookup"><span data-stu-id="c9181-199">Created the Person class</span></span>
> * <span data-ttu-id="c9181-200">Actualizado Instructor y Student</span><span class="sxs-lookup"><span data-stu-id="c9181-200">Updated Instructor and Student</span></span>
> * <span data-ttu-id="c9181-201">Agregado Person al modelo</span><span class="sxs-lookup"><span data-stu-id="c9181-201">Added Person to the model</span></span>
> * <span data-ttu-id="c9181-202">Creado y actualizado migraciones</span><span class="sxs-lookup"><span data-stu-id="c9181-202">Created and update migrations</span></span>
> * <span data-ttu-id="c9181-203">Probado la implementación</span><span class="sxs-lookup"><span data-stu-id="c9181-203">Tested the implementation</span></span>

<span data-ttu-id="c9181-204">Pase al tutorial siguiente para obtener información sobre cómo controlar una serie de escenarios de Entity Framework relativamente avanzados.</span><span class="sxs-lookup"><span data-stu-id="c9181-204">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c9181-205">Siguiente: Temas avanzados</span><span class="sxs-lookup"><span data-stu-id="c9181-205">Next: Advanced topics</span></span>](advanced.md)
