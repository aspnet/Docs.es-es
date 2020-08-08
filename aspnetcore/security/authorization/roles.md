---
title: Autorización basada en roles en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo restringir el acceso a la acción y el controlador ASP.NET Core pasando roles al atributo Authorize.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/roles
ms.openlocfilehash: 44e5f9a2a429c9f4a510d2f3e564ddd6bbe77205
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021125"
---
# <a name="role-based-authorization-in-aspnet-core"></a>Autorización basada en roles en ASP.NET Core

<a name="security-authorization-role-based"></a>

Cuando se crea una identidad, puede pertenecer a uno o varios roles. Por ejemplo, Tracy puede pertenecer a los roles de administrador y de usuario, mientras que Scott solo puede pertenecer a la función de usuario. La forma en que se crean y administran estos roles depende de la memoria auxiliar del proceso de autorización. Los roles se exponen al desarrollador a través del método [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) en la clase [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .

## <a name="adding-role-checks"></a>Agregar comprobaciones de roles

Las comprobaciones de autorización basadas en roles son declarativas &mdash; el desarrollador las inserta en el código, en un controlador o una acción dentro de un controlador, especificando los roles de los que el usuario actual debe ser miembro para tener acceso al recurso solicitado.

Por ejemplo, el código siguiente limita el acceso a las acciones de `AdministrationController` a los usuarios que son miembros del `Administrator` rol:

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Puede especificar varios roles como una lista separada por comas:

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Este controlador solo es accesible para los usuarios que son miembros del `HRManager` rol o del `Finance` rol.

Si aplica varios atributos, el acceso de un usuario debe ser miembro de todos los roles especificados. en el ejemplo siguiente se requiere que un usuario deba ser miembro del `PowerUser` rol y `ControlPanelUser` .

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Puede limitar aún más el acceso aplicando atributos de autorización de rol adicionales en el nivel de acción:

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

En los miembros del fragmento de código anterior del `Administrator` rol o el `PowerUser` rol puede tener acceso al controlador y a la `SetTime` acción, pero solo los miembros del `Administrator` rol pueden tener acceso a la `ShutDown` acción.

También puede bloquear un controlador, pero permitir el acceso anónimo y no autenticado a acciones individuales.

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

En Razor el caso de las páginas, `AuthorizeAttribute` puede aplicarse mediante:

* Mediante una [Convención](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), o
* Aplicar `AuthorizeAttribute` a la `PageModel` instancia de:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> Los atributos de filtro, incluido `AuthorizeAttribute` , solo se pueden aplicar a PageModel y no se pueden aplicar a métodos de control de página específicos.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Comprobaciones de roles basadas en directivas

Los requisitos de rol también se pueden expresar con la nueva sintaxis de Directiva, donde un desarrollador registra una directiva en el inicio como parte de la configuración del servicio de autorización. Esto ocurre normalmente en `ConfigureServices()` en el archivo *Startup.CS* .

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

Las directivas se aplican mediante la `Policy` propiedad en el `AuthorizeAttribute` atributo:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Si desea especificar varios roles permitidos en un requisito, puede especificarlos como parámetros para el `RequireRole` método:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Este ejemplo autoriza a los usuarios que pertenecen a `Administrator` los `PowerUser` roles, o `BackupAdministrator` .

### <a name="add-role-services-to-no-locidentity"></a>Agregar servicios de función aIdentity

Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para agregar servicios de rol:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

