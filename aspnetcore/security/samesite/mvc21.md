---
title: 'ASP.NET Core 2,1 MVC SameSite cookie ejemplo'
author: rick-anderson
description: 'ASP.NET Core 2,1 MVC SameSite cookie ejemplo'
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
uid: security/samesite/mvc21
ms.openlocfilehash: 61878af0f9af72284b43ffd46cca42b0cf043326
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051556"
---
# <a name="aspnet-core-21-mvc-samesite-no-loccookie-sample"></a><span data-ttu-id="d5fde-103">ASP.NET Core 2,1 MVC SameSite cookie ejemplo</span><span class="sxs-lookup"><span data-stu-id="d5fde-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="d5fde-104">ASP.NET Core 2,1 tiene compatibilidad integrada con el atributo [SameSite](https://www.owasp.org/index.php/SameSite) , pero se escribió en el estándar original.</span><span class="sxs-lookup"><span data-stu-id="d5fde-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="d5fde-105">El [comportamiento revisado](https://github.com/dotnet/aspnetcore/issues/8212) ha cambiado el significado de `SameSite.None` para emitir el atributo sameSite con un valor de `None` , en lugar de no emitir el valor.</span><span class="sxs-lookup"><span data-stu-id="d5fde-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="d5fde-106">Si desea que no se emita el valor, puede establecer la `SameSite` propiedad en cookie -1.</span><span class="sxs-lookup"><span data-stu-id="d5fde-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="d5fde-107">Escribir el atributo SameSite</span><span class="sxs-lookup"><span data-stu-id="d5fde-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="d5fde-108">A continuación se describe un ejemplo de cómo escribir un atributo SameSite en un cookie :</span><span class="sxs-lookup"><span data-stu-id="d5fde-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-no-loccookie-authentication-and-session-state-no-loccookies"></a><span data-ttu-id="d5fde-109">Establecimiento Cookie de la autenticación y el estado de la sesión cookie</span><span class="sxs-lookup"><span data-stu-id="d5fde-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="d5fde-110">Cookie autenticación, estado de sesión y [otros componentes](../samesite.md?view=aspnetcore-2.1) establecen sus opciones de sameSite a través Cookie de opciones, por ejemplo</span><span class="sxs-lookup"><span data-stu-id="d5fde-110">Cookie authentication, session state and [various other components](../samesite.md?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="d5fde-111">En el código anterior, tanto la cookie autenticación como el estado de la sesión establecen el atributo sameSite en `None` , emitiendo el atributo con un `None` valor y establecen también el atributo Secure en true.</span><span class="sxs-lookup"><span data-stu-id="d5fde-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="d5fde-112">Ejecución del ejemplo</span><span class="sxs-lookup"><span data-stu-id="d5fde-112">Run the sample</span></span>

<span data-ttu-id="d5fde-113">Si ejecuta el [proyecto de ejemplo](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), cargue el depurador del explorador en la página inicial y úselo para ver la cookie recopilación del sitio.</span><span class="sxs-lookup"><span data-stu-id="d5fde-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="d5fde-114">Para ello, en Edge y Chrome, presione la `F12` `Application` tecla TAB y haga clic en la dirección URL del sitio en la `Cookies` opción de la `Storage` sección.</span><span class="sxs-lookup"><span data-stu-id="d5fde-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Depurador del explorador::: no-LOC (cookie)::: List](BrowserDebugger.png)

<span data-ttu-id="d5fde-116">Puede ver en la imagen anterior que el cookie creado por el ejemplo al hacer clic en el botón "crear SameSite Cookie " tiene un valor de atributo SameSite de `Lax` , que coincide con el valor establecido en el [código de ejemplo](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="d5fde-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-no-loccookies"></a><a name="interception"></a><span data-ttu-id="d5fde-117">Interceptar cookie</span><span class="sxs-lookup"><span data-stu-id="d5fde-117">Intercepting cookies</span></span>

<span data-ttu-id="d5fde-118">Para interceptar cookie s, para ajustar el valor NONE según su compatibilidad en el agente del explorador del usuario, debe usar el `CookiePolicy` middleware.</span><span class="sxs-lookup"><span data-stu-id="d5fde-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="d5fde-119">Debe colocarse en la canalización de solicitudes HTTP **antes** que los componentes que escriben cookie y se configuran en `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="d5fde-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="d5fde-120">Para insertarlo en el uso de la canalización `app.UseCookiePolicy()` en el `Configure(IApplicationBuilder, IHostingEnvironment)` método de [Startup.CS](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="d5fde-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="d5fde-121">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d5fde-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="d5fde-122">Después, en la `ConfigureServices(IServiceCollection services)` configuración de la cookie Directiva que se va a llamar a una clase auxiliar cuando cookie se anexan o eliminan.</span><span class="sxs-lookup"><span data-stu-id="d5fde-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="d5fde-123">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d5fde-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="d5fde-124">La función auxiliar `CheckSameSite(HttpContext, CookieOptions)` :</span><span class="sxs-lookup"><span data-stu-id="d5fde-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="d5fde-125">Se llama a cuando se cookie anexan a la solicitud o se eliminan de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d5fde-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="d5fde-126">Comprueba si la `SameSite` propiedad está establecida en `None` .</span><span class="sxs-lookup"><span data-stu-id="d5fde-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="d5fde-127">Si `SameSite` se establece en `None` y se sabe que el agente de usuario actual no admite el valor del atributo none.</span><span class="sxs-lookup"><span data-stu-id="d5fde-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="d5fde-128">La comprobación se realiza mediante la clase [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="d5fde-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="d5fde-129">Establece `SameSite` para que no emita el valor estableciendo la propiedad en. `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="d5fde-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="d5fde-130">Destinar .NET Framework</span><span class="sxs-lookup"><span data-stu-id="d5fde-130">Targeting .NET Framework</span></span>

<span data-ttu-id="d5fde-131">ASP.NET Core y System. Web (ASP.NET clásico) tienen implementaciones independientes de SameSite.</span><span class="sxs-lookup"><span data-stu-id="d5fde-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="d5fde-132">Las revisiones de SameSite KB para .NET Framework no son necesarias si se usa ASP.NET Core y el requisito de versión mínima del marco de trabajo de System. Web SameSite (.NET 4.7.2) se aplican a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5fde-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="d5fde-133">ASP.NET Core en .NET requiere la actualización de las dependencias de paquetes Nuget para obtener las correcciones correspondientes.</span><span class="sxs-lookup"><span data-stu-id="d5fde-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="d5fde-134">Para obtener los cambios de ASP.NET Core para .NET Framework Asegúrese de que tiene una referencia directa a las versiones y los paquetes revisados (2.1.14 o versiones posteriores 2,1).</span><span class="sxs-lookup"><span data-stu-id="d5fde-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="d5fde-135">Más información</span><span class="sxs-lookup"><span data-stu-id="d5fde-135">More Information</span></span>
 
<span data-ttu-id="d5fde-136">[Actualizaciones](https://www.chromium.org/updates/same-site) 
 de Chrome [ASP.net Core documentación](../samesite.md?view=aspnetcore-2.1) 
 de SameSite [ASP.NET Core 2,1 SameSite cambio de anuncio](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="d5fde-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](../samesite.md?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>