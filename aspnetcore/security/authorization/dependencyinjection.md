---
title: Inserción de dependencias en controladores de requisitos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo insertar controladores de requisitos de autorización en una aplicación ASP.NET Core mediante la inserción de dependencias.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060266"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="b7cef-103">Inserción de dependencias en controladores de requisitos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7cef-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="b7cef-104">Los [controladores de autorización deben registrarse](xref:security/authorization/policies#handler-registration) en la colección de servicios durante la configuración mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b7cef-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration using [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="b7cef-105">Supongamos que tiene un repositorio de reglas que desea evaluar dentro de un controlador de autorización y que ese repositorio se registró en la colección de servicios.</span><span class="sxs-lookup"><span data-stu-id="b7cef-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="b7cef-106">La autorización resuelve e inserta en el constructor.</span><span class="sxs-lookup"><span data-stu-id="b7cef-106">Authorization resolves and injects that into the constructor.</span></span>

<span data-ttu-id="b7cef-107">Por ejemplo, para utilizar ASP. Infraestructura de registro de la red, inserte `ILoggerFactory` en el controlador.</span><span class="sxs-lookup"><span data-stu-id="b7cef-107">For example, to use ASP.NET's logging infrastructure, inject `ILoggerFactory` into the handler.</span></span> <span data-ttu-id="b7cef-108">Este tipo de controlador podría ser similar al código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b7cef-108">Such a handler might look like the following code:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="b7cef-109">El controlador anterior se puede registrar con cualquier [duración del servicio](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="b7cef-109">The preceding handler can be registered with any [service lifetime](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="b7cef-110">El código siguiente usa `AddSingleton` para registrar el controlador anterior:</span><span class="sxs-lookup"><span data-stu-id="b7cef-110">The following code uses `AddSingleton` to register the preceding handler:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="b7cef-111">Cuando se inicia la aplicación, se crea una instancia del controlador y DI inserta la registrada `ILoggerFactory` en el constructor.</span><span class="sxs-lookup"><span data-stu-id="b7cef-111">An instance of the handler is created when the app starts, and DI injects the registered `ILoggerFactory` into the constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="b7cef-112">Los controladores que usan Entity Framework no deben registrarse como singletons.</span><span class="sxs-lookup"><span data-stu-id="b7cef-112">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
