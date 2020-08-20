---
title: Componentes de vista en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo se usan los componentes de vista en ASP.NET Core y cómo agregarlos a las aplicaciones.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
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
uid: mvc/views/view-components
ms.openlocfilehash: 32ae699c4ef501096a9c4ab7bca6673139910f02
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635091"
---
# <a name="view-components-in-aspnet-core"></a>Componentes de vista en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="view-components"></a>Componentes de vista

Los componentes de vista son similares a las vistas parciales, pero mucho más eficaces. Los componentes de vista no usan el enlace de modelos y solo dependen de los datos proporcionados cuando se les llama. Este artículo se ha escrito mediante controladores y vistas, pero los componentes de la vista también funcionan con Razor páginas.

Un componente de vista:

* Representa un fragmento en lugar de una respuesta completa.
* Incluye las mismas ventajas de separación de conceptos y capacidad de prueba que se encuentran entre un controlador y una vista.
* Puede tener parámetros y lógica de negocios.
* Normalmente se invoca desde una página de diseño.

Los componentes de vista están diseñados para cualquier lugar que tenga lógica de representación reutilizable demasiado compleja para una vista parcial, como:

* Menús de navegación dinámica
* Nube de etiquetas (donde consulta la base de datos)
* Panel de inicio de sesión
* Carro de la compra
* Artículos publicados recientemente
* Contenido de la barra lateral de un blog típico
* Un panel de inicio de sesión que se representa en cada página y muestra los vínculos para iniciar o cerrar sesión, según el estado del usuario

Un componente de vista consta de dos partes: la clase (normalmente derivada de [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) y el resultado que devuelve (por lo general, una vista). Al igual que los controladores, un componente de vista puede ser un POCO, pero la mayoría de los desarrolladores prefieren aprovechar las ventajas que ofrecen los métodos y las propiedades disponibles al derivar de `ViewComponent`.

Al considerar si los componentes de la vista cumplen las especificaciones de una aplicación, considere la posibilidad de usar Razor componentes en su lugar. Razor Los componentes también combinan marcado con código C# para generar unidades de interfaz de usuario reutilizables. Razor Los componentes están diseñados para la productividad de los desarrolladores a la hora de proporcionar lógica y composición de la interfaz de usuario del lado cliente. Para más información, consulte <xref:blazor/components/index>.

## <a name="creating-a-view-component"></a>Crear un componente de vista

Esta sección contiene los requisitos de alto nivel para crear un componente de vista. Más adelante en el artículo examinaremos cada paso en detalle y crearemos un componente de vista.

### <a name="the-view-component-class"></a>La clase de componente de vista

Es posible crear una clase de componente de vista mediante una de las siguientes acciones:

* Derivar de *ViewComponent*
* Decorar una clase con el atributo `[ViewComponent]` o derivar de una clase con el atributo `[ViewComponent]`
* Crear una clase cuyo nombre termina con el sufijo *ViewComponent*

Al igual que los controladores, los componentes de vista deben ser clases públicas, no anidadas y no abstractas. El nombre del componente de vista es el nombre de la clase sin el sufijo "ViewComponent". También se puede especificar explícitamente mediante la propiedad `ViewComponentAttribute.Name`.

Una clase de componente de vista:

* Es totalmente compatible con la [inserción de dependencias](../../fundamentals/dependency-injection.md) de constructor.

* No participa en el ciclo de vida del controlador, lo que significa que no se pueden usar [filtros](../controllers/filters.md) en un componente de vista.

### <a name="view-component-methods"></a>Métodos de componente de vista

Un componente de vista define su lógica en un método `InvokeAsync` que devuelve un elemento `Task<IViewComponentResult>` o en un método `Invoke` sincrónico que devuelve un elemento `IViewComponentResult`. Los parámetros proceden directamente de la invocación del componente de vista, no del enlace de modelos. Un componente de vista nunca controla directamente una solicitud. Por lo general, un componente de vista inicializa un modelo y lo pasa a una vista mediante una llamada al método `View`. En resumen, los métodos de componente de vista:

* Defina un método `InvokeAsync` que devuelva un elemento `Task<IViewComponentResult>` o un método `Invoke` sincrónico que devuelva un elemento `IViewComponentResult`.
* Normalmente Inicializa un modelo y lo pasa a una vista mediante una llamada al `ViewComponent` `View` método.
* Los parámetros provienen del método que realiza la llamada y no de HTTP. No hay ningún enlace de modelos.
* No son accesibles directamente como punto de conexión HTTP. Se invocan desde el código (normalmente en una vista). Un componente de vista nunca controla una solicitud.
* Se sobrecargan en la firma, en lugar de los detalles de la solicitud HTTP actual.

### <a name="view-search-path"></a>Ruta de búsqueda de la vista

El tiempo de ejecución busca la vista en las rutas de acceso siguientes:

* /Views/{Controller Name}/Components/{Nombre de componente de vista}/{Nombre de vista}
* /Views/Shared/Components/{Nombre de componente de vista}/{Nombre de vista}
* /Pages/Shared/Components/{Nombre de componente de vista}/{Nombre de vista}

La ruta de acceso de búsqueda se aplica a los proyectos mediante controladores + vistas y Razor páginas.

El nombre de vista predeterminado para un componente de vista es *Default*, lo que significa que el archivo de vista normalmente se denominará *Default.cshtml*. Puede especificar un nombre de vista diferente al crear el resultado del componente de vista o al llamar al método `View`.

Se recomienda que asigne el nombre *Default.cshtml* al archivo de vista y use la ruta de acceso *Views/Shared/Components/{Nombre de componente de vista}/{Nombre de vista}*. El componente de vista `PriorityList` usado en este ejemplo usa *Views/Shared/Components/PriorityList/Default.cshtml* para la vista del componente de vista.

### <a name="customize-the-view-search-path"></a>Personalización de la ruta de acceso de la búsqueda de la vista

Para personalizar la ruta de búsqueda de la vista, modifique la Razor <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> colección de. Por ejemplo, para buscar vistas dentro de la ruta de acceso "/Components/{Nombre del componente de la vista}/{Nombre de la vista}", agregue un elemento nuevo a la colección:

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

En el código anterior, el marcador de posición "{0}" representa la ruta de acceso "Components/{Nombre del componente de la vista}/{Nombre de la vista}".

## <a name="invoking-a-view-component"></a>Invocar un componente de vista

Para usar el componente de vista, llame a lo siguiente dentro de una vista:

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

Los parámetros se pasarán al método `InvokeAsync`. El `PriorityList` componente de vista desarrollado en el artículo se invoca desde el archivo de vista *views/todo/index. cshtml* . En la tabla siguiente, se llama al método `InvokeAsync` con dos parámetros:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a>Invocación de un componente de vista como un asistente de etiquetas

Para ASP.NET Core 1.1 y versiones posteriores, puede invocar un componente de vista como un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro):

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

Los parámetros de clase y método con grafía Pascal para los asistentes de etiquetas se convierten a su [grafía kebab](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). El asistente de etiquetas que va a invocar un componente de vista usa el elemento `<vc></vc>`. El componente de vista se especifica de la manera siguiente:

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

Para usar un componente de vista como un asistente de etiquetas, registre el ensamblado que contiene el componente de vista mediante la directiva `@addTagHelper`. Si el componente de vista está en un ensamblado denominado `MyWebApp`, agregue la directiva siguiente al archivo *_ViewImports.cshtml*:

```cshtml
@addTagHelper *, MyWebApp
```

Puede registrar un componente de vista como un asistente de etiquetas en cualquier archivo que haga referencia al componente de vista. Vea [Administración del ámbito de los asistentes de etiquetas](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) para más información sobre cómo registrar asistentes de etiquetas.

El método `InvokeAsync` usado en este tutorial:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

En el marcado del asistente de etiquetas:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

En el ejemplo anterior, el componente de vista `PriorityList` se convierte en `priority-list`. Los parámetros para el componente de vista se pasan como atributos en grafía kebab.

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a>Invocar un componente de vista directamente desde un controlador

Los componentes de vista suelen invocarse desde una vista, pero se pueden invocar directamente desde un método de controlador. Aunque los componentes de vista no definen puntos de conexión como controladores, se puede implementar fácilmente una acción del controlador que devuelva el contenido de `ViewComponentResult`.

En este ejemplo, se llama al componente de vista directamente desde el controlador:

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>Tutorial: Creación de un componente de vista simple

[Descargue](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), compile y pruebe el código de inicio. Se trata de un proyecto simple con un controlador `ToDo` que muestra una lista de *tareas pendientes*.

![Lista de tareas pendientes](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>Agregar una clase ViewComponent

Cree una carpeta *ViewComponents* y agregue la siguiente clase `PriorityListViewComponent`:

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

Notas sobre el código:

* Las clases de componentes de vista pueden estar contenidas en **cualquier** carpeta del proyecto.
* Dado que el nombre de clase PriorityList**ViewComponent** acaba con el sufijo **ViewComponent**, el tiempo de ejecución usará la cadena "PriorityList" cuando haga referencia al componente de clase desde una vista. Más adelante explicaremos esta cuestión con más detalle.
* El atributo `[ViewComponent]` puede cambiar el nombre usado para hacer referencia a un componente de vista. Por ejemplo, podríamos haber asignado a la clase el nombre `XYZ` y aplicado el atributo `ViewComponent`:

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* El atributo `[ViewComponent]` anterior le indica al selector de componentes de vista que use el nombre `PriorityList` al buscar las vistas asociadas al componente y que use la cadena "PriorityList" al hacer referencia al componente de clase desde una vista. Más adelante explicaremos esta cuestión con más detalle.
* El componente usa la [inserción de dependencias](../../fundamentals/dependency-injection.md) para que el contexto de datos esté disponible.
* `InvokeAsync` expone un método al que se puede llamar desde una vista y puede tomar un número arbitrario de argumentos.
* El método `InvokeAsync` devuelve el conjunto de elementos `ToDo` que cumplen los parámetros `isDone` y `maxPriority`.

### <a name="create-the-view-component-no-locrazor-view"></a>Crear la vista de componentes de vista Razor

* Cree la carpeta *Views/Shared/Components*. Esta carpeta **debe** denominarse *Components*.

* Cree la carpeta *Views/Shared/Components/PriorityList*. El nombre de esta carpeta debe coincidir con el nombre de la clase de componente de vista o con el nombre de la clase sin el sufijo (si se ha seguido la convención y se ha usado el sufijo *ViewComponent* en el nombre de clase). Si ha usado el atributo `ViewComponent`, el nombre de clase debe coincidir con la designación del atributo.

* Cree una vista *views/Shared/Components/PriorityList/default. cshtml* Razor :


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   La Razor vista toma una lista de `TodoItem` y las muestra. Si el método `InvokeAsync` del componente de vista no pasa el nombre de la vista (como en nuestro ejemplo), se usa *Default* para el nombre de vista, según la convención. Más adelante en el tutorial veremos cómo pasar el nombre de la vista. Para reemplazar el estilo predeterminado de un controlador concreto, agregue una vista a la carpeta de vistas específicas del controlador (por ejemplo, *Views/ToDo/Components/PriorityList/Default.cshtml)*.

    Si el componente de vista es específico del controlador, puede agregarlo a la carpeta específica del controlador (*views/todo/Components/PriorityList/default. cshtml*).

* Agregue un elemento `div` que contenga una llamada al componente de lista de prioridad en la parte inferior del archivo *Views/ToDo/index.cshtml*:

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

El marcado `@await Component.InvokeAsync` muestra la sintaxis para llamar a los componentes de vista. El primer argumento es el nombre del componente que se quiere invocar o llamar. Los parámetros siguientes se pasan al componente. `InvokeAsync` puede tomar un número arbitrario de argumentos.

Pruebe la aplicación. En la imagen siguiente se muestra la lista de tareas pendientes y los elementos de prioridad:

![lista de tareas pendientes y elementos de prioridad](view-components/_static/pi.png)

También puede llamar al componente de vista directamente desde el controlador:

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![elementos de prioridad de la acción IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>Especificar un nombre de vista

En algunos casos, puede ser necesario que un componente de vista complejo especifique una vista no predeterminada. En el código siguiente se muestra cómo especificar la vista "PVC" desde el método `InvokeAsync`. Actualice el método `InvokeAsync` en la clase `PriorityListViewComponent`.

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

Copie el archivo *Views/Shared/Components/PriorityList/Default.cshtml* en una vista denominada *Views/Shared/Components/PriorityList/PVC.cshtml*. Agregue un encabezado para indicar que se está usando la vista PVC.

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

Actualice *Views/ToDo/Index.cshtml*:

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

Ejecute la aplicación y compruebe la vista PVC.

![Componente de vista de prioridad](view-components/_static/pvc.png)

Si la vista PVC no se representa, compruebe que está llamando al componente de vista con prioridad cuatro o superior.

### <a name="examine-the-view-path"></a>Examinar la ruta de acceso de la vista

* Cambie el parámetro de prioridad a tres o menos para que no se devuelva la vista de prioridad.
* Cambie temporalmente el nombre de *views/todo/Components/PriorityList/default. cshtml* a *1Default. cshtml*.
* Pruebe la aplicación. Obtendrá el siguiente error:

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* Copie *Views/ToDo/Components/PriorityList/1Default.cshtml* en *Views/Shared/Components/PriorityList/Default.cshtml*.
* Agregue algún marcado a la vista de componentes de la vista todo *compartida* para indicar que la vista procede de la carpeta *compartida* .
* Pruebe la vista de componentes **Shared**.

![Salida de la lista de tareas pendientes con la vista de componentes Shared](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a>Evitar cadenas codificadas de forma rígida

Si busca seguridad en tiempo de compilación, puede reemplazar el nombre del componente de vista codificado de forma rígida por el nombre de clase. Cree el componente de vista sin el sufijo "ViewComponent":

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

Agregue una `using` instrucción al Razor archivo de vista y use el `nameof` operador:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a>Realizar el trabajo sincrónico

El marco controla la invocación de un método `Invoke` sincrónico si no necesita realizar un trabajo asincrónico. El método siguiente crea un componente de vista `Invoke` sincrónico:

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

En el archivo del componente de vista Razor se enumeran las cadenas que se pasan al `Invoke` método (*views/Home/Components/PriorityList/default. cshtml*):

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

El componente de vista se invoca en un Razor archivo (por ejemplo, *views/home/index. cshtml*) mediante uno de los enfoques siguientes:

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [Asistente de etiquetas](xref:mvc/views/tag-helpers/intro)

Para usar el método <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>, llame a `Component.InvokeAsync`:

::: moniker-end

::: moniker range="< aspnetcore-1.1"

El componente de vista se invoca en un Razor archivo (por ejemplo, *views/home/index. cshtml*) con <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> .

Llame a `Component.InvokeAsync`:

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

Para usar el asistente de etiquetas, registre el ensamblado que contiene el componente de vista con el uso de la directiva `@addTagHelper` (el componente de vista se encuentra en un ensamblado denominado `MyWebApp`):

```cshtml
@addTagHelper *, MyWebApp
```

Use la aplicación auxiliar de etiquetas de componente de vista en el Razor archivo de marcado:

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

La firma del método de `PriorityList.Invoke` es sincrónica, pero Razor busca y llama al método con `Component.InvokeAsync` en el archivo de marcado.

## <a name="all-view-component-parameters-are-required"></a>Todos los parámetros de componente de vista son obligatorios

Cada parámetro de un componente de vista es un atributo obligatorio. Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/5011). Si se omite algún parámetro:

* La firma del método `InvokeAsync` no coincidirá, por lo que el método no se ejecutará.
* ViewComponent no representará ningún marcado.
* No se producirá ningún error.

## <a name="additional-resources"></a>Recursos adicionales

* [Inserción de dependencias en vistas](xref:mvc/views/dependency-injection)
