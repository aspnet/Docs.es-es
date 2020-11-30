---
title: Creación de una aplicación de lista de tareas pendientes Blazor
author: guardrex
description: Cree una aplicación Blazor paso a paso.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
ms.openlocfilehash: a32655b8afedb73ad436f023d2f821b6920c2edd
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870443"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="bc6fb-103">Creación de una aplicación de lista de tareas pendientes Blazor</span><span class="sxs-lookup"><span data-stu-id="bc6fb-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="bc6fb-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bc6fb-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bc6fb-105">En este tutorial se muestra cómo crear y modificar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="bc6fb-106">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bc6fb-107">Crear un proyecto de aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="bc6fb-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="bc6fb-108">Modificar los componentes de Razor</span><span class="sxs-lookup"><span data-stu-id="bc6fb-108">Modify Razor components</span></span>
> * <span data-ttu-id="bc6fb-109">Usar el control de eventos y el enlace de datos en los componentes</span><span class="sxs-lookup"><span data-stu-id="bc6fb-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="bc6fb-110">Uso del enrutamiento en una aplicación Blazor</span><span class="sxs-lookup"><span data-stu-id="bc6fb-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="bc6fb-111">Al final de este tutorial, tendrá una aplicación de lista de tareas funcional.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bc6fb-112">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="bc6fb-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="bc6fb-113">Creación de una aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="bc6fb-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="bc6fb-114">Cree una nueva aplicación de Blazor denominada `TodoList` en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="bc6fb-115">El comando anterior crea una carpeta denominada `TodoList` para contener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="bc6fb-116">La carpeta `TodoList` es la *carpeta raíz* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="bc6fb-117">Cambie los directorios a la carpeta `TodoList` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="bc6fb-118">Agregue un nuevo componente `Todo` Razor a la aplicación en la carpeta `Pages` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="bc6fb-119">Los nombres de archivo de componente Razor requieren poner en mayúscula la primera letra.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="bc6fb-120">Abra la carpeta `Pages` y confirme que el nombre de archivo del componente `Todo` empieza con una letra mayúscula `T`.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="bc6fb-121">El nombre de archivo debe ser `Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="bc6fb-122">En `Pages/Todo.razor`, proporcione el marcado inicial para el componente:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   <span data-ttu-id="bc6fb-123">Guarde el archivo `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-123">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="bc6fb-124">Agregue el componente `Todo` a la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-124">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="bc6fb-125">El componente `NavMenu` (`Shared/NavMenu.razor`) se usa en el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-125">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="bc6fb-126">Los diseños son componentes que le permiten impedir la duplicación de contenido en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-126">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="bc6fb-127">Agregue un elemento `<NavLink>` al componente `Todo`; para ello, incorpore el siguiente marcado de elementos de lista debajo de los elementos de lista existentes en el archivo `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-127">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   <span data-ttu-id="bc6fb-128">Guarde el archivo `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-128">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="bc6fb-129">Compile y ejecute la aplicación mediante el comando [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) en el shell de comandos desde la carpeta `TodoList`.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-129">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="bc6fb-130">Visite la nueva página Todo en `https://localhost:5001/todo` para confirmar que el vínculo de navegación de la barra lateral al componente `Todo` funcione.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-130">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the sidebar navigation link to the `Todo` component works.</span></span>

1. <span data-ttu-id="bc6fb-131">Agregue un archivo `TodoItem.cs` a la raíz del proyecto (carpeta `TodoList`) para retener una clase que represente un elemento de la lista de tareas.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-131">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="bc6fb-132">Use el siguiente código de C# para la clase `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-132">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="bc6fb-133">Vuelva al componente `Todo` (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="bc6fb-133">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="bc6fb-134">Agregue un campo a los elementos de tareas pendientes en un bloque `@code`.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-134">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="bc6fb-135">El componente `Todo` utiliza este campo para mantener el estado de la lista de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-135">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="bc6fb-136">Agregue el marcado de la lista no ordenada y un bucle `foreach` para que cada elemento de la lista se represente en un elemento de la lista de tareas pendientes (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="bc6fb-136">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="bc6fb-137">Para agregar elementos de tareas pendientes a la lista, la aplicación requiere elementos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-137">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="bc6fb-138">Agregue una entrada de texto (`<input>`) y un botón (`<button>`) debajo de la lista no ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="bc6fb-138">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="bc6fb-139">Guarde el archivo `TodoItem.cs` y el archivo `Pages/Todo.razor` actualizado.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-139">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="bc6fb-140">En el shell de comandos, la aplicación se vuelve a compilar automáticamente cuando se guardan los archivos.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-140">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="bc6fb-141">El explorador pierde temporalmente su conexión con la aplicación y, a continuación, vuelve a cargar la página cuando se restablece la conexión.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-141">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="bc6fb-142">Al seleccionar el botón **`Add todo`** , no ocurre nada porque no hay ningún controlador de eventos conectado al botón.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-142">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="bc6fb-143">Agregue un método `AddTodo` al componente `Todo` y regístrelo para seleccionar los botones mediante el atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-143">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="bc6fb-144">El método `AddTodo` de C# se llama cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-144">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="bc6fb-145">Para obtener el título del nuevo elemento de tarea pendiente, agregue un campo de cadena `newTodo` en la parte superior del bloque `@code` y enlácelo al valor de la entrada de texto mediante el atributo `bind` en el elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-145">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="bc6fb-146">Actualice el método `AddTodo` para agregar el `TodoItem` con el título especificado a la lista.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-146">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="bc6fb-147">Borre el valor de la entrada de texto mediante el establecimiento de `newTodo` en una cadena vacía:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-147">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="bc6fb-148">Guarde el archivo `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-148">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="bc6fb-149">La aplicación se vuelve a compilar automáticamente en el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-149">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="bc6fb-150">La página vuelve a cargarse en el explorador después de que el explorador vuelve a conectarse a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-150">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="bc6fb-151">Se puede hacer que el texto de título de cada elemento de tarea pendiente sea editable y una casilla puede ayudar al usuario a realizar un seguimiento de los elementos completados.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-151">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="bc6fb-152">Agregue una entrada de casilla a cada elemento de tarea pendiente y enlace su valor a la propiedad `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-152">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="bc6fb-153">Cambie `@todo.Title` a un elemento `<input>` enlazado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-153">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="bc6fb-154">Para comprobar que estos valores están enlazados, actualice el encabezado `<h3>` para mostrar un recuento del número de elementos de la lista de tareas pendientes que no se han completado (`IsDone` es `false`).</span><span class="sxs-lookup"><span data-stu-id="bc6fb-154">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="bc6fb-155">El componente `Todo` completado (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="bc6fb-155">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="bc6fb-156">Guarde el archivo `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-156">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="bc6fb-157">La aplicación se vuelve a compilar automáticamente en el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-157">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="bc6fb-158">La página vuelve a cargarse en el explorador después de que el explorador vuelve a conectarse a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-158">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="bc6fb-159">Agregue elementos de tarea pendiente para probar el nuevo código.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-159">Add todo items to test the new code.</span></span>

1. <span data-ttu-id="bc6fb-160">Cuando termine, cierre la aplicación en el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-160">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="bc6fb-161">Muchos shells de comandos aceptan el comando de teclado <kbd>Ctrl</kbd>+<kbd>c</kbd> para detener una aplicación.</span><span class="sxs-lookup"><span data-stu-id="bc6fb-161">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bc6fb-162">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="bc6fb-162">Next steps</span></span>

<span data-ttu-id="bc6fb-163">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-163">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bc6fb-164">Crear un proyecto de aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="bc6fb-164">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="bc6fb-165">Modificar los componentes de Razor</span><span class="sxs-lookup"><span data-stu-id="bc6fb-165">Modify Razor components</span></span>
> * <span data-ttu-id="bc6fb-166">Usar el control de eventos y el enlace de datos en los componentes</span><span class="sxs-lookup"><span data-stu-id="bc6fb-166">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="bc6fb-167">Uso del enrutamiento en una aplicación Blazor</span><span class="sxs-lookup"><span data-stu-id="bc6fb-167">Use routing in a Blazor app</span></span>

<span data-ttu-id="bc6fb-168">Más información sobre las herramientas de ASP.NET Core Blazor:</span><span class="sxs-lookup"><span data-stu-id="bc6fb-168">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
