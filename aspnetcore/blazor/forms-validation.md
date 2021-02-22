---
title: Formularios y validación de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: 012c8794b3d239ce93ac942000c7ec4f71d06cbf
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280010"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="edfcc-103">Formularios y validación de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="edfcc-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="edfcc-104">Blazor admite los formularios y la validación a través de [anotaciones de datos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="edfcc-104">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="edfcc-105">El siguiente tipo `ExampleModel` define la lógica de validación a través de anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="edfcc-105">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="edfcc-106">Un formulario se define mediante el componente <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-106">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="edfcc-107">En el siguiente formulario se reflejan los elementos, componentes y código de Razor típicos:</span><span class="sxs-lookup"><span data-stu-id="edfcc-107">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="edfcc-108">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="edfcc-108">In the preceding example:</span></span>

* <span data-ttu-id="edfcc-109">El formulario valida la entrada del usuario en el campo `name` por medio de la validación definida en el tipo `ExampleModel`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="edfcc-110">El modelo se crea en el bloque `@code` del componente y se conserva en un campo privado (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="edfcc-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="edfcc-111">El campo se asigna al atributo `Model` del elemento `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="edfcc-112">El elemento `@bind-Value` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText> enlaza:</span><span class="sxs-lookup"><span data-stu-id="edfcc-112">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="edfcc-113">La propiedad del modelo (`exampleModel.Name`) a la propiedad `Value` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-113">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="edfcc-114">Para más información sobre el enlace de propiedades, consulte <xref:blazor/components/data-binding#binding-with-component-parameters>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-114">For more information on property binding, see <xref:blazor/components/data-binding#binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="edfcc-115">Un delegado de evento de cambio a la propiedad `ValueChanged` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-115">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="edfcc-116">El [componente de validador](#validator-components) <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> adjunta la compatibilidad con la validación mediante anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="edfcc-116">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="edfcc-117">El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> resume los mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="edfcc-117">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="edfcc-118">`HandleValidSubmit` se desencadena cuando el formulario se envía correctamente (supera la validación).</span><span class="sxs-lookup"><span data-stu-id="edfcc-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="edfcc-119">Componentes de formularios integrados</span><span class="sxs-lookup"><span data-stu-id="edfcc-119">Built-in forms components</span></span>

<span data-ttu-id="edfcc-120">Hay disponible un conjunto de componentes integrados para recibir y validar las entradas de usuario.</span><span class="sxs-lookup"><span data-stu-id="edfcc-120">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="edfcc-121">Las entradas se validan cuando se cambian y cuando un formulario se envía.</span><span class="sxs-lookup"><span data-stu-id="edfcc-121">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="edfcc-122">Los componentes de entrada disponibles se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="edfcc-122">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="edfcc-123">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="edfcc-123">Input component</span></span> | <span data-ttu-id="edfcc-124">Se representa como&hellip;</span><span class="sxs-lookup"><span data-stu-id="edfcc-124">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="edfcc-125">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="edfcc-125">Input component</span></span> | <span data-ttu-id="edfcc-126">Se representa como&hellip;</span><span class="sxs-lookup"><span data-stu-id="edfcc-126">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> <span data-ttu-id="edfcc-127">Los componentes `InputRadio` y `InputRadioGroup` están disponibles en ASP.NET Core 5.0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="edfcc-127">The `InputRadio` and `InputRadioGroup` components are available in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="edfcc-128">Para más información, seleccione una versión 5.0 o posterior de este artículo.</span><span class="sxs-lookup"><span data-stu-id="edfcc-128">For more information, select a 5.0 or later version of this article.</span></span>

::: moniker-end

<span data-ttu-id="edfcc-129">Todos los componentes de entrada, incluido <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, admiten atributos arbitrarios.</span><span class="sxs-lookup"><span data-stu-id="edfcc-129">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="edfcc-130">Cualquier atributo que no coincida con un parámetro de componente se agrega al elemento HTML representado.</span><span class="sxs-lookup"><span data-stu-id="edfcc-130">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="edfcc-131">Los componentes de entrada proporcionan un comportamiento predeterminado de validación cuando se cambia un campo, incluida la actualización de la clase CSS para reflejar el estado del campo.</span><span class="sxs-lookup"><span data-stu-id="edfcc-131">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="edfcc-132">Algunos componentes incluyen lógica de análisis de utilidad.</span><span class="sxs-lookup"><span data-stu-id="edfcc-132">Some components include useful parsing logic.</span></span> <span data-ttu-id="edfcc-133">Así, por ejemplo, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> y <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> tratan los valores no analizables sin problemas registrándolos como errores de validación.</span><span class="sxs-lookup"><span data-stu-id="edfcc-133">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="edfcc-134">Los tipos con capacidad para aceptar valores nulos también admiten la nulabilidad del campo de destino (por ejemplo, `int?`).</span><span class="sxs-lookup"><span data-stu-id="edfcc-134">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="edfcc-135">El siguiente tipo `Starship` define una lógica de validación mediante un conjunto de propiedades y de anotaciones de datos mayor que el del tipo `ExampleModel` anterior:</span><span class="sxs-lookup"><span data-stu-id="edfcc-135">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="edfcc-136">En el ejemplo anterior, `Description` es opcional porque no hay ninguna anotación de datos presente.</span><span class="sxs-lookup"><span data-stu-id="edfcc-136">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="edfcc-137">El siguiente formulario valida la entrada de usuario por medio de la validación definida en el modelo `Starship`:</span><span class="sxs-lookup"><span data-stu-id="edfcc-137">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
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
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="edfcc-138"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> crea un elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> como un [valor en cascada](xref:blazor/components/cascading-values-and-parameters) que lleva un seguimiento de los metadatos del proceso de edición, incluidos los campos que se han modificado y los mensajes de validación actuales.</span><span class="sxs-lookup"><span data-stu-id="edfcc-138">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="edfcc-139">**Asigne** un objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **o** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> a un objeto <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-139">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="edfcc-140">No se admite la asignación de los dos y se generará un error **en tiempo de ejecución**.</span><span class="sxs-lookup"><span data-stu-id="edfcc-140">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="edfcc-141"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> proporciona eventos prácticos de envíos de formulario válidos y no válidos:</span><span class="sxs-lookup"><span data-stu-id="edfcc-141">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="edfcc-142">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> para utilizar código personalizado a fin de desencadenar la validación y comprobar los valores de los campos.</span><span class="sxs-lookup"><span data-stu-id="edfcc-142">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="edfcc-143">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="edfcc-143">In the following example:</span></span>

* <span data-ttu-id="edfcc-144">El método `HandleSubmit` se ejecuta cuando se selecciona el botón **`Submit`** .</span><span class="sxs-lookup"><span data-stu-id="edfcc-144">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="edfcc-145">El formulario se valida mediante una llamada a <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-145">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="edfcc-146">El código adicional se ejecuta según el resultado de la validación.</span><span class="sxs-lookup"><span data-stu-id="edfcc-146">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="edfcc-147">Coloque la lógica de negocios en el método asignado a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-147">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="edfcc-148">No existe una API de .NET Framework para borrar los mensajes de validación directamente de un objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-148">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="edfcc-149">Por tanto, normalmente no se recomienda agregar mensajes de validación a un objeto <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> nuevo en un formulario.</span><span class="sxs-lookup"><span data-stu-id="edfcc-149">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="edfcc-150">Para administrar los mensajes de validación, use un [componente de validador](#validator-components) con el [código de validación de lógica de negocios](#business-logic-validation), como se describe en este artículo.</span><span class="sxs-lookup"><span data-stu-id="edfcc-150">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="edfcc-151">Compatibilidad con nombres para mostrar</span><span class="sxs-lookup"><span data-stu-id="edfcc-151">Display name support</span></span>

<span data-ttu-id="edfcc-152">*Esta sección se aplica a ASP.NET Core en la versión candidata para lanzamiento 1 (RC1) de .NET 5 o una posterior.*</span><span class="sxs-lookup"><span data-stu-id="edfcc-152">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="edfcc-153">Los siguientes componentes integrados admiten nombres para mostrar con el parámetro `DisplayName`:</span><span class="sxs-lookup"><span data-stu-id="edfcc-153">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="edfcc-154">En el ejemplo de componente `InputDate` siguiente:</span><span class="sxs-lookup"><span data-stu-id="edfcc-154">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="edfcc-155">El nombre para mostrar (`DisplayName`) se establece en `birthday`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-155">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="edfcc-156">El componente se enlaza a la propiedad `BirthDate` como un tipo `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-156">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="edfcc-157">Si el usuario no proporciona un valor de fecha, el error de validación aparece de este modo:</span><span class="sxs-lookup"><span data-stu-id="edfcc-157">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="edfcc-158">Componentes de validador</span><span class="sxs-lookup"><span data-stu-id="edfcc-158">Validator components</span></span>

<span data-ttu-id="edfcc-159">Los componentes de validador admiten la validación de formularios mediante la administración de un objeto <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> para el objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> de un formulario.</span><span class="sxs-lookup"><span data-stu-id="edfcc-159">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="edfcc-160">El marco Blazor proporciona el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> para adjuntar la compatibilidad con la validación a formularios en función de [atributos de validación (anotaciones de datos)](xref:mvc/models/validation#validation-attributes).</span><span class="sxs-lookup"><span data-stu-id="edfcc-160">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="edfcc-161">Cree componentes de validador personalizados para procesar los mensajes de validación para diferentes formularios de la misma página o el mismo formulario en distintos pasos del procesamiento de formularios, por ejemplo, la validación del lado cliente seguida de la del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="edfcc-161">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="edfcc-162">El ejemplo de componente de validador que se muestra en esta sección, `CustomValidator`, se usa en las secciones siguientes de este artículo:</span><span class="sxs-lookup"><span data-stu-id="edfcc-162">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="edfcc-163">Validación de la lógica de negocios</span><span class="sxs-lookup"><span data-stu-id="edfcc-163">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="edfcc-164">Validación del servidor</span><span class="sxs-lookup"><span data-stu-id="edfcc-164">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="edfcc-165">En muchos casos, se pueden usar atributos de validación de anotación de datos personalizados en lugar de componentes de validador personalizados.</span><span class="sxs-lookup"><span data-stu-id="edfcc-165">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="edfcc-166">Los atributos personalizados que se aplican al modelo del formulario se activan con el uso del componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-166">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="edfcc-167">Cuando se usa con la validación del lado servidor, los atributos personalizados que se aplican al modelo deben ser ejecutables en el servidor.</span><span class="sxs-lookup"><span data-stu-id="edfcc-167">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="edfcc-168">Para obtener más información, vea <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-168">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="edfcc-169">Cree un componente de validador a partir de <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span><span class="sxs-lookup"><span data-stu-id="edfcc-169">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="edfcc-170">El objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> del formulario es un [parámetro en cascada](xref:blazor/components/cascading-values-and-parameters) del componente.</span><span class="sxs-lookup"><span data-stu-id="edfcc-170">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="edfcc-171">Cuando se inicializa el componente de validador, se crea un objeto <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> para mantener una lista actual de errores del formulario.</span><span class="sxs-lookup"><span data-stu-id="edfcc-171">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="edfcc-172">El almacén de mensajes recibe errores cuando el código del desarrollador del componente del formulario llama al método `DisplayErrors`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-172">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="edfcc-173">Los errores se pasan al método `DisplayErrors` en un objeto [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span><span class="sxs-lookup"><span data-stu-id="edfcc-173">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="edfcc-174">En el diccionario, la clave es el nombre del campo de formulario que tiene uno o más errores.</span><span class="sxs-lookup"><span data-stu-id="edfcc-174">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="edfcc-175">El valor es la lista de errores.</span><span class="sxs-lookup"><span data-stu-id="edfcc-175">The value is the error list.</span></span>
* <span data-ttu-id="edfcc-176">Los mensajes se borran cuando se produce alguno de los casos siguientes:</span><span class="sxs-lookup"><span data-stu-id="edfcc-176">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="edfcc-177">La validación se solicita en el objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> cuando se genera el evento <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-177">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="edfcc-178">Todos los errores se borran.</span><span class="sxs-lookup"><span data-stu-id="edfcc-178">All of the errors are cleared.</span></span>
  * <span data-ttu-id="edfcc-179">Un campo cambia en el formulario cuando se genera el evento <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-179">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="edfcc-180">Solo se borran los errores del campo.</span><span class="sxs-lookup"><span data-stu-id="edfcc-180">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="edfcc-181">El código del desarrollador llama al método `ClearErrors`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-181">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="edfcc-182">Todos los errores se borran.</span><span class="sxs-lookup"><span data-stu-id="edfcc-182">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

public class CustomValidator : ComponentBase
{
    private ValidationMessageStore messageStore;

    [CascadingParameter]
    private EditContext CurrentEditContext { get; set; }

    protected override void OnInitialized()
    {
        if (CurrentEditContext == null)
        {
            throw new InvalidOperationException(
                $"{nameof(CustomValidator)} requires a cascading " +
                $"parameter of type {nameof(EditContext)}. " +
                $"For example, you can use {nameof(CustomValidator)} " +
                $"inside an {nameof(EditForm)}.");
        }

        messageStore = new ValidationMessageStore(CurrentEditContext);

        CurrentEditContext.OnValidationRequested += (s, e) => 
            messageStore.Clear();
        CurrentEditContext.OnFieldChanged += (s, e) => 
            messageStore.Clear(e.FieldIdentifier);
    }

    public void DisplayErrors(Dictionary<string, List<string>> errors)
    {
        foreach (var err in errors)
        {
            messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
        }

        CurrentEditContext.NotifyValidationStateChanged();
    }

    public void ClearErrors()
    {
        messageStore.Clear();
        CurrentEditContext.NotifyValidationStateChanged();
    }
}
```

> [!NOTE]
> <span data-ttu-id="edfcc-183">Las expresiones lambda anónimas son controladores de eventos registrados para <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> y <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="edfcc-183">Anonymous lambda expressions are registered event handlers for <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> and <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> in the preceding example.</span></span> <span data-ttu-id="edfcc-184">No es necesario implementar <xref:System.IDisposable> y cancelar la suscripción de los delegados de eventos en este escenario.</span><span class="sxs-lookup"><span data-stu-id="edfcc-184">It isn't necessary to implement <xref:System.IDisposable> and unsubscribe the event delegates in this scenario.</span></span> <span data-ttu-id="edfcc-185">Para obtener más información, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-185">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="business-logic-validation"></a><span data-ttu-id="edfcc-186">Validación de la lógica de negocios</span><span class="sxs-lookup"><span data-stu-id="edfcc-186">Business logic validation</span></span>

<span data-ttu-id="edfcc-187">La validación de la lógica de negocios se puede realizar con un [componente de validador](#validator-components) que recibe errores del formulario en un diccionario.</span><span class="sxs-lookup"><span data-stu-id="edfcc-187">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="edfcc-188">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="edfcc-188">In the following example:</span></span>

* <span data-ttu-id="edfcc-189">Se usa el componente `CustomValidator` de la sección [Componentes de validador](#validator-components) de este artículo.</span><span class="sxs-lookup"><span data-stu-id="edfcc-189">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="edfcc-190">La validación necesita un valor para la descripción del envío (`Description`) si el usuario selecciona la clasificación de envío `Defense` (`Classification`).</span><span class="sxs-lookup"><span data-stu-id="edfcc-190">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="edfcc-191">Cuando los mensajes de validación se establecen en el componente, se agregan al componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> del validador y se muestran en el objeto <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span><span class="sxs-lookup"><span data-stu-id="edfcc-191">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="edfcc-192">Como alternativa al uso de [componentes de validación](#validator-components), se pueden utilizar atributos de validación de anotación de datos.</span><span class="sxs-lookup"><span data-stu-id="edfcc-192">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="edfcc-193">Los atributos personalizados que se aplican al modelo del formulario se activan con el uso del componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-193">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="edfcc-194">Cuando se usa con la validación del lado servidor, los atributos deben ser ejecutables en el servidor.</span><span class="sxs-lookup"><span data-stu-id="edfcc-194">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="edfcc-195">Para obtener más información, vea <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-195">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="edfcc-196">Validación del servidor</span><span class="sxs-lookup"><span data-stu-id="edfcc-196">Server validation</span></span>

<span data-ttu-id="edfcc-197">La validación del servidor se puede realizar con un [componente de validador](#validator-components) de servidor:</span><span class="sxs-lookup"><span data-stu-id="edfcc-197">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="edfcc-198">Procese la validación del lado cliente en el formulario con el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-198">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="edfcc-199">Cuando el formulario pasa la validación del lado cliente (se llama a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>), envíe el objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> a una API de servidor backend para el procesamiento del formulario.</span><span class="sxs-lookup"><span data-stu-id="edfcc-199">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="edfcc-200">Procese la validación del modelo en el servidor.</span><span class="sxs-lookup"><span data-stu-id="edfcc-200">Process model validation on the server.</span></span>
* <span data-ttu-id="edfcc-201">La API del servidor incluye la validación de anotaciones de datos del marco integrada y la lógica de validación personalizada proporcionada por el desarrollador.</span><span class="sxs-lookup"><span data-stu-id="edfcc-201">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="edfcc-202">Si la validación se supera en el servidor, procese el formulario y devuelva un código de estado correcto (*200 - Correcto*).</span><span class="sxs-lookup"><span data-stu-id="edfcc-202">If validation passes on the server, process the form and send back a success status code (*200 - OK*).</span></span> <span data-ttu-id="edfcc-203">Si se produce un error en la validación, devuelva un código de estado de error (*400 - Solicitud incorrecta*) y los errores de validación de campos.</span><span class="sxs-lookup"><span data-stu-id="edfcc-203">If validation fails, return a failure status code (*400 - Bad Request*) and the field validation errors.</span></span>
* <span data-ttu-id="edfcc-204">Si es correcto, puede deshabilitar el formulario, o bien mostrar los errores.</span><span class="sxs-lookup"><span data-stu-id="edfcc-204">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="edfcc-205">El ejemplo siguiente se basa en:</span><span class="sxs-lookup"><span data-stu-id="edfcc-205">The following example is based on:</span></span>

* <span data-ttu-id="edfcc-206">Una solución de Blazor hospedada creada mediante la [plantilla de proyecto hospedado de Blazor](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="edfcc-206">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="edfcc-207">El ejemplo se puede usar con cualquiera de las soluciones de Blazor hospedadas que se describen en la [documentación de seguridad y Identity](xref:blazor/security/webassembly/index#implementation-guidance).</span><span class="sxs-lookup"><span data-stu-id="edfcc-207">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="edfcc-208">El ejemplo de formulario *Starfleet Starship Database* de la sección [Componentes de formularios integrados](#built-in-forms-components) anterior.</span><span class="sxs-lookup"><span data-stu-id="edfcc-208">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="edfcc-209">El componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> del marco Blazor.</span><span class="sxs-lookup"><span data-stu-id="edfcc-209">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="edfcc-210">El componente `CustomValidator` mostrado en la sección [Componentes de validador](#validator-components).</span><span class="sxs-lookup"><span data-stu-id="edfcc-210">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="edfcc-211">En el ejemplo siguiente, la API de servidor valida que se ha proporcionado un valor para la descripción del envío (`Description`) si el usuario selecciona la clasificación de envío `Defense` (`Classification`).</span><span class="sxs-lookup"><span data-stu-id="edfcc-211">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="edfcc-212">Coloque el modelo `Starship` en el proyecto `Shared` de la solución para que las aplicaciones cliente y servidor puedan usarlo.</span><span class="sxs-lookup"><span data-stu-id="edfcc-212">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="edfcc-213">Como en el modelo se necesitan anotaciones de datos, agregue una referencia de paquete para [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) al archivo de proyecto del proyecto `Shared`:</span><span class="sxs-lookup"><span data-stu-id="edfcc-213">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="edfcc-214">Para determinar la versión más reciente del paquete que no sea de versión preliminar, vea el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span><span class="sxs-lookup"><span data-stu-id="edfcc-214">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="edfcc-215">En el proyecto de API de servidor, agregue un controlador para procesar las solicitudes de validación de Starship (`Controllers/StarshipValidation.cs`) y devuelva los mensajes de validación con errores:</span><span class="sxs-lookup"><span data-stu-id="edfcc-215">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace {ASSEMBLY NAME}.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {Message}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

<span data-ttu-id="edfcc-216">En el ejemplo anterior, el marcador de posición `{ASSEMBLY NAME}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample.Server`).</span><span class="sxs-lookup"><span data-stu-id="edfcc-216">In the preceding example, the placeholder `{ASSEMBLY NAME}` is the app's assembly name (for example, `BlazorSample.Server`).</span></span>

<span data-ttu-id="edfcc-217">Cuando se produce un error de validación de enlace de modelos en el servidor, un objeto [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) devuelve normalmente una [respuesta de solicitud incorrecta predeterminada](xref:web-api/index#default-badrequest-response) con un objeto <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-217">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="edfcc-218">La respuesta contiene otros datos además de los errores de validación, como se muestra en el ejemplo siguiente cuando no se envían todos los campos del formulario *Starfleet Starship Database* y no supera la validación:</span><span class="sxs-lookup"><span data-stu-id="edfcc-218">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

<span data-ttu-id="edfcc-219">Si la API del servidor devuelve la respuesta JSON predeterminada anterior, el cliente puede analizarla para obtener los elementos secundarios del nodo `errors`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-219">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="edfcc-220">Pero no es conveniente analizar el archivo.</span><span class="sxs-lookup"><span data-stu-id="edfcc-220">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="edfcc-221">El análisis de JSON requiere código adicional después de llamar a <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> a fin de generar un objeto [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) de errores para el procesamiento de errores de validación del formulario.</span><span class="sxs-lookup"><span data-stu-id="edfcc-221">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="edfcc-222">Idealmente, la API de servidor solo debe devolver los errores de validación:</span><span class="sxs-lookup"><span data-stu-id="edfcc-222">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="edfcc-223">Para modificar la respuesta de la API de servidor a fin de que solo devuelva los errores de validación, cambie el delegado que se invoca en las acciones anotadas con <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-223">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="edfcc-224">Para el punto de conexión de API (`/StarshipValidation`), devuelva un objeto <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> con el objeto <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-224">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="edfcc-225">Para cualquier otro punto de conexión de API, conserve el comportamiento predeterminado mediante la devolución el resultado del objeto con un objeto <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> nuevo:</span><span class="sxs-lookup"><span data-stu-id="edfcc-225">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

<span data-ttu-id="edfcc-226">Para obtener más información, vea <xref:web-api/handle-errors#validation-failure-error-response>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-226">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="edfcc-227">En el proyecto de cliente, agregue el componente de validador mostrado en la sección [Componentes de validador](#validator-components).</span><span class="sxs-lookup"><span data-stu-id="edfcc-227">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="edfcc-228">En el proyecto de cliente, el formulario *Starfleet Starship Database* se actualiza para mostrar los errores de validación del servidor con la ayuda del componente `CustomValidator`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-228">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="edfcc-229">Cuando la API de servidor devuelve mensajes de validación, se agregan al objeto <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>del componente `CustomValidator`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-229">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="edfcc-230">Los errores están disponibles en el objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> del formulario para representarlos mediante <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span><span class="sxs-lookup"><span data-stu-id="edfcc-230">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
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
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {Message}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="edfcc-231">Como alternativa a los [componentes de validación](#validator-components), se pueden usar atributos de validación de anotación de datos.</span><span class="sxs-lookup"><span data-stu-id="edfcc-231">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="edfcc-232">Los atributos personalizados que se aplican al modelo del formulario se activan con el uso del componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-232">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="edfcc-233">Cuando se usa con la validación del lado servidor, los atributos deben ser ejecutables en el servidor.</span><span class="sxs-lookup"><span data-stu-id="edfcc-233">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="edfcc-234">Para obtener más información, vea <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-234">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="edfcc-235">El enfoque de validación del lado servidor de esta sección es adecuado para cualquiera de los ejemplos de soluciones hospedadas de Blazor WebAssembly de este conjunto de documentación:</span><span class="sxs-lookup"><span data-stu-id="edfcc-235">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="edfcc-236">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="edfcc-236">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="edfcc-237">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="edfcc-237">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="edfcc-238">Servidor de Identity</span><span class="sxs-lookup"><span data-stu-id="edfcc-238">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="edfcc-239">InputText basado en el evento de entrada</span><span class="sxs-lookup"><span data-stu-id="edfcc-239">InputText based on the input event</span></span>

<span data-ttu-id="edfcc-240">Use el componente <xref:Microsoft.AspNetCore.Components.Forms.InputText> para crear un componente personalizado que usa el evento `input` en lugar del evento `change`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-240">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="edfcc-241">En el ejemplo siguiente, el componente `CustomInputText` hereda el componente `InputText` del marco y establece el enlace de los eventos en el evento `oninput`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-241">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets event binding to the `oninput` event.</span></span>

<span data-ttu-id="edfcc-242">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="edfcc-242">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input @attributes="AdditionalAttributes" class="@CssClass" 
    @bind="CurrentValueAsString" @bind:event="oninput" />
```

<span data-ttu-id="edfcc-243">El componente `CustomInputText` se puede usar en cualquier lugar donde <xref:Microsoft.AspNetCore.Components.Forms.InputText> se use:</span><span class="sxs-lookup"><span data-stu-id="edfcc-243">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="edfcc-244">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="edfcc-244">`Pages/TestForm.razor`:</span></span>

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
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
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="edfcc-245">Botones de radio</span><span class="sxs-lookup"><span data-stu-id="edfcc-245">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="edfcc-246">Use componentes `InputRadio` con el componente `InputRadioGroup` para crear un grupo de botones de radio.</span><span class="sxs-lookup"><span data-stu-id="edfcc-246">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="edfcc-247">En el ejemplo siguiente, las propiedades se agregan al modelo `Starship` descrito en la sección [Componentes de formularios integrados](#built-in-forms-components):</span><span class="sxs-lookup"><span data-stu-id="edfcc-247">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

<span data-ttu-id="edfcc-248">Agregue el elemento `enums` siguiente a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="edfcc-248">Add the following `enums` to the app.</span></span> <span data-ttu-id="edfcc-249">Cree un nuevo archivo que contenga `enums` o agregue `enums` al archivo `Starship.cs`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-249">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="edfcc-250">Haga que `enums` sea accesible para el modelo `Starship` y el formulario *Starfleet Starship Database*:</span><span class="sxs-lookup"><span data-stu-id="edfcc-250">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="edfcc-251">Actualice el formulario *Starfleet Starship Database* descrito en la sección [Componentes de formularios integrados](#built-in-forms-components).</span><span class="sxs-lookup"><span data-stu-id="edfcc-251">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="edfcc-252">Agregue los componentes para generar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="edfcc-252">Add the components to produce:</span></span>

* <span data-ttu-id="edfcc-253">Un grupo de botones de radio para el fabricante del envío.</span><span class="sxs-lookup"><span data-stu-id="edfcc-253">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="edfcc-254">Un grupo de botones de radio anidados para el color y el motor de envío.</span><span class="sxs-lookup"><span data-stu-id="edfcc-254">A nested radio button group for ship color and engine.</span></span>

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> <span data-ttu-id="edfcc-255">Si se omite `Name`, los componentes de `InputRadio` los agrupa su antecesor más reciente.</span><span class="sxs-lookup"><span data-stu-id="edfcc-255">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="edfcc-256">Al trabajar con botones de radio en un formulario, el enlace de datos se controla de manera diferente que otros elementos, ya que los botones de radio se evalúan como un grupo.</span><span class="sxs-lookup"><span data-stu-id="edfcc-256">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="edfcc-257">El valor de cada botón de radio es fijo, pero el valor del grupo de botones de radio es el valor del botón de radio seleccionado.</span><span class="sxs-lookup"><span data-stu-id="edfcc-257">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="edfcc-258">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="edfcc-258">The following example shows how to:</span></span>

* <span data-ttu-id="edfcc-259">Controlar el enlace de datos en un grupo de botones de radio</span><span class="sxs-lookup"><span data-stu-id="edfcc-259">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="edfcc-260">Admitir la validación usando un componente `InputRadio` personalizado</span><span class="sxs-lookup"><span data-stu-id="edfcc-260">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="edfcc-261">El siguiente elemento <xref:Microsoft.AspNetCore.Components.Forms.EditForm> usa el componente `InputRadio` anterior para obtener y validar una clasificación del usuario:</span><span class="sxs-lookup"><span data-stu-id="edfcc-261">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
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
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="edfcc-262">Enlace de opciones del elemento `<select>` a valores `null` de un objeto C#</span><span class="sxs-lookup"><span data-stu-id="edfcc-262">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="edfcc-263">No hay ninguna manera razonable de representar un valor de opción de elemento `<select>` como valor `null` de objeto C#, por los siguientes motivos:</span><span class="sxs-lookup"><span data-stu-id="edfcc-263">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="edfcc-264">Los atributos HTML no pueden tener valores `null`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-264">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="edfcc-265">El equivalente más cercano a `null` en HTML es la ausencia del atributo `value` de HTML del elemento `<option>`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-265">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="edfcc-266">Al seleccionar un `<option>` sin ningún atributo `value`, el explorador trata el valor como *contenido de texto* de ese elemento de `<option>`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-266">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="edfcc-267">El marco de Blazor no intenta suprimir el comportamiento predeterminado porque implicaría lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="edfcc-267">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="edfcc-268">Crear una cadena de soluciones alternativas de casos especiales en el marco.</span><span class="sxs-lookup"><span data-stu-id="edfcc-268">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="edfcc-269">Hacer cambios importantes en el comportamiento actual del marco.</span><span class="sxs-lookup"><span data-stu-id="edfcc-269">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="edfcc-270">El `null` más plausible equivalente en HTML es una *cadena vacía* `value`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-270">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="edfcc-271">El marco Blazor controla `null` en las conversiones de cadenas vacías para el enlace bidireccional al valor de `<select>`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-271">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="edfcc-272">El marco Blazor no controla automáticamente `null` en las conversiones de cadenas vacías al intentar el enlace bidireccional al valor de `<select>`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-272">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="edfcc-273">Para obtener más información, vea [Corrección de enlaces `<select>` a un valor null (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="edfcc-273">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="edfcc-274">Compatibilidad con la validación</span><span class="sxs-lookup"><span data-stu-id="edfcc-274">Validation support</span></span>

<span data-ttu-id="edfcc-275">El componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> adjunta la compatibilidad con la validación mediante anotaciones de datos al elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> en cascada.</span><span class="sxs-lookup"><span data-stu-id="edfcc-275">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="edfcc-276">Este gesto explícito es necesario para habilitar la compatibilidad con la validación mediante anotaciones.</span><span class="sxs-lookup"><span data-stu-id="edfcc-276">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="edfcc-277">Para usar un sistema de validación distinto al de las anotaciones de datos, reemplace <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> por una implementación personalizada.</span><span class="sxs-lookup"><span data-stu-id="edfcc-277">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="edfcc-278">La implementación de ASP.NET Core está disponible para su inspección en el origen de referencia: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="edfcc-278">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="edfcc-279">Los vínculos anteriores al origen de referencia proporcionan código de la rama `master` del repositorio, que representa el desarrollo actual de la unidad de producto para la próxima versión de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="edfcc-279">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="edfcc-280">Para seleccionar la rama de una versión diferente, use el selector de ramas de GitHub (por ejemplo, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="edfcc-280">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="edfcc-281">Blazor realiza dos tipos de validación:</span><span class="sxs-lookup"><span data-stu-id="edfcc-281">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="edfcc-282">La *validación del campo* se realiza cuando el usuario sale de un campo usando la tecla TAB.</span><span class="sxs-lookup"><span data-stu-id="edfcc-282">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="edfcc-283">Durante una validación del campo, el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> asocia al campo todos los resultados de validación notificados.</span><span class="sxs-lookup"><span data-stu-id="edfcc-283">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="edfcc-284">La *validación del modelo* se realiza cuando el usuario envía el formulario.</span><span class="sxs-lookup"><span data-stu-id="edfcc-284">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="edfcc-285">Durante una validación del modelo, el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> intenta determinar el campo en función del nombre del miembro que se indica en el resultado de la validación.</span><span class="sxs-lookup"><span data-stu-id="edfcc-285">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="edfcc-286">Los resultados de validación que no están asociados a un miembro individual se asocian al modelo en lugar de a un campo.</span><span class="sxs-lookup"><span data-stu-id="edfcc-286">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="edfcc-287">Resumen de validación y componentes de los mensajes de validación</span><span class="sxs-lookup"><span data-stu-id="edfcc-287">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="edfcc-288">El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>, que resume todos los mensajes de validación, es similar a la [aplicación auxiliar de etiquetas ValidationSummary](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="edfcc-288">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="edfcc-289">Mensajes de validación de salida de un modelo específico con el parámetro `Model`:</span><span class="sxs-lookup"><span data-stu-id="edfcc-289">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="edfcc-290">El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>, que muestra los mensajes de validación de un campo específico, es similar a la [aplicación auxiliar de etiquetas Validation Message](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="edfcc-290">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="edfcc-291">Especifique el campo de validación con el atributo `For` y una expresión lambda donde se indique la propiedad del modelo:</span><span class="sxs-lookup"><span data-stu-id="edfcc-291">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="edfcc-292">Los componentes <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> y <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> admiten atributos arbitrarios.</span><span class="sxs-lookup"><span data-stu-id="edfcc-292">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="edfcc-293">Cualquier atributo que no coincida con un parámetro de componente se agrega a los elementos `<div>` o `<ul>` generados.</span><span class="sxs-lookup"><span data-stu-id="edfcc-293">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="edfcc-294">Controle el estilo de los mensajes de validación en la hoja de estilos de la aplicación (`wwwroot/css/app.css` o `wwwroot/css/site.css`).</span><span class="sxs-lookup"><span data-stu-id="edfcc-294">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="edfcc-295">La clase `validation-message` predeterminada establece el color del texto de los mensajes de validación en rojo:</span><span class="sxs-lookup"><span data-stu-id="edfcc-295">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="edfcc-296">Atributos de validación personalizados</span><span class="sxs-lookup"><span data-stu-id="edfcc-296">Custom validation attributes</span></span>

<span data-ttu-id="edfcc-297">Para asegurarnos de que un resultado de validación está correctamente asociado a un campo cuando se usa un [atributo de validación personalizado](xref:mvc/models/validation#custom-attributes), pase el elemento <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> del contexto de validación al crear el elemento <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="edfcc-297">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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
> <span data-ttu-id="edfcc-298"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> es `null`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-298"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="edfcc-299">No se admite la inserción de servicios para la validación en el método `IsValid`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-299">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a><span data-ttu-id="edfcc-300">Atributos de clase de validación personalizados</span><span class="sxs-lookup"><span data-stu-id="edfcc-300">Custom validation class attributes</span></span>

<span data-ttu-id="edfcc-301">Los nombres de clase de validación personalizados son útiles al integrar con marcos de CSS, como [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="edfcc-301">Custom validation class names are useful when integrating with CSS frameworks, such as [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="edfcc-302">Para especificar nombres de clase de validación personalizados, cree una clase derivada de `FieldCssClassProvider` y establezca dicha clase en la instancia <xref:Microsoft.AspNetCore.Components.Forms.EditContext>:</span><span class="sxs-lookup"><span data-stu-id="edfcc-302">To specify custom validation class names, create a class derived from `FieldCssClassProvider` and set the class on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instance:</span></span>

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="blazor-data-annotations-validation-package"></a><span data-ttu-id="edfcc-303">Paquete de validación de anotaciones de datos de Blazor</span><span class="sxs-lookup"><span data-stu-id="edfcc-303">Blazor data annotations validation package</span></span>

<span data-ttu-id="edfcc-304">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) es un paquete que llena los huecos de experiencia de validación mediante el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-304">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="edfcc-305">Actualmente, el paquete está en fase *experimental*.</span><span class="sxs-lookup"><span data-stu-id="edfcc-305">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="edfcc-306">En [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) hay una *versión candidata para lanzamiento* más reciente del paquete [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Por ahora, siga usando el paquete de versión candidata para lanzamiento *experimental*.</span><span class="sxs-lookup"><span data-stu-id="edfcc-306">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="edfcc-307">El ensamblado del paquete podría moverse al marco o al runtime en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="edfcc-307">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="edfcc-308">Vea los [anuncios del repositorio de GitHub](https://github.com/aspnet/Announcements), el [repositorio dotnet/aspnetcore de GitHub](https://github.com/dotnet/aspnetcore), o bien la sección de este tema para obtener más actualizaciones.</span><span class="sxs-lookup"><span data-stu-id="edfcc-308">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

::: moniker range="< aspnetcore-5.0"

### <a name="compareproperty-attribute"></a><span data-ttu-id="edfcc-309">Atributo `[CompareProperty]`</span><span class="sxs-lookup"><span data-stu-id="edfcc-309">`[CompareProperty]` attribute</span></span>

<span data-ttu-id="edfcc-310"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> no funciona bien con el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> porque no asocia el resultado de la validación a un miembro específico.</span><span class="sxs-lookup"><span data-stu-id="edfcc-310">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="edfcc-311">Esto puede generar un comportamiento incoherente entre la validación de nivel de campo y el momento en que el modelo completo se valida al enviarse.</span><span class="sxs-lookup"><span data-stu-id="edfcc-311">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="edfcc-312">El paquete *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) incluye un atributo de validación más, `ComparePropertyAttribute`, que pone fin a estas limitaciones.</span><span class="sxs-lookup"><span data-stu-id="edfcc-312">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="edfcc-313">En una aplicación Blazor, `[CompareProperty]` es un reemplazo directo del [atributo `[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute).</span><span class="sxs-lookup"><span data-stu-id="edfcc-313">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]` attribute](xref:System.ComponentModel.DataAnnotations.CompareAttribute).</span></span>

::: moniker-end

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="edfcc-314">Tipos de colección, tipos complejos y modelos anidados</span><span class="sxs-lookup"><span data-stu-id="edfcc-314">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="edfcc-315">Blazor proporciona compatibilidad para validar la entrada del formulario mediante anotaciones de datos con el elemento integrado <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>,</span><span class="sxs-lookup"><span data-stu-id="edfcc-315">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="edfcc-316">pero <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> solo valida las propiedades de nivel superior del modelo enlazadas al formulario que no son propiedades de tipos complejos o de colección.</span><span class="sxs-lookup"><span data-stu-id="edfcc-316">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="edfcc-317">Para validar el gráfico de objetos completo del modelo enlazado, incluidas las propiedades de tipos complejos o de colección, use el elemento `ObjectGraphDataAnnotationsValidator` proporcionado por el paquete *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation):</span><span class="sxs-lookup"><span data-stu-id="edfcc-317">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="edfcc-318">Anote las propiedades del modelo con `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-318">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="edfcc-319">En las siguientes clases de modelo, la clase `ShipDescription` contiene más anotaciones de datos para validar cuando el modelo está enlazado al formulario:</span><span class="sxs-lookup"><span data-stu-id="edfcc-319">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="edfcc-320">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="edfcc-320">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

<span data-ttu-id="edfcc-321">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="edfcc-321">`ShipDescription.cs`:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="edfcc-322">Habilitar el botón Enviar según la validación del formulario</span><span class="sxs-lookup"><span data-stu-id="edfcc-322">Enable the submit button based on form validation</span></span>

<span data-ttu-id="edfcc-323">Para habilitar el botón Enviar según la validación del formulario:</span><span class="sxs-lookup"><span data-stu-id="edfcc-323">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="edfcc-324">Use el elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> del formulario para asignar el modelo cuando el componente se inicialice.</span><span class="sxs-lookup"><span data-stu-id="edfcc-324">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="edfcc-325">Valide el formulario en la devolución de llamada <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> del contexto para habilitar y deshabilitar el botón Enviar.</span><span class="sxs-lookup"><span data-stu-id="edfcc-325">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="edfcc-326">Implemente <xref:System.IDisposable> y cancele la suscripción del controlador de eventos en el método `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="edfcc-326">Implement <xref:System.IDisposable> and unsubscribe the event handler in the `Dispose` method.</span></span> <span data-ttu-id="edfcc-327">Para obtener más información, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-327">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="edfcc-328">Cuando use un objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, no asigne también una instancia de <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> al objeto <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-328">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
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

<span data-ttu-id="edfcc-329">En el ejemplo anterior, establezca `formInvalid` en `false` en los siguientes casos:</span><span class="sxs-lookup"><span data-stu-id="edfcc-329">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="edfcc-330">El formulario se carga previamente con valores predeterminados válidos.</span><span class="sxs-lookup"><span data-stu-id="edfcc-330">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="edfcc-331">Quiere habilitar el botón Enviar cuando el formulario se cargue.</span><span class="sxs-lookup"><span data-stu-id="edfcc-331">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="edfcc-332">Un efecto secundario del método anterior es que un componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> se rellena con campos no válidos después de que el usuario interactúe con algún campo.</span><span class="sxs-lookup"><span data-stu-id="edfcc-332">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="edfcc-333">Este escenario se puede abordar de cualquiera de las siguientes maneras:</span><span class="sxs-lookup"><span data-stu-id="edfcc-333">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="edfcc-334">No use un componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> en el formulario.</span><span class="sxs-lookup"><span data-stu-id="edfcc-334">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="edfcc-335">Haga que el componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> esté visible cuando se seleccione el botón Enviar (por ejemplo, en un método `HandleValidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="edfcc-335">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
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

## <a name="troubleshoot"></a><span data-ttu-id="edfcc-336">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="edfcc-336">Troubleshoot</span></span>

> <span data-ttu-id="edfcc-337">InvalidOperationException: EditForm requiere un parámetro Model o un parámetro EditContext, pero no ambos.</span><span class="sxs-lookup"><span data-stu-id="edfcc-337">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="edfcc-338">Confirme que la instancia de <xref:Microsoft.AspNetCore.Components.Forms.EditForm> tiene un objeto <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **o** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span><span class="sxs-lookup"><span data-stu-id="edfcc-338">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="edfcc-339">No use los dos para el mismo formulario.</span><span class="sxs-lookup"><span data-stu-id="edfcc-339">Don't use both for the same form.</span></span>

<span data-ttu-id="edfcc-340">Al asignar un parámetro <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> al formulario, confirme que se crea una instancia del tipo de modelo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="edfcc-340">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a><span data-ttu-id="edfcc-341">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="edfcc-341">Additional resources</span></span>

* <xref:blazor/file-uploads>

::: moniker-end
