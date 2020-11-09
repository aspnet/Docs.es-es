---
title: Componentes del asistente de etiquetas en ASP.NET Core
author: scottaddie
description: Obtenga información sobre qué son los componentes del asistente de etiquetas y cómo se usan en ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 15bddd8ce18546bef7ee7e6ec2e32e369d0858a3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060565"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="1c82b-103">Componentes del asistente de etiquetas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c82b-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="1c82b-104">Por [Scott Addie](https://twitter.com/Scott_Addie) y [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="1c82b-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="1c82b-105">El componente de un asistente de etiquetas es un asistente de etiquetas que permite modificar o agregar con condiciones elementos HTML a partir del código del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="1c82b-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="1c82b-106">Esta característica está disponible en ASP.NET Core 2.0 o versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="1c82b-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="1c82b-107">ASP.NET Core incluye dos componentes de asistente de etiquetas integrados: `head` y `body`.</span><span class="sxs-lookup"><span data-stu-id="1c82b-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="1c82b-108">Están ubicados en el <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> espacio de nombres y se pueden usar en MVC y en Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="1c82b-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="1c82b-109">Los componentes de asistente de etiquetas no requieren el registro en la aplicación en *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1c82b-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml* .</span></span>

<span data-ttu-id="1c82b-110">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1c82b-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="1c82b-111">Casos de uso</span><span class="sxs-lookup"><span data-stu-id="1c82b-111">Use cases</span></span>

<span data-ttu-id="1c82b-112">Dos casos de uso comunes de componentes de asistente de etiquetas incluyen:</span><span class="sxs-lookup"><span data-stu-id="1c82b-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="1c82b-113">Inyectar `<link>` en `<head>` .</span><span class="sxs-lookup"><span data-stu-id="1c82b-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="1c82b-114">Inyectar `<script>` en `<body>` .</span><span class="sxs-lookup"><span data-stu-id="1c82b-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="1c82b-115">En las secciones siguientes se describen estos casos de uso.</span><span class="sxs-lookup"><span data-stu-id="1c82b-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="1c82b-116">Insertar en el elemento de encabezado HTML</span><span class="sxs-lookup"><span data-stu-id="1c82b-116">Inject into HTML head element</span></span>

<span data-ttu-id="1c82b-117">Dentro del elemento `<head>` HTML, los archivos CSS suelen importarse con el elemento `<link>` HTML.</span><span class="sxs-lookup"><span data-stu-id="1c82b-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="1c82b-118">El código siguiente inserta un elemento `<link>` en el elemento `<head>` con el componente de asistente de etiquetas `head`:</span><span class="sxs-lookup"><span data-stu-id="1c82b-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="1c82b-119">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="1c82b-119">In the preceding code:</span></span>

* <span data-ttu-id="1c82b-120">`AddressStyleTagHelperComponent` implementa <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span><span class="sxs-lookup"><span data-stu-id="1c82b-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="1c82b-121">La abstracción:</span><span class="sxs-lookup"><span data-stu-id="1c82b-121">The abstraction:</span></span>
  * <span data-ttu-id="1c82b-122">Permite la inicialización de la clase con <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span><span class="sxs-lookup"><span data-stu-id="1c82b-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="1c82b-123">Permite usar componentes de asistente de etiquetas para agregar o modificar elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="1c82b-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="1c82b-124">La propiedad <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> define el orden en el que se representan los componentes.</span><span class="sxs-lookup"><span data-stu-id="1c82b-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="1c82b-125">`Order` es necesario cuando hay varios usos de los componentes de asistente de etiquetas en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="1c82b-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="1c82b-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compara el valor de propiedad <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> del contexto de ejecución con `head`.</span><span class="sxs-lookup"><span data-stu-id="1c82b-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="1c82b-127">Si la comparación se evalúa como true, el contenido del campo `_style` se inserta en el elemento `<head>` HTML.</span><span class="sxs-lookup"><span data-stu-id="1c82b-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="1c82b-128">Insertar en el elemento del cuerpo HTML</span><span class="sxs-lookup"><span data-stu-id="1c82b-128">Inject into HTML body element</span></span>

<span data-ttu-id="1c82b-129">El componente de asistente de etiquetas `body` puede insertar un elemento `<script>` en el elemento `<body>`.</span><span class="sxs-lookup"><span data-stu-id="1c82b-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="1c82b-130">El código siguiente demuestra esta técnica:</span><span class="sxs-lookup"><span data-stu-id="1c82b-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="1c82b-131">Se usa un archivo HTML independiente para almacenar el elemento `<script>`.</span><span class="sxs-lookup"><span data-stu-id="1c82b-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="1c82b-132">El archivo HTML hace que el código sea más limpio y más fácil de mantener.</span><span class="sxs-lookup"><span data-stu-id="1c82b-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="1c82b-133">El código anterior lee el contenido de *TagHelpers/Templates/AddressToolTipScript.html* y lo anexa con la salida del asistente de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="1c82b-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="1c82b-134">El archivo *AddressToolTipScript.html* incluye el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="1c82b-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="1c82b-135">El código anterior enlaza un [widget de información sobre herramientas de arranque](https://getbootstrap.com/docs/3.3/javascript/#tooltips) a cualquier elemento `<address>` que incluye un atributo `printable`.</span><span class="sxs-lookup"><span data-stu-id="1c82b-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="1c82b-136">El efecto es visible cuando el puntero del mouse se sitúa sobre el elemento.</span><span class="sxs-lookup"><span data-stu-id="1c82b-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="1c82b-137">Registro de un componente</span><span class="sxs-lookup"><span data-stu-id="1c82b-137">Register a Component</span></span>

<span data-ttu-id="1c82b-138">Se debe agregar un componente de asistente de etiquetas a la colección de componentes de asistente de etiquetas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1c82b-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="1c82b-139">Hay tres maneras de agregarlo a la colección:</span><span class="sxs-lookup"><span data-stu-id="1c82b-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="1c82b-140">Registro mediante el contenedor de servicios</span><span class="sxs-lookup"><span data-stu-id="1c82b-140">Registration via services container</span></span>](#registration-via-services-container)
* [<span data-ttu-id="1c82b-141">Registro a través de Razor archivo</span><span class="sxs-lookup"><span data-stu-id="1c82b-141">Registration via Razor file</span></span>](#registration-via-razor-file)
* [<span data-ttu-id="1c82b-142">Registro con el modelo de página o controlador</span><span class="sxs-lookup"><span data-stu-id="1c82b-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="1c82b-143">Registro mediante el contenedor de servicios</span><span class="sxs-lookup"><span data-stu-id="1c82b-143">Registration via services container</span></span>

<span data-ttu-id="1c82b-144">Si la clase de componente de asistente de etiquetas no se administran con <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, se debe registrar con el sistema de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1c82b-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="1c82b-145">El código `Startup.ConfigureServices` siguiente registra las clases `AddressStyleTagHelperComponent` y `AddressScriptTagHelperComponent` con una [duración transitoria](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span><span class="sxs-lookup"><span data-stu-id="1c82b-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-no-locrazor-file"></a><span data-ttu-id="1c82b-146">Registro a través de Razor archivo</span><span class="sxs-lookup"><span data-stu-id="1c82b-146">Registration via Razor file</span></span>

<span data-ttu-id="1c82b-147">Si el componente de aplicación auxiliar de etiquetas no está registrado con DI, se puede registrar en una Razor Página de páginas o en una vista de MVC.</span><span class="sxs-lookup"><span data-stu-id="1c82b-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="1c82b-148">Esta técnica se utiliza para controlar el marcado insertado y el orden de ejecución de los componentes desde un Razor archivo.</span><span class="sxs-lookup"><span data-stu-id="1c82b-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="1c82b-149">`ITagHelperComponentManager` se usa para agregar componentes de asistente de etiquetas o para quitarlos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1c82b-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="1c82b-150">El código siguiente demuestra esta técnica con `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="1c82b-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="1c82b-151">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="1c82b-151">In the preceding code:</span></span>

* <span data-ttu-id="1c82b-152">La directiva `@inject` proporciona una instancia de `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="1c82b-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="1c82b-153">La instancia de se asigna a una variable denominada `manager` para el acceso descendente en el Razor archivo.</span><span class="sxs-lookup"><span data-stu-id="1c82b-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="1c82b-154">Se agrega una instancia de `AddressTagHelperComponent` a la colección de componentes de asistente de etiquetas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1c82b-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="1c82b-155">`AddressTagHelperComponent` se modifica para alojar un constructor que acepta los parámetros `markup` y `order`:</span><span class="sxs-lookup"><span data-stu-id="1c82b-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="1c82b-156">El parámetro `markup` proporcionado se utiliza en `ProcessAsync` como sigue:</span><span class="sxs-lookup"><span data-stu-id="1c82b-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="1c82b-157">Registro con el modelo de página o controlador</span><span class="sxs-lookup"><span data-stu-id="1c82b-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="1c82b-158">Si el componente de aplicación auxiliar de etiquetas no está registrado con DI, se puede registrar desde un Razor modelo de página de páginas o un controlador de MVC.</span><span class="sxs-lookup"><span data-stu-id="1c82b-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="1c82b-159">Esta técnica es útil para separar la lógica de C# de Razor los archivos.</span><span class="sxs-lookup"><span data-stu-id="1c82b-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="1c82b-160">La inserción del constructor se utiliza para acceder a una instancia de `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="1c82b-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="1c82b-161">El componente de asistente de etiquetas se agrega a la colección de componentes de asistente de etiquetas de la instancia.</span><span class="sxs-lookup"><span data-stu-id="1c82b-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="1c82b-162">En el Razor modelo de página de páginas siguiente se muestra esta técnica con `AddressTagHelperComponent` :</span><span class="sxs-lookup"><span data-stu-id="1c82b-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="1c82b-163">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="1c82b-163">In the preceding code:</span></span>

* <span data-ttu-id="1c82b-164">La inserción del constructor se utiliza para acceder a una instancia de `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="1c82b-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="1c82b-165">Se agrega una instancia de `AddressTagHelperComponent` a la colección de componentes de asistente de etiquetas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1c82b-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="1c82b-166">Creación de un componente</span><span class="sxs-lookup"><span data-stu-id="1c82b-166">Create a Component</span></span>

<span data-ttu-id="1c82b-167">Para crear un componente de asistente de etiquetas personalizado:</span><span class="sxs-lookup"><span data-stu-id="1c82b-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="1c82b-168">Cree una clase pública derivada de <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="1c82b-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="1c82b-169">Aplique un [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) atributo a la clase.</span><span class="sxs-lookup"><span data-stu-id="1c82b-169">Apply an [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="1c82b-170">Especifique el nombre del elemento HTML de destino.</span><span class="sxs-lookup"><span data-stu-id="1c82b-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="1c82b-171">*Opcional* : Aplique un [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) atributo a la clase para suprimir la presentación del tipo en IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="1c82b-171">*Optional* : Apply an [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="1c82b-172">El código siguiente crea un componente de asistente de etiquetas personalizado que tiene como destino el elemento `<address>` HTML:</span><span class="sxs-lookup"><span data-stu-id="1c82b-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="1c82b-173">Use el componente de asistente de etiquetas `address` personalizado para insertar el marcado HTML como sigue:</span><span class="sxs-lookup"><span data-stu-id="1c82b-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

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

<span data-ttu-id="1c82b-174">El método `ProcessAsync` anterior inserta el elemento HTML proporcionado a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> en el elemento `<address>` coincidente.</span><span class="sxs-lookup"><span data-stu-id="1c82b-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="1c82b-175">La inserción se produce cuando:</span><span class="sxs-lookup"><span data-stu-id="1c82b-175">The injection occurs when:</span></span>

* <span data-ttu-id="1c82b-176">El valor de propiedad `TagName` del contexto de ejecución es igual a `address`.</span><span class="sxs-lookup"><span data-stu-id="1c82b-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="1c82b-177">El elemento `<address>` correspondiente tiene un atributo `printable`.</span><span class="sxs-lookup"><span data-stu-id="1c82b-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="1c82b-178">Por ejemplo, la instrucción `if` se evalúa como true al procesar el siguiente elemento `<address>`:</span><span class="sxs-lookup"><span data-stu-id="1c82b-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="1c82b-179">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1c82b-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
