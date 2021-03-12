---
title: Creación de una aplicación de lista de tareas pendientes Blazor
author: guardrex
description: Cree una aplicación Blazor paso a paso.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2021
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
ms.openlocfilehash: 260d921316d6fadecbd42db11048593b19a5ddee
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394439"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="a699d-103">Creación de una aplicación de lista de tareas pendientes Blazor</span><span class="sxs-lookup"><span data-stu-id="a699d-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="a699d-104">En este tutorial se muestra cómo crear y modificar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="a699d-104">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="a699d-105">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="a699d-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a699d-106">Crear un proyecto de aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="a699d-106">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a699d-107">Modificar los componentes de Razor</span><span class="sxs-lookup"><span data-stu-id="a699d-107">Modify Razor components</span></span>
> * <span data-ttu-id="a699d-108">Usar el control de eventos y el enlace de datos en los componentes</span><span class="sxs-lookup"><span data-stu-id="a699d-108">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a699d-109">Uso del enrutamiento en una aplicación Blazor</span><span class="sxs-lookup"><span data-stu-id="a699d-109">Use routing in a Blazor app</span></span>

<span data-ttu-id="a699d-110">Al final de este tutorial, tendrá una aplicación de lista de tareas funcional.</span><span class="sxs-lookup"><span data-stu-id="a699d-110">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a699d-111">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="a699d-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a><span data-ttu-id="a699d-112">Creación de una aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="a699d-112">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="a699d-113">Cree una nueva aplicación de Blazor denominada `TodoList` en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="a699d-113">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="a699d-114">El comando anterior crea una carpeta denominada `TodoList` con la opción `-o|--output` para contener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a699d-114">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="a699d-115">La carpeta `TodoList` es la *carpeta raíz* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a699d-115">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="a699d-116">Cambie los directorios a la carpeta `TodoList` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="a699d-116">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="a699d-117">Agregue un nuevo componente `Todo` Razor a la aplicación con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="a699d-117">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="a699d-118">La opción `-n|--name` del comando anterior especifica el nombre del nuevo componente Razor.</span><span class="sxs-lookup"><span data-stu-id="a699d-118">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="a699d-119">El nuevo componente se crea en la carpeta `Pages` del proyecto con la opción `-o|--output`.</span><span class="sxs-lookup"><span data-stu-id="a699d-119">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="a699d-120">Los nombres de archivo de componente Razor requieren poner en mayúscula la primera letra.</span><span class="sxs-lookup"><span data-stu-id="a699d-120">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="a699d-121">Abra la carpeta `Pages` y confirme que el nombre de archivo del componente `Todo` empieza con una letra mayúscula `T`.</span><span class="sxs-lookup"><span data-stu-id="a699d-121">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="a699d-122">El nombre de archivo debe ser `Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a699d-122">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="a699d-123">Abra el componente `Todo` en cualquier editor de archivos y agregue una directiva `@page` Razor a la parte superior del archivo con una dirección URL relativa de `/todo`.</span><span class="sxs-lookup"><span data-stu-id="a699d-123">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="a699d-124">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="a699d-124">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   <span data-ttu-id="a699d-125">Guarde el archivo `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a699d-125">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="a699d-126">Agregue el componente `Todo` a la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="a699d-126">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="a699d-127">El componente `NavMenu` se usa en el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a699d-127">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="a699d-128">Los diseños son componentes que le permiten impedir la duplicación de contenido en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="a699d-128">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="a699d-129">El componente `NavLink` proporciona una indicación en la interfaz de usuario de la aplicación cuando la aplicación carga la dirección URL del componente.</span><span class="sxs-lookup"><span data-stu-id="a699d-129">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="a699d-130">En la lista sin ordenar (`<ul>...</ul>`) del componente `NavMenu`, agregue los siguientes elementos de lista (`<li>...</li>`) y el componente `NavLink` para el componente `Todo`.</span><span class="sxs-lookup"><span data-stu-id="a699d-130">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="a699d-131">En `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="a699d-131">In `Shared/NavMenu.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   <span data-ttu-id="a699d-132">Guarde el archivo `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="a699d-132">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="a699d-133">Compile y ejecute la aplicación mediante el comando [`dotnet watch run`](xref:tutorials/dotnet-watch) en el shell de comandos desde la carpeta `TodoList`.</span><span class="sxs-lookup"><span data-stu-id="a699d-133">Build and run the app by executing the [`dotnet watch run`](xref:tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="a699d-134">Cuando la aplicación se ejecute, visite la nueva página de tareas pendientes seleccionando el vínculo **`Todo`** en la barra de navegación de la aplicación, que carga la página en `/todo`.</span><span class="sxs-lookup"><span data-stu-id="a699d-134">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="a699d-135">Deje la aplicación ejecutando el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="a699d-135">Leave the app running the command shell.</span></span> <span data-ttu-id="a699d-136">Cada vez que se guarda un archivo, la aplicación se vuelve a compilar automáticamente.</span><span class="sxs-lookup"><span data-stu-id="a699d-136">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="a699d-137">El explorador pierde temporalmente su conexión con la aplicación durante la compilación y el reinicio.</span><span class="sxs-lookup"><span data-stu-id="a699d-137">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="a699d-138">La página del explorador se recarga automáticamente cuando se restablece la conexión.</span><span class="sxs-lookup"><span data-stu-id="a699d-138">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="a699d-139">Agregue un archivo `TodoItem.cs` a la raíz del proyecto (carpeta `TodoList`) para retener una clase que represente un elemento de la lista de tareas.</span><span class="sxs-lookup"><span data-stu-id="a699d-139">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="a699d-140">Use el siguiente C# de código para la clase `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="a699d-140">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="a699d-141">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="a699d-141">`TodoItem.cs`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > <span data-ttu-id="a699d-142">Si usa Visual Studio para crear el archivo `TodoItem.cs` y la clase `TodoItem`, use cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="a699d-142">If using Visual Studio to create the `TodoItem.cs` file and `TodoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="a699d-143">Quite el espacio de nombres que genera Visual Studio para la clase.</span><span class="sxs-lookup"><span data-stu-id="a699d-143">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="a699d-144">Use el botón **Copiar** del bloque de código anterior y reemplace todo el contenido del archivo que genera Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a699d-144">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="a699d-145">Vuelva al componente `Todo` y realice las tareas siguientes:</span><span class="sxs-lookup"><span data-stu-id="a699d-145">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="a699d-146">Agregue un campo a los elementos de tareas pendientes en un bloque `@code`.</span><span class="sxs-lookup"><span data-stu-id="a699d-146">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="a699d-147">El componente `Todo` utiliza este campo para mantener el estado de la lista de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="a699d-147">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="a699d-148">Agregue el marcado de la lista no ordenada y un bucle `foreach` para que cada elemento de la lista se represente en un elemento de la lista de tareas pendientes (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="a699d-148">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="a699d-149">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="a699d-149">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. <span data-ttu-id="a699d-150">Para agregar elementos de tareas pendientes a la lista, la aplicación requiere elementos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="a699d-150">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="a699d-151">Agregue una entrada de texto (`<input>`) y un botón (`<button>`) debajo de la lista no ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="a699d-151">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. <span data-ttu-id="a699d-152">Guarde el archivo `TodoItem.cs` y el archivo `Pages/Todo.razor` actualizado.</span><span class="sxs-lookup"><span data-stu-id="a699d-152">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="a699d-153">En el shell de comandos, la aplicación se vuelve a compilar automáticamente cuando se guardan los archivos.</span><span class="sxs-lookup"><span data-stu-id="a699d-153">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="a699d-154">El explorador pierde temporalmente su conexión con la aplicación y, a continuación, vuelve a cargar la página cuando se restablece la conexión.</span><span class="sxs-lookup"><span data-stu-id="a699d-154">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="a699d-155">Al seleccionar el botón **`Add todo`** , no ocurre nada porque no hay ningún controlador de eventos conectado al botón.</span><span class="sxs-lookup"><span data-stu-id="a699d-155">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="a699d-156">Agregue un método `AddTodo` al componente `Todo` y registre el método para el botón mediante el atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="a699d-156">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="a699d-157">El método `AddTodo` de C# se llama cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="a699d-157">The `AddTodo` C# method is called when the button is selected:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. <span data-ttu-id="a699d-158">Para obtener el título del nuevo elemento de la lista de tareas pendientes, agregue un campo de cadena `newTodo` en la parte superior del bloque `@code`:</span><span class="sxs-lookup"><span data-stu-id="a699d-158">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   <span data-ttu-id="a699d-159">Modifique el elemento `<input>` de texto para enlazar `newTodo` con el atributo `@bind`:</span><span class="sxs-lookup"><span data-stu-id="a699d-159">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="a699d-160">Actualice el método `AddTodo` para agregar el `TodoItem` con el título especificado a la lista.</span><span class="sxs-lookup"><span data-stu-id="a699d-160">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="a699d-161">Borre el valor de la entrada de texto mediante el establecimiento de `newTodo` en una cadena vacía:</span><span class="sxs-lookup"><span data-stu-id="a699d-161">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. <span data-ttu-id="a699d-162">Guarde el archivo `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a699d-162">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="a699d-163">La aplicación se vuelve a compilar automáticamente en el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="a699d-163">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a699d-164">La página vuelve a cargarse en el explorador después de que el explorador vuelve a conectarse a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a699d-164">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a699d-165">Se puede hacer que el texto de título de cada elemento de tarea pendiente sea editable y una casilla puede ayudar al usuario a realizar un seguimiento de los elementos completados.</span><span class="sxs-lookup"><span data-stu-id="a699d-165">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="a699d-166">Agregue una entrada de casilla a cada elemento de tarea pendiente y enlace su valor a la propiedad `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="a699d-166">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="a699d-167">Cambie `@todo.Title` a un elemento `<input>` enlazado a `todo.Title` con `@bind`:</span><span class="sxs-lookup"><span data-stu-id="a699d-167">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. <span data-ttu-id="a699d-168">Actualice el encabezado `<h3>` para mostrar un recuento del número de elementos de la lista de tareas que no se han completado (`IsDone` es `false`).</span><span class="sxs-lookup"><span data-stu-id="a699d-168">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="a699d-169">El componente `Todo` completado (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="a699d-169">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. <span data-ttu-id="a699d-170">Guarde el archivo `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="a699d-170">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="a699d-171">La aplicación se vuelve a compilar automáticamente en el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="a699d-171">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a699d-172">La página vuelve a cargarse en el explorador después de que el explorador vuelve a conectarse a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a699d-172">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a699d-173">Agregue los elementos, edite los elementos y marque los elementos de la lista de tareas realizados para probar el componente.</span><span class="sxs-lookup"><span data-stu-id="a699d-173">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="a699d-174">Cuando termine, cierre la aplicación en el shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="a699d-174">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="a699d-175">Muchos shells de comandos aceptan el comando de teclado <kbd>Ctrl</kbd>+<kbd>c</kbd> para detener una aplicación.</span><span class="sxs-lookup"><span data-stu-id="a699d-175">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a699d-176">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="a699d-176">Next steps</span></span>

<span data-ttu-id="a699d-177">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="a699d-177">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a699d-178">Crear un proyecto de aplicación Blazor de lista de tareas</span><span class="sxs-lookup"><span data-stu-id="a699d-178">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a699d-179">Modificar los componentes de Razor</span><span class="sxs-lookup"><span data-stu-id="a699d-179">Modify Razor components</span></span>
> * <span data-ttu-id="a699d-180">Usar el control de eventos y el enlace de datos en los componentes</span><span class="sxs-lookup"><span data-stu-id="a699d-180">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a699d-181">Uso del enrutamiento en una aplicación Blazor</span><span class="sxs-lookup"><span data-stu-id="a699d-181">Use routing in a Blazor app</span></span>

<span data-ttu-id="a699d-182">Más información sobre las herramientas de ASP.NET Core Blazor:</span><span class="sxs-lookup"><span data-stu-id="a699d-182">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
