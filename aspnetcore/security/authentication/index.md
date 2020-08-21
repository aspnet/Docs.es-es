---
title: Información general sobre la autenticación de ASP.NET Core
author: mjrousos
description: Obtenga más información sobre la autenticación en ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
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
uid: security/authentication/index
ms.openlocfilehash: 2511d68dd049ca47dea002d8960e7d2fd0207383
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632647"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="8eb8d-103">Información general sobre la autenticación de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8eb8d-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="8eb8d-104">Por [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="8eb8d-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="8eb8d-105">La autenticación es el proceso de determinar la identidad de un usuario.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="8eb8d-106">Por su parte, la [autorización](xref:security/authorization/introduction) consiste en determinar si un usuario tiene acceso a un recurso.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="8eb8d-107">En ASP.NET Core, la autenticación se controla mediante `IAuthenticationService`, elemento que el [middleware](xref:fundamentals/middleware/index) de autenticación emplea para conseguirlo.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="8eb8d-108">El servicio de autenticación usa controladores de autenticación registrados para completar las acciones relacionadas con la autenticación.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="8eb8d-109">Estos son algunos ejemplos de acciones relacionadas con la autenticación:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="8eb8d-110">Autenticación de un usuario</span><span class="sxs-lookup"><span data-stu-id="8eb8d-110">Authenticating a user.</span></span>
* <span data-ttu-id="8eb8d-111">Respuesta si un usuario no autenticado intenta acceder a un recurso restringido</span><span class="sxs-lookup"><span data-stu-id="8eb8d-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="8eb8d-112">Los controladores de autenticación registrados y sus opciones de configuración se denominan "esquemas".</span><span class="sxs-lookup"><span data-stu-id="8eb8d-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="8eb8d-113">Para especificar esquemas de autenticación, es necesario registrar servicios de autenticación en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="8eb8d-114">Mediante una llamada a un método de extensión específico del esquema tras una llamada a `services.AddAuthentication` (por ejemplo, `AddJwtBearer` o `AddCookie`).</span><span class="sxs-lookup"><span data-stu-id="8eb8d-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="8eb8d-115">Estos métodos de extensión usan [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) para registrar esquemas con la configuración adecuada.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="8eb8d-116">Con menos frecuencia, mediante una llamada directa a [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*).</span><span class="sxs-lookup"><span data-stu-id="8eb8d-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="8eb8d-117">Por ejemplo, el código siguiente registra los servicios de autenticación y los controladores para los esquemas de autenticación de portador de JWT y de cookie:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="8eb8d-118">El parámetro `JwtBearerDefaults.AuthenticationScheme` de `AddAuthentication` es el nombre del esquema que se usará de forma predeterminada si no se solicita un esquema específico.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="8eb8d-119">Si se usan varios esquemas, las directivas de autorización (o los atributos de autorización) pueden [especificar el esquema (o esquemas) de autenticación](xref:security/authorization/limitingidentitybyscheme) del que dependen para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="8eb8d-120">En el ejemplo anterior, se podría especificar el nombre del esquema de autenticación de cookie (`CookieAuthenticationDefaults.AuthenticationScheme` de forma predeterminada, aunque se podría proporcionar otro nombre al llamar a `AddCookie`).</span><span class="sxs-lookup"><span data-stu-id="8eb8d-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="8eb8d-121">En algunos casos, la llamada a `AddAuthentication` se realiza automáticamente mediante otros métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="8eb8d-122">Por ejemplo, al usar [ASP.NET Core Identity](xref:security/authentication/identity), se llama a `AddAuthentication` de manera interna.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="8eb8d-123">Para agregar el middleware de autenticación en `Startup.Configure`, se llama al método de extensión <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> en el elemento `IApplicationBuilder` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="8eb8d-124">La llamada a `UseAuthentication` registra el middleware que usa los esquemas de autenticación previamente registrados.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="8eb8d-125">Llame a `UseAuthentication` antes de registrar cualquier middleware que dependa de que los usuarios se autentiquen.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="8eb8d-126">Al usar el enrutamiento de punto de conexión, la llamada a `UseAuthentication` debe ir:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="8eb8d-127">Después de `UseRouting`, para que la información de ruta esté disponible para las decisiones de autenticación.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="8eb8d-128">Antes de `UseEndpoints`, para que los usuarios se autentiquen como paso previo para tener acceso a los puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="8eb8d-129">Conceptos sobre la autenticación</span><span class="sxs-lookup"><span data-stu-id="8eb8d-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="8eb8d-130">Esquema de autenticación</span><span class="sxs-lookup"><span data-stu-id="8eb8d-130">Authentication scheme</span></span>

<span data-ttu-id="8eb8d-131">Un esquema de autenticación es un nombre que corresponde a:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="8eb8d-132">Un controlador de autenticación.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-132">An authentication handler.</span></span>
* <span data-ttu-id="8eb8d-133">Opciones para configurar esa instancia específica del controlador.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="8eb8d-134">Los esquemas son útiles como mecanismo para hacer referencia a los comportamientos de autenticación, desafío y prohibición del controlador asociado.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="8eb8d-135">Por ejemplo, una directiva de autorización puede usar nombres de esquemas para especificar qué esquema (o esquemas) de autenticación conviene usar para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="8eb8d-136">Al configurar la autenticación, es habitual especificar un esquema de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="8eb8d-137">A menos que un recurso solicite un esquema específico, se usará el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="8eb8d-138">También es posible:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-138">It's also possible to:</span></span>

* <span data-ttu-id="8eb8d-139">Especificar distintos esquemas predeterminados que se usarán para las acciones de autenticación, desafío y prohibición.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="8eb8d-140">Combinar varios esquemas en uno mediante [esquemas de directiva](xref:security/authentication/policyschemes).</span><span class="sxs-lookup"><span data-stu-id="8eb8d-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="8eb8d-141">Controlador de autenticación</span><span class="sxs-lookup"><span data-stu-id="8eb8d-141">Authentication handler</span></span>

<span data-ttu-id="8eb8d-142">Un controlador de autenticación:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-142">An authentication handler:</span></span>

* <span data-ttu-id="8eb8d-143">Es un tipo que implementa el comportamiento de un esquema.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="8eb8d-144">Se deriva de <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> o <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="8eb8d-145">Tiene la responsabilidad principal de autenticar a los usuarios.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="8eb8d-146">Según la configuración del esquema de autenticación y el contexto de la solicitud entrante, los controladores de autenticación:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="8eb8d-147">Construyen objetos <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> que representan la identidad del usuario si la autenticación se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="8eb8d-148">Devuelven "sin resultados" o "error" si la autenticación no es correcta.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="8eb8d-149">Tienen métodos para las acciones de desafío y prohibición para los casos en los que los usuarios intenten acceder a los recursos:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="8eb8d-150">No están autorizados a tener acceso (prohibición).</span><span class="sxs-lookup"><span data-stu-id="8eb8d-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="8eb8d-151">Cuando no están autenticados (desafío).</span><span class="sxs-lookup"><span data-stu-id="8eb8d-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="8eb8d-152">Authenticate</span><span class="sxs-lookup"><span data-stu-id="8eb8d-152">Authenticate</span></span>

<span data-ttu-id="8eb8d-153">La acción de autenticación de un esquema de autenticación es la responsable de construir la identidad del usuario basándose en el contexto de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="8eb8d-154">Devuelve <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult>, que indica si la autenticación se realizó correctamente y, en caso afirmativo, la identidad del usuario en un vale de autenticación.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="8eb8d-155">Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="8eb8d-156">Ejemplos de autenticación:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-156">Authenticate examples include:</span></span>

* <span data-ttu-id="8eb8d-157">Un esquema de autenticación de cookie que crea la identidad del usuario a partir de las cookies.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-157">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="8eb8d-158">Un esquema portador JWT que deserializa y valida un token de portador JWT para construir la identidad del usuario.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="8eb8d-159">Desafío</span><span class="sxs-lookup"><span data-stu-id="8eb8d-159">Challenge</span></span>

<span data-ttu-id="8eb8d-160">La autorización invoca un desafío de autenticación si un usuario no autenticado solicita un punto de conexión que requiere autenticación.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="8eb8d-161">Se emite un desafío de autenticación si, por ejemplo, un usuario anónimo solicita un recurso restringido o hace clic en un vínculo de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="8eb8d-162">La autorización invoca un desafío con los esquemas de autenticación especificados o con el valor predeterminado, si no se especifica ningún esquema.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="8eb8d-163">Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-163">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="8eb8d-164">Ejemplos de desafío de autenticación:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="8eb8d-165">Un esquema de autenticación de cookie que redirige al usuario a una página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-165">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="8eb8d-166">Un esquema portador JWT que devuelve un resultado 401 con un encabezado `www-authenticate: bearer`.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="8eb8d-167">Una acción de desafío debe permitir que el usuario sepa qué mecanismo de autenticación hay que emplear para tener acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="8eb8d-168">Prohibición</span><span class="sxs-lookup"><span data-stu-id="8eb8d-168">Forbid</span></span>

<span data-ttu-id="8eb8d-169">La autorización llama a una acción de prohibición del esquema de autenticación cuando un usuario autenticado intenta tener acceso a un recurso para el que no tiene permiso de acceso.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="8eb8d-170">Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-170">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="8eb8d-171">Ejemplos de prohibición de autenticación:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="8eb8d-172">Un esquema de autenticación de cookie que redirige al usuario a una página que indica que se ha prohibido el acceso.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-172">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="8eb8d-173">Un esquema portador JWT que devuelve un resultado 403.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="8eb8d-174">Un esquema de autenticación personalizado que redirige a una página en la que el usuario puede solicitar acceso al recurso.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="8eb8d-175">Una acción de prohibición puede permitir que los usuarios sepan que:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="8eb8d-176">La autenticación se ha realizado correctamente.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-176">They are authenticated.</span></span>
* <span data-ttu-id="8eb8d-177">No se les permite el acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="8eb8d-178">Consulte los vínculos siguientes para conocer las diferencias entre desafío y prohibición:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="8eb8d-179">[Desafío y prohibido con un controlador de recursos operativos](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)</span><span class="sxs-lookup"><span data-stu-id="8eb8d-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="8eb8d-180">[Diferencias entre desafío y prohibido](xref:security/authorization/secure-data#challenge)</span><span class="sxs-lookup"><span data-stu-id="8eb8d-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="8eb8d-181">Proveedores de autenticación por inquilino</span><span class="sxs-lookup"><span data-stu-id="8eb8d-181">Authentication providers per tenant</span></span>

<span data-ttu-id="8eb8d-182">El marco ASP.NET Core no tiene una solución integrada para la autenticación de varios inquilinos.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-182">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="8eb8d-183">Aunque es ciertamente posible que los clientes escriban uno, con las características integradas, se recomienda a los clientes que busquen en [Orchard Core](https://www.orchardcore.net/) con este fin.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-183">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="8eb8d-184">Orchard Core es:</span><span class="sxs-lookup"><span data-stu-id="8eb8d-184">Orchard Core is:</span></span>

* <span data-ttu-id="8eb8d-185">Un marco de aplicaciones multiinquilino y modular de código abierto creado con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-185">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="8eb8d-186">Un sistema de administración de contenido (CMS) basado en el marco de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-186">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="8eb8d-187">Visite [Orchard Core](https://github.com/OrchardCMS/OrchardCore) para ver un ejemplo de proveedores de autenticación por inquilino.</span><span class="sxs-lookup"><span data-stu-id="8eb8d-187">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8eb8d-188">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8eb8d-188">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [<span data-ttu-id="8eb8d-189">Requerir usuarios autenticados globalmente</span><span class="sxs-lookup"><span data-stu-id="8eb8d-189">Globally require authenticated users</span></span>](xref:security/authorization/secure-data#rau)