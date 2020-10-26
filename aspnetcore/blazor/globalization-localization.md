---
title: 'Globalización y localización de :::no-loc(Blazor)::: de ASP.NET Core'
author: guardrex
description: 'Aprenda a poner los componentes de :::no-loc(Razor)::: a disposición de los usuarios de diferentes referencias culturales e idiomas.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/globalization-localization
ms.openlocfilehash: 4345dd8525c2e72aaddc8e45a4fd4d9bfdd63040
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326528"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="d35d1-103">Globalización y localización de :::no-loc(Blazor)::: de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d35d1-103">ASP.NET Core :::no-loc(Blazor)::: globalization and localization</span></span>

<span data-ttu-id="d35d1-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d35d1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="d35d1-105">Los componentes de :::no-loc(Razor)::: se pueden poner a disposición de los usuarios de varias referencias culturales e idiomas.</span><span class="sxs-lookup"><span data-stu-id="d35d1-105">:::no-loc(Razor)::: components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="d35d1-106">Estos son los escenarios de globalización y localización de .NET disponibles:</span><span class="sxs-lookup"><span data-stu-id="d35d1-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="d35d1-107">Sistema de recursos de .NET</span><span class="sxs-lookup"><span data-stu-id="d35d1-107">.NET's resources system</span></span>
* <span data-ttu-id="d35d1-108">Formato de fecha y número específico de la referencia cultural</span><span class="sxs-lookup"><span data-stu-id="d35d1-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="d35d1-109">Actualmente se admite un conjunto limitado de escenarios de localización de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d35d1-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="d35d1-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> y <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *se admiten* en aplicaciones de :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="d35d1-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in :::no-loc(Blazor)::: apps.</span></span>
* <span data-ttu-id="d35d1-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> y la localización de anotaciones de datos son escenarios de MVC de ASP.NET Core y **no se admiten** en aplicaciones de :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="d35d1-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in :::no-loc(Blazor)::: apps.</span></span>

<span data-ttu-id="d35d1-112">Para obtener más información, vea <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="d35d1-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="d35d1-113">Globalización</span><span class="sxs-lookup"><span data-stu-id="d35d1-113">Globalization</span></span>

<span data-ttu-id="d35d1-114">La funcionalidad [`@bind`](xref:mvc/views/razor#bind) de :::no-loc(Blazor)::: realiza formatos y analiza valores para mostrarlos en función de la referencia cultural actual del usuario.</span><span class="sxs-lookup"><span data-stu-id="d35d1-114">:::no-loc(Blazor):::'s [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="d35d1-115">Se puede acceder a la referencia cultural actual desde la propiedad <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="d35d1-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="d35d1-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> se emplea para los siguientes tipos de campo (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="d35d1-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="d35d1-117">Los tipos de campo anteriores:</span><span class="sxs-lookup"><span data-stu-id="d35d1-117">The preceding field types:</span></span>

* <span data-ttu-id="d35d1-118">Se muestran según sus reglas de formato basado en explorador correspondientes.</span><span class="sxs-lookup"><span data-stu-id="d35d1-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="d35d1-119">No pueden contener texto de forma libre.</span><span class="sxs-lookup"><span data-stu-id="d35d1-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="d35d1-120">Proporcionar características de interacción del usuario en función de la implementación del explorador.</span><span class="sxs-lookup"><span data-stu-id="d35d1-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="d35d1-121">Los siguientes tipos de campo tienen requisitos de formato específicos y no se admiten actualmente en :::no-loc(Blazor):::, ya que no son compatibles con todos los exploradores principales:</span><span class="sxs-lookup"><span data-stu-id="d35d1-121">The following field types have specific formatting requirements and aren't currently supported by :::no-loc(Blazor)::: because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="d35d1-122">[`@bind`](xref:mvc/views/razor#bind) admite el parámetro `@bind:culture` para proporcionar un elemento <xref:System.Globalization.CultureInfo?displayProperty=fullName> para analizar y dar formato a un valor.</span><span class="sxs-lookup"><span data-stu-id="d35d1-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="d35d1-123">No se recomienda especificar una referencia cultural cuando se usen los tipos de campo `date` y `number`.</span><span class="sxs-lookup"><span data-stu-id="d35d1-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="d35d1-124">`date` y `number` tienen compatibilidad integrada con :::no-loc(Blazor)::: que proporciona la referencia cultural necesaria.</span><span class="sxs-lookup"><span data-stu-id="d35d1-124">`date` and `number` have built-in :::no-loc(Blazor)::: support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="d35d1-125">Localización</span><span class="sxs-lookup"><span data-stu-id="d35d1-125">Localization</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="d35d1-126">Las aplicaciones :::no-loc(Blazor WebAssembly)::: establecen la referencia cultural mediante la [preferencia de idioma](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages) del usuario.</span><span class="sxs-lookup"><span data-stu-id="d35d1-126">:::no-loc(Blazor WebAssembly)::: apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="d35d1-127">Para configurar explícitamente la referencia cultural, establezca <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> y <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="d35d1-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d35d1-128">De forma predeterminada, :::no-loc(Blazor WebAssembly)::: incluye los recursos de globalización mínimos necesarios para mostrar valores, como las fechas y la moneda, en la referencia cultural del usuario.</span><span class="sxs-lookup"><span data-stu-id="d35d1-128">By default, :::no-loc(Blazor WebAssembly)::: carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="d35d1-129">Las aplicaciones que deben admitir el cambio dinámico de la referencia cultural deben configurar `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="d35d1-129">Applications that must support dynamically changing the culture should configure `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>true</:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="d35d1-130">También se puede configurar :::no-loc(Blazor WebAssembly)::: para que se inicie con una referencia cultural de aplicación específica mediante las opciones que se pasan a `:::no-loc(Blazor):::.start`.</span><span class="sxs-lookup"><span data-stu-id="d35d1-130">:::no-loc(Blazor WebAssembly)::: can also be configured to launch using a specific application culture using options passed to `:::no-loc(Blazor):::.start`.</span></span> <span data-ttu-id="d35d1-131">Por ejemplo, en el ejemplo siguiente se muestra una aplicación configurada para iniciarse con la referencia cultural `en-GB`:</span><span class="sxs-lookup"><span data-stu-id="d35d1-131">For instance, the sample below shows an app configured to launch using the `en-GB` culture:</span></span>

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  :::no-loc(Blazor):::.start({
    applicationCulture: 'en-GB'
  });
</script>
```

<span data-ttu-id="d35d1-132">El valor de `applicationCulture` debe ajustarse al [formato de etiqueta de idioma BCP-47](https://tools.ietf.org/html/bcp47).</span><span class="sxs-lookup"><span data-stu-id="d35d1-132">The value for `applicationCulture` should conform to the [BCP-47 language tag format](https://tools.ietf.org/html/bcp47).</span></span>

<span data-ttu-id="d35d1-133">Si la aplicación no requiere localización, es posible configurar la aplicación para que admita la referencia cultural invariable, que se basa en la referencia cultural `en-US`:</span><span class="sxs-lookup"><span data-stu-id="d35d1-133">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d35d1-134">De forma predeterminada, la configuración del enlazador de lenguaje intermedio (IL) para aplicaciones de :::no-loc(Blazor WebAssembly)::: quita la información de internacionalización, excepto para las configuraciones regionales solicitadas de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="d35d1-134">By default, the Intermediate Language (IL) Linker configuration for :::no-loc(Blazor WebAssembly)::: apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="d35d1-135">Para obtener más información, vea <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="d35d1-135">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="d35d1-136">Si bien la referencia cultural que :::no-loc(Blazor)::: selecciona de manera predeterminada puede ser suficiente para la mayoría de los usuarios, considere la posibilidad de ofrecer una manera para que los usuarios especifiquen su configuración regional preferida.</span><span class="sxs-lookup"><span data-stu-id="d35d1-136">While the culture that :::no-loc(Blazor)::: selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="d35d1-137">Para una aplicación de ejemplo :::no-loc(Blazor WebAssembly)::: con un selector de referencia cultural, consulte la aplicación de ejemplo de localización [`LocSample`](https://github.com/pranavkm/LocSample).</span><span class="sxs-lookup"><span data-stu-id="d35d1-137">For a :::no-loc(Blazor WebAssembly)::: sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### :::no-loc(Blazor Server):::

<span data-ttu-id="d35d1-138">Las aplicaciones :::no-loc(Blazor Server)::: se localizan usando un [middleware de localización](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="d35d1-138">:::no-loc(Blazor Server)::: apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="d35d1-139">El middleware selecciona la referencia cultural adecuada según los usuarios que solicitan recursos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d35d1-139">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="d35d1-140">La referencia cultural se puede establecer con uno de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="d35d1-140">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="d35d1-141">[:::no-loc(Cookie):::s](#:::no-loc(cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="d35d1-141">[:::no-loc(Cookie):::s](#:::no-loc(cookie):::s)</span></span>
* [<span data-ttu-id="d35d1-142">Especificando una interfaz de usuario para elegir la referencia cultural</span><span class="sxs-lookup"><span data-stu-id="d35d1-142">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="d35d1-143">Para obtener más información y ejemplos, vea <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="d35d1-143">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="d35d1-144">:::no-loc(Cookie):::s</span><span class="sxs-lookup"><span data-stu-id="d35d1-144">:::no-loc(Cookie):::s</span></span>

<span data-ttu-id="d35d1-145">Una :::no-loc(cookie)::: de referencia cultural de localización puede conservar la referencia cultural del usuario.</span><span class="sxs-lookup"><span data-stu-id="d35d1-145">A localization culture :::no-loc(cookie)::: can persist the user's culture.</span></span> <span data-ttu-id="d35d1-146">El middleware de localización lee la :::no-loc(cookie)::: en solicitudes posteriores para establecer la referencia cultural del usuario.</span><span class="sxs-lookup"><span data-stu-id="d35d1-146">The Localization Middleware reads the :::no-loc(cookie)::: on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="d35d1-147">El uso de una :::no-loc(cookie)::: garantiza que la conexión WebSocket puede propagar correctamente la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="d35d1-147">Use of a :::no-loc(cookie)::: ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="d35d1-148">Si los esquemas de localización se basan en la ruta de acceso URL o en la cadena de consulta, puede que el esquema no funcione con WebSockets, por lo que no se podrá conservar la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="d35d1-148">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="d35d1-149">Por lo tanto, el uso de una :::no-loc(cookie)::: de referencia cultural de localización es el método recomendado.</span><span class="sxs-lookup"><span data-stu-id="d35d1-149">Therefore, use of a localization culture :::no-loc(cookie)::: is the recommended approach.</span></span>

<span data-ttu-id="d35d1-150">Se puede usar cualquier técnica para asignar una referencia cultural si la referencia cultural se conserva en una :::no-loc(cookie)::: de localización.</span><span class="sxs-lookup"><span data-stu-id="d35d1-150">Any technique can be used to assign a culture if the culture is persisted in a localization :::no-loc(cookie):::.</span></span> <span data-ttu-id="d35d1-151">Si la aplicación ya tiene un esquema de localización establecido para ASP.NET Core del lado servidor, siga usando la infraestructura de localización existente de la aplicación y establezca la :::no-loc(cookie)::: de cultura de localización en el esquema de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d35d1-151">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture :::no-loc(cookie)::: within the app's scheme.</span></span>

<span data-ttu-id="d35d1-152">En el siguiente ejemplo se muestra cómo establecer la referencia cultural actual en una :::no-loc(cookie)::: que el middleware de localización puede leer.</span><span class="sxs-lookup"><span data-stu-id="d35d1-152">The following example shows how to set the current culture in a :::no-loc(cookie)::: that can be read by the Localization Middleware.</span></span> <span data-ttu-id="d35d1-153">Cree una expresión de :::no-loc(Razor)::: en el archivo `Pages/_Host.cshtml`, justo dentro de la etiqueta de apertura `<body>`:</span><span class="sxs-lookup"><span data-stu-id="d35d1-153">Create a :::no-loc(Razor)::: expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.:::no-loc(Cookie):::s.Append(
            :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
            :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="d35d1-154">La aplicación controla la localización en la siguiente secuencia de eventos:</span><span class="sxs-lookup"><span data-stu-id="d35d1-154">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="d35d1-155">El explorador envía una solicitud HTTP inicial a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d35d1-155">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="d35d1-156">El middleware de localización asigna la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="d35d1-156">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="d35d1-157">La expresión :::no-loc(Razor)::: de la página `_Host` (`_Host.cshtml`) conserva la referencia cultural en una :::no-loc(cookie)::: como parte de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="d35d1-157">The :::no-loc(Razor)::: expression in the `_Host` page (`_Host.cshtml`) persists the culture in a :::no-loc(cookie)::: as part of the response.</span></span>
1. <span data-ttu-id="d35d1-158">El explorador abre una conexión WebSocket para crear una sesión :::no-loc(Blazor Server)::: interactiva.</span><span class="sxs-lookup"><span data-stu-id="d35d1-158">The browser opens a WebSocket connection to create an interactive :::no-loc(Blazor Server)::: session.</span></span>
1. <span data-ttu-id="d35d1-159">El middleware de localización lee la :::no-loc(cookie)::: y asigna la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="d35d1-159">The Localization Middleware reads the :::no-loc(cookie)::: and assigns the culture.</span></span>
1. <span data-ttu-id="d35d1-160">La sesión :::no-loc(Blazor Server)::: comienza con la referencia cultural correcta.</span><span class="sxs-lookup"><span data-stu-id="d35d1-160">The :::no-loc(Blazor Server)::: session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="d35d1-161">Especificación de una interfaz de usuario para elegir la referencia cultural</span><span class="sxs-lookup"><span data-stu-id="d35d1-161">Provide UI to choose the culture</span></span>

<span data-ttu-id="d35d1-162">Para especificar una interfaz de usuario que permita a los usuarios seleccionar una referencia cultural, se recomienda usar un *método basado en el redireccionamiento* .</span><span class="sxs-lookup"><span data-stu-id="d35d1-162">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="d35d1-163">El proceso es similar a lo que ocurre en una aplicación web cuando un usuario intenta acceder a un recurso seguro.</span><span class="sxs-lookup"><span data-stu-id="d35d1-163">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="d35d1-164">El usuario se redirige a una página de inicio de sesión y, a continuación, se redirige de vuelta al recurso original.</span><span class="sxs-lookup"><span data-stu-id="d35d1-164">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="d35d1-165">La aplicación conserva la referencia cultural seleccionada por el usuario a través de un redireccionamiento a un controlador.</span><span class="sxs-lookup"><span data-stu-id="d35d1-165">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="d35d1-166">Este controlador establece la referencia cultural seleccionada del usuario en una :::no-loc(cookie)::: y redirige al usuario de nuevo al URI original.</span><span class="sxs-lookup"><span data-stu-id="d35d1-166">The controller sets the user's selected culture into a :::no-loc(cookie)::: and redirects the user back to the original URI.</span></span>

<span data-ttu-id="d35d1-167">Establezca un punto de conexión HTTP en el servidor para establecer la referencia cultural seleccionada del usuario en una :::no-loc(cookie)::: y realizar el redireccionamiento al URI original:</span><span class="sxs-lookup"><span data-stu-id="d35d1-167">Establish an HTTP endpoint on the server to set the user's selected culture in a :::no-loc(cookie)::: and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.:::no-loc(Cookie):::s.Append(
                :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
                :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="d35d1-168">Use el resultado de la acción <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> para evitar ataques de redireccionamiento abierto.</span><span class="sxs-lookup"><span data-stu-id="d35d1-168">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="d35d1-169">Para obtener más información, vea <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="d35d1-169">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="d35d1-170">Si la aplicación no está configurada para procesar acciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="d35d1-170">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="d35d1-171">Agregue servicios de MVC a la colección de servicios en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d35d1-171">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="d35d1-172">Agregue un enrutamiento de punto de conexión del controlador en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d35d1-172">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.Map:::no-loc(Blazor):::Hub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="d35d1-173">El siguiente componente muestra un ejemplo sobre cómo realizar el redireccionamiento inicial cuando el usuario selecciona una referencia cultural:</span><span class="sxs-lookup"><span data-stu-id="d35d1-173">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="d35d1-174">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d35d1-174">Additional resources</span></span>

* <xref:fundamentals/localization>
