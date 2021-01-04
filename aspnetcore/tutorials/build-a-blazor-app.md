---
title: Creación de una aplicación de lista de tareas pendientes Blazor
author: guardrex
description: Cree una aplicación Blazor paso a paso.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/14/2020
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
ms.openlocfilehash: 87626ff30589de82a04c95634fc0dcbcf2eeac18
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507012"
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

   El comando anterior crea una carpeta denominada `TodoList` con la opción `-o|--output` para contener la aplicación. La carpeta `TodoList` es la *carpeta raíz* del proyecto. Cambie los directorios a la carpeta `TodoList` con el siguiente comando:

   ```dotnetcli
   cd TodoList
   ```

1. Agregue un nuevo componente `Todo` Razor a la aplicación con el siguiente comando:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   La opción `-n|--name` del comando anterior especifica el nombre del nuevo componente Razor. El nuevo componente se crea en la carpeta `Pages` del proyecto con la opción `-o|--output`.

   > [!IMPORTANT]
   > Los nombres de archivo de componente Razor requieren poner en mayúscula la primera letra. Abra la carpeta `Pages` y confirme que el nombre de archivo del componente `Todo` empieza con una letra mayúscula `T`. El nombre de archivo debe ser `Todo.razor`.

1. Abra el componente `Todo` en cualquier editor de archivos y agregue una directiva `@page` Razor a la parte superior del archivo con una dirección URL relativa de `/todo`.

   `Pages/Todo.razor`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo0.razor?highlight=1)]

   Guarde el archivo `Pages/Todo.razor`.

1. Agregue el componente `Todo` a la barra de navegación.

   El componente `NavMenu` se usa en el diseño de la aplicación. Los diseños son componentes que le permiten impedir la duplicación de contenido en una aplicación. El componente `NavLink` proporciona una indicación en la interfaz de usuario de la aplicación cuando la aplicación carga la dirección URL del componente.

   En la lista sin ordenar (`<ul>...</ul>`) del componente `NavMenu`, agregue los siguientes elementos de lista (`<li>...</li>`) y el componente `NavLink` para el componente `Todo`.

   En `Shared/NavMenu.razor`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   Guarde el archivo `Shared/NavMenu.razor`.

1. Compile y ejecute la aplicación mediante el comando [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) en el shell de comandos desde la carpeta `TodoList`. Cuando la aplicación se ejecute, visite la nueva página de tareas pendientes seleccionando el vínculo **`Todo`** en la barra de navegación de la aplicación, que carga la página en `/todo`.

   Deje la aplicación ejecutando el shell de comandos. Cada vez que se guarda un archivo, la aplicación se vuelve a compilar automáticamente. El explorador pierde temporalmente su conexión con la aplicación durante la compilación y el reinicio. La página del explorador se recarga automáticamente cuando se restablece la conexión.

1. Agregue un archivo `TodoItem.cs` a la raíz del proyecto (carpeta `TodoList`) para retener una clase que represente un elemento de la lista de tareas. Use el siguiente C# de código para la clase `TodoItem`.

   `TodoItem.cs`:

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. Vuelva al componente `Todo` y realice las tareas siguientes:

   * Agregue un campo a los elementos de tareas pendientes en un bloque `@code`. El componente `Todo` utiliza este campo para mantener el estado de la lista de tareas pendientes.
   * Agregue el marcado de la lista no ordenada y un bucle `foreach` para que cada elemento de la lista se represente en un elemento de la lista de tareas pendientes (`<li>`).

   `Pages/Todo.razor`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,13)]

1. Para agregar elementos de tareas pendientes a la lista, la aplicación requiere elementos de la interfaz de usuario. Agregue una entrada de texto (`<input>`) y un botón (`<button>`) debajo de la lista no ordenada (`<ul>...</ul>`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. Guarde el archivo `TodoItem.cs` y el archivo `Pages/Todo.razor` actualizado. En el shell de comandos, la aplicación se vuelve a compilar automáticamente cuando se guardan los archivos. El explorador pierde temporalmente su conexión con la aplicación y, a continuación, vuelve a cargar la página cuando se restablece la conexión.

1. Al seleccionar el botón **`Add todo`** , no ocurre nada porque no hay ningún controlador de eventos conectado al botón.

1. Agregue un método `AddTodo` al componente `Todo` y registre el método para el botón mediante el atributo `@onclick`. El método `AddTodo` de C# se llama cuando se selecciona el botón:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. Para obtener el título del nuevo elemento de la lista de tareas pendientes, agregue un campo de cadena `newTodo` en la parte superior del bloque `@code`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=3)]

   Modifique el elemento `<input>` de texto para enlazar `newTodo` con el atributo `@bind`:

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Actualice el método `AddTodo` para agregar el `TodoItem` con el título especificado a la lista. Borre el valor de la entrada de texto mediante el establecimiento de `newTodo` en una cadena vacía:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. Guarde el archivo `Pages/ToDo.razor`. La aplicación se vuelve a compilar automáticamente en el shell de comandos. La página vuelve a cargarse en el explorador después de que el explorador vuelve a conectarse a la aplicación.

1. Se puede hacer que el texto de título de cada elemento de tarea pendiente sea editable y una casilla puede ayudar al usuario a realizar un seguimiento de los elementos completados. Agregue una entrada de casilla a cada elemento de tarea pendiente y enlace su valor a la propiedad `IsDone`. Cambie `@todo.Title` a un elemento `<input>` enlazado a `todo.Title` con `@bind`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=4-7)]

1. Actualice el encabezado `<h3>` para mostrar un recuento del número de elementos de la lista de tareas que no se han completado (`IsDone` es `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. El componente `Todo` completado (`Pages/Todo.razor`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. Guarde el archivo `Pages/ToDo.razor`. La aplicación se vuelve a compilar automáticamente en el shell de comandos. La página vuelve a cargarse en el explorador después de que el explorador vuelve a conectarse a la aplicación.

1. Agregue los elementos, edite los elementos y marque los elementos de la lista de tareas realizados para probar el componente.

1. Cuando termine, cierre la aplicación en el shell de comandos. Muchos shells de comandos aceptan el comando de teclado <kbd>Ctrl</kbd>+<kbd>c</kbd> para detener una aplicación.

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
