---
title: Prevención de ataques de falsificación de solicitud entre sitios (XSRF/CSRF) en ASP.NET Core
author: steve-smith
description: Descubra cómo evitar ataques contra aplicaciones web en las que un sitio Web malintencionado puede influir en la interacción entre un explorador cliente y la aplicación.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: security/anti-request-forgery
ms.openlocfilehash: cc6f7c7e6692224f537f5eeba50b214aa84029db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018837"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="06026-103">Prevención de ataques de falsificación de solicitud entre sitios (XSRF/CSRF) en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="06026-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="06026-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="06026-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="06026-105">La falsificación de solicitudes entre sitios (también conocida como XSRF o CSRF) es un ataque contra aplicaciones hospedadas en Web, por lo que una aplicación web malintencionada puede influir en la interacción entre un explorador cliente y una aplicación web que confía en ese explorador.</span><span class="sxs-lookup"><span data-stu-id="06026-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="06026-106">Estos ataques son posibles porque los exploradores Web envían algunos tipos de tokens de autenticación automáticamente con cada solicitud a un sitio Web.</span><span class="sxs-lookup"><span data-stu-id="06026-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="06026-107">Este tipo de ataque también se conoce como un *ataque de un solo clic* o una *sesión* que se está iniciando porque el ataque aprovecha la sesión autenticada previamente del usuario.</span><span class="sxs-lookup"><span data-stu-id="06026-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="06026-108">Un ejemplo de un ataque CSRF:</span><span class="sxs-lookup"><span data-stu-id="06026-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="06026-109">Un usuario inicia sesión en `www.good-banking-site.com` mediante la autenticación de formularios.</span><span class="sxs-lookup"><span data-stu-id="06026-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="06026-110">El servidor autentica al usuario y emite una respuesta que incluye una autenticación cookie .</span><span class="sxs-lookup"><span data-stu-id="06026-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="06026-111">El sitio es vulnerable a ataques porque confía en cualquier solicitud que reciba con una autenticación válida cookie .</span><span class="sxs-lookup"><span data-stu-id="06026-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="06026-112">El usuario visita un sitio malintencionado, `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="06026-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="06026-113">El sitio malintencionado, `www.bad-crook-site.com` , contiene un formulario HTML similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="06026-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="06026-114">Tenga en cuenta que las `action` entradas del formulario se publican en el sitio vulnerable, no en el sitio malintencionado.</span><span class="sxs-lookup"><span data-stu-id="06026-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="06026-115">Esta es la parte "entre sitios" de CSRF.</span><span class="sxs-lookup"><span data-stu-id="06026-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="06026-116">El usuario selecciona el botón Enviar.</span><span class="sxs-lookup"><span data-stu-id="06026-116">The user selects the submit button.</span></span> <span data-ttu-id="06026-117">El explorador realiza la solicitud e incluye automáticamente la autenticación cookie para el dominio solicitado, `www.good-banking-site.com` .</span><span class="sxs-lookup"><span data-stu-id="06026-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="06026-118">La solicitud se ejecuta en el `www.good-banking-site.com` servidor con el contexto de autenticación del usuario y puede realizar cualquier acción que pueda realizar un usuario autenticado.</span><span class="sxs-lookup"><span data-stu-id="06026-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="06026-119">Además del escenario en el que el usuario selecciona el botón para enviar el formulario, el sitio malintencionado podría:</span><span class="sxs-lookup"><span data-stu-id="06026-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="06026-120">Ejecute un script que envíe el formulario automáticamente.</span><span class="sxs-lookup"><span data-stu-id="06026-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="06026-121">Envíe el envío del formulario como una solicitud AJAX.</span><span class="sxs-lookup"><span data-stu-id="06026-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="06026-122">Oculte el formulario mediante CSS.</span><span class="sxs-lookup"><span data-stu-id="06026-122">Hide the form using CSS.</span></span>

<span data-ttu-id="06026-123">Estos escenarios alternativos no requieren ninguna acción o entrada del usuario que no sea la primera vez que visitan el sitio malintencionado.</span><span class="sxs-lookup"><span data-stu-id="06026-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="06026-124">El uso de HTTPS no impide un ataque CSRF.</span><span class="sxs-lookup"><span data-stu-id="06026-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="06026-125">El sitio malintencionado puede enviar una `https://www.good-banking-site.com/` solicitud con la misma facilidad con la que puede enviar una solicitud no segura.</span><span class="sxs-lookup"><span data-stu-id="06026-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="06026-126">Algunos ataques apuntan a los puntos de conexión que responden a las solicitudes GET, en cuyo caso se puede usar una etiqueta de imagen para realizar la acción.</span><span class="sxs-lookup"><span data-stu-id="06026-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="06026-127">Esta forma de ataque es habitual en sitios de foros que permiten imágenes pero bloquean JavaScript.</span><span class="sxs-lookup"><span data-stu-id="06026-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="06026-128">Las aplicaciones que cambian el estado en las solicitudes GET, donde se modifican las variables o los recursos, son vulnerables a ataques malintencionados.</span><span class="sxs-lookup"><span data-stu-id="06026-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="06026-129">**Las solicitudes GET que cambian el estado son inseguras. Un procedimiento recomendado es no cambiar nunca el estado de una solicitud GET.**</span><span class="sxs-lookup"><span data-stu-id="06026-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="06026-130">Los ataques CSRF son posibles en las aplicaciones web que usan cookie para la autenticación porque:</span><span class="sxs-lookup"><span data-stu-id="06026-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="06026-131">Almacenes de exploradores cookie emitidos por una aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="06026-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="06026-132">cookieLos s almacenados incluyen sesiones cookie para los usuarios autenticados.</span><span class="sxs-lookup"><span data-stu-id="06026-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="06026-133">Los exploradores envían todos los cookie s asociados a un dominio a la aplicación web cada solicitud, con independencia de cómo se haya generado la solicitud a la aplicación en el explorador.</span><span class="sxs-lookup"><span data-stu-id="06026-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="06026-134">Sin embargo, los ataques CSRF no se limitan a la explotación de cookie .</span><span class="sxs-lookup"><span data-stu-id="06026-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="06026-135">Por ejemplo, la autenticación básica e implícita también son vulnerables.</span><span class="sxs-lookup"><span data-stu-id="06026-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="06026-136">Después de que un usuario inicie sesión con la autenticación básica o implícita, el explorador enviará automáticamente las credenciales hasta que finalice la sesión &dagger; .</span><span class="sxs-lookup"><span data-stu-id="06026-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="06026-137">&dagger;En este contexto, la *sesión* hace referencia a la sesión del lado cliente durante la que se autentica el usuario.</span><span class="sxs-lookup"><span data-stu-id="06026-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="06026-138">No está relacionado con las sesiones del lado servidor o con el [middleware de sesión ASP.net Core](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="06026-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="06026-139">Los usuarios pueden proteger contra las vulnerabilidades de CSRF tomando precauciones:</span><span class="sxs-lookup"><span data-stu-id="06026-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="06026-140">Cierre la sesión de las aplicaciones web cuando termine de usarlas.</span><span class="sxs-lookup"><span data-stu-id="06026-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="06026-141">Desactive exploradores cookie periódicamente.</span><span class="sxs-lookup"><span data-stu-id="06026-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="06026-142">Sin embargo, las vulnerabilidades CSRF son fundamentalmente un problema con la aplicación Web, no con el usuario final.</span><span class="sxs-lookup"><span data-stu-id="06026-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="06026-143">Aspectos básicos de la autenticación</span><span class="sxs-lookup"><span data-stu-id="06026-143">Authentication fundamentals</span></span>

<span data-ttu-id="06026-144">Cookiela autenticación basada en es una forma popular de autenticación.</span><span class="sxs-lookup"><span data-stu-id="06026-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="06026-145">Los sistemas de autenticación basados en tokens están creciendo en popularidad, especialmente en el caso de las aplicaciones de una sola página (Spa).</span><span class="sxs-lookup"><span data-stu-id="06026-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="06026-146">Cookieautenticación basada en</span><span class="sxs-lookup"><span data-stu-id="06026-146">Cookie-based authentication</span></span>

<span data-ttu-id="06026-147">Cuando un usuario se autentica con su nombre de usuario y contraseña, se le emite un token que contiene un vale de autenticación que se puede usar para la autenticación y la autorización.</span><span class="sxs-lookup"><span data-stu-id="06026-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="06026-148">El token se almacena como un cookie que acompaña a cada solicitud que el cliente realiza.</span><span class="sxs-lookup"><span data-stu-id="06026-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="06026-149">La generación y validación cookie se realiza mediante el Cookie middleware de autenticación.</span><span class="sxs-lookup"><span data-stu-id="06026-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="06026-150">El [middleware](xref:fundamentals/middleware/index) serializa una entidad de seguridad de usuario en un cifrado cookie .</span><span class="sxs-lookup"><span data-stu-id="06026-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="06026-151">En las solicitudes posteriores, el middleware valida cookie , vuelve a crear la entidad de seguridad y asigna la entidad de seguridad a la propiedad [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) de [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="06026-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="06026-152">Autenticación basada en token</span><span class="sxs-lookup"><span data-stu-id="06026-152">Token-based authentication</span></span>

<span data-ttu-id="06026-153">Cuando un usuario se autentica, se le emite un token (no un token antifalsificación).</span><span class="sxs-lookup"><span data-stu-id="06026-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="06026-154">El token contiene información de usuario en forma de [notificaciones](/dotnet/framework/security/claims-based-identity-model) o un token de referencia que apunta a la aplicación para que el estado de usuario se mantenga en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06026-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="06026-155">Cuando un usuario intenta acceder a un recurso que requiere autenticación, el token se envía a la aplicación con un encabezado de autorización adicional en forma de token de portador.</span><span class="sxs-lookup"><span data-stu-id="06026-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="06026-156">Esto hace que la aplicación no tenga estado.</span><span class="sxs-lookup"><span data-stu-id="06026-156">This makes the app stateless.</span></span> <span data-ttu-id="06026-157">En cada solicitud subsiguiente, el token se pasa en la solicitud de validación del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="06026-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="06026-158">Este token no está *cifrado*; está *codificado*.</span><span class="sxs-lookup"><span data-stu-id="06026-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="06026-159">En el servidor, el token se descodifica para tener acceso a su información.</span><span class="sxs-lookup"><span data-stu-id="06026-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="06026-160">Para enviar el token en solicitudes posteriores, almacene el token en el almacenamiento local del explorador.</span><span class="sxs-lookup"><span data-stu-id="06026-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="06026-161">No se preocupe de la vulnerabilidad de CSRF si el token se almacena en el almacenamiento local del explorador.</span><span class="sxs-lookup"><span data-stu-id="06026-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="06026-162">CSRF es un problema cuando el token se almacena en cookie .</span><span class="sxs-lookup"><span data-stu-id="06026-162">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="06026-163">Para obtener más información, consulte el ejemplo de código de GitHub problema [Spa agrega dos cookie s](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="06026-163">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="06026-164">Varias aplicaciones hospedadas en un dominio</span><span class="sxs-lookup"><span data-stu-id="06026-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="06026-165">Los entornos de hospedaje compartidos son vulnerables al secuestro de sesión, el inicio de sesión CSRF y otros ataques.</span><span class="sxs-lookup"><span data-stu-id="06026-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="06026-166">Aunque `example1.contoso.net` y `example2.contoso.net` son hosts diferentes, hay una relación de confianza implícita entre los hosts del `*.contoso.net` dominio.</span><span class="sxs-lookup"><span data-stu-id="06026-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="06026-167">Esta relación de confianza implícita permite que los hosts potencialmente que no son de confianza afecten a cookie los s (las directivas del mismo origen que rigen las solicitudes Ajax no se aplican necesariamente a http cookie s).</span><span class="sxs-lookup"><span data-stu-id="06026-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="06026-168">Los ataques que aprovechan cookie los elementos de confianza entre las aplicaciones hospedadas en el mismo dominio se pueden evitar al no compartir dominios.</span><span class="sxs-lookup"><span data-stu-id="06026-168">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="06026-169">Cuando cada aplicación se hospeda en su propio dominio, no hay ninguna cookie relación de confianza implícita para la explotación.</span><span class="sxs-lookup"><span data-stu-id="06026-169">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="06026-170">Configuración de ASP.NET Core antifalsificación</span><span class="sxs-lookup"><span data-stu-id="06026-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="06026-171">ASP.NET Core implementa antifalsificación mediante la [protección de datos de ASP.net Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="06026-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="06026-172">La pila de protección de datos debe estar configurada para funcionar en una granja de servidores.</span><span class="sxs-lookup"><span data-stu-id="06026-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="06026-173">Consulte [configuración](xref:security/data-protection/configuration/overview) de la protección de datos para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="06026-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="06026-174">El middleware antifalsificación se agrega al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) cuando se llama a una de las siguientes API en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="06026-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="06026-175">El middleware antifalsificación se agrega al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) cuando <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> se llama a en.`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="06026-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="06026-176">En ASP.NET Core 2,0 o posterior, [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) inserta tokens antifalsificación en elementos de formulario HTML.</span><span class="sxs-lookup"><span data-stu-id="06026-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="06026-177">El siguiente marcado en un Razor archivo genera automáticamente tokens antifalsificación:</span><span class="sxs-lookup"><span data-stu-id="06026-177">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="06026-178">De forma similar, [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) genera tokens antifalsificación de forma predeterminada si el método del formulario no se obtiene.</span><span class="sxs-lookup"><span data-stu-id="06026-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="06026-179">La generación automática de tokens antifalsificación para elementos de formulario HTML se produce cuando la `<form>` etiqueta contiene el `method="post"` atributo y se cumple cualquiera de las siguientes condiciones:</span><span class="sxs-lookup"><span data-stu-id="06026-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="06026-180">El atributo de acción está vacío ( `action=""` ).</span><span class="sxs-lookup"><span data-stu-id="06026-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="06026-181">No se ha proporcionado el atributo de acción ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="06026-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="06026-182">Se puede deshabilitar la generación automática de tokens antifalsificación para elementos de formulario HTML:</span><span class="sxs-lookup"><span data-stu-id="06026-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="06026-183">Deshabilite explícitamente los tokens antifalsificación con el `asp-antiforgery` atributo:</span><span class="sxs-lookup"><span data-stu-id="06026-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="06026-184">El elemento de formulario no participa en las aplicaciones auxiliares de etiquetas mediante el uso de la aplicación auxiliar de etiquetas [. símbolo de cancelación](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="06026-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="06026-185">Quite `FormTagHelper` de la vista.</span><span class="sxs-lookup"><span data-stu-id="06026-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="06026-186">`FormTagHelper`Se puede quitar de una vista agregando la siguiente directiva a la Razor vista:</span><span class="sxs-lookup"><span data-stu-id="06026-186">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="06026-187">[ Razor Las páginas](xref:razor-pages/index) se protegen automáticamente de XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="06026-187">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="06026-188">Para obtener más información, consulte [XSRF/CSRF y Razor pages](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="06026-188">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="06026-189">El enfoque más común para defenderse contra ataques CSRF es usar el *patrón de token de sincronizador* (STP).</span><span class="sxs-lookup"><span data-stu-id="06026-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="06026-190">STP se usa cuando el usuario solicita una página con datos de formulario:</span><span class="sxs-lookup"><span data-stu-id="06026-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="06026-191">El servidor envía al cliente un token asociado a la identidad del usuario actual.</span><span class="sxs-lookup"><span data-stu-id="06026-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="06026-192">El cliente devuelve el token al servidor para su comprobación.</span><span class="sxs-lookup"><span data-stu-id="06026-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="06026-193">Si el servidor recibe un token que no coincide con la identidad del usuario autenticado, se rechaza la solicitud.</span><span class="sxs-lookup"><span data-stu-id="06026-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="06026-194">El token es único e impredecible.</span><span class="sxs-lookup"><span data-stu-id="06026-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="06026-195">El token también se puede usar para garantizar la secuenciación correcta de una serie de solicitudes (por ejemplo, asegurándose de que la secuencia de solicitud de: Página 1 > página 2 > página 3).</span><span class="sxs-lookup"><span data-stu-id="06026-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="06026-196">Todas las formas de ASP.NET Core plantillas MVC y Razor pages generan tokens antifalsificación.</span><span class="sxs-lookup"><span data-stu-id="06026-196">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="06026-197">En el siguiente par de ejemplos de vista se generan tokens antifalsificación:</span><span class="sxs-lookup"><span data-stu-id="06026-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="06026-198">Agregue explícitamente un token antifalsificación a un `<form>` elemento sin usar aplicaciones auxiliares de etiquetas con la aplicación auxiliar HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :</span><span class="sxs-lookup"><span data-stu-id="06026-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="06026-199">En cada uno de los casos anteriores, ASP.NET Core agrega un campo de formulario oculto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="06026-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="06026-200">ASP.NET Core incluye tres [filtros](xref:mvc/controllers/filters) para trabajar con tokens antifalsificación:</span><span class="sxs-lookup"><span data-stu-id="06026-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="06026-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="06026-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="06026-202">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="06026-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="06026-203">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="06026-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="06026-204">Opciones antifalsificación</span><span class="sxs-lookup"><span data-stu-id="06026-204">Antiforgery options</span></span>

<span data-ttu-id="06026-205">Personalizar [las opciones antifalsificación](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="06026-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="06026-206">&dagger;Establezca las propiedades antifalsificación `Cookie` mediante las propiedades de la clase de [ Cookie generador](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .</span><span class="sxs-lookup"><span data-stu-id="06026-206">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="06026-207">Opción</span><span class="sxs-lookup"><span data-stu-id="06026-207">Option</span></span> | <span data-ttu-id="06026-208">Descripción</span><span class="sxs-lookup"><span data-stu-id="06026-208">Description</span></span> |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="06026-209">Determina la configuración utilizada para crear la antifalsificación cookie .</span><span class="sxs-lookup"><span data-stu-id="06026-209">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="06026-210">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="06026-210">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="06026-211">Nombre del campo de formulario oculto utilizado por el sistema antifalsificación para representar los tokens antifalsificación en las vistas.</span><span class="sxs-lookup"><span data-stu-id="06026-211">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="06026-212">HeaderName</span><span class="sxs-lookup"><span data-stu-id="06026-212">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="06026-213">Nombre del encabezado que usa el sistema antifalsificación.</span><span class="sxs-lookup"><span data-stu-id="06026-213">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="06026-214">Si es `null` , el sistema solo tiene en cuenta los datos de formulario.</span><span class="sxs-lookup"><span data-stu-id="06026-214">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="06026-215">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="06026-215">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="06026-216">Especifica si se va a suprimir la generación del `X-Frame-Options` encabezado.</span><span class="sxs-lookup"><span data-stu-id="06026-216">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="06026-217">De forma predeterminada, el encabezado se genera con un valor de "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="06026-217">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="06026-218">Tiene como valor predeterminado `false`.</span><span class="sxs-lookup"><span data-stu-id="06026-218">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="06026-219">Opción</span><span class="sxs-lookup"><span data-stu-id="06026-219">Option</span></span> | <span data-ttu-id="06026-220">Descripción</span><span class="sxs-lookup"><span data-stu-id="06026-220">Description</span></span> |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="06026-221">Determina la configuración utilizada para crear la antifalsificación cookie .</span><span class="sxs-lookup"><span data-stu-id="06026-221">Determines the settings used to create the antiforgery cookies.</span></span> |
| <span data-ttu-id="06026-222">[CookieDominio](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain)</span><span class="sxs-lookup"><span data-stu-id="06026-222">[CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain)</span></span> | <span data-ttu-id="06026-223">Dominio de cookie .</span><span class="sxs-lookup"><span data-stu-id="06026-223">The domain of the cookie.</span></span> <span data-ttu-id="06026-224">Tiene como valor predeterminado `null`.</span><span class="sxs-lookup"><span data-stu-id="06026-224">Defaults to `null`.</span></span> <span data-ttu-id="06026-225">Esta propiedad está obsoleta y se quitará en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="06026-225">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="06026-226">La alternativa recomendada es Cookie . Dominio.</span><span class="sxs-lookup"><span data-stu-id="06026-226">The recommended alternative is Cookie.Domain.</span></span> |
| <span data-ttu-id="06026-227">[CookieNombre](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename)</span><span class="sxs-lookup"><span data-stu-id="06026-227">[CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename)</span></span> | <span data-ttu-id="06026-228">Nombre del objeto cookie.</span><span class="sxs-lookup"><span data-stu-id="06026-228">The name of the cookie.</span></span> <span data-ttu-id="06026-229">Si no se establece, el sistema genera un nombre único que empieza con el [ Cookie prefijo predeterminado](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. antifalsificación. ").</span><span class="sxs-lookup"><span data-stu-id="06026-229">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="06026-230">Esta propiedad está obsoleta y se quitará en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="06026-230">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="06026-231">La alternativa recomendada es Cookie . Name.</span><span class="sxs-lookup"><span data-stu-id="06026-231">The recommended alternative is Cookie.Name.</span></span> |
| <span data-ttu-id="06026-232">[CookieCamino](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath)</span><span class="sxs-lookup"><span data-stu-id="06026-232">[CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath)</span></span> | <span data-ttu-id="06026-233">La ruta de acceso establecida en cookie .</span><span class="sxs-lookup"><span data-stu-id="06026-233">The path set on the cookie.</span></span> <span data-ttu-id="06026-234">Esta propiedad está obsoleta y se quitará en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="06026-234">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="06026-235">La alternativa recomendada es Cookie . Camino.</span><span class="sxs-lookup"><span data-stu-id="06026-235">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="06026-236">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="06026-236">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="06026-237">Nombre del campo de formulario oculto utilizado por el sistema antifalsificación para representar los tokens antifalsificación en las vistas.</span><span class="sxs-lookup"><span data-stu-id="06026-237">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="06026-238">HeaderName</span><span class="sxs-lookup"><span data-stu-id="06026-238">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="06026-239">Nombre del encabezado que usa el sistema antifalsificación.</span><span class="sxs-lookup"><span data-stu-id="06026-239">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="06026-240">Si es `null` , el sistema solo tiene en cuenta los datos de formulario.</span><span class="sxs-lookup"><span data-stu-id="06026-240">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="06026-241">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="06026-241">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="06026-242">Especifica si el sistema antifalsificación requiere HTTPS.</span><span class="sxs-lookup"><span data-stu-id="06026-242">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="06026-243">Si `true` es, se produce un error en las solicitudes que no son https.</span><span class="sxs-lookup"><span data-stu-id="06026-243">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="06026-244">Tiene como valor predeterminado `false`.</span><span class="sxs-lookup"><span data-stu-id="06026-244">Defaults to `false`.</span></span> <span data-ttu-id="06026-245">Esta propiedad está obsoleta y se quitará en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="06026-245">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="06026-246">La alternativa recomendada es establecer Cookie . SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="06026-246">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="06026-247">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="06026-247">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="06026-248">Especifica si se va a suprimir la generación del `X-Frame-Options` encabezado.</span><span class="sxs-lookup"><span data-stu-id="06026-248">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="06026-249">De forma predeterminada, el encabezado se genera con un valor de "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="06026-249">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="06026-250">Tiene como valor predeterminado `false`.</span><span class="sxs-lookup"><span data-stu-id="06026-250">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="06026-251">Para obtener más información, vea [ Cookie AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="06026-251">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="06026-252">Configuración de características antifalsificación con IAntiforgery</span><span class="sxs-lookup"><span data-stu-id="06026-252">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="06026-253">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) proporciona la API para configurar las características antifalsificación.</span><span class="sxs-lookup"><span data-stu-id="06026-253">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="06026-254">`IAntiforgery`se puede solicitar en el `Configure` método de la `Startup` clase.</span><span class="sxs-lookup"><span data-stu-id="06026-254">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="06026-255">En el ejemplo siguiente se usa middleware desde la Página principal de la aplicación para generar un token antifalsificación y enviarlo en la respuesta como cookie (mediante la Convención de nomenclatura predeterminada de angular que se describe más adelante en este tema):</span><span class="sxs-lookup"><span data-stu-id="06026-255">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="06026-256">Requerir validación antifalsificación</span><span class="sxs-lookup"><span data-stu-id="06026-256">Require antiforgery validation</span></span>

<span data-ttu-id="06026-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) es un filtro de acción que se puede aplicar a una acción individual, un controlador o globalmente.</span><span class="sxs-lookup"><span data-stu-id="06026-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="06026-258">Las solicitudes realizadas a acciones que tienen aplicado este filtro se bloquean a menos que la solicitud incluya un token antifalsificación válido.</span><span class="sxs-lookup"><span data-stu-id="06026-258">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="06026-259">El `ValidateAntiForgeryToken` atributo requiere un token para las solicitudes a los métodos de acción que marca, incluidas las solicitudes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="06026-259">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="06026-260">Si el `ValidateAntiForgeryToken` atributo se aplica a los controladores de la aplicación, se puede invalidar con el `IgnoreAntiforgeryToken` atributo.</span><span class="sxs-lookup"><span data-stu-id="06026-260">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="06026-261">ASP.NET Core no admite la adición de tokens antifalsificación para obtener solicitudes automáticamente.</span><span class="sxs-lookup"><span data-stu-id="06026-261">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="06026-262">Validar automáticamente tokens antifalsificación para métodos HTTP no seguros únicamente</span><span class="sxs-lookup"><span data-stu-id="06026-262">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="06026-263">ASP.NET Core aplicaciones no generan tokens antifalsificación para métodos HTTP seguros (GET, HEAD, OPTIONs y TRACE).</span><span class="sxs-lookup"><span data-stu-id="06026-263">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="06026-264">En lugar de aplicar ampliamente el `ValidateAntiForgeryToken` atributo y, a continuación, reemplazarlo por `IgnoreAntiforgeryToken` atributos, se puede usar el atributo [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) .</span><span class="sxs-lookup"><span data-stu-id="06026-264">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="06026-265">Este atributo funciona exactamente igual que el `ValidateAntiForgeryToken` atributo, con la salvedad de que no requiere tokens para las solicitudes realizadas mediante los siguientes métodos http:</span><span class="sxs-lookup"><span data-stu-id="06026-265">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="06026-266">GET</span><span class="sxs-lookup"><span data-stu-id="06026-266">GET</span></span>
* <span data-ttu-id="06026-267">HEAD</span><span class="sxs-lookup"><span data-stu-id="06026-267">HEAD</span></span>
* <span data-ttu-id="06026-268">OPCIONES</span><span class="sxs-lookup"><span data-stu-id="06026-268">OPTIONS</span></span>
* <span data-ttu-id="06026-269">TRACE</span><span class="sxs-lookup"><span data-stu-id="06026-269">TRACE</span></span>

<span data-ttu-id="06026-270">Se recomienda usar `AutoValidateAntiforgeryToken` en general para escenarios que no son de API.</span><span class="sxs-lookup"><span data-stu-id="06026-270">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="06026-271">Esto garantiza que las acciones POST están protegidas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="06026-271">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="06026-272">La alternativa es omitir los tokens antifalsificación de forma predeterminada, a menos que `ValidateAntiForgeryToken` se aplique a los métodos de acción individuales.</span><span class="sxs-lookup"><span data-stu-id="06026-272">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="06026-273">Es más probable que en este escenario un método POST de acción se deje sin protección por error, lo que deja la aplicación vulnerable a ataques CSRF.</span><span class="sxs-lookup"><span data-stu-id="06026-273">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="06026-274">Todas las publicaciones deben enviar el token antifalsificación.</span><span class="sxs-lookup"><span data-stu-id="06026-274">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="06026-275">Las API no tienen un mecanismo automático para enviar el token que no cookie forma parte del token.</span><span class="sxs-lookup"><span data-stu-id="06026-275">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="06026-276">La implementación probablemente depende de la implementación del código de cliente.</span><span class="sxs-lookup"><span data-stu-id="06026-276">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="06026-277">A continuación se muestran algunos ejemplos:</span><span class="sxs-lookup"><span data-stu-id="06026-277">Some examples are shown below:</span></span>

<span data-ttu-id="06026-278">Ejemplo de nivel de clase:</span><span class="sxs-lookup"><span data-stu-id="06026-278">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="06026-279">Ejemplo global:</span><span class="sxs-lookup"><span data-stu-id="06026-279">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="06026-280">Server. AddMvc (Options = opciones de>. Filters. Add (New AutoValidateAntiforgeryTokenAttribute ()));</span><span class="sxs-lookup"><span data-stu-id="06026-280">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="06026-281">Reemplazar atributos globales o de antifalsificación de controlador</span><span class="sxs-lookup"><span data-stu-id="06026-281">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="06026-282">El filtro [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) se usa para eliminar la necesidad de un token antifalsificación para una acción o un controlador determinados.</span><span class="sxs-lookup"><span data-stu-id="06026-282">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="06026-283">Cuando se aplica, este filtro reemplaza los `ValidateAntiForgeryToken` `AutoValidateAntiforgeryToken` filtros especificados en un nivel superior (globalmente o en un controlador).</span><span class="sxs-lookup"><span data-stu-id="06026-283">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="06026-284">Actualizar tokens después de la autenticación</span><span class="sxs-lookup"><span data-stu-id="06026-284">Refresh tokens after authentication</span></span>

<span data-ttu-id="06026-285">Los tokens deben actualizarse después de que el usuario se autentique redirigiendo el usuario a una vista o Razor Página de páginas.</span><span class="sxs-lookup"><span data-stu-id="06026-285">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="06026-286">JavaScript, AJAX y Spa</span><span class="sxs-lookup"><span data-stu-id="06026-286">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="06026-287">En las aplicaciones tradicionales basadas en HTML, los tokens antifalsificación se pasan al servidor mediante campos de formulario ocultos.</span><span class="sxs-lookup"><span data-stu-id="06026-287">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="06026-288">En las aplicaciones modernas basadas en JavaScript y Spa, muchas solicitudes se realizan mediante programación.</span><span class="sxs-lookup"><span data-stu-id="06026-288">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="06026-289">Estas solicitudes AJAX pueden usar otras técnicas (como encabezados de solicitud o cookie ) para enviar el token.</span><span class="sxs-lookup"><span data-stu-id="06026-289">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="06026-290">Si cookie se usan para almacenar tokens de autenticación y autenticar las solicitudes de API en el servidor, CSRF es un problema potencial.</span><span class="sxs-lookup"><span data-stu-id="06026-290">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="06026-291">Si se usa el almacenamiento local para almacenar el token, la vulnerabilidad de CSRF podría mitigarse porque los valores del almacenamiento local no se envían automáticamente al servidor con cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="06026-291">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="06026-292">Por lo tanto, el uso del almacenamiento local para almacenar el token antifalsificación en el cliente y el envío del token como un encabezado de solicitud es un enfoque recomendado.</span><span class="sxs-lookup"><span data-stu-id="06026-292">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="06026-293">JavaScript</span><span class="sxs-lookup"><span data-stu-id="06026-293">JavaScript</span></span>

<span data-ttu-id="06026-294">Con JavaScript con vistas, el token se puede crear con un servicio de dentro de la vista.</span><span class="sxs-lookup"><span data-stu-id="06026-294">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="06026-295">Inserte el servicio [Microsoft. AspNetCore. antifalsification. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) en la vista y llame a [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="06026-295">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="06026-296">Este enfoque elimina la necesidad de tratar directamente con los valores cookie de configuración del servidor o de leerlos desde el cliente.</span><span class="sxs-lookup"><span data-stu-id="06026-296">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="06026-297">En el ejemplo anterior se usa JavaScript para leer el valor de campo oculto del encabezado POST de AJAX.</span><span class="sxs-lookup"><span data-stu-id="06026-297">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="06026-298">JavaScript también puede acceder a los tokens en cookie s y usar el cookie contenido de para crear un encabezado con el valor del token.</span><span class="sxs-lookup"><span data-stu-id="06026-298">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="06026-299">Suponiendo que las solicitudes de scripts envíen el token en un encabezado denominado `X-CSRF-TOKEN` , configure el servicio antifalsificación para buscar el `X-CSRF-TOKEN` encabezado:</span><span class="sxs-lookup"><span data-stu-id="06026-299">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="06026-300">En el ejemplo siguiente se usa JavaScript para crear una solicitud AJAX con el encabezado adecuado:</span><span class="sxs-lookup"><span data-stu-id="06026-300">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="06026-301">AngularJS</span><span class="sxs-lookup"><span data-stu-id="06026-301">AngularJS</span></span>

<span data-ttu-id="06026-302">AngularJS usa una Convención para direccionar CSRF.</span><span class="sxs-lookup"><span data-stu-id="06026-302">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="06026-303">Si el servidor envía un cookie con el nombre `XSRF-TOKEN` , el `$http` servicio AngularJS agrega el cookie valor a un encabezado cuando envía una solicitud al servidor.</span><span class="sxs-lookup"><span data-stu-id="06026-303">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="06026-304">Este proceso es automático.</span><span class="sxs-lookup"><span data-stu-id="06026-304">This process is automatic.</span></span> <span data-ttu-id="06026-305">No es necesario establecer explícitamente el encabezado en el cliente.</span><span class="sxs-lookup"><span data-stu-id="06026-305">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="06026-306">El nombre del encabezado es `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="06026-306">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="06026-307">El servidor debe detectar este encabezado y validar su contenido.</span><span class="sxs-lookup"><span data-stu-id="06026-307">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="06026-308">Para que ASP.NET Core API funcione con esta Convención en el inicio de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="06026-308">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="06026-309">Configure la aplicación para proporcionar un token en un cookie llamado `XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="06026-309">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="06026-310">Configure el servicio antifalsificación para buscar un encabezado denominado `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="06026-310">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="06026-311">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="06026-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="06026-312">Extender antifalsificación</span><span class="sxs-lookup"><span data-stu-id="06026-312">Extend antiforgery</span></span>

<span data-ttu-id="06026-313">El tipo [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) permite a los desarrolladores ampliar el comportamiento del sistema anti-CSRF al realizar un recorrido de ida y vuelta por los datos adicionales de cada token.</span><span class="sxs-lookup"><span data-stu-id="06026-313">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="06026-314">Se llama al método [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) cada vez que se genera un token de campo y el valor devuelto se incrusta en el token generado.</span><span class="sxs-lookup"><span data-stu-id="06026-314">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="06026-315">Un implementador puede devolver una marca de tiempo, un valor de seguridad (nonce) o cualquier otro valor y, después, llamar a [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) para validar estos datos cuando se valida el token.</span><span class="sxs-lookup"><span data-stu-id="06026-315">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="06026-316">El nombre de usuario del cliente ya está incrustado en los tokens generados, por lo que no es necesario incluir esta información.</span><span class="sxs-lookup"><span data-stu-id="06026-316">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="06026-317">Si un token incluye datos complementarios pero no `IAntiForgeryAdditionalDataProvider` está configurado, los datos complementarios no se validan.</span><span class="sxs-lookup"><span data-stu-id="06026-317">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06026-318">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="06026-318">Additional resources</span></span>

* <span data-ttu-id="06026-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) en el [proyecto abierto de seguridad de aplicaciones web](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="06026-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
