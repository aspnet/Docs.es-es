---
title: Creación de una aplicación de lista de tareas pendientes Blazor
author: guardrex
description: Cree una aplicación Blazor paso a paso.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 174a8e561701bb3ebd68ed05e42dfc3d70a9b450
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176228"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="f23d7-103">Creación de una aplicación de lista de tareas pendientes Blazor</span><span class="sxs-lookup"><span data-stu-id="f23d7-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="f23d7-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f23d7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f23d7-105">En este tutorial se muestra cómo crear y modificar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="f23d7-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="f23d7-106">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="f23d7-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f23d7-107">Crear un proyecto de aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="f23d7-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="f23d7-108">Modificar los componentes de Razor</span><span class="sxs-lookup"><span data-stu-id="f23d7-108">Modify Razor components</span></span>
> * <span data-ttu-id="f23d7-109">Usar el control de eventos y el enlace de datos en los componentes</span><span class="sxs-lookup"><span data-stu-id="f23d7-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="f23d7-110">Usar la inserción de dependencias (DI) y el enrutamiento en una aplicación Blazor</span><span class="sxs-lookup"><span data-stu-id="f23d7-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="f23d7-111">Al final de este tutorial, tendrá una aplicación de lista de tareas funcional.</span><span class="sxs-lookup"><span data-stu-id="f23d7-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

1. <span data-ttu-id="f23d7-112">Cree una nueva aplicación de Blazor denominada `TodoList` en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="f23d7-112">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="f23d7-113">El comando anterior crea una carpeta denominada `TodoList` para contener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f23d7-113">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="f23d7-114">Cambie los directorios a la carpeta `TodoList` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="f23d7-114">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="f23d7-115">Agregue un nuevo componente `Todo` Razor a la aplicación en la carpeta `Pages` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="f23d7-115">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="f23d7-116">Puesto que la primera letra de los nombres de los archivos del componente Razor debe estar en mayúscula, asegúrese de que el nombre de archivo del componente `Todo` comience por una letra `T` mayúscula.</span><span class="sxs-lookup"><span data-stu-id="f23d7-116">Razor component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="f23d7-117">En `Pages/Todo.razor`, proporcione el marcado inicial para el componente:</span><span class="sxs-lookup"><span data-stu-id="f23d7-117">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="f23d7-118">Agregue el componente `Todo` a la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="f23d7-118">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="f23d7-119">El componente `NavMenu` (`Shared/NavMenu.razor`) se usa en el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f23d7-119">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="f23d7-120">Los diseños son componentes que le permiten impedir la duplicación de contenido en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f23d7-120">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="f23d7-121">Agregue un elemento `<NavLink>` al componente `Todo`; para ello, incorpore el siguiente marcado de elementos de lista debajo de los elementos de lista existentes en el archivo `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="f23d7-121">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="f23d7-122">Recompile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f23d7-122">Rebuild and run the app.</span></span> <span data-ttu-id="f23d7-123">Visite la nueva página Todo para confirmar que el vínculo al componente `Todo` funcione.</span><span class="sxs-lookup"><span data-stu-id="f23d7-123">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="f23d7-124">Agregue un archivo `TodoItem.cs` a la raíz del proyecto para contener una clase que represente un elemento de la lista de tareas.</span><span class="sxs-lookup"><span data-stu-id="f23d7-124">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="f23d7-125">Use el siguiente código de C# para la clase `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="f23d7-125">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="f23d7-126">Vuelva al componente `Todo` (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="f23d7-126">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="f23d7-127">Agregue un campo a los elementos de tareas pendientes en un bloque `@code`.</span><span class="sxs-lookup"><span data-stu-id="f23d7-127">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="f23d7-128">El componente `Todo` utiliza este campo para mantener el estado de la lista de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="f23d7-128">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="f23d7-129">Agregue el marcado de la lista no ordenada y un bucle `foreach` para que cada elemento de la lista se represente en un elemento de la lista de tareas pendientes (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="f23d7-129">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="f23d7-130">Para agregar elementos de tareas pendientes a la lista, la aplicación requiere elementos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="f23d7-130">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="f23d7-131">Agregue una entrada de texto (`<input>`) y un botón (`<button>`) debajo de la lista no ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="f23d7-131">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="f23d7-132">Recompile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f23d7-132">Rebuild and run the app.</span></span> <span data-ttu-id="f23d7-133">Al seleccionar el botón **`Add todo`** , no ocurre nada porque no hay ningún controlador de eventos conectado al botón.</span><span class="sxs-lookup"><span data-stu-id="f23d7-133">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="f23d7-134">Agregue un método `AddTodo` al componente `Todo` y regístrelo para seleccionar los botones mediante el atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="f23d7-134">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="f23d7-135">El método `AddTodo` de C# se llama cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="f23d7-135">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="f23d7-136">Para obtener el título del nuevo elemento de tarea pendiente, agregue un campo de cadena `newTodo` en la parte superior del bloque `@code` y enlácelo al valor de la entrada de texto mediante el atributo `bind` en el elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="f23d7-136">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="f23d7-137">Actualice el método `AddTodo` para agregar el `TodoItem` con el título especificado a la lista.</span><span class="sxs-lookup"><span data-stu-id="f23d7-137">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="f23d7-138">Borre el valor de la entrada de texto mediante el establecimiento de `newTodo` en una cadena vacía:</span><span class="sxs-lookup"><span data-stu-id="f23d7-138">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="f23d7-139">Recompile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f23d7-139">Rebuild and run the app.</span></span> <span data-ttu-id="f23d7-140">Agregue algunos elementos de tareas pendientes a la lista de tareas pendientes para probar el nuevo código.</span><span class="sxs-lookup"><span data-stu-id="f23d7-140">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="f23d7-141">Se puede hacer que el texto de título de cada elemento de tarea pendiente sea editable y una casilla puede ayudar al usuario a realizar un seguimiento de los elementos completados.</span><span class="sxs-lookup"><span data-stu-id="f23d7-141">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="f23d7-142">Agregue una entrada de casilla a cada elemento de tarea pendiente y enlace su valor a la propiedad `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="f23d7-142">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="f23d7-143">Cambie `@todo.Title` a un elemento `<input>` enlazado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="f23d7-143">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="f23d7-144">Para comprobar que estos valores están enlazados, actualice el encabezado `<h3>` para mostrar un recuento del número de elementos de la lista de tareas pendientes que no se han completado (`IsDone` es `false`).</span><span class="sxs-lookup"><span data-stu-id="f23d7-144">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="f23d7-145">El componente `Todo` completado (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="f23d7-145">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="f23d7-146">Recompile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f23d7-146">Rebuild and run the app.</span></span> <span data-ttu-id="f23d7-147">Agregue elementos de tarea pendiente para probar el nuevo código.</span><span class="sxs-lookup"><span data-stu-id="f23d7-147">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f23d7-148">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="f23d7-148">Next steps</span></span>

<span data-ttu-id="f23d7-149">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="f23d7-149">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f23d7-150">Crear un proyecto de aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="f23d7-150">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="f23d7-151">Modificar los componentes de Razor</span><span class="sxs-lookup"><span data-stu-id="f23d7-151">Modify Razor components</span></span>
> * <span data-ttu-id="f23d7-152">Usar el control de eventos y el enlace de datos en los componentes</span><span class="sxs-lookup"><span data-stu-id="f23d7-152">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="f23d7-153">Usar la inserción de dependencias (DI) y el enrutamiento en una aplicación Blazor</span><span class="sxs-lookup"><span data-stu-id="f23d7-153">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="f23d7-154">Más información sobre las herramientas de ASP.NET Core Blazor:</span><span class="sxs-lookup"><span data-stu-id="f23d7-154">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
