---
title: Autorización simple en ASP.NET Core
author: rick-anderson
description: Aprenda a usar el atributo Authorize para restringir el acceso a ASP.NET Core controladores y acciones.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/simple
ms.openlocfilehash: edf027b23ba6b22146e4521c134e67ac6fe1bd03
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634805"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="f5848-103">Autorización simple en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f5848-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="f5848-104">La autorización en ASP.NET Core se controla con <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> y sus diversos parámetros.</span><span class="sxs-lookup"><span data-stu-id="f5848-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="f5848-105">En su forma más sencilla, aplicar el `[Authorize]` atributo a un controlador, una acción o una Razor página, limita el acceso a ese componente a cualquier usuario autenticado.</span><span class="sxs-lookup"><span data-stu-id="f5848-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="f5848-106">Por ejemplo, el código siguiente limita el acceso a `AccountController` a cualquier usuario autenticado.</span><span class="sxs-lookup"><span data-stu-id="f5848-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="f5848-107">Si desea aplicar la autorización a una acción en lugar del controlador, aplique el `AuthorizeAttribute` atributo a la propia acción:</span><span class="sxs-lookup"><span data-stu-id="f5848-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="f5848-108">Ahora solo los usuarios autenticados pueden tener acceso a la `Logout` función.</span><span class="sxs-lookup"><span data-stu-id="f5848-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="f5848-109">También puede usar el `AllowAnonymous` atributo para permitir el acceso de usuarios no autenticados a acciones individuales.</span><span class="sxs-lookup"><span data-stu-id="f5848-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="f5848-110">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f5848-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="f5848-111">Esto permitiría solo a los usuarios autenticados en `AccountController` , a excepción de la `Login` acción, que es accesible para todos, independientemente de su estado autenticado o no autenticado/anónimo.</span><span class="sxs-lookup"><span data-stu-id="f5848-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="f5848-112">`[AllowAnonymous]` omite todas las instrucciones de autorización.</span><span class="sxs-lookup"><span data-stu-id="f5848-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="f5848-113">Si combina `[AllowAnonymous]` y cualquier `[Authorize]` atributo, `[Authorize]` se omiten los atributos.</span><span class="sxs-lookup"><span data-stu-id="f5848-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="f5848-114">Por ejemplo, si se aplica `[AllowAnonymous]` en el nivel de controlador, `[Authorize]` se omite cualquier atributo del mismo controlador (o de cualquier acción que contenga).</span><span class="sxs-lookup"><span data-stu-id="f5848-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-no-locrazor-pages"></a><span data-ttu-id="f5848-115">Autorización de atributos y Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f5848-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="f5848-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ***No*** se puede aplicar a los Razor controladores de páginas.</span><span class="sxs-lookup"><span data-stu-id="f5848-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can ***not*** be applied to Razor Page handlers.</span></span> <span data-ttu-id="f5848-117">Por ejemplo, `[Authorize]` no se puede aplicar a `OnGet` , `OnPost` o a cualquier otro controlador de página.</span><span class="sxs-lookup"><span data-stu-id="f5848-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span> <span data-ttu-id="f5848-118">Considere la posibilidad de usar un controlador de MVC ASP.NET Core para páginas con distintos requisitos de autorización para diferentes controladores.</span><span class="sxs-lookup"><span data-stu-id="f5848-118">Consider using an ASP.NET Core MVC controller for pages with different authorization requirements for different handlers.</span></span>

<span data-ttu-id="f5848-119">Se pueden usar los dos enfoques siguientes para aplicar la autorización a Razor métodos de control de páginas:</span><span class="sxs-lookup"><span data-stu-id="f5848-119">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

* <span data-ttu-id="f5848-120">Use páginas independientes para los controladores de páginas que requieran una autorización diferente.</span><span class="sxs-lookup"><span data-stu-id="f5848-120">Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="f5848-121">Se ha pasado el contenido compartido a una o varias [vistas parciales](xref:mvc/views/partial).</span><span class="sxs-lookup"><span data-stu-id="f5848-121">Moved shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="f5848-122">Siempre que sea posible, este es el enfoque recomendado.</span><span class="sxs-lookup"><span data-stu-id="f5848-122">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="f5848-123">En el caso de contenido que debe compartir una página común, escriba un filtro que realice la autorización como parte de [IAsyncPageFilter. OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="f5848-123">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="f5848-124">En el proyecto [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) de github se muestra este enfoque:</span><span class="sxs-lookup"><span data-stu-id="f5848-124">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="f5848-125">[AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implementa el filtro de autorización:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="f5848-125">The [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="f5848-126">El atributo [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) se aplica al `OnGet` controlador de página: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="f5848-126">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="f5848-127">El [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) enfoque de ejemplo PageHandlerAuth ***no:***</span><span class="sxs-lookup"><span data-stu-id="f5848-127">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does ***not***:</span></span>
> * <span data-ttu-id="f5848-128">Cree con los atributos de autorización aplicados a la página, al modelo de página o a nivel global.</span><span class="sxs-lookup"><span data-stu-id="f5848-128">Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="f5848-129">La composición de los atributos de autorización da lugar a la autenticación y autorización que se ejecutan varias veces cuando se tiene una `AuthorizeAttribute` o más `AuthorizeFilter` instancias que también se aplican a la página.</span><span class="sxs-lookup"><span data-stu-id="f5848-129">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="f5848-130">Trabaje junto con el resto de ASP.NET Core sistema de autenticación y autorización.</span><span class="sxs-lookup"><span data-stu-id="f5848-130">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="f5848-131">Debe comprobar que el uso de este enfoque funciona correctamente para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f5848-131">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="f5848-132">No hay planes para admitir en los `AuthorizeAttribute` Razor controladores de páginas.</span><span class="sxs-lookup"><span data-stu-id="f5848-132">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 
