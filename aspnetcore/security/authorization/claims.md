---
title: Autorización basada en notificaciones en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo agregar comprobaciones de notificaciones para la autorización en una aplicación ASP.NET Core.
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
uid: security/authorization/claims
ms.openlocfilehash: 639cacbab2688adfe769ef2c6954ea877cf0c66a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022360"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>Autorización basada en notificaciones en ASP.NET Core

<a name="security-authorization-claims-based"></a>

Cuando se crea una identidad, se le puede asignar una o varias notificaciones emitidas por una entidad de confianza. Una demanda es un par de valor de nombre que representa lo que es el sujeto, no lo que puede hacer el sujeto. Por ejemplo, puede tener una licencia de controlador, emitida por una entidad de licencia de conducción local. La licencia de su controlador tiene su fecha de nacimiento. En este caso, el nombre de la demanda sería `DateOfBirth` , el valor de la demanda sería su fecha de nacimiento, por ejemplo, `8th June 1970` y el emisor sería la autoridad de la licencia de conducción. La autorización basada en notificaciones, en su forma más simple, comprueba el valor de una notificación y permite el acceso a un recurso en función de ese valor. Por ejemplo, si desea tener acceso a un club nocturno, el proceso de autorización podría ser:

El responsable de seguridad de la puerta evaluaría el valor de la demanda de la fecha de nacimiento y si confía en el emisor (la entidad de la licencia de conducción) antes de concederle acceso.

Una identidad puede contener varias notificaciones con varios valores y puede contener varias notificaciones del mismo tipo.

## <a name="adding-claims-checks"></a>Agregar comprobaciones de notificaciones

Las comprobaciones de autorización basadas en notificaciones son declarativas: el desarrollador las inserta en el código, en un controlador o en una acción dentro de un controlador, especificando las notificaciones que el usuario actual debe poseer y, opcionalmente, el valor que debe tener la notificación para tener acceso al recurso solicitado. Los requisitos de las notificaciones están basados en directivas, el desarrollador debe compilar y registrar una directiva que exprese los requisitos de las notificaciones.

El tipo más sencillo de la Directiva de notificaciones busca la presencia de una demanda y no comprueba el valor.

En primer lugar, debe compilar y registrar la Directiva. Esto se produce como parte de la configuración del servicio de autorización, que normalmente participa en `ConfigureServices()` el archivo *Startup.CS* .

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
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
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

En este caso, la `EmployeeOnly` Directiva comprueba la presencia de una `EmployeeNumber` demanda en la identidad actual.

A continuación, aplique la Directiva mediante la `Policy` propiedad del `AuthorizeAttribute` atributo para especificar el nombre de la Directiva;

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

El `AuthorizeAttribute` atributo se puede aplicar a un controlador completo; en este caso, solo se permitirá el acceso a cualquier acción del controlador a las identidades que coincidan con la Directiva.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

Si tiene un controlador que está protegido por el `AuthorizeAttribute` atributo, pero desea permitir el acceso anónimo a determinadas acciones, aplique el `AllowAnonymousAttribute` atributo.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

La mayoría de las notificaciones tienen un valor. Puede especificar una lista de valores permitidos al crear la Directiva. El siguiente ejemplo solo se realizaría correctamente para los empleados cuyo número de empleado era 1, 2, 3, 4 o 5.

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
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
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a>Agregar una comprobación de notificaciones genéricas

Si el valor de la petición no es un valor único o se requiere una transformación, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion). Para obtener más información, vea [usar un FUNC para cumplir una directiva](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Evaluación de varias directivas

Si aplica varias directivas a un controlador o una acción, todas las directivas deben pasar antes de que se conceda el acceso. Por ejemplo:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

En el ejemplo anterior, cualquier identidad que cumpla la `EmployeeOnly` Directiva puede tener acceso a la `Payslip` acción, ya que esa Directiva se aplica en el controlador. Sin embargo, para poder llamar a la `UpdateSalary` acción, la *both* identidad debe cumplir la `EmployeeOnly` Directiva y la `HumanResources` Directiva.

Si desea directivas más complicadas, como tomar una demanda de fecha de nacimiento, calcular una edad a partir de ella y comprobar la edad es 21 o anterior, debe escribir [controladores de directivas personalizados](xref:security/authorization/policies).
