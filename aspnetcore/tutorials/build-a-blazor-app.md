---
title: Creación de una aplicación de lista de tareas pendientes Blazor
author: guardrex
description: Cree una aplicación Blazor paso a paso.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 4d5bd977b52dd20ffb62519720106ef0a4560914
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508141"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="d8aba-103">Creación de una aplicación de lista de tareas pendientes Blazor</span><span class="sxs-lookup"><span data-stu-id="d8aba-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="d8aba-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d8aba-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d8aba-105">En este tutorial se muestra cómo crear y modificar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="d8aba-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="d8aba-106">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="d8aba-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d8aba-107">Crear un proyecto de aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="d8aba-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="d8aba-108">Modificar los componentes de Razor</span><span class="sxs-lookup"><span data-stu-id="d8aba-108">Modify Razor components</span></span>
> * <span data-ttu-id="d8aba-109">Usar el control de eventos y el enlace de datos en los componentes</span><span class="sxs-lookup"><span data-stu-id="d8aba-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="d8aba-110">Uso del enrutamiento en una aplicación Blazor</span><span class="sxs-lookup"><span data-stu-id="d8aba-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="d8aba-111">Al final de este tutorial, tendrá una aplicación de lista de tareas funcional.</span><span class="sxs-lookup"><span data-stu-id="d8aba-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8aba-112">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="d8aba-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="d8aba-113">Creación de una aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="d8aba-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="d8aba-114">Cree una nueva aplicación de Blazor denominada `TodoList` en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="d8aba-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="d8aba-115">El comando anterior crea una carpeta denominada `TodoList` para contener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d8aba-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="d8aba-116">La carpeta `TodoList` es la *carpeta raíz* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="d8aba-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="d8aba-117">Cambie los directorios a la carpeta `TodoList` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="d8aba-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="d8aba-118">Agregue un nuevo componente `Todo` Razor a la aplicación en la carpeta `Pages` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="d8aba-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="d8aba-119">Los nombres de archivo de componente Razor requieren poner en mayúscula la primera letra.</span><span class="sxs-lookup"><span data-stu-id="d8aba-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="d8aba-120">Abra la carpeta `Pages` y confirme que el nombre de archivo del componente `Todo` empieza con una letra mayúscula `T`.</span><span class="sxs-lookup"><span data-stu-id="d8aba-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="d8aba-121">El nombre de archivo debe ser `Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="d8aba-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="d8aba-122">En `Pages/Todo.razor`, proporcione el marcado inicial para el componente:</span><span class="sxs-lookup"><span data-stu-id="d8aba-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="d8aba-123">Agregue el componente `Todo` a la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="d8aba-123">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="d8aba-124">El componente `NavMenu` (`Shared/NavMenu.razor`) se usa en el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d8aba-124">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="d8aba-125">Los diseños son componentes que le permiten impedir la duplicación de contenido en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d8aba-125">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="d8aba-126">Agregue un elemento `<NavLink>` al componente `Todo`; para ello, incorpore el siguiente marcado de elementos de lista debajo de los elementos de lista existentes en el archivo `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="d8aba-126">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="d8aba-127">Compile y ejecute la aplicación mediante el comando `dotnet run` en el shell de comandos desde la carpeta `TodoList`.</span><span class="sxs-lookup"><span data-stu-id="d8aba-127">Build and run the app by executing the `dotnet run` command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="d8aba-128">Visite la nueva página Todo en `https://localhost:5001/todo` para confirmar que el vínculo al componente `Todo` funcione.</span><span class="sxs-lookup"><span data-stu-id="d8aba-128">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="d8aba-129">Agregue un archivo `TodoItem.cs` a la raíz del proyecto (carpeta `TodoList`) para retener una clase que represente un elemento de la lista de tareas.</span><span class="sxs-lookup"><span data-stu-id="d8aba-129">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="d8aba-130">Use el siguiente código de C# para la clase `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="d8aba-130">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="d8aba-131">Vuelva al componente `Todo` (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="d8aba-131">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="d8aba-132">Agregue un campo a los elementos de tareas pendientes en un bloque `@code`.</span><span class="sxs-lookup"><span data-stu-id="d8aba-132">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="d8aba-133">El componente `Todo` utiliza este campo para mantener el estado de la lista de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="d8aba-133">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="d8aba-134">Agregue el marcado de la lista no ordenada y un bucle `foreach` para que cada elemento de la lista se represente en un elemento de la lista de tareas pendientes (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="d8aba-134">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="d8aba-135">Para agregar elementos de tareas pendientes a la lista, la aplicación requiere elementos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="d8aba-135">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="d8aba-136">Agregue una entrada de texto (`<input>`) y un botón (`<button>`) debajo de la lista no ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="d8aba-136">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="d8aba-137">Detenga la aplicación en ejecución en el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="d8aba-137">Stop the running app in the command shell.</span></span> <span data-ttu-id="d8aba-138">Muchos shells de comandos aceptan el comando de teclado <kbd>Ctrl</kbd>+<kbd>c</kbd> para detener una aplicación.</span><span class="sxs-lookup"><span data-stu-id="d8aba-138">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span> <span data-ttu-id="d8aba-139">Vuelva a compilar y ejecute la aplicación con el comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d8aba-139">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="d8aba-140">Al seleccionar el botón **`Add todo`** , no ocurre nada porque no hay ningún controlador de eventos conectado al botón.</span><span class="sxs-lookup"><span data-stu-id="d8aba-140">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="d8aba-141">Agregue un método `AddTodo` al componente `Todo` y regístrelo para seleccionar los botones mediante el atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="d8aba-141">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="d8aba-142">El método `AddTodo` de C# se llama cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="d8aba-142">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="d8aba-143">Para obtener el título del nuevo elemento de tarea pendiente, agregue un campo de cadena `newTodo` en la parte superior del bloque `@code` y enlácelo al valor de la entrada de texto mediante el atributo `bind` en el elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="d8aba-143">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="d8aba-144">Actualice el método `AddTodo` para agregar el `TodoItem` con el título especificado a la lista.</span><span class="sxs-lookup"><span data-stu-id="d8aba-144">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="d8aba-145">Borre el valor de la entrada de texto mediante el establecimiento de `newTodo` en una cadena vacía:</span><span class="sxs-lookup"><span data-stu-id="d8aba-145">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="d8aba-146">Detenga la aplicación en ejecución en el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="d8aba-146">Stop the running app in the command shell.</span></span> <span data-ttu-id="d8aba-147">Vuelva a compilar y ejecute la aplicación con el comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d8aba-147">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="d8aba-148">Agregue algunos elementos de tareas pendientes a la lista de tareas pendientes para probar el nuevo código.</span><span class="sxs-lookup"><span data-stu-id="d8aba-148">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="d8aba-149">Se puede hacer que el texto de título de cada elemento de tarea pendiente sea editable y una casilla puede ayudar al usuario a realizar un seguimiento de los elementos completados.</span><span class="sxs-lookup"><span data-stu-id="d8aba-149">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="d8aba-150">Agregue una entrada de casilla a cada elemento de tarea pendiente y enlace su valor a la propiedad `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="d8aba-150">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="d8aba-151">Cambie `@todo.Title` a un elemento `<input>` enlazado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="d8aba-151">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="d8aba-152">Para comprobar que estos valores están enlazados, actualice el encabezado `<h3>` para mostrar un recuento del número de elementos de la lista de tareas pendientes que no se han completado (`IsDone` es `false`).</span><span class="sxs-lookup"><span data-stu-id="d8aba-152">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="d8aba-153">El componente `Todo` completado (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="d8aba-153">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="d8aba-154">Detenga la aplicación en ejecución en el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="d8aba-154">Stop the running app in the command shell.</span></span> <span data-ttu-id="d8aba-155">Vuelva a compilar y ejecute la aplicación con el comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d8aba-155">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="d8aba-156">Agregue elementos de tarea pendiente para probar el nuevo código.</span><span class="sxs-lookup"><span data-stu-id="d8aba-156">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d8aba-157">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="d8aba-157">Next steps</span></span>

<span data-ttu-id="d8aba-158">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="d8aba-158">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d8aba-159">Crear un proyecto de aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="d8aba-159">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="d8aba-160">Modificar los componentes de Razor</span><span class="sxs-lookup"><span data-stu-id="d8aba-160">Modify Razor components</span></span>
> * <span data-ttu-id="d8aba-161">Usar el control de eventos y el enlace de datos en los componentes</span><span class="sxs-lookup"><span data-stu-id="d8aba-161">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="d8aba-162">Uso del enrutamiento en una aplicación Blazor</span><span class="sxs-lookup"><span data-stu-id="d8aba-162">Use routing in a Blazor app</span></span>

<span data-ttu-id="d8aba-163">Más información sobre las herramientas de ASP.NET Core Blazor:</span><span class="sxs-lookup"><span data-stu-id="d8aba-163">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
