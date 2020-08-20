---
title: Asistentes de etiquetas en formularios de ASP.NET Core
author: rick-anderson
description: En este tema se describen los asistentes de etiquetas que se usan en los formularios.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/working-with-forms
ms.openlocfilehash: c337e727a4683b0b3c67307af93ef8efa246e2ad
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631022"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a>Asistentes de etiquetas en formularios de ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette) y [Jerrie Pelser](https://github.com/jerriep)

En este documento se explica cómo trabajar con formularios y se detallan los elementos HTML que se usan habitualmente en un formulario. El elemento HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) proporciona el mecanismo principal que las aplicaciones web usan a la hora de devolver datos al servidor. La mayor parte de este documento se centra en describir los [asistentes de etiquetas](tag-helpers/intro.md) y cómo pueden servir para crear formularios HTML eficaces de manera productiva. Se recomienda leer [Introduction to Tag Helpers](tag-helpers/intro.md) (Introducción a los asistentes de etiquetas) antes de este documento.

En muchos casos, los asistentes de HTML proporcionan un método alternativo para un asistente de etiquetas específico, pero es importante tener en cuenta que los asistentes de etiquetas no reemplazan a los asistentes de HTML y que no hay un asistente de etiquetas para cada asistente de HTML. Si existe una alternativa del asistente de HTML, se mencionará aquí.

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a>Asistente de etiquetas de formulario (Form)

Aplicación auxiliar de etiquetas de [formulario](https://www.w3.org/TR/html401/interact/forms.html) :

* Genera el [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` valor de atributo HTML para una acción del controlador de MVC o una ruta con nombre.

* Genera un [token comprobación de solicitudes](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) oculto que impide que se falsifiquen solicitudes entre sitios (cuando se usa con el atributo `[ValidateAntiForgeryToken]` en el método de acción HTTP Post).

* Proporciona el atributo `asp-route-<Parameter Name>`, donde `<Parameter Name>` se agrega a los valores de ruta. Los parámetros `routeValues` de `Html.BeginForm` y `Html.BeginRouteForm` proporcionan una funcionalidad similar.

* Tiene `Html.BeginForm` y `Html.BeginRouteForm` como alternativa del asistente de HTML.

Sample:

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

El asistente de etiquetas Form anterior genera el siguiente HTML:

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

El tiempo de ejecución MVC genera el valor de atributo `action` de los atributos del asistente de etiquetas Form `asp-controller` y `asp-action`. El asistente de etiquetas Form genera también un [token de comprobación de solicitudes](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) oculto que impide que se falsifiquen solicitudes entre sitios (cuando se usa con el atributo `[ValidateAntiForgeryToken]` en el método de acción HTTP Post). Proteger un elemento HTML Form puro de la falsificación de solicitudes entre sitios no es tarea fácil, y el asistente de etiquetas Form presta este servicio.

### <a name="using-a-named-route"></a>Uso de una ruta con nombre

El atributo del asistente de etiquetas `asp-route` puede generar también el marcado del atributo HTML `action`. Una aplicación con una [ruta](../../fundamentals/routing.md) denominada `register` podría usar el siguiente marcado para la página de registro:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

Muchas de las vistas de la carpeta Views/Account (que se genera cuando se crea una aplicación web con Cuentas de usuario individuales) contienen el atributoasp-route-returnurl:

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>Con las plantillas integradas, `returnUrl` se rellena automáticamente solo cuando alguien intenta obtener acceso a un recurso autorizado, pero no se ha autenticado o no tiene autorización. Si se intenta realizar un acceso no autorizado, el middleware de seguridad redirige a la página de inicio de sesión con `returnUrl` configurado.

## <a name="the-form-action-tag-helper"></a>Asistente de etiquetas de acción de formulario

El asistente de etiquetas de acción de formulario genera el atributo `formaction` en la etiqueta `<button ...>` o `<input type="image" ...>` generadas. El atributo `formaction` controla el lugar donde un formulario envía sus datos. Enlaza a [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elementos de tipo `image` y [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elementos. El asistente de etiquetas de acción de formulario permite el uso de varios atributos [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` para controlar qué vínculo `formaction` se genera para el elemento correspondiente.

Atributos [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) admitidos para controlar el valor de `formaction`:

|Atributo|Descripción|
|---|---|
|[asp-controller](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|El nombre del controlador.|
|[asp-action](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|El nombre del método de acción.|
|[asp-area](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|El nombre del área.|
|[asp-page](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|Nombre de la Razor página.|
|[asp-page-handler](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|Nombre del controlador de la Razor página.|
|[asp-route](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|Nombre de la ruta.|
|[asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|Un valor único de ruta de dirección URL. Por ejemplo, `asp-route-id="1234"`.|
|[asp-all-route-data](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|Todos los valores de ruta.|
|[asp-fragment](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|El fragmento de dirección URL.|

### <a name="submit-to-controller-example"></a>Ejemplo de envío al controlador

El marcado siguiente envía el formulario a la acción `Index` de `HomeController` cuando se selecciona el elemento input o button:

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

El marcado anterior genera el siguiente código HTML:

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a>Ejemplo de envío a la página

El marcado siguiente envía el formulario a la `About` Razor página:

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

El marcado anterior genera el siguiente código HTML:

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a>Ejemplo de envío a la ruta

Tenga en cuenta el punto de conexión `/Home/Test`:

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

El marcado siguiente envía el formulario al punto de conexión `/Home/Test`.

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

El marcado anterior genera el siguiente código HTML:

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a>Asistente de etiquetas de entrada (Input)

La aplicación auxiliar de etiquetas de entrada enlaza un [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elemento HTML a una expresión de modelo en la vista de Razor.

Sintaxis:

```cshtml
<input asp-for="<Expression Name>">
```

El asistente de etiquetas Input hace lo siguiente:

* Genera los atributos HTML  y  del nombre de expresión especificado en el atributo. `asp-for="Property1.Property2"` equivale a `m => m.Property1.Property2`. El nombre de una expresión es lo que se usa para el valor de atributo `asp-for`. Vea la sección [Nombres de expresión](#expression-names) para obtener más información.

* Establece el valor de atributo HTML `type` según los atributos de tipo de modelo y de [anotación de datos](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) aplicados a la propiedad de modelo.

* No sobrescribirá el valor de atributo HTML `type` cuando se especifique uno.

* Genera atributos de validación [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) a partir de los atributos de [anotación de datos](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) aplicados a las propiedades de modelo.

* Tiene características del asistente de HTML que se superponen a `Html.TextBoxFor` y `Html.EditorFor`. Vea la sección **Alternativas del asistente de HTML al asistente de etiquetas Input** para obtener más información.

* Permite establecer tipado fuerte. Si el nombre de la propiedad cambia y no se actualiza el asistente de etiquetas, aparecerá un error similar al siguiente:

  ```
  An error occurred during the compilation of a resource required to process
  this request. Please review the following specific error details and modify
  your source code appropriately.

  Type expected
   'RegisterViewModel' does not contain a definition for 'Email' and no
   extension method 'Email' accepting a first argument of type 'RegisterViewModel'
   could be found (are you missing a using directive or an assembly reference?)
  ```

El asistente de etiquetas `Input` establece el atributo HTML `type` en función del tipo .NET. En la siguiente tabla se enumeran algunos tipos .NET habituales y el tipo HTML generado correspondiente (no incluimos aquí todos los tipos .NET).

|Tipo de .NET|Tipo de entrada|
|---|---|
|Bool|type="checkbox"|
|String|type="text"|
|DateTime|type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)|
|Byte|type="number"|
|Int|type="number"|
|Single, Double|type="number"|

En la siguiente tabla se muestran algunos atributos de [anotación de datos](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) comunes que el asistente de etiquetas Input asignará a tipos de entrada concretos (no incluimos aquí todos los atributo de validación):

|Atributo|Tipo de entrada|
|---|---|
|[EmailAddress]|type="email"|
|[Url]|type="url"|
|[HiddenInput]|type="hidden"|
|[Phone]|type="tel"|
|[DataType(DataType.Password)]|type="password"|
|[DataType(DataType.Date)]|type="date"|
|[DataType(DataType.Time)]|type="time"|

Sample:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

El código anterior genera el siguiente HTML:

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

Las anotaciones de datos que se aplican a las propiedades `Email` y `Password` generan metadatos en el modelo. El asistente de etiquetas de entrada usa esos metadatos del modelo y genera atributos [HTML5 ](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (consulte [Validación del modelo](../models/validation.md)). Estos atributos describen los validadores que se van a adjuntar a los campos de entrada, lo que proporciona HTML5 discreto y validación de [jQuery](https://jquery.com/). Los atributos discretos tienen el formato `data-val-rule="Error Message"` , donde Rule es el nombre de la regla de validación (por ejemplo,,, `data-val-required` `data-val-email` `data-val-maxlength` , etc.). Si se proporciona un mensaje de error en el atributo, se muestra como el valor del `data-val-rule` atributo. También hay atributos con el formato `data-val-ruleName-argumentName="argumentValue"` que aportan más información sobre la regla, por ejemplo, `data-val-maxlength-max="1024"`.

### <a name="html-helper-alternatives-to-input-tag-helper"></a>Alternativas del asistente de HTML al asistente de etiquetas Input

`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` y `Html.EditorFor` tienen características que se superponen al asistente de etiquetas Input. El asistente de etiquetas Input establecerá automáticamente el atributo `type`, cosa que no ocurrirá con `Html.TextBox` ni `Html.TextBoxFor`. `Html.Editor` y `Html.EditorFor` controlan colecciones, objetos complejos y plantillas, pero el asistente de etiquetas Input no. El asistente de etiquetas Input, `Html.EditorFor` y `Html.TextBoxFor` están fuertemente tipados (usan expresiones lambda), pero `Html.TextBox` y `Html.Editor` no (usan nombres de expresión).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()` y `@Html.EditorFor()` usan una entrada `ViewDataDictionary` especial denominada `htmlAttributes` al ejecutar sus plantillas predeterminadas. Si lo desea, este comportamiento se puede enriquecer con parámetros `additionalViewData`. En la clave "htmlAttributes" se distingue entre mayúsculas y minúsculas. La clave "htmlAttributes" se controla de forma similar al objeto `htmlAttributes` pasado a asistentes de etiquetas Input como `@Html.TextBox()`.

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>Nombres de expresión

El valor del atributo `asp-for` es una `ModelExpression` y la parte de la derecha de una expresión lambda. Por tanto, `asp-for="Property1"` se convierte en `m => m.Property1` en el código generado, motivo por el que no es necesario incluir el prefijo `Model`. Puede usar el carácter "\@" para iniciar una expresión insertada y moverla antes de `m.`:

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

Se genera el siguiente HTML:

```html
<input type="text" id="joe" name="joe" value="Joe">
```

Con las propiedades de colección, `asp-for="CollectionProperty[23].Member"` genera el mismo nombre que `asp-for="CollectionProperty[i].Member"` si `i` tiene el valor `23`.

Cuando ASP.NET Core MVC calcula el valor de `ModelExpression`, inspecciona varios orígenes, `ModelState` incluido. Fíjese en `<input type="text" asp-for="@Name">`. El atributo `value` calculado es el primer valor distinto de null de:

* La entrada `ModelState` con la clave "Name".
* El resultado de la expresión `Model.Name`.

### <a name="navigating-child-properties"></a>Navegar a las propiedades secundarias

También se puede navegar a las propiedades secundarias a través de la ruta de acceso de propiedades del modelo de vista. Pensemos en una clase de modelo más compleja que contiene una propiedad secundaria `Address`.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

En la vista, enlazamos a `Address.AddressLine1`:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

Se genera el siguiente código HTML para `Address.AddressLine1`:

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a>Colecciones y nombres de expresión

Como ejemplo, un modelo que contiene una matriz de `Colors`:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

El método de acción:

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

A continuación Razor se muestra cómo se obtiene acceso a un `Color` elemento específico:

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

La plantilla *Views/Shared/EditorTemplates/String.cshtml*:

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

Ejemplo en el que se usa `List<T>`:

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

A continuación Razor se muestra cómo recorrer en iteración una colección:

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

La plantilla *Views/Shared/EditorTemplates/ToDoItem.cshtml*:

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

Si es posible, debe usarse `foreach` si el valor se va a utilizar en un contexto equivalente a `asp-for` o `Html.DisplayFor`. En general, `for` es mejor que `foreach` (si el escenario lo permite), ya que no necesita asignar ningún enumerador; sin embargo, la evaluación de un indizador en una expresión LINQ puede resultar caro y, por tanto, se debe minimizar.

&nbsp;

>[!NOTE]
>El código de ejemplo comentado anterior muestra cómo reemplazaríamos la expresión lambda por el operador `@` para tener acceso a cada elemento `ToDoItem` de la lista.

## <a name="the-textarea-tag-helper"></a>Asistente de etiquetas de área de texto (Textarea)

El asistente de etiquetas `Textarea Tag Helper` es similar al asistente de etiquetas Input.

* Genera los `id` atributos y y `name` los atributos de validación de datos del modelo para un [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) elemento.

* Permite establecer tipado fuerte.

* Alternativa del asistente de HTML: `Html.TextAreaFor`

Sample:

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

Se genera el siguiente código HTML:

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a>Asistente de etiquetas Label

* Genera el título y el `for` atributo de la etiqueta en un [\<label>](https://www.w3.org/wiki/HTML/Elements/label) elemento para un nombre de expresión.

* Alternativa del asistente de HTML: `Html.LabelFor`.

`Label Tag Helper` proporciona las siguientes ventajas con respecto a un elemento HTML Label puro:

* Obtendrá automáticamente el valor de la etiqueta descriptiva del atributo `Display`. El nombre para mostrar que se busca puede cambiar con el tiempo y la combinación del atributo `Display`, y el asistente de etiquetas Label aplicará el elemento `Display` en cualquier lugar donde se use.

* El código fuente contiene menos marcado.

* Permite establecer tipado fuerte con la propiedad de modelo.

Sample:

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

Se genera el siguiente código HTML para el elemento `<label>`:

```html
<label for="Email">Email Address</label>
```

El asistente de etiquetas Label genera el valor de atributo `for` de "Email", que es el identificador asociado al elemento `<input>`. Los asistentes de etiquetas generan elementos `id` y `for` coherentes para que se puedan asociar correctamente. El título de este ejemplo proviene del atributo `Display`. Si el modelo no contuviera un atributo `Display`, el título sería el nombre de propiedad de la expresión.

## <a name="the-validation-tag-helpers"></a>Asistentes de etiquetas de validación

Hay dos asistentes de etiquetas de validación. `Validation Message Tag Helper` (que muestra un mensaje de validación relativo a una única propiedad del modelo) y `Validation Summary Tag Helper` (que muestra un resumen de los errores de validación). `Input Tag Helper` agrega atributos de validación del lado cliente HTML5 a los elementos de entrada en función de los atributos de anotación de datos de las clases del modelo. La validación también se realiza en el lado servidor. El asistente de etiquetas de validación muestra estos mensajes de error cuando se produce un error de validación.

### <a name="the-validation-message-tag-helper"></a>Asistente de etiquetas de mensaje de validación

* Agrega el atributo [HTML5 ](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` al elemento [span](https://developer.mozilla.org/docs/Web/HTML/Element/span), que adjunta los mensajes de error de validación en el campo de entrada de la propiedad de modelo especificada. Cuando se produce un error de validación en el lado cliente, [jQuery](https://jquery.com/) muestra el mensaje de error en el elemento `<span>`.

* La validación también tiene lugar en el lado servidor. Puede que JavaScript esté deshabilitado en los clientes, mientras que hay algunas validaciones que solo se pueden realizar en el lado servidor.

* Alternativa del asistente de HTML: `Html.ValidationMessageFor`

`Validation Message Tag Helper` se usa con el atributo `asp-validation-for` en un elemento HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span).

```cshtml
<span asp-validation-for="Email"></span>
```

El asistente de etiquetas de mensaje de validación generará el siguiente código HTML:

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

`Validation Message Tag Helper` suele usarse por lo general después de un asistente de etiquetas `Input` en la misma propiedad. Gracias a esto, se mostrarán todos los mensajes de error de validación cerca de la entrada que produjo el error.

> [!NOTE]
> Debe tener una vista con las referencias de script de JavaScript y de [jQuery](https://jquery.com/) adecuadas para la validación del lado cliente. Consulte [validación de modelos](../models/validation.md) para obtener más información.

Cuando se produce un error de validación del lado servidor (por ejemplo, porque haya una validación del lado servidor personalizada o porque la validación del lado cliente esté deshabilitada), MVC pone ese mensaje de error como cuerpo del elemento `<span>`.

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>Asistente de etiquetas de resumen de validación

* Tiene como destino todos los elementos `<div>` con el atributo `asp-validation-summary`.

* Alternativa del asistente de HTML: `@Html.ValidationSummary`

`Validation Summary Tag Helper` se usa para mostrar un resumen de los mensajes de validación. El valor de atributo `asp-validation-summary` puede ser cualquiera de los siguientes:

|asp-validation-summary|Mensajes de validación que se muestran|
|--- |--- |
|ValidationSummary.All|Nivel de modelo y de propiedad|
|ValidationSummary.ModelOnly|Modelo|
|ValidationSummary.None|None|

### <a name="sample"></a>Muestra

En el siguiente ejemplo, el modelo de datos tiene atributos `DataAnnotation`, lo que genera mensajes de error de validación sobre el elemento `<input>`.  Cuando se produce un error de validación, el asistente de etiquetas de validación muestra el mensaje de error:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

El código HTML generado (cuando el modelo es válido) es este:

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a>Asistente de etiquetas de selección (Select)

* Genera el elemento [select](https://www.w3.org/wiki/HTML/Elements/select) y el elemento asociado [option](https://www.w3.org/wiki/HTML/Elements/option) de las propiedades del modelo.

* Tiene `Html.DropDownListFor` y `Html.ListBoxFor` como alternativa del asistente de HTML.

`Select Tag Helper` `asp-for` especifica el nombre de la propiedad de modelo del elemento [select](https://www.w3.org/wiki/HTML/Elements/select), mientras que `asp-items` especifica los elementos [option](https://www.w3.org/wiki/HTML/Elements/option).  Por ejemplo:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

Sample:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

El método `Index` inicializa `CountryViewModel`, establece el país seleccionado y lo pasa a la vista `Index`.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

El método HTTP POST `Index` muestra la selección:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

La vista `Index`:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

Genera el siguiente código HTML (con la selección "CA"):

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> No se recomienda usar `ViewBag` o `ViewData` con el asistente de etiquetas Select. Un modelo de vista es más eficaz a la hora de proporcionar metadatos MVC y suele ser menos problemático.

El valor de atributo `asp-for` es un caso especial y no necesita un prefijo `Model`, mientras que los otros atributos del asistente de etiquetas sí (como `asp-items`).

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Enlace con enum

A menudo, conviene usar `<select>` con una propiedad `enum` y generar los elementos `SelectListItem` a partir de valores `enum`.

Sample:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

El método `GetEnumSelectList` genera un objeto `SelectList` para una enumeración.

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

Puede marcar la lista de enumeradores con el atributo `Display` para obtener una interfaz de usuario más completa:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

Se genera el siguiente código HTML:

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a>Agrupamiento de opciones

El  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) elemento HTML se genera cuando el modelo de vista contiene uno o más `SelectListGroup` objetos.

`CountryViewModelGroup` agrupa los elementos `SelectListItem` en los grupos "North America" y "Europe":

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

Aquí mostramos los dos grupos:

![Ejemplo de agrupamiento de opciones](working-with-forms/_static/grp.png)

El código HTML generado:

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a>Selección múltiple

El asistente de etiquetas Select generará automáticamente el atributo [multiple = "multiple"](https://w3c.github.io/html-reference/select.html) si la propiedad especificada en el atributo `asp-for` es `IEnumerable`. Por ejemplo, si tenemos el siguiente modelo:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

Con la siguiente vista:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

Se genera el siguiente código HTML:

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a>Sin selección

Si ve que usa la opción "sin especificar" en varias páginas, puede crear una plantilla para no tener que repetir el código HTML:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

La plantilla *Views/Shared/EditorTemplates/CountryViewModel.cshtml*:

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

La adición de [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elementos HTML no se limita al caso de *ninguna selección* . Por ejemplo, el método de acción y vista siguientes generarán un código HTML similar al código anterior:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-cshtml[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

Se seleccionará el elemento `<option>` correcto (que contenga el atributo `selected="selected"`) en función del valor real de `Country`.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:mvc/views/tag-helpers/intro>
* [HTML Form element (Elemento HTML Form)](https://www.w3.org/TR/html401/interact/forms.html)
* [Request Verification Token (Token de comprobación de solicitudes)](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [IAttributeAdapter Interface (Interfaz IAttributeAdapter)](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [Fragmentos de código de este documento](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
