---
title: Autorización basada en directivas en ASP.NET Core
author: rick-anderson
description: Aprenda a crear y usar controladores de directivas de autorización para aplicar los requisitos de autorización en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
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
uid: security/authorization/policies
ms.openlocfilehash: 03d6e7fdc4ab4b5e4925508952bfd6c835d90486
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021281"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Autorización basada en directivas en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

En segundo plano, la autorización basada en [roles](xref:security/authorization/roles) y la [autorización basada en notificaciones](xref:security/authorization/claims) usan un requisito, un controlador de requisitos y una directiva configurada previamente. Estos bloques de creación admiten la expresión de evaluaciones de autorización en el código. El resultado es una estructura de autorización más enriquecida, reutilizable y comprobable.

Una directiva de autorización se compone de uno o varios requisitos. Se registra como parte de la configuración del servicio de autorización, en el `Startup.ConfigureServices` método:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

En el ejemplo anterior, se crea una directiva "AtLeast21". Tiene un único requisito &mdash; de una edad mínima, que se proporciona como un parámetro al requisito.

## <a name="iauthorizationservice"></a>IAuthorizationService 

El servicio principal que determina si la autorización se realiza correctamente es <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

En el código anterior se resaltan los dos métodos de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>es un servicio de marcador sin métodos y el mecanismo para hacer un seguimiento de si la autorización se realiza correctamente.

Cada uno <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> de ellos es responsable de comprobar si se cumplen los requisitos:
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

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> clase es lo que el controlador usa para marcar si se cumplen los requisitos:

```csharp
 context.Succeed(requirement)
```

En el código siguiente se muestra la implementación predeterminada simplificada (y anotada con comentarios) del servicio de autorización:

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

En el código siguiente se muestra un típico `ConfigureServices` :

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
    services.AddRazorPages();
}
```

Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> o `[Authorize(Policy = "Something")]` para la autorización.

## <a name="apply-policies-to-mvc-controllers"></a>Aplicar directivas a los controladores de MVC

Si utiliza Razor páginas, consulte [aplicar directivas a Razor las páginas](#apply-policies-to-razor-pages) de este documento.

Las directivas se aplican a los controladores mediante el uso del `[Authorize]` atributo con el nombre de la Directiva. Por ejemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>Aplicar directivas a Razor las páginas

Las directivas se aplican a Razor las páginas mediante el `[Authorize]` atributo con el nombre de la Directiva. Por ejemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Las directivas ***no*** se pueden aplicar en el Razor nivel de controlador de páginas, deben aplicarse a la página.

Las directivas se pueden aplicar a Razor las páginas mediante una [Convención de autorización](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Requisitos

Un requisito de autorización es una colección de parámetros de datos que una Directiva puede usar para evaluar la entidad de seguridad de usuario actual. En la Directiva "AtLeast21", el requisito es un parámetro único &mdash; que es la edad mínima. Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que es una interfaz de marcador vacía. Un requisito de edad mínima parametrizada podría implementarse de la siguiente manera:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Si una directiva de autorización contiene varios requisitos de autorización, deben cumplirse todos los requisitos para que la evaluación de la Directiva se realice correctamente. En otras palabras, los distintos requisitos de autorización que se agregan a una sola directiva de autorización se tratan de manera **y** .

> [!NOTE]
> No es necesario que un requisito tenga datos ni propiedades.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Controladores de autorización

Un controlador de autorización es responsable de la evaluación de las propiedades de un requisito. El controlador de autorización evalúa los requisitos con respecto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) proporcionado para determinar si se permite el acceso.

Un requisito puede tener [varios controladores](#security-authorization-policies-based-multiple-handlers). Un controlador puede heredar [ \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), donde `TRequirement` es el requisito que se debe controlar. Como alternativa, un controlador puede implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para administrar más de un tipo de requisito.

### <a name="use-a-handler-for-one-requirement"></a>Usar un controlador para un requisito

<a name="security-authorization-handler-example"></a>

El siguiente es un ejemplo de una relación uno a uno en la que un controlador de edad mínima emplea un único requisito:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

El código anterior determina si la entidad de seguridad de usuario actual tiene una declaración de nacimiento de fecha que ha sido emitida por un emisor conocido y de confianza. No se puede realizar la autorización cuando falta la demanda, en cuyo caso se devuelve una tarea completada. Cuando existe una demanda, se calcula la edad del usuario. Si el usuario cumple la edad mínima definida por el requisito, la autorización se considera correcta. Cuando la autorización se realiza correctamente, `context.Succeed` se invoca con el requisito satisfecho como su único parámetro.

### <a name="use-a-handler-for-multiple-requirements"></a>Usar un controlador para varios requisitos

El siguiente es un ejemplo de una relación de uno a varios en la que un controlador de permisos puede controlar tres tipos diferentes de requisitos:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

El código anterior atraviesa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; una propiedad que contiene requisitos no marcados como correctos. Para un `ReadPermission` requisito, el usuario debe ser un propietario o un patrocinador para tener acceso al recurso solicitado. En el caso de un `EditPermission` requisito de o `DeletePermission` , debe ser un propietario para tener acceso al recurso solicitado.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registro del controlador

Los controladores se registran en la colección de servicios durante la configuración. Por ejemplo:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

El código anterior se registra `MinimumAgeHandler` como singleton mediante la invocación de `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . Los controladores se pueden registrar con cualquiera de las [duraciones de servicio](xref:fundamentals/dependency-injection#service-lifetimes)integradas.

## <a name="what-should-a-handler-return"></a>¿Qué debe devolver un controlador?

Tenga en cuenta que el `Handle` método del [ejemplo de controlador](#security-authorization-handler-example) no devuelve ningún valor. ¿Cómo se indica el estado correcto o incorrecto?

* Un controlador indica que se ha realizado correctamente llamando a `context.Succeed(IAuthorizationRequirement requirement)` , pasando el requisito que se ha validado correctamente.

* Un controlador no necesita controlar los errores por lo general, ya que otros controladores para el mismo requisito pueden realizarse correctamente.

* Para garantizar un error, incluso si se realizan correctamente otros controladores de requisitos, llame a `context.Fail` .

Si un controlador llama a `context.Succeed` o `context.Fail` , todavía se llama a todos los controladores restantes. Esto permite a los requisitos producir efectos secundarios, como el registro, que tiene lugar incluso si otro controlador ha validado o no un requisito correctamente. Cuando se establece en `false` , la propiedad [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.net Core 1,1 y versiones posteriores) cortocircuita la ejecución de los controladores cuando `context.Fail` se llama a. `InvokeHandlersAfterFailure`tiene como valor predeterminado `true` , en cuyo caso se llama a todos los controladores.

> [!NOTE]
> Se llama a los controladores de autorización incluso si se produce un error de autenticación.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>¿Por qué sería conveniente tener varios controladores para un requisito?

En los casos en los que desea que la evaluación esté en una base de **o** , implemente varios controladores para un único requisito. Por ejemplo, Microsoft tiene puertas que solo se abren con tarjetas clave. Si deja la tarjeta de la llave en casa, el recepcionista imprime una etiqueta temporal y abre la puerta. En este escenario, tendría un único requisito, *BuildingEntry*, pero varios controladores, cada uno examinando un único requisito.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Asegúrese de que ambos controladores están [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Si alguno de los controladores se realiza correctamente cuando una directiva evalúa el `BuildingEntryRequirement` , la evaluación de la Directiva se realiza correctamente.

## <a name="use-a-func-to-fulfill-a-policy"></a>Usar un FUNC para cumplir una directiva

Puede haber situaciones en las que el cumplimiento de una directiva sea fácil de expresar en el código. Es posible proporcionar un `Func<AuthorizationHandlerContext, bool>` al configurar la Directiva con el generador de `RequireAssertion` directivas.

Por ejemplo, el anterior `BadgeEntryHandler` podría volver a escribirse de la siguiente manera:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a>Acceso al contexto de solicitud MVC en los controladores

El `HandleRequirementAsync` método que se implementa en un controlador de autorización tiene dos parámetros: un `AuthorizationHandlerContext` y el `TRequirement` que se está controlando. Los marcos de trabajo como MVC o SignalR pueden agregar cualquier objeto a la `Resource` propiedad en `AuthorizationHandlerContext` para pasar información adicional.

Al usar el enrutamiento de punto de conexión, la autorización se controla normalmente mediante el middleware de autorización. En este caso, la `Resource` propiedad es una instancia de <xref:Microsoft.AspNetCore.Http.Endpoint> . El punto de conexión se puede usar para sondear el recurso subyacente al que se va a enrutar. Por ejemplo:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

El extremo no proporciona acceso al actual `HttpContext` . Al usar el enrutamiento de punto de conexión, use `IHttpContextAcessor` para tener acceso a `HttpContext` dentro de un controlador de autorización. Para obtener más información, vea [usar HttpContext desde componentes personalizados](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).

Con el enrutamiento tradicional, o cuando la autorización se produce como parte del filtro de autorización de MVC, el valor de `Resource` es una <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instancia de. Esta propiedad proporciona acceso a `HttpContext` , `RouteData` y todo lo demás proporcionado por MVC y Razor pages.

El uso de la `Resource` propiedad es específico de Framework. El uso de la información en la `Resource` propiedad limita las directivas de autorización a marcos concretos. Debe convertir la `Resource` propiedad mediante la `is` palabra clave y, a continuación, confirmar que la conversión se ha realizado correctamente para asegurarse de que el código no se bloquea con `InvalidCastException` cuando se ejecuta en otros marcos:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Requerir globalmente la autenticación de todos los usuarios

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En segundo plano, la autorización basada en [roles](xref:security/authorization/roles) y la [autorización basada en notificaciones](xref:security/authorization/claims) usan un requisito, un controlador de requisitos y una directiva configurada previamente. Estos bloques de creación admiten la expresión de evaluaciones de autorización en el código. El resultado es una estructura de autorización más enriquecida, reutilizable y comprobable.

Una directiva de autorización se compone de uno o varios requisitos. Se registra como parte de la configuración del servicio de autorización, en el `Startup.ConfigureServices` método:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

En el ejemplo anterior, se crea una directiva "AtLeast21". Tiene un único requisito &mdash; de una edad mínima, que se proporciona como un parámetro al requisito.

## <a name="iauthorizationservice"></a>IAuthorizationService 

El servicio principal que determina si la autorización se realiza correctamente es <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

En el código anterior se resaltan los dos métodos de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>es un servicio de marcador sin métodos y el mecanismo para hacer un seguimiento de si la autorización se realiza correctamente.

Cada uno <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> de ellos es responsable de comprobar si se cumplen los requisitos:
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

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> clase es lo que el controlador usa para marcar si se cumplen los requisitos:

```csharp
 context.Succeed(requirement)
```

En el código siguiente se muestra la implementación predeterminada simplificada (y anotada con comentarios) del servicio de autorización:

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

En el código siguiente se muestra un típico `ConfigureServices` :

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

Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> o `[Authorize(Policy = "Something")]` para la autorización.

## <a name="apply-policies-to-mvc-controllers"></a>Aplicar directivas a los controladores de MVC

Si utiliza Razor páginas, consulte [aplicar directivas a Razor las páginas](#apply-policies-to-razor-pages) de este documento.

Las directivas se aplican a los controladores mediante el uso del `[Authorize]` atributo con el nombre de la Directiva. Por ejemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>Aplicar directivas a Razor las páginas

Las directivas se aplican a Razor las páginas mediante el `[Authorize]` atributo con el nombre de la Directiva. Por ejemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Las directivas también se pueden aplicar a Razor las páginas mediante una [Convención de autorización](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Requisitos

Un requisito de autorización es una colección de parámetros de datos que una Directiva puede usar para evaluar la entidad de seguridad de usuario actual. En la Directiva "AtLeast21", el requisito es un parámetro único &mdash; que es la edad mínima. Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que es una interfaz de marcador vacía. Un requisito de edad mínima parametrizada podría implementarse de la siguiente manera:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Si una directiva de autorización contiene varios requisitos de autorización, deben cumplirse todos los requisitos para que la evaluación de la Directiva se realice correctamente. En otras palabras, los distintos requisitos de autorización que se agregan a una sola directiva de autorización se tratan de manera **y** .

> [!NOTE]
> No es necesario que un requisito tenga datos ni propiedades.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Controladores de autorización

Un controlador de autorización es responsable de la evaluación de las propiedades de un requisito. El controlador de autorización evalúa los requisitos con respecto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) proporcionado para determinar si se permite el acceso.

Un requisito puede tener [varios controladores](#security-authorization-policies-based-multiple-handlers). Un controlador puede heredar [ \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), donde `TRequirement` es el requisito que se debe controlar. Como alternativa, un controlador puede implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para administrar más de un tipo de requisito.

### <a name="use-a-handler-for-one-requirement"></a>Usar un controlador para un requisito

<a name="security-authorization-handler-example"></a>

El siguiente es un ejemplo de una relación uno a uno en la que un controlador de edad mínima emplea un único requisito:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

El código anterior determina si la entidad de seguridad de usuario actual tiene una declaración de nacimiento de fecha que ha sido emitida por un emisor conocido y de confianza. No se puede realizar la autorización cuando falta la demanda, en cuyo caso se devuelve una tarea completada. Cuando existe una demanda, se calcula la edad del usuario. Si el usuario cumple la edad mínima definida por el requisito, la autorización se considera correcta. Cuando la autorización se realiza correctamente, `context.Succeed` se invoca con el requisito satisfecho como su único parámetro.

### <a name="use-a-handler-for-multiple-requirements"></a>Usar un controlador para varios requisitos

El siguiente es un ejemplo de una relación de uno a varios en la que un controlador de permisos puede controlar tres tipos diferentes de requisitos:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

El código anterior atraviesa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; una propiedad que contiene requisitos no marcados como correctos. Para un `ReadPermission` requisito, el usuario debe ser un propietario o un patrocinador para tener acceso al recurso solicitado. En el caso de un `EditPermission` requisito de o `DeletePermission` , debe ser un propietario para tener acceso al recurso solicitado.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registro del controlador

Los controladores se registran en la colección de servicios durante la configuración. Por ejemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

El código anterior se registra `MinimumAgeHandler` como singleton mediante la invocación de `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . Los controladores se pueden registrar con cualquiera de las [duraciones de servicio](xref:fundamentals/dependency-injection#service-lifetimes)integradas.

## <a name="what-should-a-handler-return"></a>¿Qué debe devolver un controlador?

Tenga en cuenta que el `Handle` método del [ejemplo de controlador](#security-authorization-handler-example) no devuelve ningún valor. ¿Cómo se indica el estado correcto o incorrecto?

* Un controlador indica que se ha realizado correctamente llamando a `context.Succeed(IAuthorizationRequirement requirement)` , pasando el requisito que se ha validado correctamente.

* Un controlador no necesita controlar los errores por lo general, ya que otros controladores para el mismo requisito pueden realizarse correctamente.

* Para garantizar un error, incluso si se realizan correctamente otros controladores de requisitos, llame a `context.Fail` .

Si un controlador llama a `context.Succeed` o `context.Fail` , todavía se llama a todos los controladores restantes. Esto permite a los requisitos producir efectos secundarios, como el registro, que tiene lugar incluso si otro controlador ha validado o no un requisito correctamente. Cuando se establece en `false` , la propiedad [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.net Core 1,1 y versiones posteriores) cortocircuita la ejecución de los controladores cuando `context.Fail` se llama a. `InvokeHandlersAfterFailure`tiene como valor predeterminado `true` , en cuyo caso se llama a todos los controladores.

> [!NOTE]
> Se llama a los controladores de autorización incluso si se produce un error de autenticación.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>¿Por qué sería conveniente tener varios controladores para un requisito?

En los casos en los que desea que la evaluación esté en una base de **o** , implemente varios controladores para un único requisito. Por ejemplo, Microsoft tiene puertas que solo se abren con tarjetas clave. Si deja la tarjeta de la llave en casa, el recepcionista imprime una etiqueta temporal y abre la puerta. En este escenario, tendría un único requisito, *BuildingEntry*, pero varios controladores, cada uno examinando un único requisito.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Asegúrese de que ambos controladores están [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Si alguno de los controladores se realiza correctamente cuando una directiva evalúa el `BuildingEntryRequirement` , la evaluación de la Directiva se realiza correctamente.

## <a name="use-a-func-to-fulfill-a-policy"></a>Usar un FUNC para cumplir una directiva

Puede haber situaciones en las que el cumplimiento de una directiva sea fácil de expresar en el código. Es posible proporcionar un `Func<AuthorizationHandlerContext, bool>` al configurar la Directiva con el generador de `RequireAssertion` directivas.

Por ejemplo, el anterior `BadgeEntryHandler` podría volver a escribirse de la siguiente manera:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a>Acceso al contexto de solicitud MVC en los controladores

El `HandleRequirementAsync` método que se implementa en un controlador de autorización tiene dos parámetros: un `AuthorizationHandlerContext` y el `TRequirement` que se está controlando. Los marcos de trabajo como MVC o SignalR pueden agregar cualquier objeto a la `Resource` propiedad en `AuthorizationHandlerContext` para pasar información adicional.

Por ejemplo, MVC pasa una instancia de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) en la `Resource` propiedad. Esta propiedad proporciona acceso a `HttpContext` , `RouteData` y todo lo demás proporcionado por MVC y Razor pages.

El uso de la `Resource` propiedad es específico de Framework. El uso de la información en la `Resource` propiedad limita las directivas de autorización a marcos concretos. Debe convertir la `Resource` propiedad mediante la `is` palabra clave y, a continuación, confirmar que la conversión se ha realizado correctamente para asegurarse de que el código no se bloquea con `InvalidCastException` cuando se ejecuta en otros marcos:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
