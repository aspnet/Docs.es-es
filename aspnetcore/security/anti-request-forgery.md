---
title: Prevención de ataques de falsificación de solicitud entre sitios (XSRF/CSRF) en ASP.NET Core
author: steve-smith
description: Descubra cómo evitar ataques contra aplicaciones web en las que un sitio Web malintencionado puede influir en la interacción entre un explorador cliente y la aplicación.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 12/05/2019
no-loc:
- appsettings.json
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
uid: security/anti-request-forgery
ms.openlocfilehash: 3bb3c059eafa8e948fe2e719207927c009902e59
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057452"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Prevención de ataques de falsificación de solicitud entre sitios (XSRF/CSRF) en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)y [Steve Smith](https://ardalis.com/)

La falsificación de solicitudes entre sitios (también conocida como XSRF o CSRF) es un ataque contra aplicaciones hospedadas en Web, por lo que una aplicación web malintencionada puede influir en la interacción entre un explorador cliente y una aplicación web que confía en ese explorador. Estos ataques son posibles porque los exploradores Web envían algunos tipos de tokens de autenticación automáticamente con cada solicitud a un sitio Web. Este tipo de ataque también se conoce como un *ataque de un solo clic* o una *sesión* que se está iniciando porque el ataque aprovecha la sesión autenticada previamente del usuario.

Un ejemplo de un ataque CSRF:

1. Un usuario inicia sesión en `www.good-banking-site.com` mediante la autenticación de formularios. El servidor autentica al usuario y emite una respuesta que incluye una autenticación cookie . El sitio es vulnerable a ataques porque confía en cualquier solicitud que reciba con una autenticación válida cookie .
1. El usuario visita un sitio malintencionado, `www.bad-crook-site.com` .

   El sitio malintencionado, `www.bad-crook-site.com` , contiene un formulario HTML similar al siguiente:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Tenga en cuenta que las `action` entradas del formulario se publican en el sitio vulnerable, no en el sitio malintencionado. Esta es la parte "entre sitios" de CSRF.

1. El usuario selecciona el botón Enviar. El explorador realiza la solicitud e incluye automáticamente la autenticación cookie para el dominio solicitado, `www.good-banking-site.com` .
1. La solicitud se ejecuta en el `www.good-banking-site.com` servidor con el contexto de autenticación del usuario y puede realizar cualquier acción que pueda realizar un usuario autenticado.

Además del escenario en el que el usuario selecciona el botón para enviar el formulario, el sitio malintencionado podría:

* Ejecute un script que envíe el formulario automáticamente.
* Envíe el envío del formulario como una solicitud AJAX.
* Oculte el formulario mediante CSS.

Estos escenarios alternativos no requieren ninguna acción o entrada del usuario que no sea la primera vez que visitan el sitio malintencionado.

El uso de HTTPS no impide un ataque CSRF. El sitio malintencionado puede enviar una `https://www.good-banking-site.com/` solicitud con la misma facilidad con la que puede enviar una solicitud no segura.

Algunos ataques apuntan a los puntos de conexión que responden a las solicitudes GET, en cuyo caso se puede usar una etiqueta de imagen para realizar la acción. Esta forma de ataque es habitual en sitios de foros que permiten imágenes pero bloquean JavaScript. Las aplicaciones que cambian el estado en las solicitudes GET, donde se modifican las variables o los recursos, son vulnerables a ataques malintencionados. **Las solicitudes GET que cambian el estado son inseguras. Un procedimiento recomendado es no cambiar nunca el estado de una solicitud GET.**

Los ataques CSRF son posibles en las aplicaciones web que usan cookie para la autenticación porque:

* Almacenes de exploradores cookie emitidos por una aplicación Web.
* cookieLos s almacenados incluyen sesiones cookie para los usuarios autenticados.
* Los exploradores envían todos los cookie s asociados a un dominio a la aplicación web cada solicitud, con independencia de cómo se haya generado la solicitud a la aplicación en el explorador.

Sin embargo, los ataques CSRF no se limitan a la explotación de cookie . Por ejemplo, la autenticación básica e implícita también son vulnerables. Después de que un usuario inicie sesión con la autenticación básica o implícita, el explorador enviará automáticamente las credenciales hasta que finalice la sesión &dagger; .

&dagger;En este contexto, la *sesión* hace referencia a la sesión del lado cliente durante la que se autentica el usuario. No está relacionado con las sesiones del lado servidor o con el [middleware de sesión ASP.net Core](xref:fundamentals/app-state).

Los usuarios pueden proteger contra las vulnerabilidades de CSRF tomando precauciones:

* Cierre la sesión de las aplicaciones web cuando termine de usarlas.
* Desactive exploradores cookie periódicamente.

Sin embargo, las vulnerabilidades CSRF son fundamentalmente un problema con la aplicación Web, no con el usuario final.

## <a name="authentication-fundamentals"></a>Aspectos básicos de la autenticación

Cookiela autenticación basada en es una forma popular de autenticación. Los sistemas de autenticación basados en tokens están creciendo en popularidad, especialmente en el caso de las aplicaciones de una sola página (Spa).

### <a name="no-loccookie-based-authentication"></a>Cookieautenticación basada en

Cuando un usuario se autentica con su nombre de usuario y contraseña, se le emite un token que contiene un vale de autenticación que se puede usar para la autenticación y la autorización. El token se almacena como un cookie que acompaña a cada solicitud que el cliente realiza. La generación y validación cookie se realiza mediante el Cookie middleware de autenticación. El [middleware](xref:fundamentals/middleware/index) serializa una entidad de seguridad de usuario en un cifrado cookie . En las solicitudes posteriores, el middleware valida cookie , vuelve a crear la entidad de seguridad y asigna la entidad de seguridad a la propiedad [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) de [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).

### <a name="token-based-authentication"></a>Autenticación basada en tokens

Cuando un usuario se autentica, se le emite un token (no un token antifalsificación). El token contiene información de usuario en forma de [notificaciones](/dotnet/framework/security/claims-based-identity-model) o un token de referencia que apunta a la aplicación para que el estado de usuario se mantenga en la aplicación. Cuando un usuario intenta acceder a un recurso que requiere autenticación, el token se envía a la aplicación con un encabezado de autorización adicional en forma de token de portador. Esto hace que la aplicación no tenga estado. En cada solicitud subsiguiente, el token se pasa en la solicitud de validación del lado servidor. Este token no está *cifrado*; está *codificado*. En el servidor, el token se descodifica para tener acceso a su información. Para enviar el token en solicitudes posteriores, almacene el token en el almacenamiento local del explorador. No se preocupe de la vulnerabilidad de CSRF si el token se almacena en el almacenamiento local del explorador. CSRF es un problema cuando el token se almacena en cookie . Para obtener más información, consulte el ejemplo de código de GitHub problema [Spa agrega dos cookie s](https://github.com/dotnet/AspNetCore.Docs/issues/13369).

### <a name="multiple-apps-hosted-at-one-domain"></a>Varias aplicaciones hospedadas en un dominio

Los entornos de hospedaje compartidos son vulnerables al secuestro de sesión, el inicio de sesión CSRF y otros ataques.

Aunque `example1.contoso.net` y `example2.contoso.net` son hosts diferentes, hay una relación de confianza implícita entre los hosts del `*.contoso.net` dominio. Esta relación de confianza implícita permite que los hosts potencialmente que no son de confianza afecten a cookie los s (las directivas del mismo origen que rigen las solicitudes Ajax no se aplican necesariamente a http cookie s).

Los ataques que aprovechan cookie los elementos de confianza entre las aplicaciones hospedadas en el mismo dominio se pueden evitar al no compartir dominios. Cuando cada aplicación se hospeda en su propio dominio, no hay ninguna cookie relación de confianza implícita para la explotación.

## <a name="aspnet-core-antiforgery-configuration"></a>Configuración de ASP.NET Core antifalsificación

> [!WARNING]
> ASP.NET Core implementa antifalsificación mediante la [protección de datos de ASP.net Core](xref:security/data-protection/introduction). La pila de protección de datos debe estar configurada para funcionar en una granja de servidores. Consulte [configuración](xref:security/data-protection/configuration/overview) de la protección de datos para obtener más información.

::: moniker range=">= aspnetcore-3.0"

El middleware antifalsificación se agrega al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) cuando se llama a una de las siguientes API en `Startup.ConfigureServices` :

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

El middleware antifalsificación se agrega al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) cuando <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> se llama a en. `Startup.ConfigureServices`

::: moniker-end

En ASP.NET Core 2,0 o posterior, [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) inserta tokens antifalsificación en elementos de formulario HTML. El siguiente marcado en un Razor archivo genera automáticamente tokens antifalsificación:

```cshtml
<form method="post">
    ...
</form>
```

De forma similar, [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) genera tokens antifalsificación de forma predeterminada si el método del formulario no se obtiene.

La generación automática de tokens antifalsificación para elementos de formulario HTML se produce cuando la `<form>` etiqueta contiene el `method="post"` atributo y se cumple cualquiera de las siguientes condiciones:

* El atributo de acción está vacío ( `action=""` ).
* No se ha proporcionado el atributo de acción ( `<form method="post">` ).

Se puede deshabilitar la generación automática de tokens antifalsificación para elementos de formulario HTML:

* Deshabilite explícitamente los tokens antifalsificación con el `asp-antiforgery` atributo:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* El elemento de formulario no participa en las aplicaciones auxiliares de etiquetas mediante el uso de la aplicación auxiliar de etiquetas [. símbolo de cancelación](xref:mvc/views/tag-helpers/intro#opt-out):

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* Quite `FormTagHelper` de la vista. `FormTagHelper`Se puede quitar de una vista agregando la siguiente directiva a la Razor vista:

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [ Razor Las páginas](xref:razor-pages/index) se protegen automáticamente de XSRF/CSRF. Para obtener más información, consulte [XSRF/CSRF y Razor pages](xref:razor-pages/index#xsrf).

El enfoque más común para defenderse contra ataques CSRF es usar el *patrón de token de sincronizador* (STP). STP se usa cuando el usuario solicita una página con datos de formulario:

1. El servidor envía al cliente un token asociado a la identidad del usuario actual.
1. El cliente devuelve el token al servidor para su comprobación.
1. Si el servidor recibe un token que no coincide con la identidad del usuario autenticado, se rechaza la solicitud.

El token es único e impredecible. El token también se puede usar para garantizar la secuenciación correcta de una serie de solicitudes (por ejemplo, asegurándose de que la secuencia de solicitud de: Página 1 > página 2 > página 3). Todas las formas de ASP.NET Core plantillas MVC y Razor pages generan tokens antifalsificación. En el siguiente par de ejemplos de vista se generan tokens antifalsificación:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

Agregue explícitamente un token antifalsificación a un `<form>` elemento sin usar aplicaciones auxiliares de etiquetas con la aplicación auxiliar HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

En cada uno de los casos anteriores, ASP.NET Core agrega un campo de formulario oculto similar al siguiente:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core incluye tres [filtros](xref:mvc/controllers/filters) para trabajar con tokens antifalsificación:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Opciones antifalsificación

Personalizar [las opciones antifalsificación](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) en `Startup.ConfigureServices` :

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

&dagger;Establezca las propiedades antifalsificación `Cookie` mediante las propiedades de la clase de [ Cookie generador](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .

| Opción | Descripción |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina la configuración utilizada para crear la antifalsificación cookie . |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nombre del campo de formulario oculto utilizado por el sistema antifalsificación para representar los tokens antifalsificación en las vistas. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nombre del encabezado que usa el sistema antifalsificación. Si es `null` , el sistema solo tiene en cuenta los datos de formulario. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Especifica si se va a suprimir la generación del `X-Frame-Options` encabezado. De forma predeterminada, el encabezado se genera con un valor de "SAMEORIGIN". Tiene como valor predeterminado `false`. |

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

| Opción | Descripción |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina la configuración utilizada para crear la antifalsificación cookie . |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Dominio de cookie . Tiene como valor predeterminado `null`. Esta propiedad está obsoleta y se quitará en una versión futura. La alternativa recomendada es Cookie . Dominio. |
| [CookieNombre](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Nombre del objeto cookie. Si no se establece, el sistema genera un nombre único que empieza con el [ Cookie prefijo predeterminado](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. antifalsificación. "). Esta propiedad está obsoleta y se quitará en una versión futura. La alternativa recomendada es Cookie . Name. |
| [CookieRuta de acceso](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | La ruta de acceso establecida en cookie . Esta propiedad está obsoleta y se quitará en una versión futura. La alternativa recomendada es Cookie . Camino. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nombre del campo de formulario oculto utilizado por el sistema antifalsificación para representar los tokens antifalsificación en las vistas. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nombre del encabezado que usa el sistema antifalsificación. Si es `null` , el sistema solo tiene en cuenta los datos de formulario. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Especifica si el sistema antifalsificación requiere HTTPS. Si `true` es, se produce un error en las solicitudes que no son https. Tiene como valor predeterminado `false`. Esta propiedad está obsoleta y se quitará en una versión futura. La alternativa recomendada es establecer Cookie . SecurePolicy. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Especifica si se va a suprimir la generación del `X-Frame-Options` encabezado. De forma predeterminada, el encabezado se genera con un valor de "SAMEORIGIN". Tiene como valor predeterminado `false`. |

::: moniker-end

Para obtener más información, vea [ Cookie AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Configuración de características antifalsificación con IAntiforgery

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) proporciona la API para configurar las características antifalsificación. `IAntiforgery` se puede solicitar en el `Configure` método de la `Startup` clase. En el ejemplo siguiente se usa middleware desde la Página principal de la aplicación para generar un token antifalsificación y enviarlo en la respuesta como cookie (mediante la Convención de nomenclatura predeterminada de angular que se describe más adelante en este tema):

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

### <a name="require-antiforgery-validation"></a>Requerir validación antifalsificación

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) es un filtro de acción que se puede aplicar a una acción individual, un controlador o globalmente. Las solicitudes realizadas a acciones que tienen aplicado este filtro se bloquean a menos que la solicitud incluya un token antifalsificación válido.

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

El `ValidateAntiForgeryToken` atributo requiere un token para las solicitudes a los métodos de acción que marca, incluidas las solicitudes HTTP GET. Si el `ValidateAntiForgeryToken` atributo se aplica a los controladores de la aplicación, se puede invalidar con el `IgnoreAntiforgeryToken` atributo.

> [!NOTE]
> ASP.NET Core no admite la adición de tokens antifalsificación para obtener solicitudes automáticamente.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Validar automáticamente tokens antifalsificación para métodos HTTP no seguros únicamente

ASP.NET Core aplicaciones no generan tokens antifalsificación para métodos HTTP seguros (GET, HEAD, OPTIONs y TRACE). En lugar de aplicar ampliamente el `ValidateAntiForgeryToken` atributo y, a continuación, reemplazarlo por `IgnoreAntiforgeryToken` atributos, se puede usar el atributo [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) . Este atributo funciona exactamente igual que el `ValidateAntiForgeryToken` atributo, con la salvedad de que no requiere tokens para las solicitudes realizadas mediante los siguientes métodos http:

* GET
* HEAD
* OPCIONES
* TRACE

Se recomienda usar `AutoValidateAntiforgeryToken` en general para escenarios que no son de API. Esto garantiza que las acciones POST están protegidas de forma predeterminada. La alternativa es omitir los tokens antifalsificación de forma predeterminada, a menos que `ValidateAntiForgeryToken` se aplique a los métodos de acción individuales. Es más probable que en este escenario un método POST de acción se deje sin protección por error, lo que deja la aplicación vulnerable a ataques CSRF. Todas las publicaciones deben enviar el token antifalsificación.

Las API no tienen un mecanismo automático para enviar el token que no cookie forma parte del token. La implementación probablemente depende de la implementación del código de cliente. A continuación se muestran algunos ejemplos:

Ejemplo de nivel de clase:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Ejemplo global:

::: moniker range="< aspnetcore-3.0"

Server. AddMvc (Options = opciones de>. Filters. Add (New AutoValidateAntiforgeryTokenAttribute ()));

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a>Reemplazar atributos globales o de antifalsificación de controlador

El filtro [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) se usa para eliminar la necesidad de un token antifalsificación para una acción o un controlador determinados. Cuando se aplica, este filtro reemplaza los `ValidateAntiForgeryToken` `AutoValidateAntiforgeryToken` filtros especificados en un nivel superior (globalmente o en un controlador).

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

## <a name="refresh-tokens-after-authentication"></a>Actualizar tokens después de la autenticación

Los tokens deben actualizarse después de que el usuario se autentique redirigiendo el usuario a una vista o Razor Página de páginas.

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX y Spa

En las aplicaciones tradicionales basadas en HTML, los tokens antifalsificación se pasan al servidor mediante campos de formulario ocultos. En las aplicaciones modernas basadas en JavaScript y Spa, muchas solicitudes se realizan mediante programación. Estas solicitudes AJAX pueden usar otras técnicas (como encabezados de solicitud o cookie ) para enviar el token.

Si cookie se usan para almacenar tokens de autenticación y autenticar las solicitudes de API en el servidor, CSRF es un problema potencial. Si se usa el almacenamiento local para almacenar el token, la vulnerabilidad de CSRF podría mitigarse porque los valores del almacenamiento local no se envían automáticamente al servidor con cada solicitud. Por lo tanto, el uso del almacenamiento local para almacenar el token antifalsificación en el cliente y el envío del token como un encabezado de solicitud es un enfoque recomendado.

### <a name="javascript"></a>JavaScript

Con JavaScript con vistas, el token se puede crear con un servicio de dentro de la vista. Inserte el servicio [Microsoft. AspNetCore. antifalsification. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) en la vista y llame a [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Este enfoque elimina la necesidad de tratar directamente con los valores cookie de configuración del servidor o de leerlos desde el cliente.

En el ejemplo anterior se usa JavaScript para leer el valor de campo oculto del encabezado POST de AJAX.

JavaScript también puede acceder a los tokens en cookie s y usar el cookie contenido de para crear un encabezado con el valor del token.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Suponiendo que las solicitudes de scripts envíen el token en un encabezado denominado `X-CSRF-TOKEN` , configure el servicio antifalsificación para buscar el `X-CSRF-TOKEN` encabezado:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

En el ejemplo siguiente se usa JavaScript para crear una solicitud AJAX con el encabezado adecuado:

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

### <a name="angularjs"></a>AngularJS

AngularJS usa una Convención para direccionar CSRF. Si el servidor envía un cookie con el nombre `XSRF-TOKEN` , el `$http` servicio AngularJS agrega el cookie valor a un encabezado cuando envía una solicitud al servidor. Este proceso es automático. No es necesario establecer explícitamente el encabezado en el cliente. El nombre del encabezado es `X-XSRF-TOKEN` . El servidor debe detectar este encabezado y validar su contenido.

Para que ASP.NET Core API funcione con esta Convención en el inicio de la aplicación:

* Configure la aplicación para proporcionar un token en un cookie llamado `XSRF-TOKEN` .
* Configure el servicio antifalsificación para buscar un encabezado denominado `X-XSRF-TOKEN` .

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

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="windows-authentication-and-antiforgery-no-loccookies"></a>Autenticación de Windows y antifalsificación cookie

Cuando se usa la autenticación de Windows, los puntos de conexión de la aplicación deben protegerse contra los ataques CSRF de la misma manera que para cookie s.  El explorador envía implícitamente el contexto de autenticación al servidor, por lo que los extremos deben protegerse frente a ataques CSRF.

## <a name="extend-antiforgery"></a>Extender antifalsificación

El tipo [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) permite a los desarrolladores ampliar el comportamiento del sistema anti-CSRF al realizar un recorrido de ida y vuelta por los datos adicionales de cada token. Se llama al método [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) cada vez que se genera un token de campo y el valor devuelto se incrusta en el token generado. Un implementador puede devolver una marca de tiempo, un valor de seguridad (nonce) o cualquier otro valor y, después, llamar a [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) para validar estos datos cuando se valida el token. El nombre de usuario del cliente ya está incrustado en los tokens generados, por lo que no es necesario incluir esta información. Si un token incluye datos complementarios pero no `IAntiForgeryAdditionalDataProvider` está configurado, los datos complementarios no se validan.

## <a name="additional-resources"></a>Recursos adicionales

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) en el [proyecto abierto de seguridad de aplicaciones web](https://www.owasp.org/index.php/Main_Page) (OWASP).
* <xref:host-and-deploy/web-farm>
