---
title: Formularios y validación de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: 88c3ded79db65557d9426fde6f43aace4d9d8ae2
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606672"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>Formularios y validación de Blazor de ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/) y [Luke Latham](https://github.com/guardrex)

Blazor admite los formularios y la validación a través de [anotaciones de datos](xref:mvc/models/validation).

El siguiente tipo `ExampleModel` define la lógica de validación a través de anotaciones de datos:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Un formulario se define mediante el componente <xref:Microsoft.AspNetCore.Components.Forms.EditForm>. En el siguiente formulario se reflejan los elementos, componentes y código de Razor típicos:

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

En el ejemplo anterior:

* El formulario valida la entrada del usuario en el campo `name` por medio de la validación definida en el tipo `ExampleModel`. El modelo se crea en el bloque `@code` del componente y se conserva en un campo privado (`exampleModel`). El campo se asigna al atributo `Model` del elemento `<EditForm>`.
* El elemento `@bind-Value` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText> enlaza:
  * La propiedad del modelo (`exampleModel.Name`) a la propiedad `Value` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText>. Para más información sobre el enlace de propiedades, consulte <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.
  * Un delegado de evento de cambio a la propiedad `ValueChanged` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText>.
* El [componente de validador](#validator-components) <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> adjunta la compatibilidad con la validación mediante anotaciones de datos.
* El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> resume los mensajes de validación.
* `HandleValidSubmit` se desencadena cuando el formulario se envía correctamente (supera la validación).

## <a name="built-in-forms-components"></a>Componentes de formularios integrados

Hay disponible un conjunto de componentes integrados para recibir y validar las entradas de usuario. Las entradas se validan cuando se cambian y cuando un formulario se envía. Los componentes de entrada disponibles se muestran en la siguiente tabla.

::: moniker range=">= aspnetcore-5.0"

| Componente de entrada | Se representa como&hellip; |
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

| Componente de entrada | Se representa como&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> Los componentes `InputRadio` y `InputRadioGroup` están disponibles en ASP.NET Core 5.0 o posterior. Para más información, seleccione una versión 5.0 o posterior de este artículo.

::: moniker-end

Todos los componentes de entrada, incluido <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, admiten atributos arbitrarios. Cualquier atributo que no coincida con un parámetro de componente se agrega al elemento HTML representado.

Los componentes de entrada proporcionan un comportamiento predeterminado de validación cuando se cambia un campo, incluida la actualización de la clase CSS para reflejar el estado del campo. Algunos componentes incluyen lógica de análisis de utilidad. Así, por ejemplo, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> y <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> tratan los valores no analizables sin problemas registrándolos como errores de validación. Los tipos con capacidad para aceptar valores nulos también admiten la nulabilidad del campo de destino (por ejemplo, `int?`).

El siguiente tipo `Starship` define una lógica de validación mediante un conjunto de propiedades y de anotaciones de datos mayor que el del tipo `ExampleModel` anterior:

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

En el ejemplo anterior, `Description` es opcional porque no hay ninguna anotación de datos presente.

El siguiente formulario valida la entrada de usuario por medio de la validación definida en el modelo `Starship`:

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

<xref:Microsoft.AspNetCore.Components.Forms.EditForm> crea un elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> como un [valor en cascada](xref:blazor/components/cascading-values-and-parameters) que lleva un seguimiento de los metadatos del proceso de edición, incluidos los campos que se han modificado y los mensajes de validación actuales.

**Asigne** un objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **o** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> a un objeto <xref:Microsoft.AspNetCore.Components.Forms.EditForm>. No se admite la asignación de los dos y se generará un error **en tiempo de ejecución**.

<xref:Microsoft.AspNetCore.Components.Forms.EditForm> proporciona eventos prácticos de envíos de formulario válidos y no válidos:

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> para utilizar código personalizado a fin de desencadenar la validación y comprobar los valores de los campos.

En el ejemplo siguiente:

* El método `HandleSubmit` se ejecuta cuando se selecciona el botón **`Submit`** .
* El formulario se valida mediante una llamada a <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.
* El código adicional se ejecuta según el resultado de la validación. Coloque la lógica de negocios en el método asignado a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.

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
> No existe una API de .NET Framework para borrar los mensajes de validación directamente de un objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext>. Por tanto, normalmente no se recomienda agregar mensajes de validación a un objeto <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> nuevo en un formulario. Para administrar los mensajes de validación, use un [componente de validador](#validator-components) con el [código de validación de lógica de negocios](#business-logic-validation), como se describe en este artículo.

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a>Compatibilidad con nombres para mostrar

*Esta sección se aplica a ASP.NET Core en la versión candidata para lanzamiento 1 (RC1) de .NET 5 o una posterior.*

Los siguientes componentes integrados admiten nombres para mostrar con el parámetro `DisplayName`:

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

En el ejemplo de componente `InputDate` siguiente:

* El nombre para mostrar (`DisplayName`) se establece en `birthday`.
* El componente se enlaza a la propiedad `BirthDate` como un tipo `DateTime`.

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

Si el usuario no proporciona un valor de fecha, el error de validación aparece de este modo:

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a>Componentes de validador

Los componentes de validador admiten la validación de formularios mediante la administración de un objeto <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> para el objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> de un formulario.

El marco Blazor proporciona el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> para adjuntar la compatibilidad con la validación a formularios en función de [atributos de validación (anotaciones de datos)](xref:mvc/models/validation#validation-attributes). Cree componentes de validador personalizados para procesar los mensajes de validación para diferentes formularios de la misma página o el mismo formulario en distintos pasos del procesamiento de formularios, por ejemplo, la validación del lado cliente seguida de la del lado servidor. El ejemplo de componente de validador que se muestra en esta sección, `CustomValidator`, se usa en las secciones siguientes de este artículo:

* [Validación de la lógica de negocios](#business-logic-validation)
* [Validación del servidor](#server-validation)

> [!NOTE]
> En muchos casos, se pueden usar atributos de validación de anotación de datos personalizados en lugar de componentes de validador personalizados. Los atributos personalizados que se aplican al modelo del formulario se activan con el uso del componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. Cuando se usa con la validación del lado servidor, los atributos personalizados que se aplican al modelo deben ser ejecutables en el servidor. Para obtener más información, vea <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

Cree un componente de validador a partir de <xref:Microsoft.AspNetCore.Components.ComponentBase>:

* El objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> del formulario es un [parámetro en cascada](xref:blazor/components/cascading-values-and-parameters) del componente.
* Cuando se inicializa el componente de validador, se crea un objeto <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> para mantener una lista actual de errores del formulario.
* El almacén de mensajes recibe errores cuando el código del desarrollador del componente del formulario llama al método `DisplayErrors`. Los errores se pasan al método `DisplayErrors` en un objeto [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2). En el diccionario, la clave es el nombre del campo de formulario que tiene uno o más errores. El valor es la lista de errores.
* Los mensajes se borran cuando se produce alguno de los casos siguientes:
  * La validación se solicita en el objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> cuando se genera el evento <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested>. Todos los errores se borran.
  * Un campo cambia en el formulario cuando se genera el evento <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>. Solo se borran los errores del campo.
  * El código del desarrollador llama al método `ClearErrors`. Todos los errores se borran.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

namespace BlazorSample.Client
{
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
}
```

## <a name="business-logic-validation"></a>Validación de la lógica de negocios

La validación de la lógica de negocios se puede realizar con un [componente de validador](#validator-components) que recibe errores del formulario en un diccionario.

En el ejemplo siguiente:

* Se usa el componente `CustomValidator` de la sección [Componentes de validador](#validator-components) de este artículo.
* La validación necesita un valor para la descripción del envío (`Description`) si el usuario selecciona la clasificación de envío `Defense` (`Classification`).

Cuando los mensajes de validación se establecen en el componente, se agregan al componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> del validador y se muestran en el objeto <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:

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
> Como alternativa al uso de [componentes de validación](#validator-components), se pueden utilizar atributos de validación de anotación de datos. Los atributos personalizados que se aplican al modelo del formulario se activan con el uso del componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. Cuando se usa con la validación del lado servidor, los atributos deben ser ejecutables en el servidor. Para obtener más información, vea <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

## <a name="server-validation"></a>Validación del servidor

La validación del servidor se puede realizar con un [componente de validador](#validator-components) de servidor:

* Procese la validación del lado cliente en el formulario con el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.
* Cuando el formulario pasa la validación del lado cliente (se llama a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>), envíe el objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> a una API de servidor backend para el procesamiento del formulario.
* Procese la validación del modelo en el servidor.
* La API del servidor incluye la validación de anotaciones de datos del marco integrada y la lógica de validación personalizada proporcionada por el desarrollador. Si la validación se supera en el servidor, procese el formulario y devuelva un código de estado correcto (*200 - Correcto*). Si se produce un error en la validación, devuelva un código de estado de error (*400 - Solicitud incorrecta*) y los errores de validación de campos.
* Si es correcto, puede deshabilitar el formulario, o bien mostrar los errores.

El ejemplo siguiente se basa en:

* Una solución de Blazor hospedada creada mediante la [plantilla de proyecto hospedado de Blazor](xref:blazor/hosting-models#blazor-webassembly). El ejemplo se puede usar con cualquiera de las soluciones de Blazor hospedadas que se describen en la [documentación de seguridad y Identity](xref:blazor/security/webassembly/index#implementation-guidance).
* El ejemplo de formulario *Starfleet Starship Database* de la sección [Componentes de formularios integrados](#built-in-forms-components) anterior.
* El componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> del marco Blazor.
* El componente `CustomValidator` mostrado en la sección [Componentes de validador](#validator-components).

En el ejemplo siguiente, la API de servidor valida que se ha proporcionado un valor para la descripción del envío (`Description`) si el usuario selecciona la clasificación de envío `Defense` (`Classification`).

Coloque el modelo `Starship` en el proyecto `Shared` de la solución para que las aplicaciones cliente y servidor puedan usarlo. Como en el modelo se necesitan anotaciones de datos, agregue una referencia de paquete para [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) al archivo de proyecto del proyecto `Shared`:

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

Para determinar la versión más reciente del paquete que no sea de versión preliminar, vea el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).

En el proyecto de API de servidor, agregue un controlador para procesar las solicitudes de validación de Starship (`Controllers/StarshipValidation.cs`) y devuelva los mensajes de validación con errores:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace BlazorSample.Server.Controllers
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
                logger.LogError("Validation Error: {MESSAGE}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

Cuando se produce un error de validación de enlace de modelos en el servidor, un objeto [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) devuelve normalmente una [respuesta de solicitud incorrecta predeterminada](xref:web-api/index#default-badrequest-response) con un objeto <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>. La respuesta contiene otros datos además de los errores de validación, como se muestra en el ejemplo siguiente cuando no se envían todos los campos del formulario *Starfleet Starship Database* y no supera la validación:

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

Si la API del servidor devuelve la respuesta JSON predeterminada anterior, el cliente puede analizarla para obtener los elementos secundarios del nodo `errors`. Pero no es conveniente analizar el archivo. El análisis de JSON requiere código adicional después de llamar a <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> a fin de generar un objeto [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) de errores para el procesamiento de errores de validación del formulario. Idealmente, la API de servidor solo debe devolver los errores de validación:

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

Para modificar la respuesta de la API de servidor a fin de que solo devuelva los errores de validación, cambie el delegado que se invoca en las acciones anotadas con <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> en `Startup.ConfigureServices`. Para el punto de conexión de API (`/StarshipValidation`), devuelva un objeto <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> con el objeto <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>. Para cualquier otro punto de conexión de API, conserve el comportamiento predeterminado mediante la devolución el resultado del objeto con un objeto <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> nuevo:

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

Para obtener más información, vea <xref:web-api/handle-errors#validation-failure-error-response>.

En el proyecto de cliente, agregue el componente de validador mostrado en la sección [Componentes de validador](#validator-components).

En el proyecto de cliente, el formulario *Starfleet Starship Database* se actualiza para mostrar los errores de validación del servidor con la ayuda del componente `CustomValidator`. Cuando la API de servidor devuelve mensajes de validación, se agregan al objeto <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>del componente `CustomValidator`. Los errores están disponibles en el objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext> del formulario para representarlos mediante <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:

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
            Logger.LogError("Form processing error: {MESSAGE}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> Como alternativa a los [componentes de validación](#validator-components), se pueden usar atributos de validación de anotación de datos. Los atributos personalizados que se aplican al modelo del formulario se activan con el uso del componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. Cuando se usa con la validación del lado servidor, los atributos deben ser ejecutables en el servidor. Para obtener más información, vea <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

> [!NOTE]
> El enfoque de validación del lado servidor de esta sección es adecuado para cualquiera de los ejemplos de soluciones hospedadas de Blazor WebAssembly de este conjunto de documentación:
>
> * [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [Servidor de Identity](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a>InputText basado en el evento de entrada

Use el componente <xref:Microsoft.AspNetCore.Components.Forms.InputText> para crear un componente personalizado que usa el evento `input` en lugar del evento `change`.

En el siguiente ejemplo, el componente `CustomInputText` hereda el componente `InputText` del marco y establece el enlace de eventos (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) en el evento `oninput`.

`Shared/CustomInputText.razor`:

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

El componente `CustomInputText` se puede usar en cualquier lugar donde <xref:Microsoft.AspNetCore.Components.Forms.InputText> se use:

`Pages/TestForm.razor`:

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

## <a name="radio-buttons"></a>Botones de radio

::: moniker range=">= aspnetcore-5.0"

Use componentes `InputRadio` con el componente `InputRadioGroup` para crear un grupo de botones de radio. En el ejemplo siguiente, las propiedades se agregan al modelo `Starship` descrito en la sección [Componentes de formularios integrados](#built-in-forms-components):

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

Agregue el elemento `enums` siguiente a la aplicación. Cree un nuevo archivo que contenga `enums` o agregue `enums` al archivo `Starship.cs`. Haga que `enums` sea accesible para el modelo `Starship` y el formulario *Starfleet Starship Database*:

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, Virgin, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

Actualice el formulario *Starfleet Starship Database* descrito en la sección [Componentes de formularios integrados](#built-in-forms-components). Agregue los componentes para generar lo siguiente:

* Un grupo de botones de radio para el fabricante del envío.
* Un grupo de botones de radio anidados para el color y el motor de envío.

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
> Si se omite `Name`, los componentes de `InputRadio` los agrupa su antecesor más reciente.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Al trabajar con botones de radio en un formulario, el enlace de datos se controla de manera diferente que otros elementos, ya que los botones de radio se evalúan como un grupo. El valor de cada botón de radio es fijo, pero el valor del grupo de botones de radio es el valor del botón de radio seleccionado. El ejemplo siguiente muestra cómo:

* Controlar el enlace de datos en un grupo de botones de radio
* Admitir la validación usando un componente `InputRadio` personalizado

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

El siguiente elemento <xref:Microsoft.AspNetCore.Components.Forms.EditForm> usa el componente `InputRadio` anterior para obtener y validar una clasificación del usuario:

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

## <a name="binding-select-element-options-to-c-object-null-values"></a>Enlace de opciones del elemento `<select>` a valores `null` de un objeto C#

No hay ninguna manera razonable de representar un valor de opción de elemento `<select>` como valor `null` de objeto C#, por los siguientes motivos:

* Los atributos HTML no pueden tener valores `null`. El equivalente más cercano a `null` en HTML es la ausencia del atributo `value` de HTML del elemento `<option>`.
* Al seleccionar un `<option>` sin ningún atributo `value`, el explorador trata el valor como *contenido de texto* de ese elemento de `<option>`.

El marco de Blazor no intenta suprimir el comportamiento predeterminado porque implicaría lo siguiente:

* Crear una cadena de soluciones alternativas de casos especiales en el marco.
* Hacer cambios importantes en el comportamiento actual del marco.

::: moniker range=">= aspnetcore-5.0"

El `null` más plausible equivalente en HTML es una *cadena vacía* `value`. El marco Blazor controla `null` en las conversiones de cadenas vacías para el enlace bidireccional al valor de `<select>`.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

El marco Blazor no controla automáticamente `null` en las conversiones de cadenas vacías al intentar el enlace bidireccional al valor de `<select>`. Para obtener más información, vea [Corrección de enlaces `<select>` a un valor null (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).

::: moniker-end

## <a name="validation-support"></a>Compatibilidad con la validación

El componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> adjunta la compatibilidad con la validación mediante anotaciones de datos al elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> en cascada. Este gesto explícito es necesario para habilitar la compatibilidad con la validación mediante anotaciones. Para usar un sistema de validación distinto al de las anotaciones de datos, reemplace <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> por una implementación personalizada. La implementación de ASP.NET Core está disponible para su inspección en el origen de referencia: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs). Los vínculos anteriores al origen de referencia proporcionan código de la rama `master` del repositorio, que representa el desarrollo actual de la unidad de producto para la próxima versión de ASP.NET Core. Para seleccionar la rama de una versión diferente, use el selector de ramas de GitHub (por ejemplo, `release/3.1`).

Blazor realiza dos tipos de validación:

* La *validación del campo* se realiza cuando el usuario sale de un campo usando la tecla TAB. Durante una validación del campo, el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> asocia al campo todos los resultados de validación notificados.
* La *validación del modelo* se realiza cuando el usuario envía el formulario. Durante una validación del modelo, el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> intenta determinar el campo en función del nombre del miembro que se indica en el resultado de la validación. Los resultados de validación que no están asociados a un miembro individual se asocian al modelo en lugar de a un campo.

### <a name="validation-summary-and-validation-message-components"></a>Resumen de validación y componentes de los mensajes de validación

El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>, que resume todos los mensajes de validación, es similar a la [aplicación auxiliar de etiquetas ValidationSummary](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Mensajes de validación de salida de un modelo específico con el parámetro `Model`:
  
```razor
<ValidationSummary Model="@starship" />
```

El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>, que muestra los mensajes de validación de un campo específico, es similar a la [aplicación auxiliar de etiquetas Validation Message](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Especifique el campo de validación con el atributo `For` y una expresión lambda donde se indique la propiedad del modelo:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Los componentes <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> y <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> admiten atributos arbitrarios. Cualquier atributo que no coincida con un parámetro de componente se agrega a los elementos `<div>` o `<ul>` generados.

Controle el estilo de los mensajes de validación en la hoja de estilos de la aplicación (`wwwroot/css/app.css` o `wwwroot/css/site.css`). La clase `validation-message` predeterminada establece el color del texto de los mensajes de validación en rojo:

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>Atributos de validación personalizados

Para asegurarnos de que un resultado de validación está correctamente asociado a un campo cuando se usa un [atributo de validación personalizado](xref:mvc/models/validation#custom-attributes), pase el elemento <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> del contexto de validación al crear el elemento <xref:System.ComponentModel.DataAnnotations.ValidationResult>:

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
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> es `null`. No se admite la inserción de servicios para la validación en el método `IsValid`.

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a>Atributos de clase de validación personalizados

Los nombres de clase de validación personalizados son útiles al integrar con marcos de CSS, como [Bootstrap](https://getbootstrap.com/). Para especificar nombres de clase de validación personalizados, cree una clase derivada de `FieldCssClassProvider` y establezca dicha clase en la instancia <xref:Microsoft.AspNetCore.Components.Forms.EditContext>:

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

### <a name="no-locblazor-data-annotations-validation-package"></a>Paquete de validación de anotaciones de datos de Blazor

[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) es un paquete que llena los huecos de experiencia de validación mediante el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. Actualmente, el paquete está en fase *experimental*.

> [!NOTE]
> En [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) hay una *versión candidata para lanzamiento* más reciente del paquete [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Por ahora, siga usando el paquete de versión candidata para lanzamiento *experimental*. El ensamblado del paquete podría moverse al marco o al runtime en una versión futura. Vea los [anuncios del repositorio de GitHub](https://github.com/aspnet/Announcements), el [repositorio dotnet/aspnetcore de GitHub](https://github.com/dotnet/aspnetcore), o bien la sección de este tema para obtener más actualizaciones.

### <a name="compareproperty-attribute"></a>Atributo [CompareProperty]

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> no funciona bien con el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> porque no asocia el resultado de la validación a un miembro específico. Esto puede generar un comportamiento incoherente entre la validación de nivel de campo y el momento en que el modelo completo se valida al enviarse. El paquete *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) incluye un atributo de validación más, `ComparePropertyAttribute`, que pone fin a estas limitaciones. En una aplicación Blazor, `[CompareProperty]` es un reemplazo directo del atributo [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute).

### <a name="nested-models-collection-types-and-complex-types"></a>Tipos de colección, tipos complejos y modelos anidados

Blazor proporciona compatibilidad para validar la entrada del formulario mediante anotaciones de datos con el elemento integrado <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>, pero <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> solo valida las propiedades de nivel superior del modelo enlazadas al formulario que no son propiedades de tipos complejos o de colección.

Para validar el gráfico de objetos completo del modelo enlazado, incluidas las propiedades de tipos complejos o de colección, use el elemento `ObjectGraphDataAnnotationsValidator` proporcionado por el paquete *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation):

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Anote las propiedades del modelo con `[ValidateComplexType]`. En las siguientes clases de modelo, la clase `ShipDescription` contiene más anotaciones de datos para validar cuando el modelo está enlazado al formulario:

`Starship.cs`:

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

`ShipDescription.cs`:

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>Habilitar el botón Enviar según la validación del formulario

Para habilitar el botón Enviar según la validación del formulario:

* Use el elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> del formulario para asignar el modelo cuando el componente se inicialice.
* Valide el formulario en la devolución de llamada <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> del contexto para habilitar y deshabilitar el botón Enviar.
* Desenlace el controlador de eventos en el método `Dispose`. Para obtener más información, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

> [!NOTE]
> Cuando use un objeto <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, no asigne también una instancia de <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> al objeto <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.

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

En el ejemplo anterior, establezca `formInvalid` en `false` en los siguientes casos:

* El formulario se carga previamente con valores predeterminados válidos.
* Quiere habilitar el botón Enviar cuando el formulario se cargue.

Un efecto secundario del método anterior es que un componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> se rellena con campos no válidos después de que el usuario interactúe con algún campo. Este escenario se puede abordar de cualquiera de las siguientes maneras:

* No use un componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> en el formulario.
* Haga que el componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> esté visible cuando se seleccione el botón Enviar (por ejemplo, en un método `HandleValidSubmit`).

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

## <a name="troubleshoot"></a>Solucionar problemas

> InvalidOperationException: EditForm requiere un parámetro Model o un parámetro EditContext, pero no ambos.

Confirme que la instancia de <xref:Microsoft.AspNetCore.Components.Forms.EditForm> tiene un objeto <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **o** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>. No use los dos para el mismo formulario.

Al asignar un parámetro <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> al formulario, confirme que se crea una instancia del tipo de modelo, como se muestra en el ejemplo siguiente:

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/file-uploads>

::: moniker-end
