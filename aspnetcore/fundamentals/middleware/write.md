---
title: Escritura de middleware de ASP.NET Core personalizado
author: rick-anderson
description: Obtenga información sobre cómo escribir middleware de ASP.NET Core personalizado.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/18/2020
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
uid: fundamentals/middleware/write
ms.openlocfilehash: 52985917c34ebf007c0d205625956c772456ee2b
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635260"
---
# <a name="write-custom-aspnet-core-middleware"></a>Escritura de middleware de ASP.NET Core personalizado

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Steve Smith](https://ardalis.com/)

El software intermedio es un software que se ensambla en una canalización de una aplicación para controlar las solicitudes y las respuestas. ASP.NET Core proporciona un completo conjunto de componentes de middleware integrados, pero en algunos escenarios es posible que quiera escribir middleware personalizado.

> [!NOTE]
> En este tema se describe cómo escribir middleware *basado en convenciones*. Para ver un enfoque que emplea el establecimiento de tipos seguros y la activación por solicitud, consulte <xref:fundamentals/middleware/extensibility>.

## <a name="middleware-class"></a>Clase de middleware

El middleware normalmente está encapsulado en una clase y se expone con un método de extensión. Use el siguiente software intermedio a modo de ejemplo. En este se establece la referencia cultural de la solicitud actual a partir de la cadena de solicitud:

[!code-csharp[](write/snapshot/StartupCulture.cs)]

El código de ejemplo anterior se usa para mostrar la creación de un componente de software intermedio. Para obtener más información sobre la compatibilidad con la localización integrada de ASP.NET Core, vea <xref:fundamentals/localization>.

Pruebe el middleware pasando la referencia cultural. Por ejemplo, solicite `https://localhost:5001/?culture=no`.

El código siguiente mueve el delegado de middleware a una clase:

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

La clase de middleware debe incluir:

* Un constructor público con un parámetro de tipo <xref:Microsoft.AspNetCore.Http.RequestDelegate>.
* Un método público llamado `Invoke` o `InvokeAsync`. Este método debe:
  * Devolver `Task`.
  * Aceptar un primer parámetro de tipo <xref:Microsoft.AspNetCore.Http.HttpContext>.
  
Los parámetros adicionales para el constructor y `Invoke`/`InvokeAsync` se rellenan mediante la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).

## <a name="middleware-dependencies"></a>Dependencias de middleware

El middleware debería seguir el [principio de dependencias explicitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) mediante la exposición de sus dependencias en el constructor. El middleware se construye una vez por *duración de la aplicación*. Si necesita compartir servicios con software intermedio en una solicitud, vea la sección [Dependencias de middleware bajo solicitud](#per-request-middleware-dependencies).

Los componentes de software intermedio pueden resolver sus dependencias de una [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) mediante parámetros del constructor. [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) también puede aceptar parámetros adicionales directamente.

## <a name="per-request-middleware-dependencies"></a>Dependencias de middleware bajo solicitud

Dado que el software intermedio se construye al inicio de la aplicación y no bajo solicitud, los servicios de duración *con ámbito* que usan los constructores de software intermedio no se comparten con otros tipos insertados mediante dependencias durante cada solicitud. Si debe compartir un servicio *con ámbito* entre su middleware y otros tipos, agregue esos servicios a la signatura del método `Invoke`. El método `Invoke` puede aceptar parámetros adicionales que la inserción de dependencias propaga:

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

Las [opciones de registro y duración](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contienen un ejemplo completo de middleware con servicios de duración *con ámbito*.

## <a name="middleware-extension-method"></a>Método de extensión de middleware

El método de extensión siguiente expone el software intermedio mediante <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

El código siguiente llama al middleware desde `Startup.Configure`:

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>Recursos adicionales

* Las [opciones de registro y duración](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contienen un ejemplo completo de middleware con servicios de duración *con ámbito*, *transitorios*y *singleton*.
* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
