---
title: ASP.NET Core 2,1 MVC SameSite cookie ejemplo
author: rick-anderson
description: ASP.NET Core 2,1 MVC SameSite cookie ejemplo
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
uid: security/samesite/mvc21
ms.openlocfilehash: 61878af0f9af72284b43ffd46cca42b0cf043326
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051556"
---
# <a name="aspnet-core-21-mvc-samesite-no-loccookie-sample"></a>ASP.NET Core 2,1 MVC SameSite cookie ejemplo

ASP.NET Core 2,1 tiene compatibilidad integrada con el atributo [SameSite](https://www.owasp.org/index.php/SameSite) , pero se escribió en el estándar original. El [comportamiento revisado](https://github.com/dotnet/aspnetcore/issues/8212) ha cambiado el significado de `SameSite.None` para emitir el atributo sameSite con un valor de `None` , en lugar de no emitir el valor. Si desea que no se emita el valor, puede establecer la `SameSite` propiedad en cookie -1.

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a>Escribir el atributo SameSite

A continuación se describe un ejemplo de cómo escribir un atributo SameSite en un cookie :

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

## <a name="setting-no-loccookie-authentication-and-session-state-no-loccookies"></a>Establecimiento Cookie de la autenticación y el estado de la sesión cookie

Cookie autenticación, estado de sesión y [otros componentes](../samesite.md?view=aspnetcore-2.1) establecen sus opciones de sameSite a través Cookie de opciones, por ejemplo

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

En el código anterior, tanto la cookie autenticación como el estado de la sesión establecen el atributo sameSite en `None` , emitiendo el atributo con un `None` valor y establecen también el atributo Secure en true.

### <a name="run-the-sample"></a>Ejecución del ejemplo

Si ejecuta el [proyecto de ejemplo](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), cargue el depurador del explorador en la página inicial y úselo para ver la cookie recopilación del sitio. Para ello, en Edge y Chrome, presione la `F12` `Application` tecla TAB y haga clic en la dirección URL del sitio en la `Cookies` opción de la `Storage` sección.

![Depurador del explorador::: no-LOC (cookie)::: List](BrowserDebugger.png)

Puede ver en la imagen anterior que el cookie creado por el ejemplo al hacer clic en el botón "crear SameSite Cookie " tiene un valor de atributo SameSite de `Lax` , que coincide con el valor establecido en el [código de ejemplo](#sampleCode).

## <a name="intercepting-no-loccookies"></a><a name="interception"></a>Interceptar cookie

Para interceptar cookie s, para ajustar el valor NONE según su compatibilidad en el agente del explorador del usuario, debe usar el `CookiePolicy` middleware. Debe colocarse en la canalización de solicitudes HTTP **antes** que los componentes que escriben cookie y se configuran en `ConfigureServices()` .

Para insertarlo en el uso de la canalización `app.UseCookiePolicy()` en el `Configure(IApplicationBuilder, IHostingEnvironment)` método de [Startup.CS](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs). Por ejemplo:

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

Después, en la `ConfigureServices(IServiceCollection services)` configuración de la cookie Directiva que se va a llamar a una clase auxiliar cuando cookie se anexan o eliminan. Por ejemplo:

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

La función auxiliar `CheckSameSite(HttpContext, CookieOptions)` :

* Se llama a cuando se cookie anexan a la solicitud o se eliminan de la solicitud.
* Comprueba si la `SameSite` propiedad está establecida en `None` .
* Si `SameSite` se establece en `None` y se sabe que el agente de usuario actual no admite el valor del atributo none. La comprobación se realiza mediante la clase [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :
  * Establece `SameSite` para que no emita el valor estableciendo la propiedad en. `(SameSiteMode)(-1)`

## <a name="targeting-net-framework"></a>Destinar .NET Framework

ASP.NET Core y System. Web (ASP.NET clásico) tienen implementaciones independientes de SameSite. Las revisiones de SameSite KB para .NET Framework no son necesarias si se usa ASP.NET Core y el requisito de versión mínima del marco de trabajo de System. Web SameSite (.NET 4.7.2) se aplican a ASP.NET Core.

ASP.NET Core en .NET requiere la actualización de las dependencias de paquetes Nuget para obtener las correcciones correspondientes.

Para obtener los cambios de ASP.NET Core para .NET Framework Asegúrese de que tiene una referencia directa a las versiones y los paquetes revisados (2.1.14 o versiones posteriores 2,1).

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a>Más información
 
[Actualizaciones](https://www.chromium.org/updates/same-site) 
 de Chrome [ASP.net Core documentación](../samesite.md?view=aspnetcore-2.1) 
 de SameSite [ASP.NET Core 2,1 SameSite cambio de anuncio](https://github.com/dotnet/aspnetcore/issues/8212)