---
title: Formularios y validación de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: b57e2a34f79691f7f2b1ed69cfad25de00c5ca13
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176212"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="d5fad-103">Formularios y validación de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5fad-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="d5fad-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d5fad-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d5fad-105">Blazor admite los formularios y la validación a través de [anotaciones de datos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="d5fad-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="d5fad-106">El siguiente tipo `ExampleModel` define la lógica de validación a través de anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="d5fad-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="d5fad-107">Un formulario se define mediante el componente <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span><span class="sxs-lookup"><span data-stu-id="d5fad-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="d5fad-108">En el siguiente formulario se reflejan los elementos, componentes y código de Razor típicos:</span><span class="sxs-lookup"><span data-stu-id="d5fad-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="d5fad-109">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="d5fad-109">In the preceding example:</span></span>

* <span data-ttu-id="d5fad-110">El formulario valida la entrada del usuario en el campo `name` por medio de la validación definida en el tipo `ExampleModel`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="d5fad-111">El modelo se crea en el bloque `@code` del componente y se conserva en un campo privado (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="d5fad-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="d5fad-112">El campo se asigna al atributo `Model` del elemento `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="d5fad-113">El elemento `@bind-Value` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText> enlaza:</span><span class="sxs-lookup"><span data-stu-id="d5fad-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="d5fad-114">La propiedad del modelo (`exampleModel.Name`) a la propiedad `Value` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText>.</span><span class="sxs-lookup"><span data-stu-id="d5fad-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="d5fad-115">Para más información sobre el enlace de propiedades, consulte <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span><span class="sxs-lookup"><span data-stu-id="d5fad-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="d5fad-116">Un delegado de evento de cambio a la propiedad `ValueChanged` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText>.</span><span class="sxs-lookup"><span data-stu-id="d5fad-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="d5fad-117">El componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> adjunta la compatibilidad con la validación mediante anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="d5fad-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="d5fad-118">El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> resume los mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="d5fad-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="d5fad-119">`HandleValidSubmit` se desencadena cuando el formulario se envía correctamente (supera la validación).</span><span class="sxs-lookup"><span data-stu-id="d5fad-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="d5fad-120">Componentes de formularios integrados</span><span class="sxs-lookup"><span data-stu-id="d5fad-120">Built-in forms components</span></span>

<span data-ttu-id="d5fad-121">Hay disponible un conjunto de componentes de entrada integrados para recibir y validar las entradas de usuario.</span><span class="sxs-lookup"><span data-stu-id="d5fad-121">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="d5fad-122">Las entradas se validan cuando se cambian y cuando un formulario se envía.</span><span class="sxs-lookup"><span data-stu-id="d5fad-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="d5fad-123">Los componentes de entrada disponibles se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="d5fad-123">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="d5fad-124">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="d5fad-124">Input component</span></span> | <span data-ttu-id="d5fad-125">Se representa como&hellip;</span><span class="sxs-lookup"><span data-stu-id="d5fad-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

<span data-ttu-id="d5fad-126">Todos los componentes de entrada, incluido <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, admiten atributos arbitrarios.</span><span class="sxs-lookup"><span data-stu-id="d5fad-126">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="d5fad-127">Cualquier atributo que no coincida con un parámetro de componente se agrega al elemento HTML representado.</span><span class="sxs-lookup"><span data-stu-id="d5fad-127">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="d5fad-128">Los componentes de entrada proporcionan un comportamiento predeterminado de validación al editarse y cambiar su clase CSS para reflejar el estado del campo.</span><span class="sxs-lookup"><span data-stu-id="d5fad-128">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="d5fad-129">Algunos componentes incluyen lógica de análisis de utilidad.</span><span class="sxs-lookup"><span data-stu-id="d5fad-129">Some components include useful parsing logic.</span></span> <span data-ttu-id="d5fad-130">Así, por ejemplo, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> e <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> tratan los valores no analizables sin problemas registrándolos como errores de validación.</span><span class="sxs-lookup"><span data-stu-id="d5fad-130">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="d5fad-131">Los tipos con capacidad para aceptar valores nulos también admiten la nulabilidad del campo de destino (por ejemplo, `int?`).</span><span class="sxs-lookup"><span data-stu-id="d5fad-131">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="d5fad-132">El siguiente tipo `Starship` define una lógica de validación mediante un conjunto de propiedades y de anotaciones de datos mayor que el del tipo `ExampleModel` anterior:</span><span class="sxs-lookup"><span data-stu-id="d5fad-132">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="d5fad-133">En el ejemplo anterior, `Description` es opcional porque no hay ninguna anotación de datos presente.</span><span class="sxs-lookup"><span data-stu-id="d5fad-133">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="d5fad-134">El siguiente formulario valida la entrada de usuario por medio de la validación definida en el modelo `Starship`:</span><span class="sxs-lookup"><span data-stu-id="d5fad-134">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="d5fad-135"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> crea un elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> como un [valor en cascada](xref:blazor/components/cascading-values-and-parameters) que lleva un seguimiento de los metadatos del proceso de edición, incluidos los campos que se han modificado y los mensajes de validación actuales.</span><span class="sxs-lookup"><span data-stu-id="d5fad-135">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="d5fad-136"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> también proporciona eventos prácticos de envíos válidos y no válidos (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span><span class="sxs-lookup"><span data-stu-id="d5fad-136">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="d5fad-137">También se puede usar <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> para desencadenar la validación y comprobar los valores de campo con código de validación personalizado.</span><span class="sxs-lookup"><span data-stu-id="d5fad-137">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="d5fad-138">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d5fad-138">In the following example:</span></span>

* <span data-ttu-id="d5fad-139">El método `HandleSubmit` se ejecuta cuando se selecciona el botón **`Submit`** .</span><span class="sxs-lookup"><span data-stu-id="d5fad-139">The `HandleSubmit` method runs when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="d5fad-140">El formulario se valida con el elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> de dicho formulario.</span><span class="sxs-lookup"><span data-stu-id="d5fad-140">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="d5fad-141">El formulario se sigue validando pasando <xref:Microsoft.AspNetCore.Components.Forms.EditContext> al método `ServerValidate` que llama a un punto de conexión de API web en el servidor (*no se muestra*).</span><span class="sxs-lookup"><span data-stu-id="d5fad-141">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="d5fad-142">Se ejecutará más código en función del resultado de la validación del lado cliente y del lado servidor, mediante la comprobación de `isValid`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-142">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="d5fad-143">InputText basado en el evento de entrada</span><span class="sxs-lookup"><span data-stu-id="d5fad-143">InputText based on the input event</span></span>

<span data-ttu-id="d5fad-144">Use el componente <xref:Microsoft.AspNetCore.Components.Forms.InputText> para crear un componente personalizado que usa el evento `input` en lugar del evento `change`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-144">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="d5fad-145">En el siguiente ejemplo, el componente `CustomInputText` hereda el componente `InputText` del marco y establece el enlace de eventos (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) en el evento `oninput`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-145">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="d5fad-146">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="d5fad-146">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="d5fad-147">El componente `CustomInputText` se puede usar en cualquier lugar donde <xref:Microsoft.AspNetCore.Components.Forms.InputText> se use:</span><span class="sxs-lookup"><span data-stu-id="d5fad-147">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="d5fad-148">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="d5fad-148">`Pages/TestForm.razor`:</span></span>

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="d5fad-149">Botones de radio</span><span class="sxs-lookup"><span data-stu-id="d5fad-149">Radio buttons</span></span>

<span data-ttu-id="d5fad-150">Al trabajar con botones de radio en un formulario, el enlace de datos se controla de manera diferente que otros elementos, ya que los botones de radio se evalúan como un grupo.</span><span class="sxs-lookup"><span data-stu-id="d5fad-150">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="d5fad-151">El valor de cada botón de radio es fijo, pero el valor del grupo de botones de radio es el valor del botón de radio seleccionado.</span><span class="sxs-lookup"><span data-stu-id="d5fad-151">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="d5fad-152">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="d5fad-152">The following example shows how to:</span></span>

* <span data-ttu-id="d5fad-153">Controlar el enlace de datos en un grupo de botones de radio</span><span class="sxs-lookup"><span data-stu-id="d5fad-153">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="d5fad-154">Admitir la validación usando un componente `InputRadio` personalizado</span><span class="sxs-lookup"><span data-stu-id="d5fad-154">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="d5fad-155">El siguiente elemento <xref:Microsoft.AspNetCore.Components.Forms.EditForm> usa el componente `InputRadio` anterior para obtener y validar una clasificación del usuario:</span><span class="sxs-lookup"><span data-stu-id="d5fad-155">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="d5fad-156">Enlace de opciones del elemento `<select>` a valores `null` de un objeto C#</span><span class="sxs-lookup"><span data-stu-id="d5fad-156">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="d5fad-157">No hay ninguna manera razonable de representar un valor de opción de elemento `<select>` como valor `null` de objeto C#, por los siguientes motivos:</span><span class="sxs-lookup"><span data-stu-id="d5fad-157">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="d5fad-158">Los atributos HTML no pueden tener valores `null`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-158">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="d5fad-159">El equivalente más cercano a `null` en HTML es la ausencia del atributo `value` de HTML del elemento `<option>`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-159">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="d5fad-160">Al seleccionar un `<option>` sin ningún atributo `value`, el explorador trata el valor como *contenido de texto* de ese elemento de `<option>`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-160">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="d5fad-161">El marco de Blazor no intenta suprimir el comportamiento predeterminado porque implicaría lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d5fad-161">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="d5fad-162">Crear una cadena de soluciones alternativas de casos especiales en el marco.</span><span class="sxs-lookup"><span data-stu-id="d5fad-162">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="d5fad-163">Hacer cambios importantes en el comportamiento actual del marco.</span><span class="sxs-lookup"><span data-stu-id="d5fad-163">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d5fad-164">El `null` más plausible equivalente en HTML es una *cadena vacía* `value`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-164">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="d5fad-165">El marco Blazor controla `null` en las conversiones de cadenas vacías para el enlace bidireccional al valor de `<select>`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-165">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d5fad-166">El marco Blazor no controla automáticamente `null` en las conversiones de cadenas vacías al intentar el enlace bidireccional al valor de `<select>`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-166">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="d5fad-167">Para obtener más información, vea [Corrección de enlaces `<select>` a un valor null (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="d5fad-167">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="d5fad-168">Compatibilidad con la validación</span><span class="sxs-lookup"><span data-stu-id="d5fad-168">Validation support</span></span>

<span data-ttu-id="d5fad-169">El componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> adjunta la compatibilidad con la validación mediante anotaciones de datos al elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> en cascada.</span><span class="sxs-lookup"><span data-stu-id="d5fad-169">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="d5fad-170">Este gesto explícito es necesario para habilitar la compatibilidad con la validación mediante anotaciones.</span><span class="sxs-lookup"><span data-stu-id="d5fad-170">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="d5fad-171">Para usar un sistema de validación distinto al de las anotaciones de datos, reemplace <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> por una implementación personalizada.</span><span class="sxs-lookup"><span data-stu-id="d5fad-171">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="d5fad-172">La implementación de ASP.NET Core está disponible para su inspección en el origen de referencia: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="d5fad-172">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="d5fad-173">Los vínculos anteriores al origen de referencia proporcionan código de la rama `master` del repositorio, que representa el desarrollo actual de la unidad de producto para la próxima versión de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5fad-173">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="d5fad-174">Para seleccionar la rama de una versión diferente, use el selector de ramas de GitHub (por ejemplo, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="d5fad-174">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

Blazor<span data-ttu-id="d5fad-175"> realiza dos tipos de validación:</span><span class="sxs-lookup"><span data-stu-id="d5fad-175"> performs two types of validation:</span></span>

* <span data-ttu-id="d5fad-176">La *validación del campo* se realiza cuando el usuario sale de un campo usando la tecla TAB.</span><span class="sxs-lookup"><span data-stu-id="d5fad-176">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="d5fad-177">Durante una validación del campo, el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> asocia al campo todos los resultados de validación notificados.</span><span class="sxs-lookup"><span data-stu-id="d5fad-177">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="d5fad-178">La *validación del modelo* se realiza cuando el usuario envía el formulario.</span><span class="sxs-lookup"><span data-stu-id="d5fad-178">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="d5fad-179">Durante una validación del modelo, el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> intenta determinar el campo en función del nombre del miembro que se indica en el resultado de la validación.</span><span class="sxs-lookup"><span data-stu-id="d5fad-179">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="d5fad-180">Los resultados de validación que no están asociados a un miembro individual se asocian al modelo en lugar de a un campo.</span><span class="sxs-lookup"><span data-stu-id="d5fad-180">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="d5fad-181">Resumen de validación y componentes de los mensajes de validación</span><span class="sxs-lookup"><span data-stu-id="d5fad-181">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="d5fad-182">El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>, que resume todos los mensajes de validación, es similar a la [aplicación auxiliar de etiquetas ValidationSummary](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="d5fad-182">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="d5fad-183">Mensajes de validación de salida de un modelo específico con el parámetro `Model`:</span><span class="sxs-lookup"><span data-stu-id="d5fad-183">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="d5fad-184">El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>, que muestra los mensajes de validación de un campo específico, es similar a la [aplicación auxiliar de etiquetas Validation Message](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="d5fad-184">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="d5fad-185">Especifique el campo de validación con el atributo `For` y una expresión lambda donde se indique la propiedad del modelo:</span><span class="sxs-lookup"><span data-stu-id="d5fad-185">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="d5fad-186">Los componentes <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> y <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> admiten atributos arbitrarios.</span><span class="sxs-lookup"><span data-stu-id="d5fad-186">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="d5fad-187">Cualquier atributo que no coincida con un parámetro de componente se agrega a los elementos `<div>` o `<ul>` generados.</span><span class="sxs-lookup"><span data-stu-id="d5fad-187">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="d5fad-188">Controle el estilo de los mensajes de validación en la hoja de estilos de la aplicación (`wwwroot/css/app.css` o `wwwroot/css/site.css`).</span><span class="sxs-lookup"><span data-stu-id="d5fad-188">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="d5fad-189">La clase `validation-message` predeterminada establece el color del texto de los mensajes de validación en rojo:</span><span class="sxs-lookup"><span data-stu-id="d5fad-189">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="d5fad-190">Atributos de validación personalizados</span><span class="sxs-lookup"><span data-stu-id="d5fad-190">Custom validation attributes</span></span>

<span data-ttu-id="d5fad-191">Para asegurarnos de que un resultado de validación está correctamente asociado a un campo cuando se usa un [atributo de validación personalizado](xref:mvc/models/validation#custom-attributes), pase el elemento <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> del contexto de validación al crear el elemento <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="d5fad-191">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <span data-ttu-id="d5fad-192"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> es `null`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-192"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="d5fad-193">No se admite la inserción de servicios para la validación en el método `IsValid`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-193">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

### <a name="blazor-data-annotations-validation-package"></a><span data-ttu-id="d5fad-194">Paquete de validación de anotaciones de datos de Blazor</span><span class="sxs-lookup"><span data-stu-id="d5fad-194">Blazor data annotations validation package</span></span>

<span data-ttu-id="d5fad-195">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) es un paquete que llena los huecos de experiencia de validación mediante el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="d5fad-195">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="d5fad-196">Actualmente, el paquete está en fase *experimental*.</span><span class="sxs-lookup"><span data-stu-id="d5fad-196">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="d5fad-197">Atributo [CompareProperty]</span><span class="sxs-lookup"><span data-stu-id="d5fad-197">[CompareProperty] attribute</span></span>

<span data-ttu-id="d5fad-198"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> no funciona bien con el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> porque no asocia el resultado de la validación a un miembro específico.</span><span class="sxs-lookup"><span data-stu-id="d5fad-198">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="d5fad-199">Esto puede generar un comportamiento incoherente entre la validación de nivel de campo y el momento en que el modelo completo se valida al enviarse.</span><span class="sxs-lookup"><span data-stu-id="d5fad-199">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="d5fad-200">El paquete *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) incluye un atributo de validación más, `ComparePropertyAttribute`, que pone fin a estas limitaciones.</span><span class="sxs-lookup"><span data-stu-id="d5fad-200">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="d5fad-201">En una aplicación Blazor, `[CompareProperty]` es un reemplazo directo del atributo [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute).</span><span class="sxs-lookup"><span data-stu-id="d5fad-201">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="d5fad-202">Tipos de colección, tipos complejos y modelos anidados</span><span class="sxs-lookup"><span data-stu-id="d5fad-202">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="d5fad-203"> proporciona compatibilidad para validar la entrada del formulario mediante anotaciones de datos con el elemento integrado <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>,</span><span class="sxs-lookup"><span data-stu-id="d5fad-203"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="d5fad-204">pero <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> solo valida las propiedades de nivel superior del modelo enlazadas al formulario que no son propiedades de tipos complejos o de colección.</span><span class="sxs-lookup"><span data-stu-id="d5fad-204">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="d5fad-205">Para validar el gráfico de objetos completo del modelo enlazado, incluidas las propiedades de tipos complejos o de colección, use el elemento `ObjectGraphDataAnnotationsValidator` proporcionado por el paquete *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation):</span><span class="sxs-lookup"><span data-stu-id="d5fad-205">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="d5fad-206">Anote las propiedades del modelo con `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-206">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="d5fad-207">En las siguientes clases de modelo, la clase `ShipDescription` contiene más anotaciones de datos para validar cuando el modelo está enlazado al formulario:</span><span class="sxs-lookup"><span data-stu-id="d5fad-207">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="d5fad-208">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="d5fad-208">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="d5fad-209">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="d5fad-209">`ShipDescription.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="d5fad-210">Habilitar el botón Enviar según la validación del formulario</span><span class="sxs-lookup"><span data-stu-id="d5fad-210">Enable the submit button based on form validation</span></span>

<span data-ttu-id="d5fad-211">Para habilitar el botón Enviar según la validación del formulario:</span><span class="sxs-lookup"><span data-stu-id="d5fad-211">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="d5fad-212">Use el elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> del formulario para asignar el modelo cuando el componente se inicialice.</span><span class="sxs-lookup"><span data-stu-id="d5fad-212">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="d5fad-213">Valide el formulario en la devolución de llamada <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> del contexto para habilitar y deshabilitar el botón Enviar.</span><span class="sxs-lookup"><span data-stu-id="d5fad-213">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="d5fad-214">Desenlace el controlador de eventos en el método `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="d5fad-214">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="d5fad-215">Para obtener más información, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="d5fad-215">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="d5fad-216">En el ejemplo anterior, establezca `formInvalid` en `false` en los siguientes casos:</span><span class="sxs-lookup"><span data-stu-id="d5fad-216">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="d5fad-217">El formulario se carga previamente con valores predeterminados válidos.</span><span class="sxs-lookup"><span data-stu-id="d5fad-217">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="d5fad-218">Quiere habilitar el botón Enviar cuando el formulario se cargue.</span><span class="sxs-lookup"><span data-stu-id="d5fad-218">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="d5fad-219">Un efecto secundario del método anterior es que un componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> se rellena con campos no válidos después de que el usuario interactúe con algún campo.</span><span class="sxs-lookup"><span data-stu-id="d5fad-219">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="d5fad-220">Este escenario se puede abordar de cualquiera de las siguientes maneras:</span><span class="sxs-lookup"><span data-stu-id="d5fad-220">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="d5fad-221">No use un componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> en el formulario.</span><span class="sxs-lookup"><span data-stu-id="d5fad-221">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="d5fad-222">Haga que el componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> esté visible cuando se seleccione el botón Enviar (por ejemplo, en un método `HandleValidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="d5fad-222">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="d5fad-223">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="d5fad-223">Troubleshoot</span></span>

> <span data-ttu-id="d5fad-224">InvalidOperationException: EditForm requiere un parámetro Model o un parámetro EditContext, pero no ambos.</span><span class="sxs-lookup"><span data-stu-id="d5fad-224">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="d5fad-225">Confirme que <xref:Microsoft.AspNetCore.Components.Forms.EditForm> tiene un parámetro <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> o <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span><span class="sxs-lookup"><span data-stu-id="d5fad-225">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="d5fad-226">Al asignar un parámetro <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> al formulario, confirme que se crea una instancia del tipo de modelo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d5fad-226">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
