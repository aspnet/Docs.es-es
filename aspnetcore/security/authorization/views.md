---
title: Autorización basada en vistas en ASP.NET Core MVC
author: rick-anderson
description: 'En este documento se muestra cómo insertar y emplear el servicio de autorización dentro de una vista de ASP.NET Core Razor .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/views
ms.openlocfilehash: b3d6e595aa08208f2bf9e95d7070cf9c24802b62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061332"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="f897b-103">Autorización basada en vistas en ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="f897b-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="f897b-104">A menudo, un desarrollador desea mostrar, ocultar o modificar de otra forma una interfaz de usuario basada en la identidad del usuario actual.</span><span class="sxs-lookup"><span data-stu-id="f897b-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="f897b-105">Puede tener acceso al servicio de autorización en las vistas de MVC a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f897b-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f897b-106">Para insertar el servicio de autorización en una Razor vista, use la `@inject` Directiva:</span><span class="sxs-lookup"><span data-stu-id="f897b-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="f897b-107">Si desea usar el servicio de autorización en cada vista, coloque la `@inject` Directiva en el archivo *_ViewImports. cshtml* del directorio *views* .</span><span class="sxs-lookup"><span data-stu-id="f897b-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="f897b-108">Para más información, vea [Dependency injection into views](xref:mvc/views/dependency-injection) (Inserción de dependencias en vistas).</span><span class="sxs-lookup"><span data-stu-id="f897b-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="f897b-109">Use el servicio de autorización insertado para invocar `AuthorizeAsync` exactamente de la misma manera que en la [autorización basada en recursos](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="f897b-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="f897b-110">En algunos casos, el recurso será el modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="f897b-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="f897b-111">Invoque `AuthorizeAsync` exactamente del mismo modo que lo haría durante la [autorización basada en recursos](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="f897b-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="f897b-112">En el código anterior, el modelo se pasa como un recurso que debería tener en cuenta la evaluación de la Directiva.</span><span class="sxs-lookup"><span data-stu-id="f897b-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="f897b-113">No se base en la alternancia de la visibilidad de los elementos de la interfaz de usuario de la aplicación como la única comprobación de autorización.</span><span class="sxs-lookup"><span data-stu-id="f897b-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="f897b-114">Ocultar un elemento de la interfaz de usuario puede no impedir completamente el acceso a su acción de controlador asociada.</span><span class="sxs-lookup"><span data-stu-id="f897b-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="f897b-115">Por ejemplo, considere el botón en el fragmento de código anterior.</span><span class="sxs-lookup"><span data-stu-id="f897b-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="f897b-116">Un usuario puede invocar el `Edit` método de acción si sabe que la dirección URL relativa del recurso es */Document/Edit/1* .</span><span class="sxs-lookup"><span data-stu-id="f897b-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1* .</span></span> <span data-ttu-id="f897b-117">Por esta razón, el `Edit` método de acción debe realizar su propia comprobación de autorización.</span><span class="sxs-lookup"><span data-stu-id="f897b-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
