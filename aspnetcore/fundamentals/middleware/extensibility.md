---
title: Activación de middleware basada en Factory en ASP.NET Core
author: rick-anderson
description: Aprenda a usar middleware fuertemente tipado con la implementación de una activación basada en Factory en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 893dce119328acaa4ffd3087b94328017f5915ca
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399912"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>Activación de middleware basada en Factory en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> es un punto de extensibilidad para la activación de [middleware](xref:fundamentals/middleware/index).

Los métodos de extensión <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> comprueban si un tipo registrado de middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>. Si es así, la instancia <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrada en el contenedor se usa para resolver la implementación <xref:Microsoft.AspNetCore.Http.IMiddleware> en lugar de usar la lógica de activación de middleware basado en convenciones. El middleware se registra como un [servicio con ámbito o transitorio](xref:fundamentals/dependency-injection#service-lifetimes) en el contenedor de servicios de la aplicación.

Ventajas:

* Activación por solicitud de cliente (inyección de servicios con ámbito)
* Tipado fuerte de middleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> se activa por solicitud de cliente (conexión), por lo que los servicios con ámbito se pueden insertar en el constructor del middleware.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> define el middleware para la canalización de solicitudes de la aplicación. El método [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) controla las solicitudes y devuelve una <xref:System.Threading.Tasks.Task> que representa la ejecución del middleware.

Middleware activado por convención:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware activado por <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Las extensiones se crean para los middlewares:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

No se pueden pasar objetos al middleware activado por Factory con <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

El middleware activado por Factory se agrega al contenedor integrado en `Startup.ConfigureServices`:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Ambos middlewares se registran en la canalización de procesamiento de solicitudes en `Startup.Configure`:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> proporciona métodos para crear middleware. La implementación de Middleware Factory se registra en el contenedor como un servicio con ámbito.

La implementación <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> predeterminada, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, se encuentra en el paquete [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> es un punto de extensibilidad para la activación de [middleware](xref:fundamentals/middleware/index).

Los métodos de extensión <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> comprueban si un tipo registrado de middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>. Si es así, la instancia <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrada en el contenedor se usa para resolver la implementación <xref:Microsoft.AspNetCore.Http.IMiddleware> en lugar de usar la lógica de activación de middleware basado en convenciones. El middleware se registra como un [servicio con ámbito o transitorio](xref:fundamentals/dependency-injection#service-lifetimes) en el contenedor de servicios de la aplicación.

Ventajas:

* Activación por solicitud de cliente (inyección de servicios con ámbito)
* Tipado fuerte de middleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> se activa por solicitud de cliente (conexión), por lo que los servicios con ámbito se pueden insertar en el constructor del middleware.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> define el middleware para la canalización de solicitudes de la aplicación. El método [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) controla las solicitudes y devuelve una <xref:System.Threading.Tasks.Task> que representa la ejecución del middleware.

Middleware activado por convención:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware activado por <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Las extensiones se crean para los middlewares:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

No se pueden pasar objetos al middleware activado por Factory con <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

El middleware activado por Factory se agrega al contenedor integrado en `Startup.ConfigureServices`:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Ambos middlewares se registran en la canalización de procesamiento de solicitudes en `Startup.Configure`:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> proporciona métodos para crear middleware. La implementación de Middleware Factory se registra en el contenedor como un servicio con ámbito.

La implementación <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> predeterminada, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, se encuentra en el paquete [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
