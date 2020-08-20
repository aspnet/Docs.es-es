---
title: Esquemas de directiva en ASP.NET Core
author: rick-anderson
description: Los esquemas de directivas de autenticación facilitan el uso de un único esquema de autenticación lógica
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 60ac9914ef811a705c61ab3b2bec61643acc6ec0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634987"
---
# <a name="policy-schemes-in-aspnet-core"></a>Esquemas de directiva en ASP.NET Core

Los esquemas de directivas de autenticación facilitan que un único esquema de autenticación lógica use varios enfoques. Por ejemplo, un esquema de directiva podría usar la autenticación de Google para los desafíos y la cookie autenticación para todo lo demás. Los esquemas de la Directiva de autenticación lo hacen:

* Es fácil reenviar cualquier acción de autenticación a otro esquema.
* Reenviar dinámicamente según la solicitud.

Todos los esquemas de autenticación que usan derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> y el [AuthenticationHandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)asociado:

* Son esquemas de directivas automáticamente en ASP.NET Core 2,1 y versiones posteriores.
* Se puede habilitar mediante la configuración de las opciones del esquema.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra un esquema de nivel superior que combina esquemas de nivel inferior. La autenticación de Google se usa para los desafíos y la cookie autenticación se usa para todo lo demás:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

En el ejemplo siguiente se habilita la selección dinámica de esquemas por solicitud. Es decir, cómo mezclar cookie s y la autenticación de API:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
