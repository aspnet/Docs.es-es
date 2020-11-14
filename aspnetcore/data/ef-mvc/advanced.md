---
title: 'Tutorial: Información sobre escenarios avanzados: ASP.NET MVC con EF Core'
description: En este tutorial se presentan varios temas que le serán de utilidad cuando quiera ir más allá de los conceptos básicos del desarrollo de aplicaciones web ASP.NET Core que usan Entity Framework Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/advanced
ms.openlocfilehash: 386be395399bf4131e4b6c8cac8221f994e8b7c5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054390"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="18e51-103">Tutorial: Información sobre escenarios avanzados: ASP.NET MVC con EF Core</span><span class="sxs-lookup"><span data-stu-id="18e51-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="18e51-104">En el tutorial anterior, se implementó la herencia de tabla por jerarquía.</span><span class="sxs-lookup"><span data-stu-id="18e51-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="18e51-105">En este tutorial se presentan varios temas que es importante tener en cuenta cuando va más allá de los conceptos básicos del desarrollo de aplicaciones web ASP.NET Core que usan Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="18e51-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="18e51-106">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="18e51-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="18e51-107">Realiza consultas SQL sin formato</span><span class="sxs-lookup"><span data-stu-id="18e51-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="18e51-108">Llama a una consulta para devolver entidades</span><span class="sxs-lookup"><span data-stu-id="18e51-108">Call a query to return entities</span></span>
> * <span data-ttu-id="18e51-109">Llama a una consulta para devolver otros tipos</span><span class="sxs-lookup"><span data-stu-id="18e51-109">Call a query to return other types</span></span>
> * <span data-ttu-id="18e51-110">Llamar a una consulta update</span><span class="sxs-lookup"><span data-stu-id="18e51-110">Call an update query</span></span>
> * <span data-ttu-id="18e51-111">Examina consultas SQL</span><span class="sxs-lookup"><span data-stu-id="18e51-111">Examine SQL queries</span></span>
> * <span data-ttu-id="18e51-112">Crea una capa de abstracción</span><span class="sxs-lookup"><span data-stu-id="18e51-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="18e51-113">Obtiene información sobre la detección de cambios automática</span><span class="sxs-lookup"><span data-stu-id="18e51-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="18e51-114">Obtiene información sobre el código fuente y planes de desarrollo de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="18e51-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="18e51-115">Obtiene información sobre cómo usar LINQ dinámico para simplificar el código</span><span class="sxs-lookup"><span data-stu-id="18e51-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="18e51-116">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="18e51-116">Prerequisites</span></span>

* [<span data-ttu-id="18e51-117">Implementación de la herencia</span><span class="sxs-lookup"><span data-stu-id="18e51-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="18e51-118">Realiza consultas SQL sin formato</span><span class="sxs-lookup"><span data-stu-id="18e51-118">Perform raw SQL queries</span></span>

<span data-ttu-id="18e51-119">Una de las ventajas del uso de Entity Framework es que evita enlazar el código demasiado estrechamente a un método concreto de almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="18e51-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="18e51-120">Lo consigue mediante la generación de consultas SQL y comandos, lo que también le evita tener que escribirlos usted mismo.</span><span class="sxs-lookup"><span data-stu-id="18e51-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="18e51-121">Pero hay situaciones excepcionales en las que necesita ejecutar consultas específicas de SQL que ha creado manualmente.</span><span class="sxs-lookup"><span data-stu-id="18e51-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="18e51-122">En estos casos, la API de Entity Framework Code First incluye métodos que le permiten pasar comandos SQL directamente a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18e51-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="18e51-123">En EF Core 1.0 dispone de las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="18e51-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="18e51-124">Use el método `DbSet.FromSql` para las consultas que devuelven tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="18e51-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="18e51-125">Los objetos devueltos deben ser del tipo esperado por el objeto `DbSet` y se les realiza automáticamente un seguimiento mediante el contexto de base de datos a menos que se [desactive el seguimiento](crud.md#no-tracking-queries).</span><span class="sxs-lookup"><span data-stu-id="18e51-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="18e51-126">Use `Database.ExecuteSqlCommand` para comandos sin consulta.</span><span class="sxs-lookup"><span data-stu-id="18e51-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="18e51-127">Si tiene que ejecutar una consulta que devuelve tipos que no son entidades, puede usar ADO.NET con la conexión de base de datos proporcionada por EF.</span><span class="sxs-lookup"><span data-stu-id="18e51-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="18e51-128">No se realiza un seguimiento de los datos devueltos por el contexto de la base de datos, incluso si usa este método para recuperar tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="18e51-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="18e51-129">Como siempre es true cuando ejecuta comandos SQL en una aplicación web, debe tomar precauciones para proteger su sitio contra los ataques por inyección de código SQL.</span><span class="sxs-lookup"><span data-stu-id="18e51-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="18e51-130">Una manera de hacerlo es mediante consultas parametrizadas para asegurarse de que las cadenas enviadas por una página web no se pueden interpretar como comandos SQL.</span><span class="sxs-lookup"><span data-stu-id="18e51-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="18e51-131">En este tutorial usará las consultas con parámetros al integrar la entrada de usuario en una consulta.</span><span class="sxs-lookup"><span data-stu-id="18e51-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="18e51-132">Llama a una consulta para devolver entidades</span><span class="sxs-lookup"><span data-stu-id="18e51-132">Call a query to return entities</span></span>

<span data-ttu-id="18e51-133">La clase `DbSet<TEntity>` proporciona un método que puede usar para ejecutar una consulta que devuelve una entidad de tipo `TEntity`.</span><span class="sxs-lookup"><span data-stu-id="18e51-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="18e51-134">Para ver cómo funciona, cambiará el código en el método `Details` del controlador de departamento.</span><span class="sxs-lookup"><span data-stu-id="18e51-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="18e51-135">En *DepartmentsController.cs* , en el método `Details`, reemplace el código que recupera un departamento con una llamada al método `FromSql`, como se muestra en el código resaltado siguiente:</span><span class="sxs-lookup"><span data-stu-id="18e51-135">In *DepartmentsController.cs* , in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="18e51-136">Para comprobar que el nuevo código funciona correctamente, seleccione la pestaña **Departments** y, después, **Details** para uno de los departamentos.</span><span class="sxs-lookup"><span data-stu-id="18e51-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Detalles del departamento](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="18e51-138">Llama a una consulta para devolver otros tipos</span><span class="sxs-lookup"><span data-stu-id="18e51-138">Call a query to return other types</span></span>

<span data-ttu-id="18e51-139">Anteriormente creó una cuadrícula de estadísticas de alumno de la página About que mostraba el número de alumnos para cada fecha de inscripción.</span><span class="sxs-lookup"><span data-stu-id="18e51-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="18e51-140">Obtuvo los datos del conjunto de entidades Students (`_context.Students`) y usó LINQ para proyectar los resultados en una lista de objetos de modelo de vista `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="18e51-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="18e51-141">Suponga que quiere escribir la instrucción SQL propia en lugar de usar LINQ.</span><span class="sxs-lookup"><span data-stu-id="18e51-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="18e51-142">Para ello, necesita ejecutar una consulta SQL que devuelve un valor distinto de objetos entidad.</span><span class="sxs-lookup"><span data-stu-id="18e51-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="18e51-143">En EF Core 1.0, una manera de hacerlo es escribir código de ADO.NET y obtener la conexión de base de datos de EF.</span><span class="sxs-lookup"><span data-stu-id="18e51-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="18e51-144">En *HomeController.cs* , reemplace el método `About` por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="18e51-144">In *HomeController.cs* , replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="18e51-145">Agregue una instrucción using:</span><span class="sxs-lookup"><span data-stu-id="18e51-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="18e51-146">Ejecute la aplicación y vaya a la página About.</span><span class="sxs-lookup"><span data-stu-id="18e51-146">Run the app and go to the About page.</span></span> <span data-ttu-id="18e51-147">Muestra los mismos datos que anteriormente.</span><span class="sxs-lookup"><span data-stu-id="18e51-147">It displays the same data it did before.</span></span>

![Página About](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="18e51-149">Llamar a una consulta update</span><span class="sxs-lookup"><span data-stu-id="18e51-149">Call an update query</span></span>

<span data-ttu-id="18e51-150">Imagine que los administradores de Contoso University quieren realizar cambios globales en la base de datos, como cambiar el número de créditos para cada curso.</span><span class="sxs-lookup"><span data-stu-id="18e51-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="18e51-151">Si la universidad tiene un gran número de cursos, sería poco eficaz recuperarlos todos como entidades y cambiarlos de forma individual.</span><span class="sxs-lookup"><span data-stu-id="18e51-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="18e51-152">En esta sección implementará una página web que permite al usuario especificar un factor por el cual se va a cambiar el número de créditos para todos los cursos y podrá realizar el cambio mediante la ejecución de una instrucción UPDATE de SQL.</span><span class="sxs-lookup"><span data-stu-id="18e51-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="18e51-153">La página web tendrá el mismo aspecto que la ilustración siguiente:</span><span class="sxs-lookup"><span data-stu-id="18e51-153">The web page will look like the following illustration:</span></span>

![Página Update Course Credits](advanced/_static/update-credits.png)

<span data-ttu-id="18e51-155">En *CoursesContoller.cs* , agregue los métodos UpdateCourseCredits para HttpGet y HttpPost:</span><span class="sxs-lookup"><span data-stu-id="18e51-155">In *CoursesController.cs* , add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="18e51-156">Cuando el controlador procesa una solicitud HttpGet, no se devuelve nada en `ViewData["RowsAffected"]` y la vista muestra un cuadro de texto vacío y un botón de envío, tal como se muestra en la ilustración anterior.</span><span class="sxs-lookup"><span data-stu-id="18e51-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="18e51-157">Cuando se hace clic en el botón **Update** , se llama al método HttpPost y el multiplicador tiene el valor especificado en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="18e51-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="18e51-158">A continuación, el código ejecuta la instrucción SQL que actualiza los cursos y devuelve el número de filas afectadas a la vista en `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="18e51-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="18e51-159">Cuando la vista obtiene un valor `RowsAffected`, muestra el número de filas actualizadas.</span><span class="sxs-lookup"><span data-stu-id="18e51-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="18e51-160">En el **Explorador de soluciones** , haga clic con el botón derecho en la carpeta *Views/Courses* y luego haga clic en **Agregar > Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="18e51-160">In **Solution Explorer** , right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="18e51-161">En el cuadro de diálogo **Agregar nuevo elemento** , haga clic en **ASP.NET Core** en **Instalado** en el panel izquierdo, haga clic en **Vista de Razor** y ponga el nombre *UpdateCourseCredits.cshtml* a la nueva vista.</span><span class="sxs-lookup"><span data-stu-id="18e51-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View** , and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="18e51-162">En *Views/Courses/UpdateCourseCredits.cshtml* , reemplace el código de plantilla con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="18e51-162">In *Views/Courses/UpdateCourseCredits.cshtml* , replace the template code with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="18e51-163">Ejecute el método `UpdateCourseCredits` seleccionando la pestaña **Courses** , después, agregue "/UpdateCourseCredits" al final de la dirección URL en la barra de direcciones del explorador (por ejemplo: `http://localhost:5813/Courses/UpdateCourseCredits`).</span><span class="sxs-lookup"><span data-stu-id="18e51-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="18e51-164">Escriba un número en el cuadro de texto:</span><span class="sxs-lookup"><span data-stu-id="18e51-164">Enter a number in the text box:</span></span>

![Página Update Course Credits](advanced/_static/update-credits.png)

<span data-ttu-id="18e51-166">Haga clic en **Actualizar**.</span><span class="sxs-lookup"><span data-stu-id="18e51-166">Click **Update**.</span></span> <span data-ttu-id="18e51-167">Verá el número de filas afectadas:</span><span class="sxs-lookup"><span data-stu-id="18e51-167">You see the number of rows affected:</span></span>

![Filas afectadas de la página Update Course Credits](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="18e51-169">Haga clic en **Volver a la lista** para ver la lista de cursos con el número de créditos revisado.</span><span class="sxs-lookup"><span data-stu-id="18e51-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="18e51-170">Tenga en cuenta que el código de producción garantiza que las actualizaciones siempre dan como resultado datos válidos.</span><span class="sxs-lookup"><span data-stu-id="18e51-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="18e51-171">El código simplificado que se muestra a continuación podría multiplicar el número de créditos lo suficiente para que el resultado sea un número superior a 5.</span><span class="sxs-lookup"><span data-stu-id="18e51-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="18e51-172">(La propiedad `Credits` tiene un atributo `[Range(0, 5)]`). La consulta update podría funcionar, pero los datos no válidos podrían provocar resultados inesperados en otras partes del sistema que asumen que el número de créditos es igual o inferior a 5.</span><span class="sxs-lookup"><span data-stu-id="18e51-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="18e51-173">Para obtener más información sobre las consultas SQL básicas, vea [Consultas SQL básicas](/ef/core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="18e51-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="18e51-174">Examina consultas SQL</span><span class="sxs-lookup"><span data-stu-id="18e51-174">Examine SQL queries</span></span>

<span data-ttu-id="18e51-175">A veces resulta útil poder ver las consultas SQL reales que se envían a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18e51-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="18e51-176">EF Core usa automáticamente la funcionalidad de registro integrada para ASP.NET Core para escribir los registros que contienen el código SQL para las consultas y actualizaciones.</span><span class="sxs-lookup"><span data-stu-id="18e51-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="18e51-177">En esta sección podrá ver algunos ejemplos de registros de SQL.</span><span class="sxs-lookup"><span data-stu-id="18e51-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="18e51-178">Abra *StudentsController.cs* y, en el método `Details`, establezca un punto de interrupción en la instrucción `if (student == null)`.</span><span class="sxs-lookup"><span data-stu-id="18e51-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="18e51-179">Ejecute la aplicación en modo de depuración y vaya a la página Details de un alumno.</span><span class="sxs-lookup"><span data-stu-id="18e51-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="18e51-180">Vaya a la ventana **Salida** que muestra la salida de depuración y verá la consulta:</span><span class="sxs-lookup"><span data-stu-id="18e51-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

<span data-ttu-id="18e51-181">Aquí verá algo que podría sorprenderle: la instrucción SQL selecciona hasta 2 filas (`TOP(2)`) de la tabla Person.</span><span class="sxs-lookup"><span data-stu-id="18e51-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="18e51-182">El método `SingleOrDefaultAsync` no se resuelve en 1 fila en el servidor.</span><span class="sxs-lookup"><span data-stu-id="18e51-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="18e51-183">El motivo es el siguiente:</span><span class="sxs-lookup"><span data-stu-id="18e51-183">Here's why:</span></span>

* <span data-ttu-id="18e51-184">Si la consulta devolvería varias filas, el método devuelve NULL.</span><span class="sxs-lookup"><span data-stu-id="18e51-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="18e51-185">Para determinar si la consulta devolvería varias filas, EF tiene que comprobar si devuelve al menos 2.</span><span class="sxs-lookup"><span data-stu-id="18e51-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="18e51-186">Tenga en cuenta que no tiene que usar el modo de depuración y parar en un punto de interrupción para obtener los resultados del registro en la ventana **Salida**.</span><span class="sxs-lookup"><span data-stu-id="18e51-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="18e51-187">Es una manera cómoda de detener el registro en el punto que quiere ver en la salida.</span><span class="sxs-lookup"><span data-stu-id="18e51-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="18e51-188">Si no lo hace, el registro continúa y tiene que desplazarse hacia atrás para encontrar los elementos que le interesan.</span><span class="sxs-lookup"><span data-stu-id="18e51-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="18e51-189">Crea una capa de abstracción</span><span class="sxs-lookup"><span data-stu-id="18e51-189">Create an abstraction layer</span></span>

<span data-ttu-id="18e51-190">Muchos desarrolladores escriben código para implementar el repositorio y una unidad de patrones de trabajo como un contenedor en torno al código que funciona con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="18e51-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="18e51-191">Estos patrones están previstos para crear una capa de abstracción entre la capa de acceso de datos y la capa de lógica de negocios de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="18e51-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="18e51-192">Implementar estos patrones puede ayudar a aislar la aplicación de cambios en el almacén de datos y puede facilitar la realización de pruebas unitarias automatizadas o el desarrollo controlado por pruebas (TDD).</span><span class="sxs-lookup"><span data-stu-id="18e51-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="18e51-193">Pero escribir código adicional para implementar estos patrones no siempre es la mejor opción para las aplicaciones que usan EF, por varias razones:</span><span class="sxs-lookup"><span data-stu-id="18e51-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="18e51-194">La propia clase de contexto de EF aísla el código del código específico del almacén de datos.</span><span class="sxs-lookup"><span data-stu-id="18e51-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="18e51-195">La clase de contexto de EF puede actuar como una clase de unidad de trabajo para las actualizaciones de base de datos que hace con EF.</span><span class="sxs-lookup"><span data-stu-id="18e51-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="18e51-196">EF incluye características para implementar TDD sin escribir código de repositorio.</span><span class="sxs-lookup"><span data-stu-id="18e51-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="18e51-197">Para obtener información sobre cómo implementar los patrones de repositorio y de unidad de trabajo, consulte [la versión de Entity Framework 5 de esta serie de tutoriales](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="18e51-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="18e51-198">Entity Framework Core implementa un proveedor de base de datos en memoria que puede usarse para realizar pruebas.</span><span class="sxs-lookup"><span data-stu-id="18e51-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="18e51-199">Para más información, vea [Pruebas con InMemory](/ef/core/miscellaneous/testing/in-memory).</span><span class="sxs-lookup"><span data-stu-id="18e51-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="18e51-200">Detección de cambios automática</span><span class="sxs-lookup"><span data-stu-id="18e51-200">Automatic change detection</span></span>

<span data-ttu-id="18e51-201">Entity Framework determina cómo ha cambiado una entidad (y, por tanto, las actualizaciones que hay que enviar a la base de datos) comparando los valores actuales de una entidad con los valores originales.</span><span class="sxs-lookup"><span data-stu-id="18e51-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="18e51-202">Cuando se consulta o se adjunta la entidad, se almacenan los valores originales.</span><span class="sxs-lookup"><span data-stu-id="18e51-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="18e51-203">Algunos de los métodos que provocan la detección de cambios automática son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="18e51-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="18e51-204">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="18e51-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="18e51-205">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="18e51-205">DbContext.Entry</span></span>

* <span data-ttu-id="18e51-206">ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="18e51-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="18e51-207">Si está realizando el seguimiento de un gran número de entidades y llama a uno de estos métodos muchas veces en un bucle, podría obtener mejoras de rendimiento significativas si desactiva temporalmente la detección de cambios automática mediante la propiedad `ChangeTracker.AutoDetectChangesEnabled`.</span><span class="sxs-lookup"><span data-stu-id="18e51-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="18e51-208">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="18e51-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="18e51-209">Código fuente y planes de desarrollo de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="18e51-209">EF Core source code and development plans</span></span>

<span data-ttu-id="18e51-210">El origen de Entity Framework Core está en [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span><span class="sxs-lookup"><span data-stu-id="18e51-210">The Entity Framework Core source is at [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span></span> <span data-ttu-id="18e51-211">El repositorio de EF Core contiene las compilaciones nocturnas, el seguimiento de problemas, las especificaciones de características, las notas de las reuniones de diseño y [el plan de desarrollo futuro](https://github.com/dotnet/efcore/wiki/Roadmap).</span><span class="sxs-lookup"><span data-stu-id="18e51-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/dotnet/efcore/wiki/Roadmap).</span></span> <span data-ttu-id="18e51-212">Puede archivar o buscar errores y contribuir.</span><span class="sxs-lookup"><span data-stu-id="18e51-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="18e51-213">Aunque el código fuente es abierto, Entity Framework Core es totalmente compatible como producto de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="18e51-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="18e51-214">El equipo de Microsoft Entity Framework mantiene el control sobre qué contribuciones se aceptan y comprueba todos los cambios de código para garantizar la calidad de cada versión.</span><span class="sxs-lookup"><span data-stu-id="18e51-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="18e51-215">Ingeniería inversa desde la base de datos existente</span><span class="sxs-lookup"><span data-stu-id="18e51-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="18e51-216">Para usar técnicas de ingeniería inversa a un modelo de datos, incluidas las clases de entidad de una base de datos existente, use el comando [scaffold dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="18e51-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="18e51-217">Consulte el [tutorial de introducción](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="18e51-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="18e51-218">Usar LINQ dinámico para simplificar el código</span><span class="sxs-lookup"><span data-stu-id="18e51-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="18e51-219">El [tercer tutorial de esta serie](sort-filter-page.md) muestra cómo escribir código LINQ mediante el codificado de forma rígida de los nombres de columna en una instrucción `switch`.</span><span class="sxs-lookup"><span data-stu-id="18e51-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="18e51-220">Con dos columnas entre las que elegir, esto funciona bien, pero si tiene muchas columnas, el código podría considerarse detallado.</span><span class="sxs-lookup"><span data-stu-id="18e51-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="18e51-221">Para solucionar este problema, puede usar el método `EF.Property` para especificar el nombre de la propiedad como una cadena.</span><span class="sxs-lookup"><span data-stu-id="18e51-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="18e51-222">Para probar este enfoque, reemplace el método `Index` en `StudentsController` con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="18e51-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="18e51-223">Agradecimientos</span><span class="sxs-lookup"><span data-stu-id="18e51-223">Acknowledgments</span></span>

<span data-ttu-id="18e51-224">Tom Dykstra y Rick Anderson (Twitter @RickAndMSFT) escribieron este tutorial.</span><span class="sxs-lookup"><span data-stu-id="18e51-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="18e51-225">Rowan Miller, Diego Vega y otros miembros del equipo de Entity Framework participaron con revisiones de código y ayudaron a depurar problemas que surgieron mientras se estaba escribiendo el código para los tutoriales.</span><span class="sxs-lookup"><span data-stu-id="18e51-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="18e51-226">John Parente y Paul Goldman trabajaron en la actualización del tutorial de ASP.NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="18e51-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="18e51-227">Solucionar problemas de errores comunes</span><span class="sxs-lookup"><span data-stu-id="18e51-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="18e51-228">ContosoUniversity.dll usado por otro proceso</span><span class="sxs-lookup"><span data-stu-id="18e51-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="18e51-229">Mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="18e51-229">Error message:</span></span>

> <span data-ttu-id="18e51-230">No se puede abrir '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' para escribir: El proceso no puede obtener acceso al archivo '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll', otro proceso lo está usando.</span><span class="sxs-lookup"><span data-stu-id="18e51-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="18e51-231">Solución:</span><span class="sxs-lookup"><span data-stu-id="18e51-231">Solution:</span></span>

<span data-ttu-id="18e51-232">Detenga el sitio en IIS Express.</span><span class="sxs-lookup"><span data-stu-id="18e51-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="18e51-233">Vaya a la bandeja del sistema de Windows, busque IIS Express y haga clic con el botón derecho en su icono; seleccione el sitio de Contoso University y, después, haga clic en **Detener sitio**.</span><span class="sxs-lookup"><span data-stu-id="18e51-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="18e51-234">La migración aplicó la técnica scaffolding sin código en los métodos Up y Down</span><span class="sxs-lookup"><span data-stu-id="18e51-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="18e51-235">Causa posible:</span><span class="sxs-lookup"><span data-stu-id="18e51-235">Possible cause:</span></span>

<span data-ttu-id="18e51-236">Los comandos de la CLI de EF no cierran y guardan automáticamente los archivos de código.</span><span class="sxs-lookup"><span data-stu-id="18e51-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="18e51-237">Si no ha guardado los cambios cuando ejecuta el comando `migrations add`, EF no encontrará los cambios.</span><span class="sxs-lookup"><span data-stu-id="18e51-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="18e51-238">Solución:</span><span class="sxs-lookup"><span data-stu-id="18e51-238">Solution:</span></span>

<span data-ttu-id="18e51-239">Ejecute el comando `migrations remove`, guarde los cambios de código y vuelva a ejecutar el comando `migrations add`.</span><span class="sxs-lookup"><span data-stu-id="18e51-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="18e51-240">Errores durante la ejecución de la actualización de la base de datos</span><span class="sxs-lookup"><span data-stu-id="18e51-240">Errors while running database update</span></span>

<span data-ttu-id="18e51-241">Al hacer cambios en el esquema, se pueden generar otros errores en una base de datos que contenga los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="18e51-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="18e51-242">Si se producen errores de migración que no se pueden resolver, puede cambiar el nombre de la base de datos en la cadena de conexión o eliminar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18e51-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="18e51-243">Con una base de datos nueva, no hay ningún dato para migrar y es mucho más probable que el comando de actualización de base de datos se complete sin errores.</span><span class="sxs-lookup"><span data-stu-id="18e51-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="18e51-244">El enfoque más sencillo consiste en cambiar el nombre de la base de datos en *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="18e51-244">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="18e51-245">La próxima vez que ejecute `database update`, se creará una base de datos.</span><span class="sxs-lookup"><span data-stu-id="18e51-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="18e51-246">Para eliminar una base de datos en SSOX, haga clic con el botón derecho en la base de datos, haga clic en **Eliminar** y, después, en el cuadro de diálogo **Eliminar base de datos** , seleccione **Cerrar conexiones existentes** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="18e51-246">To delete a database in SSOX, right-click the database, click **Delete** , and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="18e51-247">Para eliminar una base de datos mediante el uso de la CLI, ejecute el comando de la CLI `database drop`:</span><span class="sxs-lookup"><span data-stu-id="18e51-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="18e51-248">Error al buscar la instancia de SQL Server</span><span class="sxs-lookup"><span data-stu-id="18e51-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="18e51-249">Mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="18e51-249">Error Message:</span></span>

> <span data-ttu-id="18e51-250">Error relacionado con la red o específico de la instancia mientras se establecía una conexión con el servidor SQL Server.</span><span class="sxs-lookup"><span data-stu-id="18e51-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="18e51-251">No se encontró el servidor o no era accesible.</span><span class="sxs-lookup"><span data-stu-id="18e51-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="18e51-252">Compruebe que el nombre de la instancia es correcto y que SQL Server está configurado para admitir conexiones remotas.</span><span class="sxs-lookup"><span data-stu-id="18e51-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="18e51-253">(proveedor: Interfaces de red SQL, error: 26: error al buscar el servidor o la instancia especificados)</span><span class="sxs-lookup"><span data-stu-id="18e51-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="18e51-254">Solución:</span><span class="sxs-lookup"><span data-stu-id="18e51-254">Solution:</span></span>

<span data-ttu-id="18e51-255">Compruebe la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="18e51-255">Check the connection string.</span></span> <span data-ttu-id="18e51-256">Si ha eliminado manualmente el archivo de base de datos, cambie el nombre de la base de datos en la cadena de construcción para volver a empezar con una base de datos nueva.</span><span class="sxs-lookup"><span data-stu-id="18e51-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="18e51-257">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="18e51-257">Get the code</span></span>

[<span data-ttu-id="18e51-258">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="18e51-258">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="18e51-259">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="18e51-259">Additional resources</span></span>

<span data-ttu-id="18e51-260">Para obtener más información sobre EF Core, consulte la [documentación de Entity Framework Core](/ef/core).</span><span class="sxs-lookup"><span data-stu-id="18e51-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="18e51-261">También hay disponible un libro: [Entity Framework Core en acción](https://www.manning.com/books/entity-framework-core-in-action).</span><span class="sxs-lookup"><span data-stu-id="18e51-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="18e51-262">Para obtener información sobre cómo implementar una aplicación web, consulte <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="18e51-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="18e51-263">Para obtener información sobre otros temas relacionados con ASP.NET Core MVC, como la autenticación y autorización, vea <xref:index>.</span><span class="sxs-lookup"><span data-stu-id="18e51-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="18e51-264">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="18e51-264">Next steps</span></span>

<span data-ttu-id="18e51-265">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="18e51-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="18e51-266">Realizado consultas SQL sin formato</span><span class="sxs-lookup"><span data-stu-id="18e51-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="18e51-267">Llamado a una consulta para devolver entidades</span><span class="sxs-lookup"><span data-stu-id="18e51-267">Called a query to return entities</span></span>
> * <span data-ttu-id="18e51-268">Llamado a una consulta para devolver otros tipos</span><span class="sxs-lookup"><span data-stu-id="18e51-268">Called a query to return other types</span></span>
> * <span data-ttu-id="18e51-269">Llamado a una consulta update</span><span class="sxs-lookup"><span data-stu-id="18e51-269">Called an update query</span></span>
> * <span data-ttu-id="18e51-270">Examinado consultas SQL</span><span class="sxs-lookup"><span data-stu-id="18e51-270">Examined SQL queries</span></span>
> * <span data-ttu-id="18e51-271">Creado una capa de abstracción</span><span class="sxs-lookup"><span data-stu-id="18e51-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="18e51-272">Obtenido información sobre la detección de cambios automática</span><span class="sxs-lookup"><span data-stu-id="18e51-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="18e51-273">Obtenido información sobre el código fuente y planes de desarrollo de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="18e51-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="18e51-274">Obtenido información sobre cómo usar LINQ dinámico para simplificar el código</span><span class="sxs-lookup"><span data-stu-id="18e51-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="18e51-275">Con esto finaliza esta serie de tutoriales sobre cómo usar Entity Framework Core en una aplicación ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="18e51-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="18e51-276">En esta serie se ha trabajado con una base de datos nueva, pero también se pueden utilizar técnicas de ingeniería inversa en un modelo de una base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="18e51-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="18e51-277">Tutorial: EF Core con MVC, base de datos existente</span><span class="sxs-lookup"><span data-stu-id="18e51-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
