---
title: Procedimientos recomendados de rendimiento de Blazor WebAssembly en ASP.NET Core
author: pranavkm
description: Sugerencias para aumentar el rendimiento de las aplicaciones Blazor WebAssembly en ASP.NET Core y evitar problemas de rendimiento comunes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: d1ad646f82e5c9ba611a60fc9be8378bedef8dee
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721728"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="a40ed-103">Procedimientos recomendados de rendimiento de Blazor WebAssembly en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a40ed-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="a40ed-104">Por [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="a40ed-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="a40ed-105">En este artículo se proporcionan instrucciones relativas a procedimientos recomendados de rendimiento de Blazor WebAssembly en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a40ed-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="a40ed-106">Evitar representaciones de componentes innecesarias</span><span class="sxs-lookup"><span data-stu-id="a40ed-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="a40ed-107">El algoritmo de diferenciación de Blazor evita que un componente se represente cuando dicho algoritmo percibe que ese componente no ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="a40ed-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="a40ed-108">Invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> si prefiere tener un control específico sobre la representación de componentes.</span><span class="sxs-lookup"><span data-stu-id="a40ed-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="a40ed-109">Si crea un componente de solo interfaz de usuario que nunca cambia tras la representación inicial, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para que devuelva `false`:</span><span class="sxs-lookup"><span data-stu-id="a40ed-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="a40ed-110">La mayoría de las aplicaciones no requieren un control específico, pero <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> se puede usar para representar de forma selectiva un componente en respuesta a un evento de interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="a40ed-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="a40ed-111">Usar <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> también podría ser importante en escenarios en los que se representa un gran número de componentes.</span><span class="sxs-lookup"><span data-stu-id="a40ed-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="a40ed-112">Piense en una cuadrícula, donde el uso de <xref:Microsoft.AspNetCore.Components.EventCallback> en un componente de una celda de la cuadrícula llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en la cuadrícula.</span><span class="sxs-lookup"><span data-stu-id="a40ed-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="a40ed-113">La llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> provoca una nueva representación de todos los componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="a40ed-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="a40ed-114">Si solo un pequeño número de celdas requiere una nueva representación, utilice <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para evitar la reducción de rendimiento de las representaciones innecesarias.</span><span class="sxs-lookup"><span data-stu-id="a40ed-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="a40ed-115">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a40ed-115">In the following example:</span></span>

* <span data-ttu-id="a40ed-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> se invalida y se establece en el valor del campo <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, que es `false` inicialmente, cuando el componente se carga.</span><span class="sxs-lookup"><span data-stu-id="a40ed-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="a40ed-117">Cuando el botón se selecciona, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> se establece en `true`, lo que obliga al componente a volver a representarse con el elemento `currentCount` actualizado.</span><span class="sxs-lookup"><span data-stu-id="a40ed-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="a40ed-118">Inmediatamente después de que el componente se vuelva a representar, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> establece el valor de <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> de nuevo en `false` para impedir que se vuelva a representar hasta la próxima vez que el botón se seleccione.</span><span class="sxs-lookup"><span data-stu-id="a40ed-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="a40ed-119">Para obtener más información, vea <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="a40ed-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="a40ed-120">Virtualización de fragmentos reutilizables</span><span class="sxs-lookup"><span data-stu-id="a40ed-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="a40ed-121">Los componentes constituyen un método práctico para generar fragmentos de código y marcado reutilizables.</span><span class="sxs-lookup"><span data-stu-id="a40ed-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="a40ed-122">En general, se recomienda crear componentes individuales que se ajusten más adecuadamente a los requisitos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a40ed-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="a40ed-123">Una advertencia que hacer a este respecto es que cada componente secundario adicional contribuye al tiempo total necesario para representar un componente primario.</span><span class="sxs-lookup"><span data-stu-id="a40ed-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="a40ed-124">En la mayoría de las aplicaciones, esta sobrecarga adicional es insignificante.</span><span class="sxs-lookup"><span data-stu-id="a40ed-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="a40ed-125">Las aplicaciones que generan un gran número de componentes deben considerar el uso de estrategias para reducir la sobrecarga de procesamiento, como limitar el número de componentes representados.</span><span class="sxs-lookup"><span data-stu-id="a40ed-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="a40ed-126">Para obtener más información, vea <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="a40ed-126">For more information, see <xref:blazor/components/virtualization>.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="a40ed-127">Evitar la interoperabilidad de JavaScript para serializar datos</span><span class="sxs-lookup"><span data-stu-id="a40ed-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="a40ed-128">En Blazor WebAssembly, una llamada de interoperabilidad de JavaScript debe atravesar el límite WebAssembly-JS.</span><span class="sxs-lookup"><span data-stu-id="a40ed-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="a40ed-129">Al serializar y deserializar contenido en los dos contextos, se crea una sobrecarga de procesamiento para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a40ed-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="a40ed-130">Las llamadas de interoperabilidad de JavaScript frecuentes suelen afectar negativamente al rendimiento.</span><span class="sxs-lookup"><span data-stu-id="a40ed-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="a40ed-131">Para reducir la serialización de datos en el límite, determine si la aplicación es capaz de aglutinar muchas cargas pequeñas en una sola carga grande para evitar el gran volumen de cambios de contexto entre WebAssembly y JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a40ed-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="a40ed-132">Uso de System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="a40ed-132">Use System.Text.Json</span></span>

<span data-ttu-id="a40ed-133">La implementación de interoperabilidad de JavaScript de Blazor se basa en <xref:System.Text.Json>, que es una biblioteca de serialización de JSON de alto rendimiento con una asignación de memoria reducida.</span><span class="sxs-lookup"><span data-stu-id="a40ed-133">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="a40ed-134">El uso de <xref:System.Text.Json> no da como resultado un mayor tamaño de carga de aplicación frente a la adición de una o varias bibliotecas JSON alternativas.</span><span class="sxs-lookup"><span data-stu-id="a40ed-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="a40ed-135">Para obtener instrucciones sobre la migración, vea [Procedimiento para realizar la migración de `Newtonsoft.Json` a `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="a40ed-135">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="a40ed-136">Uso de API de interoperabilidad de JavaScript sincrónicas y no serializadas cuando sea necesario</span><span class="sxs-lookup"><span data-stu-id="a40ed-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

<span data-ttu-id="a40ed-137">Blazor WebAssembly ofrece dos versiones más de <xref:Microsoft.JSInterop.IJSRuntime> con respecto a la única versión disponible en las aplicaciones Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="a40ed-137">Blazor WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="a40ed-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> permite invocar llamadas de interoperabilidad de JavaScript sincrónicamente, lo que genera una sobrecarga menor que las versiones asincrónicas:</span><span class="sxs-lookup"><span data-stu-id="a40ed-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="a40ed-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permite llamadas de interoperabilidad de JavaScript no serializadas:</span><span class="sxs-lookup"><span data-stu-id="a40ed-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="a40ed-140">Aunque el uso de <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> es el método de interoperabilidad de JavaScript con menor sobrecarga, las API de JavaScript necesarias para interactuar con estas API no están documentadas en este momento y están sujetas a cambios importantes en versiones futuras.</span><span class="sxs-lookup"><span data-stu-id="a40ed-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="a40ed-141">Reducción del tamaño de la aplicación</span><span class="sxs-lookup"><span data-stu-id="a40ed-141">Reduce app size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="a40ed-142">Recorte de lenguaje intermedio (IL)</span><span class="sxs-lookup"><span data-stu-id="a40ed-142">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="a40ed-143">[Cuando una aplicación Blazor WebAssembly se recorta](xref:blazor/host-and-deploy/configure-trimmer), el tamaño de la aplicación se reduce quitando el código que no se usa en los archivos binarios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a40ed-143">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="a40ed-144">De forma predeterminada, el recortador se ejecuta al publicar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="a40ed-144">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="a40ed-145">Para sacar partido del recorte, publique la aplicación de implementación mediante el comando [`dotnet publish`](/dotnet/core/tools/dotnet-publish), con la opción [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) establecida en `Release`:</span><span class="sxs-lookup"><span data-stu-id="a40ed-145">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="a40ed-146">Vinculación de lenguaje intermedio</span><span class="sxs-lookup"><span data-stu-id="a40ed-146">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="a40ed-147">[Cuando una aplicación Blazor WebAssembly se vincula](xref:blazor/host-and-deploy/configure-linker), el tamaño de la aplicación se reduce recortando el código que no se usa en los archivos binarios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a40ed-147">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="a40ed-148">De forma predeterminada, el enlazador de lenguaje intermedio (IL) solo está habilitado cuando se compila en la configuración de `Release`.</span><span class="sxs-lookup"><span data-stu-id="a40ed-148">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="a40ed-149">Para sacar partido de esto, publique la aplicación de implementación con el comando [`dotnet publish`](/dotnet/core/tools/dotnet-publish), con la opción [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) establecida en `Release`:</span><span class="sxs-lookup"><span data-stu-id="a40ed-149">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a><span data-ttu-id="a40ed-150">Ensamblados de carga diferida</span><span class="sxs-lookup"><span data-stu-id="a40ed-150">Lazy load assemblies</span></span>

<span data-ttu-id="a40ed-151">Cargue los ensamblados en tiempo de ejecución cuando una ruta los requiera.</span><span class="sxs-lookup"><span data-stu-id="a40ed-151">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="a40ed-152">Para obtener más información, vea <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="a40ed-152">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="a40ed-153">Compresión</span><span class="sxs-lookup"><span data-stu-id="a40ed-153">Compression</span></span>

<span data-ttu-id="a40ed-154">Cuando se publica una aplicación Blazor WebAssembly, la salida se comprime estáticamente durante la publicación para reducir el tamaño de la aplicación y acabar con la necesidad de compresión en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="a40ed-154">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="a40ed-155">Blazor se basa en el servidor para realizar negociación de contenido y proporcionar archivos comprimidos estáticamente.</span><span class="sxs-lookup"><span data-stu-id="a40ed-155">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="a40ed-156">Cuando una aplicación se haya implementado, compruebe que proporciona archivos comprimidos.</span><span class="sxs-lookup"><span data-stu-id="a40ed-156">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="a40ed-157">Inspeccione la pestaña Red de las herramientas de desarrollo del explorador y compruebe que los archivos se proporcionan con `Content-Encoding: br` o `Content-Encoding: gz`.</span><span class="sxs-lookup"><span data-stu-id="a40ed-157">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="a40ed-158">Si el host no proporciona archivos comprimidos, siga las instrucciones de <xref:blazor/host-and-deploy/webassembly#compression>.</span><span class="sxs-lookup"><span data-stu-id="a40ed-158">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="a40ed-159">Deshabilitar las características sin usar</span><span class="sxs-lookup"><span data-stu-id="a40ed-159">Disable unused features</span></span>

<span data-ttu-id="a40ed-160">El runtime de Blazor WebAssembly incluye las siguientes características de .NET, que se pueden deshabilitar si la aplicación no las necesita cuando el tamaño de la carga útil es más pequeño:</span><span class="sxs-lookup"><span data-stu-id="a40ed-160">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="a40ed-161">Se incluye un archivo de datos para que la información de zona horaria sea correcta.</span><span class="sxs-lookup"><span data-stu-id="a40ed-161">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="a40ed-162">Si la aplicación no necesita esta característica, considere la posibilidad de deshabilitarla estableciendo en `false` la propiedad de MSBuild `BlazorEnableTimeZoneSupport` del archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a40ed-162">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="a40ed-163">De forma predeterminada, Blazor WebAssembly incluye los recursos de globalización necesarios para mostrar valores, como las fechas y la moneda, en la referencia cultural del usuario.</span><span class="sxs-lookup"><span data-stu-id="a40ed-163">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="a40ed-164">Si la aplicación no requiere localización, es posible [configurar la aplicación para que admita la referencia cultural invariable](xref:blazor/globalization-localization), que se basa en la referencia cultural `en-US`:</span><span class="sxs-lookup"><span data-stu-id="a40ed-164">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="a40ed-165">Se incluye información de intercalación para que API como <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionen correctamente.</span><span class="sxs-lookup"><span data-stu-id="a40ed-165">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="a40ed-166">Si está convencido de que la aplicación no necesita datos de intercalación, considere la posibilidad de deshabilitarla estableciendo en `false` la propiedad de MSBuild `BlazorWebAssemblyPreserveCollationData` del archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a40ed-166">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
