---
title: Creación de una aplicación de lista de tareas pendientes Blazor
author: guardrex
description: Cree una aplicación Blazor paso a paso.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2020
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
ms.openlocfilehash: 1efcd167d9a45b2def271b239c9b360749d72791
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570190"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>Creación de una aplicación de lista de tareas pendientes Blazor

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

En este tutorial se muestra cómo crear y modificar una aplicación Blazor. Aprenderá a:

> [!div class="checklist"]
> * Crear un proyecto de aplicación Blazor de lista de tareas
> * Modificar los componentes de Razor
> * Usar el control de eventos y el enlace de datos en los componentes
> * Uso del enrutamiento en una aplicación Blazor

Al final de este tutorial, tendrá una aplicación de lista de tareas funcional.

## <a name="prerequisites"></a>Requisitos previos

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a>Creación de una aplicación Blazor de lista de tareas

1. Cree una nueva aplicación de Blazor denominada `TodoList` en un shell de comandos:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   El comando anterior crea una carpeta denominada `TodoList` para contener la aplicación. La carpeta `TodoList` es la *carpeta raíz* del proyecto. Cambie los directorios a la carpeta `TodoList` con el siguiente comando:

   ```dotnetcli
   cd TodoList
   ```

1. Agregue un nuevo componente `Todo` Razor a la aplicación en la carpeta `Pages` con el siguiente comando:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Los nombres de archivo de componente Razor requieren poner en mayúscula la primera letra. Abra la carpeta `Pages` y confirme que el nombre de archivo del componente `Todo` empieza con una letra mayúscula `T`. El nombre de archivo debe ser `Todo.razor`.

1. En `Pages/Todo.razor`, proporcione el marcado inicial para el componente:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Agregue el componente `Todo` a la barra de navegación.

   El componente `NavMenu` (`Shared/NavMenu.razor`) se usa en el diseño de la aplicación. Los diseños son componentes que le permiten impedir la duplicación de contenido en la aplicación.

   Agregue un elemento `<NavLink>` al componente `Todo`; para ello, incorpore el siguiente marcado de elementos de lista debajo de los elementos de lista existentes en el archivo `Shared/NavMenu.razor`:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Compile y ejecute la aplicación mediante el comando `dotnet run` en el shell de comandos desde la carpeta `TodoList`. Visite la nueva página Todo en `https://localhost:5001/todo` para confirmar que el vínculo al componente `Todo` funcione.

1. Agregue un archivo `TodoItem.cs` a la raíz del proyecto (carpeta `TodoList`) para retener una clase que represente un elemento de la lista de tareas. Use el siguiente código de C# para la clase `TodoItem`:

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. Vuelva al componente `Todo` (`Pages/Todo.razor`):

   * Agregue un campo a los elementos de tareas pendientes en un bloque `@code`. El componente `Todo` utiliza este campo para mantener el estado de la lista de tareas pendientes.
   * Agregue el marcado de la lista no ordenada y un bucle `foreach` para que cada elemento de la lista se represente en un elemento de la lista de tareas pendientes (`<li>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. Para agregar elementos de tareas pendientes a la lista, la aplicación requiere elementos de la interfaz de usuario. Agregue una entrada de texto (`<input>`) y un botón (`<button>`) debajo de la lista no ordenada (`<ul>...</ul>`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. Detenga la aplicación en ejecución en el shell de comandos. Muchos shells de comandos aceptan el comando de teclado <kbd>Ctrl</kbd>+<kbd>c</kbd> para detener una aplicación. Vuelva a compilar y ejecute la aplicación con el comando `dotnet run`. Al seleccionar el botón **`Add todo`** , no ocurre nada porque no hay ningún controlador de eventos conectado al botón.

1. Agregue un método `AddTodo` al componente `Todo` y regístrelo para seleccionar los botones mediante el atributo `@onclick`. El método `AddTodo` de C# se llama cuando se selecciona el botón:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. Para obtener el título del nuevo elemento de tarea pendiente, agregue un campo de cadena `newTodo` en la parte superior del bloque `@code` y enlácelo al valor de la entrada de texto mediante el atributo `bind` en el elemento `<input>`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Actualice el método `AddTodo` para agregar el `TodoItem` con el título especificado a la lista. Borre el valor de la entrada de texto mediante el establecimiento de `newTodo` en una cadena vacía:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. Detenga la aplicación en ejecución en el shell de comandos. Vuelva a compilar y ejecute la aplicación con el comando `dotnet run`. Agregue algunos elementos de tareas pendientes a la lista de tareas pendientes para probar el nuevo código.

1. Se puede hacer que el texto de título de cada elemento de tarea pendiente sea editable y una casilla puede ayudar al usuario a realizar un seguimiento de los elementos completados. Agregue una entrada de casilla a cada elemento de tarea pendiente y enlace su valor a la propiedad `IsDone`. Cambie `@todo.Title` a un elemento `<input>` enlazado a `@todo.Title`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. Para comprobar que estos valores están enlazados, actualice el encabezado `<h3>` para mostrar un recuento del número de elementos de la lista de tareas pendientes que no se han completado (`IsDone` es `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. El componente `Todo` completado (`Pages/Todo.razor`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. Detenga la aplicación en ejecución en el shell de comandos. Vuelva a compilar y ejecute la aplicación con el comando `dotnet run`. Agregue elementos de tarea pendiente para probar el nuevo código.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a:

> [!div class="checklist"]
> * Crear un proyecto de aplicación Blazor de lista de tareas
> * Modificar los componentes de Razor
> * Usar el control de eventos y el enlace de datos en los componentes
> * Uso del enrutamiento en una aplicación Blazor

Más información sobre las herramientas de ASP.NET Core Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
