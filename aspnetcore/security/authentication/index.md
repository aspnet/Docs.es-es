---
title: Información general sobre la autenticación de ASP.NET Core
author: mjrousos
description: Obtenga más información sobre la autenticación en ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
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
uid: security/authentication/index
ms.openlocfilehash: e9e4ca11d20557666c75b84e56af825d002df0f1
ms.sourcegitcommit: fbd5427293d9ecccc388bd5fd305c2eb8ada7281
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94464008"
---
# <a name="overview-of-aspnet-core-authentication"></a>Información general sobre la autenticación de ASP.NET Core

Por [Mike Rousos](https://github.com/mjrousos)

La autenticación es el proceso de determinar la identidad de un usuario. Por su parte, la [autorización](xref:security/authorization/introduction) consiste en determinar si un usuario tiene acceso a un recurso. En ASP.NET Core, la autenticación se controla mediante `IAuthenticationService`, elemento que el [middleware](xref:fundamentals/middleware/index) de autenticación emplea para conseguirlo. El servicio de autenticación usa controladores de autenticación registrados para completar las acciones relacionadas con la autenticación. Estos son algunos ejemplos de acciones relacionadas con la autenticación:

* Autenticación de un usuario
* Respuesta si un usuario no autenticado intenta acceder a un recurso restringido

Los controladores de autenticación registrados y sus opciones de configuración se denominan "esquemas".

Para especificar esquemas de autenticación, es necesario registrar servicios de autenticación en `Startup.ConfigureServices`:

* Mediante una llamada a un método de extensión específico del esquema tras una llamada a `services.AddAuthentication` (por ejemplo, `AddJwtBearer` o `AddCookie`). Estos métodos de extensión usan [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) para registrar esquemas con la configuración adecuada.
* Con menos frecuencia, mediante una llamada directa a [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*).

Por ejemplo, el código siguiente registra los servicios de autenticación y los controladores para los esquemas de autenticación de portador de JWT y de cookie:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

El parámetro `JwtBearerDefaults.AuthenticationScheme` de `AddAuthentication` es el nombre del esquema que se usará de forma predeterminada si no se solicita un esquema específico.

Si se usan varios esquemas, las directivas de autorización (o los atributos de autorización) pueden [especificar el esquema (o esquemas) de autenticación](xref:security/authorization/limitingidentitybyscheme) del que dependen para autenticar al usuario. En el ejemplo anterior, se podría especificar el nombre del esquema de autenticación de cookie (`CookieAuthenticationDefaults.AuthenticationScheme` de forma predeterminada, aunque se podría proporcionar otro nombre al llamar a `AddCookie`).

En algunos casos, la llamada a `AddAuthentication` se realiza automáticamente mediante otros métodos de extensión. Por ejemplo, al usar [ASP.NET Core Identity](xref:security/authentication/identity), se llama a `AddAuthentication` de manera interna.

Para agregar el middleware de autenticación en `Startup.Configure`, se llama al método de extensión <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> en el elemento `IApplicationBuilder` de la aplicación. La llamada a `UseAuthentication` registra el middleware que usa los esquemas de autenticación previamente registrados. Llame a `UseAuthentication` antes de registrar cualquier middleware que dependa de que los usuarios se autentiquen. Al usar el enrutamiento de punto de conexión, la llamada a `UseAuthentication` debe ir:

* Después de `UseRouting`, para que la información de ruta esté disponible para las decisiones de autenticación.
* Antes de `UseEndpoints`, para que los usuarios se autentiquen como paso previo para tener acceso a los puntos de conexión.

## <a name="authentication-concepts"></a>Conceptos sobre la autenticación

### <a name="authentication-scheme"></a>Esquema de autenticación

Un esquema de autenticación es un nombre que corresponde a:

* Un controlador de autenticación.
* Opciones para configurar esa instancia específica del controlador.

Los esquemas son útiles como mecanismo para hacer referencia a los comportamientos de autenticación, desafío y prohibición del controlador asociado. Por ejemplo, una directiva de autorización puede usar nombres de esquemas para especificar qué esquema (o esquemas) de autenticación conviene usar para autenticar al usuario. Al configurar la autenticación, es habitual especificar un esquema de autenticación predeterminado. A menos que un recurso solicite un esquema específico, se usará el predeterminado. También es posible:

* Especificar distintos esquemas predeterminados que se usarán para las acciones de autenticación, desafío y prohibición.
* Combinar varios esquemas en uno mediante [esquemas de directiva](xref:security/authentication/policyschemes).

### <a name="authentication-handler"></a>Controlador de autenticación

Un controlador de autenticación:

* Es un tipo que implementa el comportamiento de un esquema.
* Se deriva de <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> o <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.
* Tiene la responsabilidad principal de autenticar a los usuarios.

Según la configuración del esquema de autenticación y el contexto de la solicitud entrante, los controladores de autenticación:

* Construyen objetos <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> que representan la identidad del usuario si la autenticación se realiza correctamente.
* Devuelven "sin resultados" o "error" si la autenticación no es correcta.
* Tienen métodos para las acciones de desafío y prohibición para los casos en los que los usuarios intenten acceder a los recursos:
  * No están autorizados a tener acceso (prohibición).
  * Cuando no están autenticados (desafío).

### <a name="authenticate"></a>Authenticate

La acción de autenticación de un esquema de autenticación es la responsable de construir la identidad del usuario basándose en el contexto de la solicitud. Devuelve <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult>, que indica si la autenticación se realizó correctamente y, en caso afirmativo, la identidad del usuario en un vale de autenticación. Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Ejemplos de autenticación:

* Un esquema de autenticación de cookie que crea la identidad del usuario a partir de las cookies.
* Un esquema portador JWT que deserializa y valida un token de portador JWT para construir la identidad del usuario.

### <a name="challenge"></a>Desafío

La autorización invoca un desafío de autenticación si un usuario no autenticado solicita un punto de conexión que requiere autenticación. Se emite un desafío de autenticación si, por ejemplo, un usuario anónimo solicita un recurso restringido o hace clic en un vínculo de inicio de sesión. La autorización invoca un desafío con los esquemas de autenticación especificados o con el valor predeterminado, si no se especifica ningún esquema. Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Ejemplos de desafío de autenticación:

* Un esquema de autenticación de cookie que redirige al usuario a una página de inicio de sesión.
* Un esquema portador JWT que devuelve un resultado 401 con un encabezado `www-authenticate: bearer`.

Una acción de desafío debe permitir que el usuario sepa qué mecanismo de autenticación hay que emplear para tener acceso al recurso solicitado.

### <a name="forbid"></a>Prohibición

La autorización llama a una acción de prohibición del esquema de autenticación cuando un usuario autenticado intenta tener acceso a un recurso para el que no tiene permiso de acceso. Vea <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. Ejemplos de prohibición de autenticación:
* Un esquema de autenticación de cookie que redirige al usuario a una página que indica que se ha prohibido el acceso.
* Un esquema portador JWT que devuelve un resultado 403.
* Un esquema de autenticación personalizado que redirige a una página en la que el usuario puede solicitar acceso al recurso.

Una acción de prohibición puede permitir que los usuarios sepan que:

* La autenticación se ha realizado correctamente.
* No se les permite el acceso al recurso solicitado.

Consulte los vínculos siguientes para conocer las diferencias entre desafío y prohibición:

* [Desafío y prohibido con un controlador de recursos operativos](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)
* [Diferencias entre desafío y prohibido](xref:security/authorization/secure-data#challenge)

## <a name="authentication-providers-per-tenant"></a>Proveedores de autenticación por inquilino

El marco ASP.NET Core no tiene una solución integrada para la autenticación de varios inquilinos.
Aunque es ciertamente posible que los clientes escriban uno, con las características integradas, se recomienda a los clientes que busquen en [Orchard Core](https://www.orchardcore.net/) con este fin.

Orchard Core es:

* Un marco de aplicaciones multiinquilino y modular de código abierto creado con ASP.NET Core.
* Un sistema de administración de contenido (CMS) basado en el marco de la aplicación.

Visite [Orchard Core](https://github.com/OrchardCMS/OrchardCore) para ver un ejemplo de proveedores de autenticación por inquilino.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [Requerir usuarios autenticados globalmente](xref:security/authorization/secure-data#rau)
* [Problema de GitHub sobre el uso de varios esquemas de autenticación](https://github.com/dotnet/aspnetcore/issues/26002)
