---
title: Inserción de dependencias en controladores de requisitos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo insertar controladores de requisitos de autorización en una aplicación ASP.NET Core mediante la inserción de dependencias.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: e58498cc7a28b598b919c5cab128249448bde32a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021008"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="edc8b-103">Inserción de dependencias en controladores de requisitos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="edc8b-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="edc8b-104">Los [controladores de autorización deben registrarse](xref:security/authorization/policies#handler-registration) en la colección de servicios durante la configuración (mediante la [inserción de dependencias](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="edc8b-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="edc8b-105">Supongamos que tiene un repositorio de reglas que desea evaluar dentro de un controlador de autorización y que ese repositorio se registró en la colección de servicios.</span><span class="sxs-lookup"><span data-stu-id="edc8b-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="edc8b-106">La autorización se resolverá y se insertará en el constructor.</span><span class="sxs-lookup"><span data-stu-id="edc8b-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="edc8b-107">Por ejemplo, si desea utilizar ASP. La infraestructura de registro de la red que se desea insertar `ILoggerFactory` en el controlador.</span><span class="sxs-lookup"><span data-stu-id="edc8b-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="edc8b-108">Este tipo de controlador podría ser similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="edc8b-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="edc8b-109">Registraría el controlador con `services.AddSingleton()` :</span><span class="sxs-lookup"><span data-stu-id="edc8b-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="edc8b-110">Cuando se inicia la aplicación, se creará una instancia del controlador y DI insertará el registrado `ILoggerFactory` en el constructor.</span><span class="sxs-lookup"><span data-stu-id="edc8b-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="edc8b-111">Los controladores que usan Entity Framework no deben registrarse como singletons.</span><span class="sxs-lookup"><span data-stu-id="edc8b-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
