---
title: Activación de middleware con un contenedor de terceros en ASP.NET Core
author: rick-anderson
description: Aprenda a usar middleware fuertemente tipado con la implementación de una activación basada en Factory y un contenedor de terceros en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: d322b1b3dda161c9948359253c3f7fee64a1f9ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057770"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="44d32-103">Activación de middleware con un contenedor de terceros en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="44d32-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44d32-104">En este artículo se explica cómo usar <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> y <xref:Microsoft.AspNetCore.Http.IMiddleware> como un punto de extensibilidad para la activación de [middleware](xref:fundamentals/middleware/index) con un contenedor de terceros.</span><span class="sxs-lookup"><span data-stu-id="44d32-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="44d32-105">Para información de introducción sobre `IMiddlewareFactory` y `IMiddleware`, consulte <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="44d32-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="44d32-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="44d32-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="44d32-107">En la aplicación de ejemplo se muestra una activación de middleware por medio de una implementación de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="44d32-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="44d32-108">En el ejemplo se usa el contenedor de inserción de dependencias [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="44d32-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="44d32-109">La implementación de middleware del ejemplo registra el valor proporcionado por un parámetro de cadena de consulta (`key`).</span><span class="sxs-lookup"><span data-stu-id="44d32-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="44d32-110">El middleware usa un contexto de base de datos insertado (un servicio con ámbito) para registrar el valor de cadena de consulta en una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="44d32-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="44d32-111">En la aplicación de ejemplo se usa [Simple Injector](https://github.com/simpleinjector/SimpleInjector) única y exclusivamente con fines de demostración.</span><span class="sxs-lookup"><span data-stu-id="44d32-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="44d32-112">El uso de Simple Injector no está avalado.</span><span class="sxs-lookup"><span data-stu-id="44d32-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="44d32-113">Los métodos de activación de middleware descritos en la documentación de Simple Injector y los problemas de GitHub está recomendado por los responsables de Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="44d32-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="44d32-114">Para más información, vea la [documentación de Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) y el [repositorio de GitHub de Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="44d32-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="44d32-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="44d32-115">IMiddlewareFactory</span></span>

<span data-ttu-id="44d32-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> proporciona métodos para crear middleware.</span><span class="sxs-lookup"><span data-stu-id="44d32-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="44d32-117">En la aplicación de ejemplo, se implementa un Middleware Factory para crear una instancia de `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="44d32-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="44d32-118">Ese Middleware Factory usa el contenedor de Simple Injector para resolver el middleware:</span><span class="sxs-lookup"><span data-stu-id="44d32-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="44d32-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="44d32-119">IMiddleware</span></span>

<span data-ttu-id="44d32-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> define el middleware para la canalización de solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="44d32-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="44d32-121">Middleware activado por una implementación de `IMiddlewareFactory` ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span><span class="sxs-lookup"><span data-stu-id="44d32-121">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="44d32-122">Se crea una extensión para el middleware ( *Middleware/MiddlewareExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="44d32-122">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="44d32-123">`Startup.ConfigureServices` debe realizar varias tareas:</span><span class="sxs-lookup"><span data-stu-id="44d32-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="44d32-124">Configurar el contenedor de Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="44d32-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="44d32-125">Registrar tanto el Factory como el Middleware.</span><span class="sxs-lookup"><span data-stu-id="44d32-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="44d32-126">Poner disponible el contexto de base de datos de la aplicación en el contenedor de Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="44d32-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="44d32-127">El middleware se registra en la canalización de procesamiento de solicitudes en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="44d32-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="44d32-128">En este artículo se explica cómo usar <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> y <xref:Microsoft.AspNetCore.Http.IMiddleware> como un punto de extensibilidad para la activación de [middleware](xref:fundamentals/middleware/index) con un contenedor de terceros.</span><span class="sxs-lookup"><span data-stu-id="44d32-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="44d32-129">Para información de introducción sobre `IMiddlewareFactory` y `IMiddleware`, consulte <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="44d32-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="44d32-130">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="44d32-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="44d32-131">En la aplicación de ejemplo se muestra una activación de middleware por medio de una implementación de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="44d32-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="44d32-132">En el ejemplo se usa el contenedor de inserción de dependencias [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="44d32-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="44d32-133">La implementación de middleware del ejemplo registra el valor proporcionado por un parámetro de cadena de consulta (`key`).</span><span class="sxs-lookup"><span data-stu-id="44d32-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="44d32-134">El middleware usa un contexto de base de datos insertado (un servicio con ámbito) para registrar el valor de cadena de consulta en una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="44d32-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="44d32-135">En la aplicación de ejemplo se usa [Simple Injector](https://github.com/simpleinjector/SimpleInjector) única y exclusivamente con fines de demostración.</span><span class="sxs-lookup"><span data-stu-id="44d32-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="44d32-136">El uso de Simple Injector no está avalado.</span><span class="sxs-lookup"><span data-stu-id="44d32-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="44d32-137">Los métodos de activación de middleware descritos en la documentación de Simple Injector y los problemas de GitHub está recomendado por los responsables de Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="44d32-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="44d32-138">Para más información, vea la [documentación de Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) y el [repositorio de GitHub de Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="44d32-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="44d32-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="44d32-139">IMiddlewareFactory</span></span>

<span data-ttu-id="44d32-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> proporciona métodos para crear middleware.</span><span class="sxs-lookup"><span data-stu-id="44d32-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="44d32-141">En la aplicación de ejemplo, se implementa un Middleware Factory para crear una instancia de `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="44d32-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="44d32-142">Ese Middleware Factory usa el contenedor de Simple Injector para resolver el middleware:</span><span class="sxs-lookup"><span data-stu-id="44d32-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="44d32-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="44d32-143">IMiddleware</span></span>

<span data-ttu-id="44d32-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> define el middleware para la canalización de solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="44d32-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="44d32-145">Middleware activado por una implementación de `IMiddlewareFactory` ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span><span class="sxs-lookup"><span data-stu-id="44d32-145">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="44d32-146">Se crea una extensión para el middleware ( *Middleware/MiddlewareExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="44d32-146">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="44d32-147">`Startup.ConfigureServices` debe realizar varias tareas:</span><span class="sxs-lookup"><span data-stu-id="44d32-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="44d32-148">Configurar el contenedor de Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="44d32-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="44d32-149">Registrar tanto el Factory como el Middleware.</span><span class="sxs-lookup"><span data-stu-id="44d32-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="44d32-150">Poner disponible el contexto de base de datos de la aplicación en el contenedor de Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="44d32-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="44d32-151">El middleware se registra en la canalización de procesamiento de solicitudes en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="44d32-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="44d32-152">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="44d32-152">Additional resources</span></span>

* [<span data-ttu-id="44d32-153">Middleware</span><span class="sxs-lookup"><span data-stu-id="44d32-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* <span data-ttu-id="44d32-154">[Factory-based middleware activation](xref:fundamentals/middleware/extensibility) (Activación de middleware basada en Factory)</span><span class="sxs-lookup"><span data-stu-id="44d32-154">[Factory-based middleware activation](xref:fundamentals/middleware/extensibility)</span></span>
* [<span data-ttu-id="44d32-155">Repositorio de GitHub de Simple Injector</span><span class="sxs-lookup"><span data-stu-id="44d32-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="44d32-156">Documentación de Simple Injector</span><span class="sxs-lookup"><span data-stu-id="44d32-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
