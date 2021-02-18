---
title: Enlace de datos de ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre las características de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 76cc0ddc46dd08a5b8b88cf6045b84beab57c295
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280138"
---
# <a name="aspnet-core-blazor-data-binding"></a>Enlace de datos de ASP.NET Core Blazor

Los componentes de Razor proporcionan características de enlace de datos por medio de un atributo de elemento HTML denominado [`@bind`](xref:mvc/views/razor#bind) con un valor de campo, propiedad o expresión de Razor.

En el ejemplo siguiente se enlaza un elemento `<input>` al campo `currentValue` y un elemento `<input>` a la propiedad `CurrentValue`:

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

Cuando uno de los elementos pierde el foco, se actualiza su campo o propiedad enlazada.

El cuadro de texto se actualiza en la interfaz de usuario solo cuando se representa el componente, no en respuesta al cambio de valor del campo o la propiedad. Como los componentes se representan a sí mismos después de que se ejecute el código del controlador de eventos, las actualizaciones de campos y propiedades *normalmente* se reflejan en la interfaz de usuario justo después de que se desencadene un controlador de eventos.

Usar [`@bind`](xref:mvc/views/razor#bind) con la propiedad `CurrentValue` (`<input @bind="CurrentValue" />`) es prácticamente igual que usar lo siguiente:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

Cuando se representa el componente, el valor (`value`) del elemento de entrada procede de la propiedad `CurrentValue`. Cuando el usuario escribe en el cuadro de texto y cambia el foco del elemento, se desencadena el evento `onchange` y la propiedad `CurrentValue` se establece en el valor modificado. En realidad, la generación de código es más compleja, porque [`@bind`](xref:mvc/views/razor#bind) controla casos en los que se realizan conversiones de tipos. En principio, [`@bind`](xref:mvc/views/razor#bind) asocia el valor actual de una expresión con un atributo `value` y controla los cambios mediante el controlador registrado.

También puede enlazar una propiedad o un campo con otros eventos incluyendo un atributo `@bind:event` con un parámetro `event`. En el siguiente ejemplo se enlaza la propiedad `CurrentValue` en el evento `oninput`:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

A diferencia de `onchange`, que se activa cuando el elemento pierde el foco, `oninput` se desencadena cuando cambia el valor del cuadro de texto.

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

El enlace de atributos distingue mayúsculas de minúsculas:

* `@bind` es válido.
* `@Bind` ni `@BIND` son válidos.

## <a name="unparsable-values"></a>Valores no analizables

Cuando un usuario proporciona un valor no analizable a un elemento enlazado a datos, el valor no analizable se revierte automáticamente a su valor anterior al desencadenar el evento de enlace.

Considere el caso siguiente:

* Un elemento `<input>` se enlaza a un tipo `int` con un valor inicial de `123`:

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* El usuario actualiza el valor del elemento a `123.45` en la página y cambia el foco del elemento.

En el escenario anterior, el valor del elemento se revierte a `123`. Cuando el valor `123.45` se rechaza en favor del valor original de `123`, el usuario entiende que no se ha aceptado su valor.

De forma predeterminada, el enlace de datos se aplica al evento `onchange` del elemento (`@bind="{PROPERTY OR FIELD}"`). Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para desencadenar el enlace en un evento diferente. En el caso del evento `oninput` (`@bind:event="oninput"`), la reversión se produce después de cualquier pulsación de tecla que introduzca un valor no analizable. Cuando el destino es el evento `oninput` con un tipo enlazado a `int`, se impide que el usuario escriba el carácter `.`. El carácter `.` se elimina de forma inmediata, por lo que el usuario recibe un comentario al instante informándole de que solo se permiten números enteros. Hay escenarios en los que la reversión del valor del evento `oninput` no es ideal, por ejemplo, cuando se debe permitir que el usuario borre un valor `<input>` que no se puede analizar. De forma alternativa, puede hacer lo siguiente:

* No use el evento `oninput`. Use el evento `onchange` predeterminado (especifique solo `@bind="{PROPERTY OR FIELD}"`) para que no se revierta un valor no válido hasta que el elemento pierda el foco.
* Enlace a un tipo que acepte valores NULL, como `int?` o `string`, y proporcione lógica personalizada para administrar las entradas no válidas.
* Use un [componente de validación de formularios](xref:blazor/forms-validation), como <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> o <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>. Los componentes de validación de formularios tienen compatibilidad integrada para administrar entradas no válidas. Para obtener más información, vea <xref:blazor/forms-validation>. Componentes de validación de formularios:
  * Permiten que el usuario proporcione entradas no válidas y reciba errores de validación en la clase <xref:Microsoft.AspNetCore.Components.Forms.EditContext> asociada.
  * Muestran errores de validación en la interfaz de usuario sin impedir que el usuario escriba datos adicionales en el formulario web.

## <a name="format-strings"></a>Cadenas de formato

El enlace de datos funciona con cadenas de formato <xref:System.DateTime> mediante `@bind:format`. Otras expresiones de formato, como los formatos de moneda o número, no están disponibles en este momento.

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

En el código anterior, el tipo de campo (`type`) del elemento `<input>` tiene como valor predeterminado `text`. `@bind:format` se admite para enlazar los siguientes tipos de .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

El atributo `@bind:format` especifica el formato de fecha que se va a aplicar al valor (`value`) del elemento `<input>`. El formato también se usa para analizar el valor cuando se produce un evento `onchange`.

No se recomienda especificar un formato para el tipo de campo `date` porque Blazor tiene compatibilidad integrada para dar formato a las fechas. A pesar de la recomendación, use solo el formato de fecha `yyyy-MM-dd` para que el enlace funcione correctamente si se proporciona un formato con el tipo de campo `date`:

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a>Enlace con parámetros de componente

Un escenario habitual es enlazar una propiedad de un componente secundario a una propiedad de su elemento primario. Este escenario se denomina *enlace encadenado* porque se producen varios niveles de enlace simultáneamente.

Los [parámetros de componente](xref:blazor/components/index#component-parameters) permiten el enlace de propiedades de un componente primario con la sintaxis `@bind-{PROPERTY}`.

No se pueden implementar enlaces encadenados con sintaxis [`@bind`](xref:mvc/views/razor#bind) en el componente secundario. Es necesario especificar un controlador de eventos y un valor por separado para permitir la actualización de la propiedad en el elemento primario desde el componente secundario.

El componente primario sigue aprovechando la sintaxis [`@bind`](xref:mvc/views/razor#bind) para configurar el enlace de datos con el componente secundario.

El siguiente componente `Child` (`Shared/Child.razor`) tiene un parámetro de componente `Year` y la devolución de llamada `YearChanged`:

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

La devolución de llamada (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) debe tener el nombre del parámetro del componente seguido del sufijo "`Changed`" (`{PARAMETER NAME}Changed`). En el ejemplo anterior, el nombre de la devolución de llamada es `YearChanged`. <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invoca al delegado asociado al enlace con el argumento proporcionado y envía una notificación de evento de la propiedad modificada.

En el siguiente componente `Parent` (`Parent.razor`), el campo `year` se enlaza al parámetro `Year` del componente secundario:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

El parámetro `Year` se puede enlazar porque tiene un evento `YearChanged` complementario que coincide con el tipo del parámetro `Year`.

Por convención, una propiedad se puede enlazar a un controlador de eventos correspondiente si se incluye un atributo `@bind-{PROPERTY}:event` asignado al controlador. `<Child @bind-Year="year" />` equivale a escribir lo siguiente:

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

En un ejemplo real más sofisticado, el siguiente componente `PasswordField` (`PasswordField.razor`):

* Establece el valor del elemento `<input>` en un campo `password`.
* Expone los cambios de una propiedad `Password` a un componente primario con un objeto [`EventCallback`](xref:blazor/components/event-handling#eventcallback) que pasa el valor actual del campo `password` del elemento secundario como su argumento.
* Usa el evento `onclick` para desencadenar el método `ToggleShowPassword`. Para obtener más información, vea <xref:blazor/components/event-handling>.

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private async Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        await PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

El componente `PasswordField` se usa en otro componente:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Realice comprobaciones o errores de captura en el método que invoca el delegado del enlace. El ejemplo siguiente informa de inmediato al usuario si se usa un espacio en el valor de la contraseña:

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

Para más información sobre <xref:Microsoft.AspNetCore.Components.EventCallback%601>, consulte <xref:blazor/components/event-handling#eventcallback>.

## <a name="bind-across-more-than-two-components"></a>Enlace entre más de dos componentes

Puede enlazar a través de cualquier número de componentes anidados, pero debe respetar el flujo unidireccional de datos:

* Las notificaciones de cambio *suben en la jerarquía*.
* Los valores nuevos de parámetro *bajan en la jerarquía*.

Un enfoque común y recomendado es almacenar solo los datos subyacentes en el componente primario para evitar la confusión sobre el estado que se debe actualizar.

Los siguientes componentes demuestran los conceptos anteriores:

`ParentComponent.razor`:

```razor
<h1>Parent Component</h1>

<p>Parent Message: <b>@parentMessage</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-ChildMessage="parentMessage" />

@code {
    private string parentMessage = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentMessage = $"Set in Parent {DateTime.Now}";
    }
}
```

`ChildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Message: <b>@ChildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-GrandchildMessage="BoundValue" />
</div>

@code {
    [Parameter]
    public string ChildMessage { get; set; }

    [Parameter]
    public EventCallback<string> ChildMessageChanged { get; set; }

    private string BoundValue
    {
        get => ChildMessage;
        set => ChildMessageChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await ChildMessageChanged.InvokeAsync(
            $"Set in Child {DateTime.Now}");
    }
}
```

`GrandchildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Message: <b>@GrandchildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string GrandchildMessage { get; set; }

    [Parameter]
    public EventCallback<string> GrandchildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await GrandchildMessageChanged.InvokeAsync(
            $"Set in Grandchild {DateTime.Now}");
    }
}
```

Para obtener un enfoque alternativo adecuado para compartir datos en memoria entre componentes que no están necesariamente anidados, consulte la sección *Servicio de contenedor de estado en memoria* del artículo <xref:blazor/state-management>.

## <a name="additional-resources"></a>Recursos adicionales

* [Enlace a botones de radio en un formulario](xref:blazor/forms-validation#radio-buttons)
* [Enlace de opciones del elemento `<select>` a valores `null` de un objeto C# en un formulario](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
