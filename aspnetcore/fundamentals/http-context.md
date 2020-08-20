---
title: Acceso a HttpContext en ASP.NET Core
author: coderandhiker
description: Obtenga información sobre cómo acceder a HttpContext en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
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
uid: fundamentals/httpcontext
ms.openlocfilehash: 0eade76f8cf0bdd81cc290218f36fe9276233104
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635286"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Acceso a HttpContext en ASP.NET Core

Las aplicaciones ASP.NET Core acceden `HttpContext` a través de la interfaz <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> y su implementación predeterminada <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>. Solo es necesario utilizar `IHttpContextAccessor` si necesita acceder al valor `HttpContext` dentro de un servicio.

## <a name="use-httpcontext-from-no-locrazor-pages"></a>Uso de HttpContext desde Razor Pages

<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> de Razor Pages expone la propiedad <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>:

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-no-locrazor-view"></a>Uso de HttpContext desde una vista de Razor

Las vistas de Razor exponen el valor `HttpContext` directamente mediante una propiedad [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) de la vista. En el ejemplo siguiente se recupera el nombre de usuario actual de una aplicación de intranet mediante Autenticación de Windows:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Uso de HttpContext desde un controlador

Los controladores exponen la propiedad [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext):

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a>Uso de HttpContext desde el software intermedio

Cuando se trabaja con componentes de software intermedio personalizado, `HttpContext` se pasa al método `Invoke` o `InvokeAsync` y es accesible cuando el software intermedio está configurado:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Uso de HttpContext desde componentes personalizados

Para los componentes de otro marco y componentes personalizados que requieren acceso a `HttpContext`, el enfoque recomendado es registrar una dependencia mediante el contenedor integrado de [inserción de dependencias](xref:fundamentals/dependency-injection). El contenedor de inserción de dependencias proporciona `IHttpContextAccessor` a cualquier clase que la declare como una dependencia en sus constructores:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

En el ejemplo siguiente:

* `UserRepository` declara su dependencia de `IHttpContextAccessor`.
* La dependencia se proporciona cuando la inserción de dependencias resuelve la cadena de dependencias y crea una instancia de `UserRepository`.

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a>Acceso de HttpContext desde un subproceso en segundo plano

`HttpContext` no es seguro para subprocesos. La lectura o escritura de propiedades de `HttpContext` fuera del procesamiento de una solicitud puede iniciar una excepción <xref:System.NullReferenceException>.

> [!NOTE]
> Si la aplicación inicia errores `NullReferenceException` esporádicos, revise los elementos del código que inician el procesamiento en segundo plano, o bien que lo continúan después de que se complete una solicitud. Busque errores, como la definición de un método de controlador como `async void`.

Para realizar el trabajo en segundo plano de forma segura con datos `HttpContext`:

* Copie los datos necesarios durante el procesamiento de la solicitud.
* Pase los datos copiados a una tarea en segundo plano.

Para evitar código no seguro, no pase nunca `HttpContext` a un método que realice trabajos en segundo plano. En su lugar, pase los datos que necesite. En el ejemplo siguiente, se llama a `SendEmailCore` para empezar a enviar un correo electrónico. `correlationId` se pasa a `SendEmailCore`, no a `HttpContext`. La ejecución de código no espera a que se complete `SendEmailCore`:

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
```

## <a name="no-locblazor-and-shared-state"></a>Blazor y estado compartido

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]
