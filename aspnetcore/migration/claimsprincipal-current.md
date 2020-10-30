---
title: Migrar desde ClaimsPrincipal. Current
author: mjrousos
description: Obtenga información sobre cómo migrar de ClaimsPrincipal. Current para recuperar la identidad del usuario autenticado actual y las notificaciones en ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
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
uid: migration/claimsprincipal-current
ms.openlocfilehash: 3aa0adb299789efbb071cdb934d43832a84cf540
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059772"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrar desde ClaimsPrincipal. Current

En los proyectos de ASP.NET 4. x, era habitual usar [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) para recuperar la identidad y las notificaciones del usuario autenticado actual. En ASP.NET Core, esta propiedad ya no se establece. El código que depende de él debe actualizarse para obtener la identidad del usuario autenticado actual a través de un medio diferente.

## <a name="context-specific-state-instead-of-static-state"></a>Estado específico del contexto en lugar de estado estático

Cuando se usa ASP.NET Core, los valores de `ClaimsPrincipal.Current` y `Thread.CurrentPrincipal` no están establecidos. Estas propiedades representan el estado estático, que normalmente evita ASP.NET Core. En su lugar, ASP.NET Core usa la [inserción de dependencias](xref:fundamentals/dependency-injection) (di) para proporcionar dependencias como la identidad del usuario actual. Obtener la identidad del usuario actual de DI es más fácil de probar, ya que las identidades de prueba se pueden insertar fácilmente.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Recuperar el usuario actual en una aplicación ASP.NET Core

Hay varias opciones para recuperar el usuario autenticado actual `ClaimsPrincipal` en ASP.net Core en lugar de `ClaimsPrincipal.Current` :

* **ControllerBase. usuario** . Los controladores MVC pueden tener acceso al usuario autenticado actual con su propiedad [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User** . Los componentes con acceso al actual `HttpContext` (por ejemplo, middleware) pueden obtener el del usuario actual `ClaimsPrincipal` de [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Pasado del llamador** . A menudo se llama a las bibliotecas que no tienen acceso al actual `HttpContext` desde los controladores o los componentes de middleware, y puede tener la identidad del usuario actual pasada como argumento.
* **IHttpContextAccessor** . El proyecto que se va a migrar a ASP.NET Core puede ser demasiado grande para pasar fácilmente la identidad del usuario actual a todas las ubicaciones necesarias. En tales casos, [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) se puede usar como solución alternativa. `IHttpContextAccessor` puede tener acceso al actual `HttpContext` (si existe). Si se usa DI, vea <xref:fundamentals/httpcontext> . Una solución a corto plazo para obtener la identidad del usuario actual en el código que todavía no se ha actualizado para funcionar con la arquitectura basada en DI de ASP.NET Core sería:

  * Haga que `IHttpContextAccessor` esté disponible en el contenedor de di llamando a [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) en `Startup.ConfigureServices` .
  * Obtiene una instancia de `IHttpContextAccessor` durante el inicio y la almacena en una variable estática. La instancia de se pone a disposición del código que antes recuperaba el usuario actual de una propiedad estática.
  * Recupere el usuario actual `ClaimsPrincipal` mediante `HttpContextAccessor.HttpContext?.User` . Si este código se usa fuera del contexto de una solicitud HTTP, el `HttpContext` valor de es NULL.

La última opción, el uso de una `IHttpContextAccessor` instancia almacenada en una variable estática, es contraria al principio de ASP.net Core de la preferencia de dependencias insertadas a dependencias estáticas. En su lugar, planee la recuperación `IHttpContextAccessor` de instancias de di. Sin embargo, una aplicación auxiliar estática puede ser un puente útil al migrar aplicaciones de ASP.NET grandes existentes que usan `ClaimsPrincipal.Current` .
