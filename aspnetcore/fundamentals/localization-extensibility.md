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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053740"
---
# <a name="localization-extensibility"></a><span data-ttu-id="083ab-103">Extensibilidad de la localización</span><span class="sxs-lookup"><span data-stu-id="083ab-103">Localization Extensibility</span></span>

<span data-ttu-id="083ab-104">Por [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="083ab-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="083ab-105">Este artículo:</span><span class="sxs-lookup"><span data-stu-id="083ab-105">This article:</span></span>

* <span data-ttu-id="083ab-106">Enumera los puntos de extensibilidad en las API de localización.</span><span class="sxs-lookup"><span data-stu-id="083ab-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="083ab-107">Proporciona instrucciones sobre cómo extender la localización en aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="083ab-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="083ab-108">Puntos extensibles en las API de localización</span><span class="sxs-lookup"><span data-stu-id="083ab-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="083ab-109">Las API de localización de ASP.NET Core están diseñadas para ser extensibles.</span><span class="sxs-lookup"><span data-stu-id="083ab-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="083ab-110">La extensibilidad permite a los desarrolladores personalizar la localización según sus necesidades.</span><span class="sxs-lookup"><span data-stu-id="083ab-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="083ab-111">Por ejemplo, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) tiene un elemento `POStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="083ab-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="083ab-112">`POStringLocalizer` describe en detalle el uso de [Localización de un objeto portátil](xref:fundamentals/portable-object-localization) para usar archivos `PO` con el fin de almacenar los recursos de localización.</span><span class="sxs-lookup"><span data-stu-id="083ab-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="083ab-113">En este artículo se enumeran los dos puntos de extensibilidad principales que proporcionan las API de localización:</span><span class="sxs-lookup"><span data-stu-id="083ab-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="083ab-114">Proveedores de referencias culturales de localización</span><span class="sxs-lookup"><span data-stu-id="083ab-114">Localization Culture Providers</span></span>

<span data-ttu-id="083ab-115">Las API de localización de ASP.NET Core tienen cuatro proveedores por defecto que pueden determinar la referencia cultural actual de una solicitud en ejecución:</span><span class="sxs-lookup"><span data-stu-id="083ab-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="083ab-116">Los proveedores anteriores se describen de forma detallada en la documentación del [middleware de localización](xref:fundamentals/localization).</span><span class="sxs-lookup"><span data-stu-id="083ab-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="083ab-117">Si los proveedores predeterminados no satisfacen sus necesidades, cree un proveedor personalizado mediante uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="083ab-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="083ab-118">Uso de CustomRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="083ab-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="083ab-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> proporciona un elemento <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> personalizado que utiliza un delegado simple para determinar la referencia cultural de localización actual:</span><span class="sxs-lookup"><span data-stu-id="083ab-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

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

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="083ab-120">Uso de una nueva implementación de RequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="083ab-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="083ab-121">Se puede crear una nueva implementación de <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> que determine la información de referencia cultural de la solicitud de un origen personalizado.</span><span class="sxs-lookup"><span data-stu-id="083ab-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="083ab-122">Por ejemplo, el origen personalizado puede ser una base de datos o un archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="083ab-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="083ab-123">En el ejemplo siguiente se muestra el elemento `AppSettingsRequestCultureProvider`, que extiende el elemento <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> para determinar la información de referencia cultural de la solicitud de *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="083ab-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

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

## <a name="localization-resources"></a><span data-ttu-id="083ab-124">Recursos de localización</span><span class="sxs-lookup"><span data-stu-id="083ab-124">Localization resources</span></span>

<span data-ttu-id="083ab-125">La localización de ASP.NET Core proporciona <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span><span class="sxs-lookup"><span data-stu-id="083ab-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="083ab-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> es una implementación de <xref:Microsoft.Extensions.Localization.IStringLocalizer> que usa `resx` para almacenar los recursos de localización.</span><span class="sxs-lookup"><span data-stu-id="083ab-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="083ab-127">No es necesario limitarse a los archivos `resx`.</span><span class="sxs-lookup"><span data-stu-id="083ab-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="083ab-128">Al implementar `IStringLocalized`, se puede usar cualquier origen de datos.</span><span class="sxs-lookup"><span data-stu-id="083ab-128">By implementing `IStringLocalized`, any data source can be used.</span></span>

<span data-ttu-id="083ab-129">En los proyectos de ejemplo siguientes, se implementa <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span><span class="sxs-lookup"><span data-stu-id="083ab-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="083ab-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="083ab-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="083ab-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="083ab-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="083ab-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="083ab-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)
