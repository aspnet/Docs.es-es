---
title: Autorización basada en directivas en ASP.NET Core
author: rick-anderson
description: Aprenda a crear y usar controladores de directivas de autorización para aplicar los requisitos de autorización en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/policies
ms.openlocfilehash: 668c68bc328860ef17e1f2df09103fca07733ef7
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160180"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="e26cf-103">Autorización basada en directivas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e26cf-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e26cf-104">En segundo plano, la autorización basada en [roles](xref:security/authorization/roles) y la [autorización basada en notificaciones](xref:security/authorization/claims) usan un requisito, un controlador de requisitos y una directiva configurada previamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="e26cf-105">Estos bloques de creación admiten la expresión de evaluaciones de autorización en el código.</span><span class="sxs-lookup"><span data-stu-id="e26cf-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="e26cf-106">El resultado es una estructura de autorización más enriquecida, reutilizable y comprobable.</span><span class="sxs-lookup"><span data-stu-id="e26cf-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="e26cf-107">Una directiva de autorización se compone de uno o varios requisitos.</span><span class="sxs-lookup"><span data-stu-id="e26cf-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="e26cf-108">Se registra como parte de la configuración del servicio de autorización, en el `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="e26cf-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="e26cf-109">En el ejemplo anterior, se crea una directiva "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="e26cf-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="e26cf-110">Tiene un único requisito &mdash; de una edad mínima, que se proporciona como un parámetro al requisito.</span><span class="sxs-lookup"><span data-stu-id="e26cf-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="e26cf-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="e26cf-111">IAuthorizationService</span></span> 

<span data-ttu-id="e26cf-112">El servicio principal que determina si la autorización se realiza correctamente es <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="e26cf-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="e26cf-113">En el código anterior se resaltan los dos métodos de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="e26cf-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="e26cf-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>es un servicio de marcador sin métodos y el mecanismo para hacer un seguimiento de si la autorización se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="e26cf-115">Cada uno <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> de ellos es responsable de comprobar si se cumplen los requisitos:</span><span class="sxs-lookup"><span data-stu-id="e26cf-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="e26cf-116">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> clase es lo que el controlador usa para marcar si se cumplen los requisitos:</span><span class="sxs-lookup"><span data-stu-id="e26cf-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="e26cf-117">En el código siguiente se muestra la implementación predeterminada simplificada (y anotada con comentarios) del servicio de autorización:</span><span class="sxs-lookup"><span data-stu-id="e26cf-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="e26cf-118">En el código siguiente se muestra un típico `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e26cf-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();
}
```

<span data-ttu-id="e26cf-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> o `[Authorize(Policy = "Something")]` para la autorización.</span><span class="sxs-lookup"><span data-stu-id="e26cf-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="e26cf-120">Aplicar directivas a los controladores de MVC</span><span class="sxs-lookup"><span data-stu-id="e26cf-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="e26cf-121">Si utiliza :::no-loc(Razor)::: páginas, consulte [aplicar directivas a :::no-loc(Razor)::: las páginas](#apply-policies-to-razor-pages) de este documento.</span><span class="sxs-lookup"><span data-stu-id="e26cf-121">If you're using :::no-loc(Razor)::: Pages, see [Apply policies to :::no-loc(Razor)::: Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="e26cf-122">Las directivas se aplican a los controladores mediante el uso del `[Authorize]` atributo con el nombre de la Directiva.</span><span class="sxs-lookup"><span data-stu-id="e26cf-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e26cf-123">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e26cf-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="e26cf-124">Aplicar directivas a :::no-loc(Razor)::: las páginas</span><span class="sxs-lookup"><span data-stu-id="e26cf-124">Apply policies to :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="e26cf-125">Las directivas se aplican a :::no-loc(Razor)::: las páginas mediante el `[Authorize]` atributo con el nombre de la Directiva.</span><span class="sxs-lookup"><span data-stu-id="e26cf-125">Policies are applied to :::no-loc(Razor)::: Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e26cf-126">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e26cf-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="e26cf-127">Las directivas ***no*** se pueden aplicar en el :::no-loc(Razor)::: nivel de controlador de páginas, deben aplicarse a la página.</span><span class="sxs-lookup"><span data-stu-id="e26cf-127">Policies can ***not*** be applied at the :::no-loc(Razor)::: Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="e26cf-128">Las directivas se pueden aplicar a :::no-loc(Razor)::: las páginas mediante una [Convención de autorización](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="e26cf-128">Policies can be applied to :::no-loc(Razor)::: Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="e26cf-129">Requisitos</span><span class="sxs-lookup"><span data-stu-id="e26cf-129">Requirements</span></span>

<span data-ttu-id="e26cf-130">Un requisito de autorización es una colección de parámetros de datos que una Directiva puede usar para evaluar la entidad de seguridad de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="e26cf-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="e26cf-131">En la Directiva "AtLeast21", el requisito es un parámetro único &mdash; que es la edad mínima.</span><span class="sxs-lookup"><span data-stu-id="e26cf-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="e26cf-132">Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que es una interfaz de marcador vacía.</span><span class="sxs-lookup"><span data-stu-id="e26cf-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="e26cf-133">Un requisito de edad mínima parametrizada podría implementarse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="e26cf-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="e26cf-134">Si una directiva de autorización contiene varios requisitos de autorización, deben cumplirse todos los requisitos para que la evaluación de la Directiva se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="e26cf-135">En otras palabras, los distintos requisitos de autorización que se agregan a una sola directiva de autorización se tratan de manera **y** .</span><span class="sxs-lookup"><span data-stu-id="e26cf-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="e26cf-136">No es necesario que un requisito tenga datos ni propiedades.</span><span class="sxs-lookup"><span data-stu-id="e26cf-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="e26cf-137">Controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="e26cf-137">Authorization handlers</span></span>

<span data-ttu-id="e26cf-138">Un controlador de autorización es responsable de la evaluación de las propiedades de un requisito.</span><span class="sxs-lookup"><span data-stu-id="e26cf-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="e26cf-139">El controlador de autorización evalúa los requisitos con respecto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) proporcionado para determinar si se permite el acceso.</span><span class="sxs-lookup"><span data-stu-id="e26cf-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="e26cf-140">Un requisito puede tener [varios controladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="e26cf-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="e26cf-141">Un controlador puede heredar [ \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), donde `TRequirement` es el requisito que se debe controlar.</span><span class="sxs-lookup"><span data-stu-id="e26cf-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="e26cf-142">Como alternativa, un controlador puede implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para administrar más de un tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="e26cf-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="e26cf-143">Usar un controlador para un requisito</span><span class="sxs-lookup"><span data-stu-id="e26cf-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="e26cf-144">El siguiente es un ejemplo de una relación uno a uno en la que un controlador de edad mínima emplea un único requisito:</span><span class="sxs-lookup"><span data-stu-id="e26cf-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="e26cf-145">El código anterior determina si la entidad de seguridad de usuario actual tiene una declaración de nacimiento de fecha que ha sido emitida por un emisor conocido y de confianza.</span><span class="sxs-lookup"><span data-stu-id="e26cf-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="e26cf-146">No se puede realizar la autorización cuando falta la demanda, en cuyo caso se devuelve una tarea completada.</span><span class="sxs-lookup"><span data-stu-id="e26cf-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="e26cf-147">Cuando existe una demanda, se calcula la edad del usuario.</span><span class="sxs-lookup"><span data-stu-id="e26cf-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="e26cf-148">Si el usuario cumple la edad mínima definida por el requisito, la autorización se considera correcta.</span><span class="sxs-lookup"><span data-stu-id="e26cf-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="e26cf-149">Cuando la autorización se realiza correctamente, `context.Succeed` se invoca con el requisito satisfecho como su único parámetro.</span><span class="sxs-lookup"><span data-stu-id="e26cf-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="e26cf-150">Usar un controlador para varios requisitos</span><span class="sxs-lookup"><span data-stu-id="e26cf-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="e26cf-151">El siguiente es un ejemplo de una relación de uno a varios en la que un controlador de permisos puede controlar tres tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="e26cf-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="e26cf-152">El código anterior atraviesa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; una propiedad que contiene requisitos no marcados como correctos.</span><span class="sxs-lookup"><span data-stu-id="e26cf-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="e26cf-153">Para un `ReadPermission` requisito, el usuario debe ser un propietario o un patrocinador para tener acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="e26cf-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="e26cf-154">En el caso de un `EditPermission` requisito de o `DeletePermission` , debe ser un propietario para tener acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="e26cf-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="e26cf-155">Registro del controlador</span><span class="sxs-lookup"><span data-stu-id="e26cf-155">Handler registration</span></span>

<span data-ttu-id="e26cf-156">Los controladores se registran en la colección de servicios durante la configuración.</span><span class="sxs-lookup"><span data-stu-id="e26cf-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="e26cf-157">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e26cf-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="e26cf-158">El código anterior se registra `MinimumAgeHandler` como singleton mediante la invocación de `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="e26cf-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="e26cf-159">Los controladores se pueden registrar con cualquiera de las [duraciones de servicio](xref:fundamentals/dependency-injection#service-lifetimes)integradas.</span><span class="sxs-lookup"><span data-stu-id="e26cf-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="e26cf-160">¿Qué debe devolver un controlador?</span><span class="sxs-lookup"><span data-stu-id="e26cf-160">What should a handler return?</span></span>

<span data-ttu-id="e26cf-161">Tenga en cuenta que el `Handle` método del [ejemplo de controlador](#security-authorization-handler-example) no devuelve ningún valor.</span><span class="sxs-lookup"><span data-stu-id="e26cf-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="e26cf-162">¿Cómo se indica el estado correcto o incorrecto?</span><span class="sxs-lookup"><span data-stu-id="e26cf-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="e26cf-163">Un controlador indica que se ha realizado correctamente llamando a `context.Succeed(IAuthorizationRequirement requirement)` , pasando el requisito que se ha validado correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="e26cf-164">Un controlador no necesita controlar los errores por lo general, ya que otros controladores para el mismo requisito pueden realizarse correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="e26cf-165">Para garantizar un error, incluso si se realizan correctamente otros controladores de requisitos, llame a `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="e26cf-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="e26cf-166">Si un controlador llama a `context.Succeed` o `context.Fail` , todavía se llama a todos los controladores restantes.</span><span class="sxs-lookup"><span data-stu-id="e26cf-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="e26cf-167">Esto permite a los requisitos producir efectos secundarios, como el registro, que tiene lugar incluso si otro controlador ha validado o no un requisito correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="e26cf-168">Cuando se establece en `false` , la propiedad [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.net Core 1,1 y versiones posteriores) cortocircuita la ejecución de los controladores cuando `context.Fail` se llama a.</span><span class="sxs-lookup"><span data-stu-id="e26cf-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="e26cf-169">`InvokeHandlersAfterFailure`tiene como valor predeterminado `true` , en cuyo caso se llama a todos los controladores.</span><span class="sxs-lookup"><span data-stu-id="e26cf-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="e26cf-170">Se llama a los controladores de autorización incluso si se produce un error de autenticación.</span><span class="sxs-lookup"><span data-stu-id="e26cf-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="e26cf-171">¿Por qué sería conveniente tener varios controladores para un requisito?</span><span class="sxs-lookup"><span data-stu-id="e26cf-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="e26cf-172">En los casos en los que desea que la evaluación esté en una base de **o** , implemente varios controladores para un único requisito.</span><span class="sxs-lookup"><span data-stu-id="e26cf-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="e26cf-173">Por ejemplo, Microsoft tiene puertas que solo se abren con tarjetas clave.</span><span class="sxs-lookup"><span data-stu-id="e26cf-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="e26cf-174">Si deja la tarjeta de la llave en casa, el recepcionista imprime una etiqueta temporal y abre la puerta.</span><span class="sxs-lookup"><span data-stu-id="e26cf-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="e26cf-175">En este escenario, tendría un único requisito, *BuildingEntry*, pero varios controladores, cada uno examinando un único requisito.</span><span class="sxs-lookup"><span data-stu-id="e26cf-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="e26cf-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="e26cf-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="e26cf-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e26cf-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="e26cf-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e26cf-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="e26cf-179">Asegúrese de que ambos controladores están [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="e26cf-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="e26cf-180">Si alguno de los controladores se realiza correctamente cuando una directiva evalúa el `BuildingEntryRequirement` , la evaluación de la Directiva se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="e26cf-181">Usar un FUNC para cumplir una directiva</span><span class="sxs-lookup"><span data-stu-id="e26cf-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="e26cf-182">Puede haber situaciones en las que el cumplimiento de una directiva sea fácil de expresar en el código.</span><span class="sxs-lookup"><span data-stu-id="e26cf-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="e26cf-183">Es posible proporcionar un `Func<AuthorizationHandlerContext, bool>` al configurar la Directiva con el generador de `RequireAssertion` directivas.</span><span class="sxs-lookup"><span data-stu-id="e26cf-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="e26cf-184">Por ejemplo, el anterior `BadgeEntryHandler` podría volver a escribirse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="e26cf-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="e26cf-185">Acceso al contexto de solicitud MVC en los controladores</span><span class="sxs-lookup"><span data-stu-id="e26cf-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="e26cf-186">El `HandleRequirementAsync` método que se implementa en un controlador de autorización tiene dos parámetros: un `AuthorizationHandlerContext` y el `TRequirement` que se está controlando.</span><span class="sxs-lookup"><span data-stu-id="e26cf-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="e26cf-187">Los marcos de trabajo como MVC o :::no-loc(SignalR)::: pueden agregar cualquier objeto a la `Resource` propiedad en `AuthorizationHandlerContext` para pasar información adicional.</span><span class="sxs-lookup"><span data-stu-id="e26cf-187">Frameworks such as MVC or :::no-loc(SignalR)::: are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="e26cf-188">Al usar el enrutamiento de punto de conexión, la autorización se controla normalmente mediante el middleware de autorización.</span><span class="sxs-lookup"><span data-stu-id="e26cf-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="e26cf-189">En este caso, la `Resource` propiedad es una instancia de <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="e26cf-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="e26cf-190">El punto de conexión se puede usar para sondear el recurso subyacente al que se va a enrutar.</span><span class="sxs-lookup"><span data-stu-id="e26cf-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="e26cf-191">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e26cf-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="e26cf-192">El extremo no proporciona acceso al actual `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="e26cf-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="e26cf-193">Al usar el enrutamiento de punto de conexión, use `IHttpContextAcessor` para tener acceso a `HttpContext` dentro de un controlador de autorización.</span><span class="sxs-lookup"><span data-stu-id="e26cf-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="e26cf-194">Para obtener más información, vea [usar HttpContext desde componentes personalizados](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="e26cf-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="e26cf-195">Con el enrutamiento tradicional, o cuando la autorización se produce como parte del filtro de autorización de MVC, el valor de `Resource` es una <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instancia de.</span><span class="sxs-lookup"><span data-stu-id="e26cf-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="e26cf-196">Esta propiedad proporciona acceso a `HttpContext` , `RouteData` y todo lo demás proporcionado por MVC y :::no-loc(Razor)::: pages.</span><span class="sxs-lookup"><span data-stu-id="e26cf-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="e26cf-197">El uso de la `Resource` propiedad es específico de Framework.</span><span class="sxs-lookup"><span data-stu-id="e26cf-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="e26cf-198">El uso de la información en la `Resource` propiedad limita las directivas de autorización a marcos concretos.</span><span class="sxs-lookup"><span data-stu-id="e26cf-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="e26cf-199">Debe convertir la `Resource` propiedad mediante la `is` palabra clave y, a continuación, confirmar que la conversión se ha realizado correctamente para asegurarse de que el código no se bloquea con `InvalidCastException` cuando se ejecuta en otros marcos:</span><span class="sxs-lookup"><span data-stu-id="e26cf-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="e26cf-200">Requerir globalmente la autenticación de todos los usuarios</span><span class="sxs-lookup"><span data-stu-id="e26cf-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e26cf-201">En segundo plano, la autorización basada en [roles](xref:security/authorization/roles) y la [autorización basada en notificaciones](xref:security/authorization/claims) usan un requisito, un controlador de requisitos y una directiva configurada previamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="e26cf-202">Estos bloques de creación admiten la expresión de evaluaciones de autorización en el código.</span><span class="sxs-lookup"><span data-stu-id="e26cf-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="e26cf-203">El resultado es una estructura de autorización más enriquecida, reutilizable y comprobable.</span><span class="sxs-lookup"><span data-stu-id="e26cf-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="e26cf-204">Una directiva de autorización se compone de uno o varios requisitos.</span><span class="sxs-lookup"><span data-stu-id="e26cf-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="e26cf-205">Se registra como parte de la configuración del servicio de autorización, en el `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="e26cf-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="e26cf-206">En el ejemplo anterior, se crea una directiva "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="e26cf-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="e26cf-207">Tiene un único requisito &mdash; de una edad mínima, que se proporciona como un parámetro al requisito.</span><span class="sxs-lookup"><span data-stu-id="e26cf-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="e26cf-208">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="e26cf-208">IAuthorizationService</span></span> 

<span data-ttu-id="e26cf-209">El servicio principal que determina si la autorización se realiza correctamente es <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="e26cf-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="e26cf-210">En el código anterior se resaltan los dos métodos de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="e26cf-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="e26cf-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>es un servicio de marcador sin métodos y el mecanismo para hacer un seguimiento de si la autorización se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="e26cf-212">Cada uno <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> de ellos es responsable de comprobar si se cumplen los requisitos:</span><span class="sxs-lookup"><span data-stu-id="e26cf-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="e26cf-213">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> clase es lo que el controlador usa para marcar si se cumplen los requisitos:</span><span class="sxs-lookup"><span data-stu-id="e26cf-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="e26cf-214">En el código siguiente se muestra la implementación predeterminada simplificada (y anotada con comentarios) del servicio de autorización:</span><span class="sxs-lookup"><span data-stu-id="e26cf-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="e26cf-215">En el código siguiente se muestra un típico `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e26cf-215">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="e26cf-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> o `[Authorize(Policy = "Something")]` para la autorización.</span><span class="sxs-lookup"><span data-stu-id="e26cf-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="e26cf-217">Aplicar directivas a los controladores de MVC</span><span class="sxs-lookup"><span data-stu-id="e26cf-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="e26cf-218">Si utiliza :::no-loc(Razor)::: páginas, consulte [aplicar directivas a :::no-loc(Razor)::: las páginas](#apply-policies-to-razor-pages) de este documento.</span><span class="sxs-lookup"><span data-stu-id="e26cf-218">If you're using :::no-loc(Razor)::: Pages, see [Apply policies to :::no-loc(Razor)::: Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="e26cf-219">Las directivas se aplican a los controladores mediante el uso del `[Authorize]` atributo con el nombre de la Directiva.</span><span class="sxs-lookup"><span data-stu-id="e26cf-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e26cf-220">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e26cf-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="e26cf-221">Aplicar directivas a :::no-loc(Razor)::: las páginas</span><span class="sxs-lookup"><span data-stu-id="e26cf-221">Apply policies to :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="e26cf-222">Las directivas se aplican a :::no-loc(Razor)::: las páginas mediante el `[Authorize]` atributo con el nombre de la Directiva.</span><span class="sxs-lookup"><span data-stu-id="e26cf-222">Policies are applied to :::no-loc(Razor)::: Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e26cf-223">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e26cf-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="e26cf-224">Las directivas también se pueden aplicar a :::no-loc(Razor)::: las páginas mediante una [Convención de autorización](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="e26cf-224">Policies can also be applied to :::no-loc(Razor)::: Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="e26cf-225">Requisitos</span><span class="sxs-lookup"><span data-stu-id="e26cf-225">Requirements</span></span>

<span data-ttu-id="e26cf-226">Un requisito de autorización es una colección de parámetros de datos que una Directiva puede usar para evaluar la entidad de seguridad de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="e26cf-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="e26cf-227">En la Directiva "AtLeast21", el requisito es un parámetro único &mdash; que es la edad mínima.</span><span class="sxs-lookup"><span data-stu-id="e26cf-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="e26cf-228">Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que es una interfaz de marcador vacía.</span><span class="sxs-lookup"><span data-stu-id="e26cf-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="e26cf-229">Un requisito de edad mínima parametrizada podría implementarse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="e26cf-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="e26cf-230">Si una directiva de autorización contiene varios requisitos de autorización, deben cumplirse todos los requisitos para que la evaluación de la Directiva se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="e26cf-231">En otras palabras, los distintos requisitos de autorización que se agregan a una sola directiva de autorización se tratan de manera **y** .</span><span class="sxs-lookup"><span data-stu-id="e26cf-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="e26cf-232">No es necesario que un requisito tenga datos ni propiedades.</span><span class="sxs-lookup"><span data-stu-id="e26cf-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="e26cf-233">Controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="e26cf-233">Authorization handlers</span></span>

<span data-ttu-id="e26cf-234">Un controlador de autorización es responsable de la evaluación de las propiedades de un requisito.</span><span class="sxs-lookup"><span data-stu-id="e26cf-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="e26cf-235">El controlador de autorización evalúa los requisitos con respecto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) proporcionado para determinar si se permite el acceso.</span><span class="sxs-lookup"><span data-stu-id="e26cf-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="e26cf-236">Un requisito puede tener [varios controladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="e26cf-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="e26cf-237">Un controlador puede heredar [ \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), donde `TRequirement` es el requisito que se debe controlar.</span><span class="sxs-lookup"><span data-stu-id="e26cf-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="e26cf-238">Como alternativa, un controlador puede implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para administrar más de un tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="e26cf-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="e26cf-239">Usar un controlador para un requisito</span><span class="sxs-lookup"><span data-stu-id="e26cf-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="e26cf-240">El siguiente es un ejemplo de una relación uno a uno en la que un controlador de edad mínima emplea un único requisito:</span><span class="sxs-lookup"><span data-stu-id="e26cf-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="e26cf-241">El código anterior determina si la entidad de seguridad de usuario actual tiene una declaración de nacimiento de fecha que ha sido emitida por un emisor conocido y de confianza.</span><span class="sxs-lookup"><span data-stu-id="e26cf-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="e26cf-242">No se puede realizar la autorización cuando falta la demanda, en cuyo caso se devuelve una tarea completada.</span><span class="sxs-lookup"><span data-stu-id="e26cf-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="e26cf-243">Cuando existe una demanda, se calcula la edad del usuario.</span><span class="sxs-lookup"><span data-stu-id="e26cf-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="e26cf-244">Si el usuario cumple la edad mínima definida por el requisito, la autorización se considera correcta.</span><span class="sxs-lookup"><span data-stu-id="e26cf-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="e26cf-245">Cuando la autorización se realiza correctamente, `context.Succeed` se invoca con el requisito satisfecho como su único parámetro.</span><span class="sxs-lookup"><span data-stu-id="e26cf-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="e26cf-246">Usar un controlador para varios requisitos</span><span class="sxs-lookup"><span data-stu-id="e26cf-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="e26cf-247">El siguiente es un ejemplo de una relación de uno a varios en la que un controlador de permisos puede controlar tres tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="e26cf-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="e26cf-248">El código anterior atraviesa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; una propiedad que contiene requisitos no marcados como correctos.</span><span class="sxs-lookup"><span data-stu-id="e26cf-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="e26cf-249">Para un `ReadPermission` requisito, el usuario debe ser un propietario o un patrocinador para tener acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="e26cf-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="e26cf-250">En el caso de un `EditPermission` requisito de o `DeletePermission` , debe ser un propietario para tener acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="e26cf-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="e26cf-251">Registro del controlador</span><span class="sxs-lookup"><span data-stu-id="e26cf-251">Handler registration</span></span>

<span data-ttu-id="e26cf-252">Los controladores se registran en la colección de servicios durante la configuración.</span><span class="sxs-lookup"><span data-stu-id="e26cf-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="e26cf-253">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e26cf-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="e26cf-254">El código anterior se registra `MinimumAgeHandler` como singleton mediante la invocación de `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="e26cf-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="e26cf-255">Los controladores se pueden registrar con cualquiera de las [duraciones de servicio](xref:fundamentals/dependency-injection#service-lifetimes)integradas.</span><span class="sxs-lookup"><span data-stu-id="e26cf-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="e26cf-256">¿Qué debe devolver un controlador?</span><span class="sxs-lookup"><span data-stu-id="e26cf-256">What should a handler return?</span></span>

<span data-ttu-id="e26cf-257">Tenga en cuenta que el `Handle` método del [ejemplo de controlador](#security-authorization-handler-example) no devuelve ningún valor.</span><span class="sxs-lookup"><span data-stu-id="e26cf-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="e26cf-258">¿Cómo se indica el estado correcto o incorrecto?</span><span class="sxs-lookup"><span data-stu-id="e26cf-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="e26cf-259">Un controlador indica que se ha realizado correctamente llamando a `context.Succeed(IAuthorizationRequirement requirement)` , pasando el requisito que se ha validado correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="e26cf-260">Un controlador no necesita controlar los errores por lo general, ya que otros controladores para el mismo requisito pueden realizarse correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="e26cf-261">Para garantizar un error, incluso si se realizan correctamente otros controladores de requisitos, llame a `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="e26cf-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="e26cf-262">Si un controlador llama a `context.Succeed` o `context.Fail` , todavía se llama a todos los controladores restantes.</span><span class="sxs-lookup"><span data-stu-id="e26cf-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="e26cf-263">Esto permite a los requisitos producir efectos secundarios, como el registro, que tiene lugar incluso si otro controlador ha validado o no un requisito correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="e26cf-264">Cuando se establece en `false` , la propiedad [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.net Core 1,1 y versiones posteriores) cortocircuita la ejecución de los controladores cuando `context.Fail` se llama a.</span><span class="sxs-lookup"><span data-stu-id="e26cf-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="e26cf-265">`InvokeHandlersAfterFailure`tiene como valor predeterminado `true` , en cuyo caso se llama a todos los controladores.</span><span class="sxs-lookup"><span data-stu-id="e26cf-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="e26cf-266">Se llama a los controladores de autorización incluso si se produce un error de autenticación.</span><span class="sxs-lookup"><span data-stu-id="e26cf-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="e26cf-267">¿Por qué sería conveniente tener varios controladores para un requisito?</span><span class="sxs-lookup"><span data-stu-id="e26cf-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="e26cf-268">En los casos en los que desea que la evaluación esté en una base de **o** , implemente varios controladores para un único requisito.</span><span class="sxs-lookup"><span data-stu-id="e26cf-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="e26cf-269">Por ejemplo, Microsoft tiene puertas que solo se abren con tarjetas clave.</span><span class="sxs-lookup"><span data-stu-id="e26cf-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="e26cf-270">Si deja la tarjeta de la llave en casa, el recepcionista imprime una etiqueta temporal y abre la puerta.</span><span class="sxs-lookup"><span data-stu-id="e26cf-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="e26cf-271">En este escenario, tendría un único requisito, *BuildingEntry*, pero varios controladores, cada uno examinando un único requisito.</span><span class="sxs-lookup"><span data-stu-id="e26cf-271">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="e26cf-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="e26cf-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="e26cf-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e26cf-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="e26cf-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e26cf-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="e26cf-275">Asegúrese de que ambos controladores están [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="e26cf-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="e26cf-276">Si alguno de los controladores se realiza correctamente cuando una directiva evalúa el `BuildingEntryRequirement` , la evaluación de la Directiva se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="e26cf-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="e26cf-277">Usar un FUNC para cumplir una directiva</span><span class="sxs-lookup"><span data-stu-id="e26cf-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="e26cf-278">Puede haber situaciones en las que el cumplimiento de una directiva sea fácil de expresar en el código.</span><span class="sxs-lookup"><span data-stu-id="e26cf-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="e26cf-279">Es posible proporcionar un `Func<AuthorizationHandlerContext, bool>` al configurar la Directiva con el generador de `RequireAssertion` directivas.</span><span class="sxs-lookup"><span data-stu-id="e26cf-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="e26cf-280">Por ejemplo, el anterior `BadgeEntryHandler` podría volver a escribirse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="e26cf-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="e26cf-281">Acceso al contexto de solicitud MVC en los controladores</span><span class="sxs-lookup"><span data-stu-id="e26cf-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="e26cf-282">El `HandleRequirementAsync` método que se implementa en un controlador de autorización tiene dos parámetros: un `AuthorizationHandlerContext` y el `TRequirement` que se está controlando.</span><span class="sxs-lookup"><span data-stu-id="e26cf-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="e26cf-283">Los marcos de trabajo como MVC o :::no-loc(SignalR)::: pueden agregar cualquier objeto a la `Resource` propiedad en `AuthorizationHandlerContext` para pasar información adicional.</span><span class="sxs-lookup"><span data-stu-id="e26cf-283">Frameworks such as MVC or :::no-loc(SignalR)::: are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="e26cf-284">Por ejemplo, MVC pasa una instancia de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) en la `Resource` propiedad.</span><span class="sxs-lookup"><span data-stu-id="e26cf-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="e26cf-285">Esta propiedad proporciona acceso a `HttpContext` , `RouteData` y todo lo demás proporcionado por MVC y :::no-loc(Razor)::: pages.</span><span class="sxs-lookup"><span data-stu-id="e26cf-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="e26cf-286">El uso de la `Resource` propiedad es específico de Framework.</span><span class="sxs-lookup"><span data-stu-id="e26cf-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="e26cf-287">El uso de la información en la `Resource` propiedad limita las directivas de autorización a marcos concretos.</span><span class="sxs-lookup"><span data-stu-id="e26cf-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="e26cf-288">Debe convertir la `Resource` propiedad mediante la `is` palabra clave y, a continuación, confirmar que la conversión se ha realizado correctamente para asegurarse de que el código no se bloquea con `InvalidCastException` cuando se ejecuta en otros marcos:</span><span class="sxs-lookup"><span data-stu-id="e26cf-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
