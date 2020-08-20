---
title: Procedimientos recomendados de rendimiento de Blazor WebAssembly en ASP.NET Core
author: pranavkm
description: Sugerencias para aumentar el rendimiento de las aplicaciones Blazor WebAssembly en ASP.NET Core y evitar problemas de rendimiento comunes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
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
ms.openlocfilehash: 819947be90e7f09c7ba853df1af1f3c7066c0219
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625822"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>Procedimientos recomendados de rendimiento de Blazor WebAssembly en ASP.NET Core

Por [Pranav Krishnamoorthy](https://github.com/pranavkm)

En este artículo se proporcionan instrucciones relativas a procedimientos recomendados de rendimiento de Blazor WebAssembly en ASP.NET Core.

## <a name="avoid-unnecessary-component-renders"></a>Evitar representaciones de componentes innecesarias

El algoritmo de diferenciación de Blazor evita que un componente se represente cuando dicho algoritmo percibe que ese componente no ha cambiado. Invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> si prefiere tener un control específico sobre la representación de componentes.

Si crea un componente de solo interfaz de usuario que nunca cambia tras la representación inicial, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para que devuelva `false`:

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

La mayoría de las aplicaciones no requieren un control específico, pero <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> se puede usar para representar de forma selectiva un componente en respuesta a un evento de interfaz de usuario. Usar <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> también podría ser importante en escenarios en los que se representa un gran número de componentes. Piense en una cuadrícula, donde el uso de <xref:Microsoft.AspNetCore.Components.EventCallback> en un componente de una celda de la cuadrícula llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en la cuadrícula. La llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> provoca una nueva representación de todos los componentes secundarios. Si solo un pequeño número de celdas requiere una nueva representación, utilice <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para evitar la reducción de rendimiento de las representaciones innecesarias.

En el ejemplo siguiente:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> se invalida y se establece en el valor del campo <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, que es `false` inicialmente, cuando el componente se carga.
* Cuando el botón se selecciona, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> se establece en `true`, lo que obliga al componente a volver a representarse con el elemento `currentCount` actualizado.
* Inmediatamente después de que el componente se vuelva a representar, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> establece el valor de <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> de nuevo en `false` para impedir que se vuelva a representar hasta la próxima vez que el botón se seleccione.

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

Para obtener más información, vea <xref:blazor/components/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Virtualización de fragmentos reutilizables

Los componentes constituyen un método práctico para generar fragmentos de código y marcado reutilizables. En general, se recomienda crear componentes individuales que se ajusten más adecuadamente a los requisitos de la aplicación. Una advertencia que hacer a este respecto es que cada componente secundario adicional contribuye al tiempo total necesario para representar un componente primario. En la mayoría de las aplicaciones, esta sobrecarga adicional es insignificante. Las aplicaciones que generan un gran número de componentes deben considerar el uso de estrategias para reducir la sobrecarga de procesamiento, como limitar el número de componentes representados.

Por ejemplo, una cuadrícula o una lista que representa cientos de filas que contienen componentes hace un uso intensivo del procesador para lograr la representación. Así pues, considere la posibilidad de virtualizar un diseño de lista o cuadrícula para que solo se represente un subconjunto de los componentes en un momento determinado. Para obtener un ejemplo de representación de un subconjunto de componentes, vea los siguientes componentes en la [aplicación de ejemplo de `Virtualization` (repositorio de GitHub aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* Componente `Virtualize` ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): componente escrito en C# que implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> para representar un conjunto de filas de datos meteorológicos en función el desplazamiento del usuario con el ratón.
* Componente `FetchData` ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): usa el componente `Virtualize` para mostrar 25 filas de datos meteorológicos a la vez.

## <a name="avoid-javascript-interop-to-marshal-data"></a>Evitar la interoperabilidad de JavaScript para serializar datos

En Blazor WebAssembly, una llamada de interoperabilidad de JavaScript debe atravesar el límite WebAssembly-JS. Al serializar y deserializar contenido en los dos contextos, se crea una sobrecarga de procesamiento para la aplicación. Las llamadas de interoperabilidad de JavaScript frecuentes suelen afectar negativamente al rendimiento. Para reducir la serialización de datos en el límite, determine si la aplicación es capaz de aglutinar muchas cargas pequeñas en una sola carga grande para evitar el gran volumen de cambios de contexto entre WebAssembly y JavaScript.

## <a name="use-systemtextjson"></a>Uso de System.Text.Json

La implementación de interoperabilidad de JavaScript de Blazor se basa en <xref:System.Text.Json>, que es una biblioteca de serialización de JSON de alto rendimiento con una asignación de memoria reducida. El uso de <xref:System.Text.Json> no da como resultado un mayor tamaño de carga de aplicación frente a la adición de una o varias bibliotecas JSON alternativas.

Para obtener instrucciones sobre la migración, vea [Procedimiento para realizar la migración de `Newtonsoft.Json` a `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>Uso de API de interoperabilidad de JavaScript sincrónicas y no serializadas cuando sea necesario

Blazor WebAssembly ofrece dos versiones más de <xref:Microsoft.JSInterop.IJSRuntime> con respecto a la única versión disponible en las aplicaciones Blazor Server:

* <xref:Microsoft.JSInterop.IJSInProcessRuntime> permite invocar llamadas de interoperabilidad de JavaScript sincrónicamente, lo que genera una sobrecarga menor que las versiones asincrónicas:

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

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permite llamadas de interoperabilidad de JavaScript no serializadas:

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
  > Aunque el uso de <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> es el método de interoperabilidad de JavaScript con menor sobrecarga, las API de JavaScript necesarias para interactuar con estas API no están documentadas en este momento y están sujetas a cambios importantes en versiones futuras.

## <a name="reduce-app-size"></a>Reducción del tamaño de la aplicación

### <a name="intermediate-language-il-linking"></a>Vinculación de lenguaje intermedio

[Cuando una aplicación Blazor WebAssembly se vincula](xref:blazor/host-and-deploy/configure-linker), el tamaño de la aplicación se reduce recortando el código que no se usa en los archivos binarios de la aplicación. El vinculador solo está habilitado de forma predeterminada cuando se compila en la configuración `Release`. Para sacar partido de esto, publique la aplicación de implementación con el comando [`dotnet publish`](/dotnet/core/tools/dotnet-publish), con la opción [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) establecida en `Release`:

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a>Ensamblados de carga diferida

Cargue los ensamblados en tiempo de ejecución cuando una ruta los requiera. Para obtener más información, vea <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Compresión

Cuando se publica una aplicación Blazor WebAssembly, la salida se comprime estáticamente durante la publicación para reducir el tamaño de la aplicación y acabar con la necesidad de compresión en tiempo de ejecución. Blazor se basa en el servidor para realizar negociación de contenido y proporcionar archivos comprimidos estáticamente.

Cuando una aplicación se haya implementado, compruebe que proporciona archivos comprimidos. Inspeccione la pestaña Red de las herramientas de desarrollo del explorador y compruebe que los archivos se proporcionan con `Content-Encoding: br` o `Content-Encoding: gz`. Si el host no proporciona archivos comprimidos, siga las instrucciones de <xref:blazor/host-and-deploy/webassembly#compression>.

### <a name="disable-unused-features"></a>Deshabilitar las características sin usar

El runtime de Blazor WebAssembly incluye las siguientes características de .NET, que se pueden deshabilitar si la aplicación no las necesita cuando el tamaño de la carga útil es más pequeño:

* Se incluye un archivo de datos para que la información de zona horaria sea correcta. Si la aplicación no necesita esta característica, considere la posibilidad de deshabilitarla estableciendo en `false` la propiedad de MSBuild `BlazorEnableTimeZoneSupport` del archivo de proyecto de la aplicación:

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* Se incluye información de intercalación para que API como <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionen correctamente. Si está convencido de que la aplicación no necesita datos de intercalación, considere la posibilidad de deshabilitarla estableciendo en `false` la propiedad de MSBuild `BlazorWebAssemblyPreserveCollationData` del archivo de proyecto de la aplicación:

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
