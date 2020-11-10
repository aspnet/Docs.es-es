---
title: Extensibilidad de la localización
author: hishamco
description: Obtenga información sobre cómo extender las API de localización en aplicaciones ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
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
uid: fundamentals/localization-extensibility
ms.openlocfilehash: a6ef5a547e6ccba6771cdf892a9636f83d6796b1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053740"
---
# <a name="localization-extensibility"></a>Extensibilidad de la localización

Por [Hisham Bin Ateya](https://github.com/hishamco)

Este artículo:

* Enumera los puntos de extensibilidad en las API de localización.
* Proporciona instrucciones sobre cómo extender la localización en aplicaciones ASP.NET Core.

## <a name="extensible-points-in-localization-apis"></a>Puntos extensibles en las API de localización

Las API de localización de ASP.NET Core están diseñadas para ser extensibles. La extensibilidad permite a los desarrolladores personalizar la localización según sus necesidades. Por ejemplo, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) tiene un elemento `POStringLocalizer`. `POStringLocalizer` describe en detalle el uso de [Localización de un objeto portátil](xref:fundamentals/portable-object-localization) para usar archivos `PO` con el fin de almacenar los recursos de localización.

En este artículo se enumeran los dos puntos de extensibilidad principales que proporcionan las API de localización: 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>Proveedores de referencias culturales de localización

Las API de localización de ASP.NET Core tienen cuatro proveedores por defecto que pueden determinar la referencia cultural actual de una solicitud en ejecución:

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

Los proveedores anteriores se describen de forma detallada en la documentación del [middleware de localización](xref:fundamentals/localization). Si los proveedores predeterminados no satisfacen sus necesidades, cree un proveedor personalizado mediante uno de los métodos siguientes:

### <a name="use-customrequestcultureprovider"></a>Uso de CustomRequestCultureProvider

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> proporciona un elemento <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> personalizado que utiliza un delegado simple para determinar la referencia cultural de localización actual:

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>Uso de una nueva implementación de RequestCultureProvider

Se puede crear una nueva implementación de <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> que determine la información de referencia cultural de la solicitud de un origen personalizado. Por ejemplo, el origen personalizado puede ser una base de datos o un archivo de configuración.

En el ejemplo siguiente se muestra el elemento `AppSettingsRequestCultureProvider`, que extiende el elemento <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> para determinar la información de referencia cultural de la solicitud de *appsettings.json* :

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a>Recursos de localización

La localización de ASP.NET Core proporciona <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>. <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> es una implementación de <xref:Microsoft.Extensions.Localization.IStringLocalizer> que usa `resx` para almacenar los recursos de localización.

No es necesario limitarse a los archivos `resx`. Al implementar `IStringLocalized`, se puede usar cualquier origen de datos.

En los proyectos de ejemplo siguientes, se implementa <xref:Microsoft.Extensions.Localization.IStringLocalizer>: 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [SqlLocalizer](https://github.com/damienbod/AspNetCoreLocalization)
