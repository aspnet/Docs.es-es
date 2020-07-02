---
title: 'Tutorial: Adición de ordenación, filtrado y paginación: ASP.NET MVC con EF Core'
description: En este tutorial agregaremos la funcionalidad de ordenación, filtrado y paginación a la página de índice de Students. También crearemos una página que realice agrupaciones sencillas.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 45aabc644fbeaeaa31d534877ba93cb0611f3f34
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401342"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="8b868-104">Tutorial: Adición de ordenación, filtrado y paginación: ASP.NET MVC con EF Core</span><span class="sxs-lookup"><span data-stu-id="8b868-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="8b868-105">En el tutorial anterior, implementamos un conjunto de páginas web para operaciones básicas de CRUD para las entidades Student.</span><span class="sxs-lookup"><span data-stu-id="8b868-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="8b868-106">En este tutorial agregaremos la funcionalidad de ordenación, filtrado y paginación a la página de índice de Students.</span><span class="sxs-lookup"><span data-stu-id="8b868-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="8b868-107">También crearemos una página que realice agrupaciones sencillas.</span><span class="sxs-lookup"><span data-stu-id="8b868-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="8b868-108">En la siguiente ilustración se muestra el aspecto que tendrá la página cuando haya terminado.</span><span class="sxs-lookup"><span data-stu-id="8b868-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="8b868-109">Los encabezados de columna son vínculos en los que el usuario puede hacer clic para ordenar las columnas correspondientes.</span><span class="sxs-lookup"><span data-stu-id="8b868-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="8b868-110">Si se hace clic de forma consecutiva en el encabezado de una columna, el criterio de ordenación alterna entre ascendente y descendente.</span><span class="sxs-lookup"><span data-stu-id="8b868-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Página de índice de Students](sort-filter-page/_static/paging.png)

<span data-ttu-id="8b868-112">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="8b868-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8b868-113">Agrega vínculos de ordenación de columnas</span><span class="sxs-lookup"><span data-stu-id="8b868-113">Add column sort links</span></span>
> * <span data-ttu-id="8b868-114">Agrega un cuadro de búsqueda</span><span class="sxs-lookup"><span data-stu-id="8b868-114">Add a Search box</span></span>
> * <span data-ttu-id="8b868-115">Agrega paginación al índice de Students</span><span class="sxs-lookup"><span data-stu-id="8b868-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="8b868-116">Agrega paginación al método Index</span><span class="sxs-lookup"><span data-stu-id="8b868-116">Add paging to Index method</span></span>
> * <span data-ttu-id="8b868-117">Agrega vínculos de paginación</span><span class="sxs-lookup"><span data-stu-id="8b868-117">Add paging links</span></span>
> * <span data-ttu-id="8b868-118">Crea una página About</span><span class="sxs-lookup"><span data-stu-id="8b868-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8b868-119">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="8b868-119">Prerequisites</span></span>

* [<span data-ttu-id="8b868-120">Implementación de la funcionalidad CRUD</span><span class="sxs-lookup"><span data-stu-id="8b868-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="8b868-121">Agrega vínculos de ordenación de columnas</span><span class="sxs-lookup"><span data-stu-id="8b868-121">Add column sort links</span></span>

<span data-ttu-id="8b868-122">Para agregar ordenación a la página de índice de Student, deberá cambiar el método `Index` del controlador de Students y agregar código a la vista de índice de Student.</span><span class="sxs-lookup"><span data-stu-id="8b868-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="8b868-123">Agregar la funcionalidad de ordenación al método Index</span><span class="sxs-lookup"><span data-stu-id="8b868-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="8b868-124">En *StudentsController.cs*, reemplace el método `Index` por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8b868-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="8b868-125">Este código recibe un parámetro `sortOrder` de la cadena de consulta en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8b868-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="8b868-126">ASP.NET Core MVC proporciona el valor de la cadena de consulta como un parámetro al método de acción.</span><span class="sxs-lookup"><span data-stu-id="8b868-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="8b868-127">El parámetro es una cadena que puede ser "Name" o "Date", seguido (opcionalmente) por un guión bajo y la cadena "desc" para especificar el orden descendente.</span><span class="sxs-lookup"><span data-stu-id="8b868-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="8b868-128">El criterio de ordenación predeterminado es el ascendente.</span><span class="sxs-lookup"><span data-stu-id="8b868-128">The default sort order is ascending.</span></span>

<span data-ttu-id="8b868-129">La primera vez que se solicita la página de índice, no hay ninguna cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="8b868-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="8b868-130">Los alumnos se muestran por apellido en orden ascendente, que es el valor predeterminado establecido por el caso desestimado en la instrucción `switch`.</span><span class="sxs-lookup"><span data-stu-id="8b868-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="8b868-131">Cuando el usuario hace clic en un hipervínculo de encabezado de columna, se proporciona el valor `sortOrder` correspondiente en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="8b868-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="8b868-132">La vista usa los dos elementos `ViewData` (NameSortParm y DateSortParm) para configurar los hipervínculos del encabezado de columna con los valores de cadena de consulta adecuados.</span><span class="sxs-lookup"><span data-stu-id="8b868-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="8b868-133">Estas son las instrucciones ternarias.</span><span class="sxs-lookup"><span data-stu-id="8b868-133">These are ternary statements.</span></span> <span data-ttu-id="8b868-134">La primera de ellas especifica que, si el parámetro `sortOrder` es NULL o está vacío, NameSortParm debe establecerse en "name_desc"; en caso contrario, se debe establecer en una cadena vacía.</span><span class="sxs-lookup"><span data-stu-id="8b868-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="8b868-135">Estas dos instrucciones habilitan la vista para establecer los hipervínculos de encabezado de columna de la forma siguiente:</span><span class="sxs-lookup"><span data-stu-id="8b868-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="8b868-136">Criterio de ordenación actual</span><span class="sxs-lookup"><span data-stu-id="8b868-136">Current sort order</span></span>  | <span data-ttu-id="8b868-137">Hipervínculo de apellido</span><span class="sxs-lookup"><span data-stu-id="8b868-137">Last Name Hyperlink</span></span> | <span data-ttu-id="8b868-138">Hipervínculo de fecha</span><span class="sxs-lookup"><span data-stu-id="8b868-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="8b868-139">Apellido: ascendente</span><span class="sxs-lookup"><span data-stu-id="8b868-139">Last Name ascending</span></span>  | <span data-ttu-id="8b868-140">descending</span><span class="sxs-lookup"><span data-stu-id="8b868-140">descending</span></span>          | <span data-ttu-id="8b868-141">ascending</span><span class="sxs-lookup"><span data-stu-id="8b868-141">ascending</span></span>      |
| <span data-ttu-id="8b868-142">Apellido: descendente</span><span class="sxs-lookup"><span data-stu-id="8b868-142">Last Name descending</span></span> | <span data-ttu-id="8b868-143">ascending</span><span class="sxs-lookup"><span data-stu-id="8b868-143">ascending</span></span>           | <span data-ttu-id="8b868-144">ascending</span><span class="sxs-lookup"><span data-stu-id="8b868-144">ascending</span></span>      |
| <span data-ttu-id="8b868-145">Fecha: ascendente</span><span class="sxs-lookup"><span data-stu-id="8b868-145">Date ascending</span></span>       | <span data-ttu-id="8b868-146">ascending</span><span class="sxs-lookup"><span data-stu-id="8b868-146">ascending</span></span>           | <span data-ttu-id="8b868-147">descending</span><span class="sxs-lookup"><span data-stu-id="8b868-147">descending</span></span>     |
| <span data-ttu-id="8b868-148">Fecha: descendente</span><span class="sxs-lookup"><span data-stu-id="8b868-148">Date descending</span></span>      | <span data-ttu-id="8b868-149">ascending</span><span class="sxs-lookup"><span data-stu-id="8b868-149">ascending</span></span>           | <span data-ttu-id="8b868-150">ascending</span><span class="sxs-lookup"><span data-stu-id="8b868-150">ascending</span></span>      |

<span data-ttu-id="8b868-151">El método usa LINQ to Entities para especificar la columna por la que se va a ordenar.</span><span class="sxs-lookup"><span data-stu-id="8b868-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="8b868-152">El código crea una variable `IQueryable` antes de la instrucción de cambio, la modifica en la instrucción de cambio y llama al método `ToListAsync` después de la instrucción `switch`.</span><span class="sxs-lookup"><span data-stu-id="8b868-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="8b868-153">Al crear y modificar variables `IQueryable`, no se envía ninguna consulta a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8b868-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="8b868-154">La consulta no se ejecuta hasta que convierta el objeto `IQueryable` en una colección mediante una llamada a un método, como `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="8b868-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="8b868-155">Por lo tanto, este código produce una única consulta que no se ejecuta hasta la instrucción `return View`.</span><span class="sxs-lookup"><span data-stu-id="8b868-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="8b868-156">Este código podría detallarse con un gran número de columnas.</span><span class="sxs-lookup"><span data-stu-id="8b868-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="8b868-157">[En el último tutorial de esta serie](advanced.md#dynamic-linq) se muestra cómo escribir código que le permita pasar el nombre de la columna `OrderBy` en una variable de cadenas.</span><span class="sxs-lookup"><span data-stu-id="8b868-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="8b868-158">Agregar hipervínculos del encabezado de columna a la vista de índice de Student</span><span class="sxs-lookup"><span data-stu-id="8b868-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="8b868-159">Reemplace el código de *Views/Students/Index.cshtml* por el código siguiente para agregar los hipervínculos del encabezado de columna.</span><span class="sxs-lookup"><span data-stu-id="8b868-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="8b868-160">Se resaltan las líneas modificadas.</span><span class="sxs-lookup"><span data-stu-id="8b868-160">The changed lines are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="8b868-161">Este código usa la información que se incluye en las propiedades `ViewData` para configurar hipervínculos con los valores de cadena de consulta adecuados.</span><span class="sxs-lookup"><span data-stu-id="8b868-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="8b868-162">Ejecute la aplicación, seleccione la ficha **Students** y haga clic en los encabezados de columna **Last Name** y **Enrollment Date** para comprobar que la ordenación funciona correctamente.</span><span class="sxs-lookup"><span data-stu-id="8b868-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Página de índice de Students ordenada por nombre](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="8b868-164">Agrega un cuadro de búsqueda</span><span class="sxs-lookup"><span data-stu-id="8b868-164">Add a Search box</span></span>

<span data-ttu-id="8b868-165">Para agregar filtrado a la página de índice de Students, agregue un cuadro de texto y un botón de envío a la vista y haga los cambios correspondientes en el método `Index`.</span><span class="sxs-lookup"><span data-stu-id="8b868-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="8b868-166">El cuadro de texto le permite escribir la cadena que quiera buscar en los campos de nombre y apellido.</span><span class="sxs-lookup"><span data-stu-id="8b868-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="8b868-167">Agregar la funcionalidad de filtrado al método Index</span><span class="sxs-lookup"><span data-stu-id="8b868-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="8b868-168">En *StudentsController.cs*, reemplace el método `Index` por el código siguiente (los cambios se resaltan).</span><span class="sxs-lookup"><span data-stu-id="8b868-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="8b868-169">Ha agregado un parámetro `searchString` al método `Index`.</span><span class="sxs-lookup"><span data-stu-id="8b868-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="8b868-170">El valor de la cadena de búsqueda se recibe desde un cuadro de texto que agregará a la vista de índice.</span><span class="sxs-lookup"><span data-stu-id="8b868-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="8b868-171">También ha agregado a la instrucción LINQ una cláusula where que solo selecciona los alumnos cuyo nombre o apellido contienen la cadena de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="8b868-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="8b868-172">La instrucción que agrega la cláusula where solo se ejecuta si hay un valor que se tiene que buscar.</span><span class="sxs-lookup"><span data-stu-id="8b868-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="8b868-173">Aquí se llama al método `Where` en un objeto `IQueryable` y el filtro se procesa en el servidor.</span><span class="sxs-lookup"><span data-stu-id="8b868-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="8b868-174">En algunos escenarios, puede hacer una llamada al método `Where` como un método de extensión en una colección en memoria.</span><span class="sxs-lookup"><span data-stu-id="8b868-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="8b868-175">(Por ejemplo, imagine que cambia la referencia a `_context.Students`, de modo que, en lugar de hacer referencia a EF `DbSet`, haga referencia a un método de repositorio que devuelva una colección `IEnumerable`). Lo más habitual es que el resultado fuera el mismo, pero en algunos casos puede ser diferente.</span><span class="sxs-lookup"><span data-stu-id="8b868-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="8b868-176">Por ejemplo, la implementación de .NET Framework del método `Contains` realiza de forma predeterminada una comparación que diferencia entre mayúsculas y minúsculas, pero en SQL Server se determina por la configuración de intercalación de la instancia de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8b868-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="8b868-177">De forma predeterminada, esta opción de configuración no diferencia entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="8b868-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="8b868-178">Podría llamar al método `ToUpper` para hacer explícitamente que la prueba no distinga entre mayúsculas y minúsculas:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())* .</span><span class="sxs-lookup"><span data-stu-id="8b868-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="8b868-179">Esto garantiza que los resultados permanezcan invariables aunque cambie el código más adelante para usar un repositorio que devuelva una colección `IEnumerable` en vez de un objeto `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="8b868-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="8b868-180">(Al hacer una llamada al método `Contains` en una colección `IEnumerable`, obtendrá la implementación de .NET Framework; al hacer una llamada a un objeto `IQueryable`, obtendrá la implementación del proveedor de base de datos). En cambio, el rendimiento de esta solución se ve reducido.</span><span class="sxs-lookup"><span data-stu-id="8b868-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="8b868-181">El código `ToUpper` tendría que poner una función en la cláusula WHERE de la instrucción SELECT de TSQL.</span><span class="sxs-lookup"><span data-stu-id="8b868-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="8b868-182">Esto impediría que el optimizador usara un índice.</span><span class="sxs-lookup"><span data-stu-id="8b868-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="8b868-183">Dado que principalmente SQL se instala de forma que no diferencia entre mayúsculas y minúsculas, es mejor que evite el código `ToUpper` hasta que migre a un almacén de datos que distinga entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="8b868-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="8b868-184">Agregar un cuadro de búsqueda a la vista de índice de Student</span><span class="sxs-lookup"><span data-stu-id="8b868-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="8b868-185">En *Views/Student/Index.cshtml*, agregue el código resaltado justo antes de la etiqueta de apertura de tabla para crear un título, un cuadro de texto y un botón de **búsqueda**.</span><span class="sxs-lookup"><span data-stu-id="8b868-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="8b868-186">Este código usa el [asistente de etiquetas](xref:mvc/views/tag-helpers/intro) `<form>` para agregar el cuadro de texto de búsqueda y el botón.</span><span class="sxs-lookup"><span data-stu-id="8b868-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="8b868-187">De forma predeterminada, el asistente de etiquetas `<form>` envía datos de formulario con POST, lo que significa que los parámetros se pasan en el cuerpo del mensaje HTTP y no en la dirección URL como cadenas de consulta.</span><span class="sxs-lookup"><span data-stu-id="8b868-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="8b868-188">Al especificar HTTP GET, los datos de formulario se pasan en la dirección URL como cadenas de consulta, lo que permite que los usuarios marquen la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8b868-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="8b868-189">Las directrices de W3C recomiendan que use GET cuando la acción no produzca ninguna actualización.</span><span class="sxs-lookup"><span data-stu-id="8b868-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="8b868-190">Ejecute la aplicación, seleccione la ficha **Students**, escriba una cadena de búsqueda y haga clic en Search para comprobar que el filtrado funciona correctamente.</span><span class="sxs-lookup"><span data-stu-id="8b868-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Página de índice de Students con filtrado](sort-filter-page/_static/filtering.png)

<span data-ttu-id="8b868-192">Fíjese en que la dirección URL contiene la cadena de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="8b868-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="8b868-193">Si marca esta página, obtendrá la lista filtrada al usar el marcador.</span><span class="sxs-lookup"><span data-stu-id="8b868-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="8b868-194">El hecho de agregar `method="get"` a la etiqueta `form` es lo que ha provocado que se generara la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="8b868-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="8b868-195">En esta fase, si hace clic en un vínculo de ordenación del encabezado de columna, el valor de filtro que especificó en el cuadro de **búsqueda** se perderá.</span><span class="sxs-lookup"><span data-stu-id="8b868-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="8b868-196">Podrá corregirlo en la siguiente sección.</span><span class="sxs-lookup"><span data-stu-id="8b868-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="8b868-197">Agrega paginación al índice de Students</span><span class="sxs-lookup"><span data-stu-id="8b868-197">Add paging to Students Index</span></span>

<span data-ttu-id="8b868-198">Para agregar paginación a la página de índice de Students, tendrá que crear una clase `PaginatedList` que use las instrucciones `Skip` y `Take` para filtrar los datos en el servidor en lugar de recuperar siempre todas las filas de la tabla.</span><span class="sxs-lookup"><span data-stu-id="8b868-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="8b868-199">A continuación, podrá realizar cambios adicionales en el método `Index` y agregar botones de paginación a la vista `Index`.</span><span class="sxs-lookup"><span data-stu-id="8b868-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="8b868-200">La ilustración siguiente muestra los botones de paginación.</span><span class="sxs-lookup"><span data-stu-id="8b868-200">The following illustration shows the paging buttons.</span></span>

![Página de índice de Students con vínculos de paginación](sort-filter-page/_static/paging.png)

<span data-ttu-id="8b868-202">En la carpeta del proyecto, cree `PaginatedList.cs` y después reemplace el código de plantilla por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="8b868-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="8b868-203">El método `CreateAsync` en este código toma el tamaño y el número de la página y aplica las instrucciones `Skip` y `Take` correspondientes a `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="8b868-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="8b868-204">Cuando se llama a `ToListAsync` en `IQueryable`, devuelve una lista que solo contiene la página solicitada.</span><span class="sxs-lookup"><span data-stu-id="8b868-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="8b868-205">Las propiedades `HasPreviousPage` y `HasNextPage` se pueden usar para habilitar o deshabilitar los botones de página **Previous** y **Next**.</span><span class="sxs-lookup"><span data-stu-id="8b868-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="8b868-206">Para crear el objeto `PaginatedList<T>`, se usa un método `CreateAsync` en vez de un constructor, porque los constructores no pueden ejecutar código asincrónico.</span><span class="sxs-lookup"><span data-stu-id="8b868-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="8b868-207">Agrega paginación al método Index</span><span class="sxs-lookup"><span data-stu-id="8b868-207">Add paging to Index method</span></span>

<span data-ttu-id="8b868-208">En *StudentsController.cs*, reemplace el método `Index` por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="8b868-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="8b868-209">Este código agrega un parámetro de número de página, un parámetro de criterio de ordenación actual y un parámetro de filtro actual a la firma del método.</span><span class="sxs-lookup"><span data-stu-id="8b868-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="8b868-210">La primera vez que se muestra la página, o si el usuario no ha hecho clic en un vínculo de ordenación o paginación, todos los parámetros son nulos.</span><span class="sxs-lookup"><span data-stu-id="8b868-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="8b868-211">Si se hace clic en un vínculo de paginación, la variable de página contiene el número de página que se tiene que mostrar.</span><span class="sxs-lookup"><span data-stu-id="8b868-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="8b868-212">El elemento `ViewData`, denominado CurrentSort, proporciona la vista con el criterio de ordenación actual, que debe incluirse en los vínculos de paginación para mantener el criterio de ordenación durante la paginación.</span><span class="sxs-lookup"><span data-stu-id="8b868-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="8b868-213">El elemento `ViewData`, denominado CurrentFilter, proporciona la vista con la cadena de filtro actual.</span><span class="sxs-lookup"><span data-stu-id="8b868-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="8b868-214">Este valor debe incluirse en los vínculos de paginación para mantener la configuración de filtrado durante la paginación y debe restaurarse en el cuadro de texto cuando se vuelve a mostrar la página.</span><span class="sxs-lookup"><span data-stu-id="8b868-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="8b868-215">Si se cambia la cadena de búsqueda durante la paginación, la página debe restablecerse a 1, porque el nuevo filtro puede hacer que se muestren diferentes datos.</span><span class="sxs-lookup"><span data-stu-id="8b868-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="8b868-216">La cadena de búsqueda cambia cuando se escribe un valor en el cuadro de texto y se presiona el botón Submit.</span><span class="sxs-lookup"><span data-stu-id="8b868-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="8b868-217">En ese caso, el parámetro `searchString` no es NULL.</span><span class="sxs-lookup"><span data-stu-id="8b868-217">In that case, the `searchString` parameter isn't null.</span></span>

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

<span data-ttu-id="8b868-218">Al final del método `Index`, el método `PaginatedList.CreateAsync` convierte la consulta del alumno en una sola página de alumnos de un tipo de colección que admita la paginación.</span><span class="sxs-lookup"><span data-stu-id="8b868-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="8b868-219">Entonces, esa única página de alumnos pasa a la vista.</span><span class="sxs-lookup"><span data-stu-id="8b868-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="8b868-220">El método `PaginatedList.CreateAsync` toma un número de página.</span><span class="sxs-lookup"><span data-stu-id="8b868-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="8b868-221">Los dos signos de interrogación representan el operador de uso combinado de NULL.</span><span class="sxs-lookup"><span data-stu-id="8b868-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="8b868-222">El operador de uso combinado de NULL define un valor predeterminado para un tipo que acepta valores NULL; la expresión `(pageNumber ?? 1)` devuelve el valor de `pageNumber` si tiene algún valor o devuelve 1 si `pageNumber` es NULL.</span><span class="sxs-lookup"><span data-stu-id="8b868-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="8b868-223">Agrega vínculos de paginación</span><span class="sxs-lookup"><span data-stu-id="8b868-223">Add paging links</span></span>

<span data-ttu-id="8b868-224">En *Views/Students/Index.cshtml*, reemplace el código existente por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="8b868-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="8b868-225">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="8b868-225">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="8b868-226">La instrucción `@model` de la parte superior de la página especifica que ahora la vista obtiene un objeto `PaginatedList<T>` en lugar de un objeto `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="8b868-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="8b868-227">Los vínculos del encabezado de la columna usan la cadena de consulta para pasar la cadena de búsqueda actual al controlador, de modo que el usuario pueda ordenar los resultados del filtro:</span><span class="sxs-lookup"><span data-stu-id="8b868-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="8b868-228">Los botones de paginación se muestran mediante asistentes de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="8b868-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="8b868-229">Ejecute la aplicación y vaya a la página Students.</span><span class="sxs-lookup"><span data-stu-id="8b868-229">Run the app and go to the Students page.</span></span>

![Página de índice de Students con vínculos de paginación](sort-filter-page/_static/paging.png)

<span data-ttu-id="8b868-231">Haga clic en los vínculos de paginación en distintos criterios de ordenación para comprobar que la paginación funciona correctamente.</span><span class="sxs-lookup"><span data-stu-id="8b868-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="8b868-232">A continuación, escriba una cadena de búsqueda e intente llevar a cabo la paginación de nuevo, para comprobar que la paginación también funciona correctamente con filtrado y ordenación.</span><span class="sxs-lookup"><span data-stu-id="8b868-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="8b868-233">Crea una página About</span><span class="sxs-lookup"><span data-stu-id="8b868-233">Create an About page</span></span>

<span data-ttu-id="8b868-234">En la página **About** del sitio web de Contoso University, se muestran cuántos alumnos se han inscrito en cada fecha de inscripción.</span><span class="sxs-lookup"><span data-stu-id="8b868-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="8b868-235">Esto requiere realizar agrupaciones y cálculos sencillos en los grupos.</span><span class="sxs-lookup"><span data-stu-id="8b868-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="8b868-236">Para conseguirlo, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8b868-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="8b868-237">Cree una clase de modelo de vista para los datos que necesita pasar a la vista.</span><span class="sxs-lookup"><span data-stu-id="8b868-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="8b868-238">Cree el método About en el controlador Home.</span><span class="sxs-lookup"><span data-stu-id="8b868-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="8b868-239">Cree la vista About.</span><span class="sxs-lookup"><span data-stu-id="8b868-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="8b868-240">Creación del modelo de vista</span><span class="sxs-lookup"><span data-stu-id="8b868-240">Create the view model</span></span>

<span data-ttu-id="8b868-241">Cree una carpeta *SchoolViewModels* en la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="8b868-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="8b868-242">En la nueva carpeta, agregue un archivo de clase *EnrollmentDateGroup.cs* y reemplace el código de plantilla con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8b868-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="8b868-243">Modificación del controlador Home</span><span class="sxs-lookup"><span data-stu-id="8b868-243">Modify the Home Controller</span></span>

<span data-ttu-id="8b868-244">En *HomeController.cs*, agregue lo siguiente mediante instrucciones en la parte superior del archivo:</span><span class="sxs-lookup"><span data-stu-id="8b868-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="8b868-245">Agregue una variable de clase para el contexto de base de datos inmediatamente después de la llave de apertura para la clase y obtenga una instancia del contexto de ASP.NET Core DI:</span><span class="sxs-lookup"><span data-stu-id="8b868-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="8b868-246">Agregue un método `About` en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8b868-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="8b868-247">La instrucción LINQ agrupa las entidades de alumnos por fecha de inscripción, calcula la cantidad de entidades que se incluyen en cada grupo y almacena los resultados en una colección de objetos de modelo de la vista `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="8b868-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="8b868-248">Creación de la vista About</span><span class="sxs-lookup"><span data-stu-id="8b868-248">Create the About View</span></span>

<span data-ttu-id="8b868-249">Agregue un archivo *Views/Home/About.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8b868-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="8b868-250">Ejecute la aplicación y vaya a la página About.</span><span class="sxs-lookup"><span data-stu-id="8b868-250">Run the app and go to the About page.</span></span> <span data-ttu-id="8b868-251">En una tabla se muestra el número de alumnos para cada fecha de inscripción.</span><span class="sxs-lookup"><span data-stu-id="8b868-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="8b868-252">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="8b868-252">Get the code</span></span>

[<span data-ttu-id="8b868-253">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="8b868-253">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="8b868-254">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="8b868-254">Next steps</span></span>

<span data-ttu-id="8b868-255">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="8b868-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8b868-256">Agregado vínculos de ordenación de columnas</span><span class="sxs-lookup"><span data-stu-id="8b868-256">Added column sort links</span></span>
> * <span data-ttu-id="8b868-257">Agregado un cuadro de búsqueda</span><span class="sxs-lookup"><span data-stu-id="8b868-257">Added a Search box</span></span>
> * <span data-ttu-id="8b868-258">Agregado paginación al índice de Students</span><span class="sxs-lookup"><span data-stu-id="8b868-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="8b868-259">Agregado paginación al método Index</span><span class="sxs-lookup"><span data-stu-id="8b868-259">Added paging to Index method</span></span>
> * <span data-ttu-id="8b868-260">Agregado vínculos de paginación</span><span class="sxs-lookup"><span data-stu-id="8b868-260">Added paging links</span></span>
> * <span data-ttu-id="8b868-261">Creado una página About</span><span class="sxs-lookup"><span data-stu-id="8b868-261">Created an About page</span></span>

<span data-ttu-id="8b868-262">Pase al tutorial siguiente para obtener información sobre cómo controlar los cambios en el modelo de datos mediante migraciones.</span><span class="sxs-lookup"><span data-stu-id="8b868-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="8b868-263">Siguiente: Control de los cambios en el modelo de datos</span><span class="sxs-lookup"><span data-stu-id="8b868-263">Next: Handle data model changes</span></span>](migrations.md)
