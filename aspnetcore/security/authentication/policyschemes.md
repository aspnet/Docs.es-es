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
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="8d006-103">Esquemas de directiva en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8d006-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="8d006-104">Los esquemas de directivas de autenticación facilitan que un único esquema de autenticación lógica use varios enfoques.</span><span class="sxs-lookup"><span data-stu-id="8d006-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="8d006-105">Por ejemplo, un esquema de directiva podría usar la autenticación de Google para los desafíos y la cookie autenticación para todo lo demás.</span><span class="sxs-lookup"><span data-stu-id="8d006-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="8d006-106">Los esquemas de la Directiva de autenticación lo hacen:</span><span class="sxs-lookup"><span data-stu-id="8d006-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="8d006-107">Es fácil reenviar cualquier acción de autenticación a otro esquema.</span><span class="sxs-lookup"><span data-stu-id="8d006-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="8d006-108">Reenviar dinámicamente según la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8d006-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="8d006-109">Todos los esquemas de autenticación que usan derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> y el [AuthenticationHandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)asociado:</span><span class="sxs-lookup"><span data-stu-id="8d006-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="8d006-110">Son esquemas de directivas automáticamente en ASP.NET Core 2,1 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="8d006-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="8d006-111">Se puede habilitar mediante la configuración de las opciones del esquema.</span><span class="sxs-lookup"><span data-stu-id="8d006-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="8d006-112">Ejemplos</span><span class="sxs-lookup"><span data-stu-id="8d006-112">Examples</span></span>

<span data-ttu-id="8d006-113">En el ejemplo siguiente se muestra un esquema de nivel superior que combina esquemas de nivel inferior.</span><span class="sxs-lookup"><span data-stu-id="8d006-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="8d006-114">La autenticación de Google se usa para los desafíos y la cookie autenticación se usa para todo lo demás:</span><span class="sxs-lookup"><span data-stu-id="8d006-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="8d006-115">En el ejemplo siguiente se habilita la selección dinámica de esquemas por solicitud.</span><span class="sxs-lookup"><span data-stu-id="8d006-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="8d006-116">Es decir, cómo mezclar cookie s y la autenticación de API:</span><span class="sxs-lookup"><span data-stu-id="8d006-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
