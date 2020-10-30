---
title: Componentes del asistente de etiquetas en ASP.NET Core
author: scottaddie
description: Obtenga información sobre qué son los componentes del asistente de etiquetas y cómo se usan en ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
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
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 15bddd8ce18546bef7ee7e6ec2e32e369d0858a3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060565"
---
# <a name="tag-helper-components-in-aspnet-core"></a>Componentes del asistente de etiquetas en ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie) y [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)

El componente de un asistente de etiquetas es un asistente de etiquetas que permite modificar o agregar con condiciones elementos HTML a partir del código del lado servidor. Esta característica está disponible en ASP.NET Core 2.0 o versiones posteriores.

ASP.NET Core incluye dos componentes de asistente de etiquetas integrados: `head` y `body`. Están ubicados en el <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> espacio de nombres y se pueden usar en MVC y en Razor páginas. Los componentes de asistente de etiquetas no requieren el registro en la aplicación en *_ViewImports.cshtml* .

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Casos de uso

Dos casos de uso comunes de componentes de asistente de etiquetas incluyen:

1. [Inyectar `<link>` en `<head>` .](#inject-into-html-head-element)
1. [Inyectar `<script>` en `<body>` .](#inject-into-html-body-element)

En las secciones siguientes se describen estos casos de uso.

### <a name="inject-into-html-head-element"></a>Insertar en el elemento de encabezado HTML

Dentro del elemento `<head>` HTML, los archivos CSS suelen importarse con el elemento `<link>` HTML. El código siguiente inserta un elemento `<link>` en el elemento `<head>` con el componente de asistente de etiquetas `head`:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

En el código anterior:

* `AddressStyleTagHelperComponent` implementa <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>. La abstracción:
  * Permite la inicialización de la clase con <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.
  * Permite usar componentes de asistente de etiquetas para agregar o modificar elementos HTML.
* La propiedad <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> define el orden en el que se representan los componentes. `Order` es necesario cuando hay varios usos de los componentes de asistente de etiquetas en una aplicación.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compara el valor de propiedad <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> del contexto de ejecución con `head`. Si la comparación se evalúa como true, el contenido del campo `_style` se inserta en el elemento `<head>` HTML.

### <a name="inject-into-html-body-element"></a>Insertar en el elemento del cuerpo HTML

El componente de asistente de etiquetas `body` puede insertar un elemento `<script>` en el elemento `<body>`. El código siguiente demuestra esta técnica:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

Se usa un archivo HTML independiente para almacenar el elemento `<script>`. El archivo HTML hace que el código sea más limpio y más fácil de mantener. El código anterior lee el contenido de *TagHelpers/Templates/AddressToolTipScript.html* y lo anexa con la salida del asistente de etiquetas. El archivo *AddressToolTipScript.html* incluye el siguiente marcado:

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

El código anterior enlaza un [widget de información sobre herramientas de arranque](https://getbootstrap.com/docs/3.3/javascript/#tooltips) a cualquier elemento `<address>` que incluye un atributo `printable`. El efecto es visible cuando el puntero del mouse se sitúa sobre el elemento.

## <a name="register-a-component"></a>Registro de un componente

Se debe agregar un componente de asistente de etiquetas a la colección de componentes de asistente de etiquetas de la aplicación. Hay tres maneras de agregarlo a la colección:

* [Registro mediante el contenedor de servicios](#registration-via-services-container)
* [Registro a través de Razor archivo](#registration-via-razor-file)
* [Registro con el modelo de página o controlador](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a>Registro mediante el contenedor de servicios

Si la clase de componente de asistente de etiquetas no se administran con <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, se debe registrar con el sistema de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection). El código `Startup.ConfigureServices` siguiente registra las clases `AddressStyleTagHelperComponent` y `AddressScriptTagHelperComponent` con una [duración transitoria](xref:fundamentals/dependency-injection#lifetime-and-registration-options):

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-no-locrazor-file"></a>Registro a través de Razor archivo

Si el componente de aplicación auxiliar de etiquetas no está registrado con DI, se puede registrar en una Razor Página de páginas o en una vista de MVC. Esta técnica se utiliza para controlar el marcado insertado y el orden de ejecución de los componentes desde un Razor archivo.

`ITagHelperComponentManager` se usa para agregar componentes de asistente de etiquetas o para quitarlos de la aplicación. El código siguiente demuestra esta técnica con `AddressTagHelperComponent`:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

En el código anterior:

* La directiva `@inject` proporciona una instancia de `ITagHelperComponentManager`. La instancia de se asigna a una variable denominada `manager` para el acceso descendente en el Razor archivo.
* Se agrega una instancia de `AddressTagHelperComponent` a la colección de componentes de asistente de etiquetas de la aplicación.

`AddressTagHelperComponent` se modifica para alojar un constructor que acepta los parámetros `markup` y `order`:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

El parámetro `markup` proporcionado se utiliza en `ProcessAsync` como sigue:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Registro con el modelo de página o controlador

Si el componente de aplicación auxiliar de etiquetas no está registrado con DI, se puede registrar desde un Razor modelo de página de páginas o un controlador de MVC. Esta técnica es útil para separar la lógica de C# de Razor los archivos.

La inserción del constructor se utiliza para acceder a una instancia de `ITagHelperComponentManager`. El componente de asistente de etiquetas se agrega a la colección de componentes de asistente de etiquetas de la instancia. En el Razor modelo de página de páginas siguiente se muestra esta técnica con `AddressTagHelperComponent` :

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

En el código anterior:

* La inserción del constructor se utiliza para acceder a una instancia de `ITagHelperComponentManager`.
* Se agrega una instancia de `AddressTagHelperComponent` a la colección de componentes de asistente de etiquetas de la aplicación.

## <a name="create-a-component"></a>Creación de un componente

Para crear un componente de asistente de etiquetas personalizado:

* Cree una clase pública derivada de <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.
* Aplique un [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) atributo a la clase. Especifique el nombre del elemento HTML de destino.
* *Opcional* : Aplique un [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) atributo a la clase para suprimir la presentación del tipo en IntelliSense.

El código siguiente crea un componente de asistente de etiquetas personalizado que tiene como destino el elemento `<address>` HTML:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

Use el componente de asistente de etiquetas `address` personalizado para insertar el marcado HTML como sigue:

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

El método `ProcessAsync` anterior inserta el elemento HTML proporcionado a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> en el elemento `<address>` coincidente. La inserción se produce cuando:

* El valor de propiedad `TagName` del contexto de ejecución es igual a `address`.
* El elemento `<address>` correspondiente tiene un atributo `printable`.

Por ejemplo, la instrucción `if` se evalúa como true al procesar el siguiente elemento `<address>`:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
