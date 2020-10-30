---
title: Prevención de ataques de redireccionamiento abiertos en ASP.NET Core
author: ardalis
description: Muestra cómo evitar ataques de redireccionamiento abierto contra una aplicación ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/preventing-open-redirects
ms.openlocfilehash: e546cd852367921c7c694db3639f7a233f606e75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058394"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="8a540-103">Prevención de ataques de redireccionamiento abiertos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a540-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="8a540-104">Una aplicación web que redirige a una dirección URL que se especifica a través de la solicitud, como la cadena de consulta o los datos del formulario, se pueden alterar para redirigir a los usuarios a una dirección URL externa y malintencionada.</span><span class="sxs-lookup"><span data-stu-id="8a540-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="8a540-105">Esta manipulación se denomina ataque de redireccionamiento abierto.</span><span class="sxs-lookup"><span data-stu-id="8a540-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="8a540-106">Siempre que la lógica de la aplicación redirija a una dirección URL especificada, debe comprobar que la dirección URL de redireccionamiento no se ha alterado.</span><span class="sxs-lookup"><span data-stu-id="8a540-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="8a540-107">ASP.NET Core tiene funcionalidad integrada para ayudar a proteger las aplicaciones frente a ataques de redireccionamiento abierto (también conocidos como redireccionamiento abierto).</span><span class="sxs-lookup"><span data-stu-id="8a540-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="8a540-108">¿Qué es un ataque de redireccionamiento abierto?</span><span class="sxs-lookup"><span data-stu-id="8a540-108">What is an open redirect attack?</span></span>

<span data-ttu-id="8a540-109">Con frecuencia, las aplicaciones web redirigen a los usuarios a una página de inicio de sesión cuando acceden a recursos que requieren autenticación.</span><span class="sxs-lookup"><span data-stu-id="8a540-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="8a540-110">Normalmente, el redireccionamiento incluye un `returnUrl` parámetro QueryString para que el usuario pueda volver a la dirección URL solicitada originalmente después de haber iniciado sesión correctamente.</span><span class="sxs-lookup"><span data-stu-id="8a540-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="8a540-111">Una vez que el usuario se autentica, se le redirige a la dirección URL que se solicitó originalmente.</span><span class="sxs-lookup"><span data-stu-id="8a540-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="8a540-112">Dado que la dirección URL de destino se especifica en la cadena de consulta de la solicitud, un usuario malintencionado podría manipular la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="8a540-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="8a540-113">Una QueryString modificada podría permitir que el sitio redirija al usuario a un sitio externo malintencionado.</span><span class="sxs-lookup"><span data-stu-id="8a540-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="8a540-114">Esta técnica se denomina ataque de redireccionamiento (o redireccionamiento) abierto.</span><span class="sxs-lookup"><span data-stu-id="8a540-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="8a540-115">Un ataque de ejemplo</span><span class="sxs-lookup"><span data-stu-id="8a540-115">An example attack</span></span>

<span data-ttu-id="8a540-116">Un usuario malintencionado puede desarrollar un ataque diseñado para permitir el acceso de los usuarios malintencionados a las credenciales de un usuario o a la información confidencial.</span><span class="sxs-lookup"><span data-stu-id="8a540-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="8a540-117">Para comenzar el ataque, el usuario malintencionado hace que el usuario haga clic en un vínculo a la página de inicio de sesión del sitio con un `returnUrl` valor QueryString agregado a la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8a540-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="8a540-118">Por ejemplo, considere una aplicación en `contoso.com` que incluye una página de inicio de sesión en `http://contoso.com/Account/LogOn?returnUrl=/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="8a540-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="8a540-119">El ataque sigue estos pasos:</span><span class="sxs-lookup"><span data-stu-id="8a540-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="8a540-120">El usuario hace clic en un vínculo malintencionado a `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (la segunda dirección URL es "Contoso **1** . com", no "contoso.com").</span><span class="sxs-lookup"><span data-stu-id="8a540-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso **1** .com", not "contoso.com").</span></span>
2. <span data-ttu-id="8a540-121">El usuario inicia sesión correctamente.</span><span class="sxs-lookup"><span data-stu-id="8a540-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="8a540-122">El usuario es redirigido (por el sitio) a `http://contoso1.com/Account/LogOn` (un sitio malintencionado que se parece exactamente al sitio real).</span><span class="sxs-lookup"><span data-stu-id="8a540-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="8a540-123">El usuario vuelve a iniciar sesión (dando a un sitio malintencionado sus credenciales) y se redirige de nuevo al sitio real.</span><span class="sxs-lookup"><span data-stu-id="8a540-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="8a540-124">Lo más probable es que el usuario cree que se produjo un error en el primer intento de iniciar sesión y que el segundo intento es correcto.</span><span class="sxs-lookup"><span data-stu-id="8a540-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="8a540-125">Lo más probable es que el usuario no tenga constancia de que sus credenciales se ven comprometidas.</span><span class="sxs-lookup"><span data-stu-id="8a540-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Proceso de ataque de redireccionamiento abierto](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="8a540-127">Además de las páginas de inicio de sesión, algunos sitios proporcionan páginas o puntos de conexión de redireccionamiento.</span><span class="sxs-lookup"><span data-stu-id="8a540-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="8a540-128">Imagine que la aplicación tiene una página con una redirección abierta, `/Home/Redirect` .</span><span class="sxs-lookup"><span data-stu-id="8a540-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="8a540-129">Un atacante podría crear, por ejemplo, un vínculo en un correo electrónico que va a `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` .</span><span class="sxs-lookup"><span data-stu-id="8a540-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="8a540-130">Un usuario típico examinará la dirección URL y verá que comienza con el nombre del sitio.</span><span class="sxs-lookup"><span data-stu-id="8a540-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="8a540-131">Si confía en él, se hará clic en el vínculo.</span><span class="sxs-lookup"><span data-stu-id="8a540-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="8a540-132">A continuación, el redireccionamiento abierto enviará el usuario al sitio de suplantación de identidad (phishing), que es idéntico al suyo y es probable que el usuario inicie sesión en lo que creemos que es su sitio.</span><span class="sxs-lookup"><span data-stu-id="8a540-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="8a540-133">Protección contra ataques de redireccionamiento abierto</span><span class="sxs-lookup"><span data-stu-id="8a540-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="8a540-134">Al desarrollar aplicaciones Web, trate todos los datos proporcionados por el usuario como no confiables.</span><span class="sxs-lookup"><span data-stu-id="8a540-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="8a540-135">Si la aplicación tiene una funcionalidad que redirige al usuario en función del contenido de la dirección URL, asegúrese de que estas redirecciones solo se realizan localmente en la aplicación (o en una dirección URL conocida, no en ninguna dirección URL que se pueda proporcionar en la cadena de tipo).</span><span class="sxs-lookup"><span data-stu-id="8a540-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="8a540-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="8a540-136">LocalRedirect</span></span>

<span data-ttu-id="8a540-137">Use el `LocalRedirect` método auxiliar de la clase base `Controller` :</span><span class="sxs-lookup"><span data-stu-id="8a540-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="8a540-138">`LocalRedirect` producirá una excepción si se especifica una dirección URL no local.</span><span class="sxs-lookup"><span data-stu-id="8a540-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="8a540-139">De lo contrario, se comporta igual que el `Redirect` método.</span><span class="sxs-lookup"><span data-stu-id="8a540-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="8a540-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="8a540-140">IsLocalUrl</span></span>

<span data-ttu-id="8a540-141">Use el método [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) para probar las direcciones URL antes de redirigir:</span><span class="sxs-lookup"><span data-stu-id="8a540-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="8a540-142">En el ejemplo siguiente se muestra cómo comprobar si una dirección URL es local antes de la redirección.</span><span class="sxs-lookup"><span data-stu-id="8a540-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="8a540-143">El `IsLocalUrl` método evita que los usuarios se redirijan accidentalmente a un sitio malintencionado.</span><span class="sxs-lookup"><span data-stu-id="8a540-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="8a540-144">Puede registrar los detalles de la dirección URL que se proporcionó cuando se proporciona una dirección URL no local en una situación en la que esperaba una dirección URL local.</span><span class="sxs-lookup"><span data-stu-id="8a540-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="8a540-145">El registro de direcciones URL de redireccionamiento puede ayudar a diagnosticar los ataques de redirección.</span><span class="sxs-lookup"><span data-stu-id="8a540-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
