---
title: Prevención de ataques de redireccionamiento abiertos en ASP.NET Core
author: ardalis
description: Muestra cómo evitar ataques de redireccionamiento abierto contra una aplicación ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
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
uid: security/preventing-open-redirects
ms.openlocfilehash: 5226e301960a56145b94b6128d0034c40b86bffd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633466"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>Prevención de ataques de redireccionamiento abiertos en ASP.NET Core

Una aplicación web que redirige a una dirección URL que se especifica a través de la solicitud, como la cadena de consulta o los datos del formulario, se pueden alterar para redirigir a los usuarios a una dirección URL externa y malintencionada. Esta manipulación se denomina ataque de redireccionamiento abierto.

Siempre que la lógica de la aplicación redirija a una dirección URL especificada, debe comprobar que la dirección URL de redireccionamiento no se ha alterado. ASP.NET Core tiene funcionalidad integrada para ayudar a proteger las aplicaciones frente a ataques de redireccionamiento abierto (también conocidos como redireccionamiento abierto).

## <a name="what-is-an-open-redirect-attack"></a>¿Qué es un ataque de redireccionamiento abierto?

Con frecuencia, las aplicaciones web redirigen a los usuarios a una página de inicio de sesión cuando acceden a recursos que requieren autenticación. Normalmente, el redireccionamiento incluye un `returnUrl` parámetro QueryString para que el usuario pueda volver a la dirección URL solicitada originalmente después de haber iniciado sesión correctamente. Una vez que el usuario se autentica, se le redirige a la dirección URL que se solicitó originalmente.

Dado que la dirección URL de destino se especifica en la cadena de consulta de la solicitud, un usuario malintencionado podría manipular la cadena de consulta. Una QueryString modificada podría permitir que el sitio redirija al usuario a un sitio externo malintencionado. Esta técnica se denomina ataque de redireccionamiento (o redireccionamiento) abierto.

### <a name="an-example-attack"></a>Un ataque de ejemplo

Un usuario malintencionado puede desarrollar un ataque diseñado para permitir el acceso de los usuarios malintencionados a las credenciales de un usuario o a la información confidencial. Para comenzar el ataque, el usuario malintencionado hace que el usuario haga clic en un vínculo a la página de inicio de sesión del sitio con un `returnUrl` valor QueryString agregado a la dirección URL. Por ejemplo, considere una aplicación en `contoso.com` que incluye una página de inicio de sesión en `http://contoso.com/Account/LogOn?returnUrl=/Home/About` . El ataque sigue estos pasos:

1. El usuario hace clic en un vínculo malintencionado a `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (la segunda dirección URL es "Contoso**1**. com", no "contoso.com").
2. El usuario inicia sesión correctamente.
3. El usuario es redirigido (por el sitio) a `http://contoso1.com/Account/LogOn` (un sitio malintencionado que se parece exactamente al sitio real).
4. El usuario vuelve a iniciar sesión (dando a un sitio malintencionado sus credenciales) y se redirige de nuevo al sitio real.

Lo más probable es que el usuario cree que se produjo un error en el primer intento de iniciar sesión y que el segundo intento es correcto. Lo más probable es que el usuario no tenga constancia de que sus credenciales se ven comprometidas.

![Proceso de ataque de redireccionamiento abierto](preventing-open-redirects/_static/open-redirection-attack-process.png)

Además de las páginas de inicio de sesión, algunos sitios proporcionan páginas o puntos de conexión de redireccionamiento. Imagine que la aplicación tiene una página con una redirección abierta, `/Home/Redirect` . Un atacante podría crear, por ejemplo, un vínculo en un correo electrónico que va a `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` . Un usuario típico examinará la dirección URL y verá que comienza con el nombre del sitio. Si confía en él, se hará clic en el vínculo. A continuación, el redireccionamiento abierto enviará el usuario al sitio de suplantación de identidad (phishing), que es idéntico al suyo y es probable que el usuario inicie sesión en lo que creemos que es su sitio.

## <a name="protecting-against-open-redirect-attacks"></a>Protección contra ataques de redireccionamiento abierto

Al desarrollar aplicaciones Web, trate todos los datos proporcionados por el usuario como no confiables. Si la aplicación tiene una funcionalidad que redirige al usuario en función del contenido de la dirección URL, asegúrese de que estas redirecciones solo se realizan localmente en la aplicación (o en una dirección URL conocida, no en ninguna dirección URL que se pueda proporcionar en la cadena de tipo).

### <a name="localredirect"></a>LocalRedirect

Use el `LocalRedirect` método auxiliar de la clase base `Controller` :

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` producirá una excepción si se especifica una dirección URL no local. De lo contrario, se comporta igual que el `Redirect` método.

### <a name="islocalurl"></a>IsLocalUrl

Use el método [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) para probar las direcciones URL antes de redirigir:

En el ejemplo siguiente se muestra cómo comprobar si una dirección URL es local antes de la redirección.

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

El `IsLocalUrl` método evita que los usuarios se redirijan accidentalmente a un sitio malintencionado. Puede registrar los detalles de la dirección URL que se proporcionó cuando se proporciona una dirección URL no local en una situación en la que esperaba una dirección URL local. El registro de direcciones URL de redireccionamiento puede ayudar a diagnosticar los ataques de redirección.
