---
title: Acceso a HttpContext en ASP.NET Core
author: coderandhiker
description: Obtenga información sobre cómo acceder a HttpContext en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/httpcontext
ms.openlocfilehash: d4512c9fa136e518fa0230c0cf9c607519eed6d8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399457"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="5dbd7-103">Acceso a HttpContext en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5dbd7-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="5dbd7-104">Las aplicaciones ASP.NET Core acceden `HttpContext` a través de la interfaz <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> y su implementación predeterminada <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="5dbd7-105">Solo es necesario utilizar `IHttpContextAccessor` si necesita acceder al valor `HttpContext` dentro de un servicio.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="5dbd7-106">Uso de HttpContext desde Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5dbd7-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="5dbd7-107"><xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> de Razor Pages expone la propiedad <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>:</span><span class="sxs-lookup"><span data-stu-id="5dbd7-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="5dbd7-108">Uso de HttpContext desde una vista de Razor</span><span class="sxs-lookup"><span data-stu-id="5dbd7-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="5dbd7-109">Las vistas de Razor exponen el valor `HttpContext` directamente mediante una propiedad [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) de la vista.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="5dbd7-110">En el ejemplo siguiente se recupera el nombre de usuario actual de una aplicación de intranet mediante Autenticación de Windows:</span><span class="sxs-lookup"><span data-stu-id="5dbd7-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="5dbd7-111">Uso de HttpContext desde un controlador</span><span class="sxs-lookup"><span data-stu-id="5dbd7-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="5dbd7-112">Los controladores exponen la propiedad [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext):</span><span class="sxs-lookup"><span data-stu-id="5dbd7-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="5dbd7-113">Uso de HttpContext desde el software intermedio</span><span class="sxs-lookup"><span data-stu-id="5dbd7-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="5dbd7-114">Cuando se trabaja con componentes de software intermedio personalizado, `HttpContext` se pasa al método `Invoke` o `InvokeAsync` y es accesible cuando el software intermedio está configurado:</span><span class="sxs-lookup"><span data-stu-id="5dbd7-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="5dbd7-115">Uso de HttpContext desde componentes personalizados</span><span class="sxs-lookup"><span data-stu-id="5dbd7-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="5dbd7-116">Para los componentes de otro marco y componentes personalizados que requieren acceso a `HttpContext`, el enfoque recomendado es registrar una dependencia mediante el contenedor integrado de [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5dbd7-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5dbd7-117">El contenedor de inserción de dependencias proporciona `IHttpContextAccessor` a cualquier clase que la declare como una dependencia en sus constructores:</span><span class="sxs-lookup"><span data-stu-id="5dbd7-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="5dbd7-118">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5dbd7-118">In the following example:</span></span>

* <span data-ttu-id="5dbd7-119">`UserRepository` declara su dependencia de `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="5dbd7-120">La dependencia se proporciona cuando la inserción de dependencias resuelve la cadena de dependencias y crea una instancia de `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="5dbd7-121">Acceso de HttpContext desde un subproceso en segundo plano</span><span class="sxs-lookup"><span data-stu-id="5dbd7-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="5dbd7-122">`HttpContext` no es seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="5dbd7-123">La lectura o escritura de propiedades de `HttpContext` fuera del procesamiento de una solicitud puede iniciar una excepción <xref:System.NullReferenceException>.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="5dbd7-124">Si la aplicación inicia errores `NullReferenceException` esporádicos, revise los elementos del código que inician el procesamiento en segundo plano, o bien que lo continúan después de que se complete una solicitud.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="5dbd7-125">Busque errores, como la definición de un método de controlador como `async void`.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="5dbd7-126">Para realizar el trabajo en segundo plano de forma segura con datos `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="5dbd7-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="5dbd7-127">Copie los datos necesarios durante el procesamiento de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="5dbd7-128">Pase los datos copiados a una tarea en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="5dbd7-129">Para evitar código no seguro, no pase nunca `HttpContext` a un método que realice trabajos en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="5dbd7-130">En su lugar, pase los datos que necesite.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-130">Pass the required data instead.</span></span> <span data-ttu-id="5dbd7-131">En el ejemplo siguiente, se llama a `SendEmailCore` para empezar a enviar un correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="5dbd7-132">`correlationId` se pasa a `SendEmailCore`, no a `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="5dbd7-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="5dbd7-133">La ejecución de código no espera a que se complete `SendEmailCore`:</span><span class="sxs-lookup"><span data-stu-id="5dbd7-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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

## <a name="blazor-and-shared-state"></a>Blazor<span data-ttu-id="5dbd7-134"> y estado compartido</span><span class="sxs-lookup"><span data-stu-id="5dbd7-134"> and shared state</span></span>

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]
