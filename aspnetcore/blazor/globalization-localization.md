---
title: Globalización y localización de [Blazor de ASP.NET Core
author: guardrex
description: Aprenda a poner los componentes de [Razor a disposición de los usuarios de diferentes referencias culturales e idiomas.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: blazor/globalization-localization
ms.openlocfilehash: 1d24ebe900dfcdeb8b7bcc97f1d212deea9cecae
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402733"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a><span data-ttu-id="30230-103">Globalización y localización de [Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="30230-103">ASP.NET Core [Blazor globalization and localization</span></span>

<span data-ttu-id="30230-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="30230-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="30230-105">Los componentes de [Razor se pueden poner a disposición de los usuarios de varias referencias culturales e idiomas.</span><span class="sxs-lookup"><span data-stu-id="30230-105">[Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="30230-106">Estos son los escenarios de globalización y localización de .NET disponibles:</span><span class="sxs-lookup"><span data-stu-id="30230-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="30230-107">Sistema de recursos de .NET</span><span class="sxs-lookup"><span data-stu-id="30230-107">.NET's resources system</span></span>
* <span data-ttu-id="30230-108">Formato de fecha y número específico de la referencia cultural</span><span class="sxs-lookup"><span data-stu-id="30230-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="30230-109">Actualmente se admite un conjunto limitado de escenarios de localización de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="30230-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="30230-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> y <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *se admiten* en aplicaciones de [Blazor.</span><span class="sxs-lookup"><span data-stu-id="30230-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in [Blazor apps.</span></span>
* <span data-ttu-id="30230-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> y la localización de anotaciones de datos son escenarios de MVC de ASP.NET Core y **no se admiten** en aplicaciones de [Blazor.</span><span class="sxs-lookup"><span data-stu-id="30230-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in [Blazor apps.</span></span>

<span data-ttu-id="30230-112">Para obtener más información, vea <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="30230-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="30230-113">Globalización</span><span class="sxs-lookup"><span data-stu-id="30230-113">Globalization</span></span>

<span data-ttu-id="30230-114">La funcionalidad [`@bind`](xref:mvc/views/razor#bind) de [Blazor realiza formatos y analiza valores para mostrarlos en función de la referencia cultural actual del usuario.</span><span class="sxs-lookup"><span data-stu-id="30230-114">[Blazor's [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="30230-115">Se puede acceder a la referencia cultural actual desde la propiedad <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="30230-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="30230-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> se emplea para los siguientes tipos de campo (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="30230-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="30230-117">Los tipos de campo anteriores:</span><span class="sxs-lookup"><span data-stu-id="30230-117">The preceding field types:</span></span>

* <span data-ttu-id="30230-118">Se muestran según sus reglas de formato basado en explorador correspondientes.</span><span class="sxs-lookup"><span data-stu-id="30230-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="30230-119">No pueden contener texto de forma libre.</span><span class="sxs-lookup"><span data-stu-id="30230-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="30230-120">Proporcionar características de interacción del usuario en función de la implementación del explorador.</span><span class="sxs-lookup"><span data-stu-id="30230-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="30230-121">Los siguientes tipos de campo tienen requisitos de formato específicos y no se admiten actualmente en [Blazor, ya que no son compatibles con todos los exploradores principales:</span><span class="sxs-lookup"><span data-stu-id="30230-121">The following field types have specific formatting requirements and aren't currently supported by [Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="30230-122">[`@bind`](xref:mvc/views/razor#bind) admite el parámetro `@bind:culture` para proporcionar un elemento <xref:System.Globalization.CultureInfo?displayProperty=fullName> para analizar y dar formato a un valor.</span><span class="sxs-lookup"><span data-stu-id="30230-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="30230-123">No se recomienda especificar una referencia cultural cuando se usen los tipos de campo `date` y `number`.</span><span class="sxs-lookup"><span data-stu-id="30230-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="30230-124">`date` y `number` tienen compatibilidad integrada con [Blazor que proporciona la referencia cultural necesaria.</span><span class="sxs-lookup"><span data-stu-id="30230-124">`date` and `number` have built-in [Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="30230-125">Localización</span><span class="sxs-lookup"><span data-stu-id="30230-125">Localization</span></span>

### [Blazor WebAssembly

<span data-ttu-id="30230-126">Las aplicaciones [Blazor WebAssembly establecen la referencia cultural mediante la [preferencia de idioma](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages) del usuario.</span><span class="sxs-lookup"><span data-stu-id="30230-126">[Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="30230-127">Para configurar explícitamente la referencia cultural, establezca <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> y <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="30230-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="30230-128">De forma predeterminada, la configuración del enlazador de [Blazor para aplicaciones [Blazor WebAssembly quita información de internacionalización, excepto para las configuraciones regionales solicitadas de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="30230-128">By default, [Blazor's linker configuration for [Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="30230-129">Para obtener más información e instrucciones sobre cómo controlar el comportamiento del enlazador, vea <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="30230-129">For more information and guidance on controlling the linker's behavior, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="30230-130">Si bien la referencia cultural que [Blazor selecciona de manera predeterminada puede ser suficiente para la mayoría de los usuarios, considere la posibilidad de ofrecer una manera para que los usuarios especifiquen su configuración regional preferida.</span><span class="sxs-lookup"><span data-stu-id="30230-130">While the culture that [Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="30230-131">Para una aplicación de ejemplo [Blazor WebAssembly con un selector de referencia cultural, consulte la aplicación de ejemplo de localización [`LocSample`](https://github.com/pranavkm/LocSample).</span><span class="sxs-lookup"><span data-stu-id="30230-131">For a [Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### [Blazor Server

<span data-ttu-id="30230-132">Las aplicaciones [Blazor Server se localizan usando un [middleware de localización](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="30230-132">[Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="30230-133">El middleware selecciona la referencia cultural adecuada según los usuarios que solicitan recursos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="30230-133">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="30230-134">La referencia cultural se puede establecer con uno de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="30230-134">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="30230-135">Cookies</span><span class="sxs-lookup"><span data-stu-id="30230-135">Cookies</span></span>](#cookies)
* [<span data-ttu-id="30230-136">Especificando una interfaz de usuario para elegir la referencia cultural</span><span class="sxs-lookup"><span data-stu-id="30230-136">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="30230-137">Para obtener más información y ejemplos, vea <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="30230-137">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="30230-138">Cookies</span><span class="sxs-lookup"><span data-stu-id="30230-138">Cookies</span></span>

<span data-ttu-id="30230-139">Una cookie de referencia cultural de localización puede conservar la referencia cultural del usuario.</span><span class="sxs-lookup"><span data-stu-id="30230-139">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="30230-140">El middleware de localización lee la cookie en solicitudes posteriores para establecer la referencia cultural del usuario.</span><span class="sxs-lookup"><span data-stu-id="30230-140">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="30230-141">El uso de una cookie garantiza que la conexión WebSocket puede propagar correctamente la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="30230-141">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="30230-142">Si los esquemas de localización se basan en la ruta de acceso URL o en la cadena de consulta, puede que el esquema no funcione con WebSockets, por lo que no se podrá conservar la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="30230-142">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="30230-143">Por lo tanto, el uso de una cookie de referencia cultural de localización es el método recomendado.</span><span class="sxs-lookup"><span data-stu-id="30230-143">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="30230-144">Se puede usar cualquier técnica para asignar una referencia cultural si la referencia cultural se conserva en una cookie de localización.</span><span class="sxs-lookup"><span data-stu-id="30230-144">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="30230-145">Si la aplicación ya tiene un esquema de localización establecido para ASP.NET Core del lado servidor, siga usando la infraestructura de localización existente de la aplicación y establezca la cookie de cultura de localización en el esquema de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="30230-145">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="30230-146">En el siguiente ejemplo se muestra cómo establecer la referencia cultural actual en una cookie que el middleware de localización puede leer.</span><span class="sxs-lookup"><span data-stu-id="30230-146">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="30230-147">Cree una expresión de [Razor en el archivo `Pages/_Host.cshtml`, justo dentro de la etiqueta de apertura `<body>`:</span><span class="sxs-lookup"><span data-stu-id="30230-147">Create a [Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="30230-148">La aplicación controla la localización en la siguiente secuencia de eventos:</span><span class="sxs-lookup"><span data-stu-id="30230-148">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="30230-149">El explorador envía una solicitud HTTP inicial a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="30230-149">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="30230-150">El middleware de localización asigna la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="30230-150">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="30230-151">La expresión [Razor de la página `_Host` (`_Host.cshtml`) conserva la referencia cultural en una cookie como parte de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="30230-151">The [Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="30230-152">El explorador abre una conexión WebSocket para crear una sesión [Blazor Server interactiva.</span><span class="sxs-lookup"><span data-stu-id="30230-152">The browser opens a WebSocket connection to create an interactive [Blazor Server session.</span></span>
1. <span data-ttu-id="30230-153">El middleware de localización lee la cookie y asigna la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="30230-153">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="30230-154">La sesión [Blazor Server comienza con la referencia cultural correcta.</span><span class="sxs-lookup"><span data-stu-id="30230-154">The [Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="30230-155">Especificación de una interfaz de usuario para elegir la referencia cultural</span><span class="sxs-lookup"><span data-stu-id="30230-155">Provide UI to choose the culture</span></span>

<span data-ttu-id="30230-156">Para especificar una interfaz de usuario que permita a los usuarios seleccionar una referencia cultural, se recomienda usar un *método basado en el redireccionamiento*.</span><span class="sxs-lookup"><span data-stu-id="30230-156">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="30230-157">El proceso es similar a lo que ocurre en una aplicación web cuando un usuario intenta acceder a un recurso seguro.</span><span class="sxs-lookup"><span data-stu-id="30230-157">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="30230-158">El usuario se redirige a una página de inicio de sesión y, a continuación, se redirige de vuelta al recurso original.</span><span class="sxs-lookup"><span data-stu-id="30230-158">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="30230-159">La aplicación conserva la referencia cultural seleccionada por el usuario a través de un redireccionamiento a un controlador.</span><span class="sxs-lookup"><span data-stu-id="30230-159">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="30230-160">Este controlador establece la referencia cultural seleccionada del usuario en una cookie y redirige al usuario de nuevo al URI original.</span><span class="sxs-lookup"><span data-stu-id="30230-160">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="30230-161">Establezca un punto de conexión HTTP en el servidor para establecer la referencia cultural seleccionada del usuario en una cookie y realizar el redireccionamiento al URI original:</span><span class="sxs-lookup"><span data-stu-id="30230-161">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="30230-162">Use el resultado de la acción <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> para evitar ataques de redireccionamiento abierto.</span><span class="sxs-lookup"><span data-stu-id="30230-162">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="30230-163">Para obtener más información, vea <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="30230-163">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="30230-164">Si la aplicación no está configurada para procesar acciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="30230-164">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="30230-165">Agregue servicios de MVC a la colección de servicios en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="30230-165">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="30230-166">Agregue un enrutamiento de punto de conexión del controlador en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="30230-166">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="30230-167">El siguiente componente muestra un ejemplo sobre cómo realizar el redireccionamiento inicial cuando el usuario selecciona una referencia cultural:</span><span class="sxs-lookup"><span data-stu-id="30230-167">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="30230-168">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="30230-168">Additional resources</span></span>

* <xref:fundamentals/localization>
