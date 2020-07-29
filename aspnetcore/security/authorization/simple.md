---
title: Autorización simple en ASP.NET Core
author: rick-anderson
description: Aprenda a usar el atributo Authorize para restringir el acceso a ASP.NET Core controladores y acciones.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: 09514032349d489b73d5bb785f11e44ca18b169c
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160236"
---
# <a name="simple-authorization-in-aspnet-core"></a>Autorización simple en ASP.NET Core

<a name="security-authorization-simple"></a>

La autorización en ASP.NET Core se controla con <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> y sus diversos parámetros. En su forma más sencilla, aplicar el `[Authorize]` atributo a un controlador, una acción o una Razor página, limita el acceso a ese componente a cualquier usuario autenticado.

Por ejemplo, el código siguiente limita el acceso a `AccountController` a cualquier usuario autenticado.

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

Si desea aplicar la autorización a una acción en lugar del controlador, aplique el `AuthorizeAttribute` atributo a la propia acción:

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

Ahora solo los usuarios autenticados pueden tener acceso a la `Logout` función.

También puede usar el `AllowAnonymous` atributo para permitir el acceso de usuarios no autenticados a acciones individuales. Por ejemplo:

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

Esto permitiría solo a los usuarios autenticados en `AccountController` , a excepción de la `Login` acción, que es accesible para todos, independientemente de su estado autenticado o no autenticado/anónimo.

> [!WARNING]
> `[AllowAnonymous]`omite todas las instrucciones de autorización. Si combina `[AllowAnonymous]` y cualquier `[Authorize]` atributo, `[Authorize]` se omiten los atributos. Por ejemplo, si se aplica `[AllowAnonymous]` en el nivel de controlador, `[Authorize]` se omite cualquier atributo del mismo controlador (o de cualquier acción que contenga).

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-no-locrazor-pages"></a>Autorización de atributos y Razor Pages

<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ***No*** se puede aplicar a los Razor controladores de páginas. Por ejemplo, `[Authorize]` no se puede aplicar a `OnGet` , `OnPost` o a cualquier otro controlador de página. Considere la posibilidad de usar un controlador de MVC ASP.NET Core para páginas con distintos requisitos de autorización para diferentes controladores.

Se pueden usar los dos enfoques siguientes para aplicar la autorización a Razor métodos de control de páginas:

* Use páginas independientes para los controladores de páginas que requieran una autorización diferente. Se ha pasado el contenido compartido a una o varias [vistas parciales](xref:mvc/views/partial). Siempre que sea posible, este es el enfoque recomendado.
* En el caso de contenido que debe compartir una página común, escriba un filtro que realice la autorización como parte de [IAsyncPageFilter. OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A). En el proyecto [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) de github se muestra este enfoque:
  * [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implementa el filtro de autorización:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]

  * El atributo [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) se aplica al `OnGet` controlador de página:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]

> [!WARNING]
> El [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) enfoque de ejemplo PageHandlerAuth ***no:***
> * Cree con los atributos de autorización aplicados a la página, al modelo de página o a nivel global. La composición de los atributos de autorización da lugar a la autenticación y autorización que se ejecutan varias veces cuando se tiene una `AuthorizeAttribute` o más `AuthorizeFilter` instancias que también se aplican a la página.
> * Trabaje junto con el resto de ASP.NET Core sistema de autenticación y autorización. Debe comprobar que el uso de este enfoque funciona correctamente para la aplicación.

No hay planes para admitir en los `AuthorizeAttribute` Razor controladores de páginas. 
