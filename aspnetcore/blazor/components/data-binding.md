---
title: Enlace de datos de ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre las características de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/data-binding
ms.openlocfilehash: 896eed0e55986678b6bb86638ca92b04a77b4fef
ms.sourcegitcommit: d00a200bc8347af794b24184da14ad5c8b6bba9a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/21/2020
ms.locfileid: "86869944"
---
# <a name="aspnet-core-blazor-data-binding"></a>Enlace de datos de ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

Los componentes de Razor proporcionan características de enlace de datos por medio de un atributo de elemento HTML denominado [`@bind`](xref:mvc/views/razor#bind) con un valor de campo, propiedad o expresión de Razor.

En el ejemplo siguiente se enlaza la propiedad `CurrentValue` al valor del cuadro de texto:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Cuando el cuadro de texto pierde el foco, se actualiza el valor de la propiedad.

El cuadro de texto se actualiza en la interfaz de usuario solo cuando se representa el componente, no en respuesta al cambio de valor de la propiedad. Como los componentes se representan a sí mismos después de que se ejecute el código del controlador de eventos, las actualizaciones de propiedades *normalmente* se reflejan en la interfaz de usuario justo después de que se desencadene un controlador de eventos.

Usar [`@bind`](xref:mvc/views/razor#bind) con la propiedad `CurrentValue` (`<input @bind="CurrentValue" />`) es prácticamente igual que usar lo siguiente:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Cuando se representa el componente, el valor (`value`) del elemento de entrada procede de la propiedad `CurrentValue`. Cuando el usuario escribe en el cuadro de texto y cambia el foco del elemento, se desencadena el evento `onchange` y la propiedad `CurrentValue` se establece en el valor modificado. En realidad, la generación de código es más compleja, porque [`@bind`](xref:mvc/views/razor#bind) administra los casos en los que se realizan conversiones de tipos. En principio, [`@bind`](xref:mvc/views/razor#bind) asocia el valor actual de una expresión con un atributo `value` y controla los cambios mediante el controlador registrado.

También puede enlazar una propiedad o un campo con otros eventos incluyendo un atributo `@bind:event` con un parámetro `event`. En el siguiente ejemplo se enlaza la propiedad `CurrentValue` en el evento `oninput`:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

A diferencia de `onchange`, que se activa cuando el elemento pierde el foco, `oninput` se desencadena cuando cambia el valor del cuadro de texto.

Use `@bind-{ATTRIBUTE}` con la sintaxis `@bind-{ATTRIBUTE}:event` para enlazar atributos de elementos que no sean `value`. En el ejemplo siguiente, el estilo del párrafo se actualiza cuando cambia el valor de `paragraphStyle`:

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

El enlace de atributos distingue mayúsculas de minúsculas:

* `@bind` es válido.
* `@Bind` ni `@BIND` son válidos.

## <a name="unparsable-values"></a>Valores no analizables

Cuando un usuario proporciona un valor no analizable a un elemento enlazado a datos, el valor no analizable se revierte automáticamente a su valor anterior al desencadenar el evento de enlace.

Considere el caso siguiente:

* Un elemento `<input>` se enlaza a un tipo `int` con un valor inicial de `123`:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* El usuario actualiza el valor del elemento a `123.45` en la página y cambia el foco del elemento.

En el escenario anterior, el valor del elemento se revierte a `123`. Cuando el valor `123.45` se rechaza en favor del valor original de `123`, el usuario entiende que no se ha aceptado su valor.

De forma predeterminada, el enlace de datos se aplica al evento `onchange` del elemento (`@bind="{PROPERTY OR FIELD}"`). Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para desencadenar el enlace en un evento diferente. En el caso del evento `oninput` (`@bind:event="oninput"`), la reversión se produce después de cualquier pulsación de tecla que introduzca un valor no analizable. Cuando el destino es el evento `oninput` con un tipo enlazado a `int`, se impide que el usuario escriba el carácter `.`. El carácter `.` se elimina de forma inmediata, por lo que el usuario recibe un comentario al instante informándole de que solo se permiten números enteros. Hay escenarios en los que la reversión del valor del evento `oninput` no es ideal, por ejemplo, cuando se debe permitir que el usuario borre un valor `<input>` que no se puede analizar. De forma alternativa, puede hacer lo siguiente:

* No use el evento `oninput`. Use el evento `onchange` predeterminado (especifique solo `@bind="{PROPERTY OR FIELD}"`) para que no se revierta un valor no válido hasta que el elemento pierda el foco.
* Enlace a un tipo que acepte valores NULL, como `int?` o `string`, y proporcione una lógica personalizada para administrar las entradas no válidas.
* Use un [componente de validación de formularios](xref:blazor/forms-validation), como <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> o <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>. Los componentes de validación de formularios tienen compatibilidad integrada para administrar entradas no válidas. Componentes de validación de formularios:
  * Permiten que el usuario proporcione entradas no válidas y reciba errores de validación en la clase <xref:Microsoft.AspNetCore.Components.Forms.EditContext> asociada.
  * Muestran errores de validación en la interfaz de usuario sin impedir que el usuario escriba datos adicionales en el formulario web.

## <a name="format-strings"></a>Cadenas de formato

El enlace de datos funciona con cadenas de formato <xref:System.DateTime> mediante `@bind:format`. Otras expresiones de formato, como los formatos de moneda o número, no están disponibles en este momento.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
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
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Enlace de componentes primarios a secundarios con parámetros de componente

El enlace reconoce los parámetros de componente, por lo que `@bind-{PROPERTY}` puede enlazar un valor de propiedad de un componente primario con un componente secundario. En la sección [Enlace de componentes secundarios a primarios con un enlace encadenado](#child-to-parent-binding-with-chained-bind) se describe como enlazar un elemento secundario con uno primario.

El siguiente componente secundario (`ChildComponent`) tiene un parámetro de componente `Year` y un evento `YearChanged`:

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<xref:Microsoft.AspNetCore.Components.EventCallback%601> debe tener el nombre del parámetro del componente seguido del sufijo `Changed` (`{PARAMETER NAME}Changed`), `YearChanged` en el ejemplo anterior. Para más información sobre <xref:Microsoft.AspNetCore.Components.EventCallback%601>, consulte <xref:blazor/components/event-handling#eventcallback>.

El siguiente componente primario usa:

* `ChildComponent` y enlaza el parámetro `ParentYear` desde el elemento primario con el parámetro `Year` en el componente secundario.
* El evento `onclick` se usa para desencadenar el método `ChangeTheYear`. Para obtener más información, vea <xref:blazor/components/event-handling>.

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

Al cargar el componente primario, `ParentComponent`, se produce el siguiente marcado:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Si el valor de la propiedad `ParentYear` se cambia seleccionando el botón del componente primario (`ParentComponent`), se actualiza la propiedad `Year` del componente secundario (`ChildComponent`). El nuevo valor de `Year` se representa en la interfaz de usuario junto con el componente primario (`ParentComponent`):

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

El parámetro `Year` se puede enlazar porque tiene un evento `YearChanged` complementario que coincide con el tipo del parámetro `Year`.

Por convención, `<ChildComponent @bind-Year="ParentYear" />` es equivalente a escribir:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

En general, una propiedad se puede enlazar a un controlador de eventos correspondiente si se incluye un atributo `@bind-{PROPRETY}:event`. Por ejemplo, la propiedad `MyProp` se puede enlazar a `MyEventHandler` mediante los dos atributos siguientes:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Enlace de componentes secundarios a primarios con un enlace encadenado

Un escenario común es encadenar un parámetro enlazado a datos a un elemento de página en la salida del componente. Este escenario se denomina *enlace encadenado* porque se producen varios niveles de enlace simultáneamente.

No se puede implementar un enlace encadenado con sintaxis [`@bind`](xref:mvc/views/razor#bind) en el elemento de la página. El controlador de eventos y el valor se deben especificar por separado. Sin embargo, un componente primario puede usar la sintaxis [`@bind`](xref:mvc/views/razor#bind) con el parámetro del componente.

El siguiente componente `PasswordField` (`PasswordField.razor`):

* Establece el valor del elemento `<input>` en una propiedad `Password`.
* Expone los cambios de la propiedad `Password` en un componente primario con [`EventCallback`](xref:blazor/components/event-handling#eventcallback).
* Usa el evento `onclick` para desencadenar el método `ToggleShowPassword`. Para obtener más información, vea <xref:blazor/components/event-handling>.

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

El componente `PasswordField` se usa en otro componente:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Para realizar comprobaciones o detectar errores en la contraseña en el ejemplo anterior:

* Cree un campo de respaldo para `Password` (`password` en el siguiente código de ejemplo).
* Realice las comprobaciones o detecte los errores en el establecedor de `Password`.

El ejemplo siguiente informa de inmediato al usuario si se usa un espacio en el valor de la contraseña:

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="additional-resources"></a>Recursos adicionales

* [Enlace a botones de radio en un formulario](xref:blazor/forms-validation#radio-buttons)
* [Enlace de opciones del elemento `<select>` a valores `null` de un objeto C# en un formulario](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
