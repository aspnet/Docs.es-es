---
title: Inserción de dependencias en controladores en ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo los controladores de ASP.NET Core MVC solicitan sus dependencias explícitamente a través de sus constructores por medio de la inserción de dependencias en ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: f3654e008733c57b4cd4cd34a52b747af2258be1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589080"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="c44a9-103">Inserción de dependencias en controladores en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c44a9-103">Dependency injection into controllers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c44a9-104">Por [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) y [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="c44a9-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="c44a9-105">Los controladores de ASP.NET Core MVC solicitan las dependencias de forma explícita a través de constructores.</span><span class="sxs-lookup"><span data-stu-id="c44a9-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="c44a9-106">ASP.NET Core tiene compatibilidad integrada con la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c44a9-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c44a9-107">La inserción de dependencias facilita las pruebas y el mantenimiento de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="c44a9-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="c44a9-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c44a9-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="c44a9-109">Inserción de constructores</span><span class="sxs-lookup"><span data-stu-id="c44a9-109">Constructor Injection</span></span>

<span data-ttu-id="c44a9-110">Los servicios se agregan como un parámetro de constructor y el runtime resuelve el servicio desde el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="c44a9-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="c44a9-111">Normalmente, los servicios se definen mediante interfaces.</span><span class="sxs-lookup"><span data-stu-id="c44a9-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="c44a9-112">Por ejemplo, considere una aplicación que requiere la hora actual.</span><span class="sxs-lookup"><span data-stu-id="c44a9-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="c44a9-113">En la interfaz siguiente se expone el servicio `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="c44a9-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="c44a9-114">En el código siguiente se implementa la interfaz `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="c44a9-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="c44a9-115">Agregue el servicio al contenedor de servicios:</span><span class="sxs-lookup"><span data-stu-id="c44a9-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="c44a9-116">Para más información sobre <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, vea [Duraciones de servicio de DI](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="c44a9-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="c44a9-117">En el código siguiente se muestra un saludo al usuario según la hora del día:</span><span class="sxs-lookup"><span data-stu-id="c44a9-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="c44a9-118">Ejecute la aplicación y se mostrará un mensaje en función de la hora.</span><span class="sxs-lookup"><span data-stu-id="c44a9-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="c44a9-119">Inserción de acción con FromServices</span><span class="sxs-lookup"><span data-stu-id="c44a9-119">Action injection with FromServices</span></span>

<span data-ttu-id="c44a9-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> permite la inserción de un servicio directamente en un método de acción sin usar la inserción de constructores:</span><span class="sxs-lookup"><span data-stu-id="c44a9-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="c44a9-121">Acceso a la configuración desde un controlador</span><span class="sxs-lookup"><span data-stu-id="c44a9-121">Access settings from a controller</span></span>

<span data-ttu-id="c44a9-122">El acceso a la configuración de la aplicación o a los valores de configuración desde un controlador es un patrón habitual.</span><span class="sxs-lookup"><span data-stu-id="c44a9-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="c44a9-123">El *patrón de opciones* que se describe en <xref:fundamentals/configuration/options> es el enfoque preferido para administrar la configuración.</span><span class="sxs-lookup"><span data-stu-id="c44a9-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="c44a9-124">Por lo general, <xref:Microsoft.Extensions.Configuration.IConfiguration> no se inserta directamente en un controlador.</span><span class="sxs-lookup"><span data-stu-id="c44a9-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="c44a9-125">Cree una clase que represente las opciones.</span><span class="sxs-lookup"><span data-stu-id="c44a9-125">Create a class that represents the options.</span></span> <span data-ttu-id="c44a9-126">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c44a9-126">For example:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="c44a9-127">Agregue la clase de configuración a la colección de servicios:</span><span class="sxs-lookup"><span data-stu-id="c44a9-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="c44a9-128">Configure la aplicación para leer la configuración de un archivo con formato JSON:</span><span class="sxs-lookup"><span data-stu-id="c44a9-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="c44a9-129">En el código siguiente se solicita la configuración `IOptions<SampleWebSettings>` del contenedor de servicios y se usa en el método `Index`:</span><span class="sxs-lookup"><span data-stu-id="c44a9-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="c44a9-130">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c44a9-130">Additional resources</span></span>

* <span data-ttu-id="c44a9-131">Vea <xref:mvc/controllers/testing> para obtener información sobre cómo solicitar explícitamente dependencias en controladores para facilitar la comprobación del código.</span><span class="sxs-lookup"><span data-stu-id="c44a9-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="c44a9-132">[Reemplazo del contenedor de inserción de dependencias predeterminado con una implementación de terceros](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="c44a9-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c44a9-133">Por [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) y [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="c44a9-133">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="c44a9-134">Los controladores de ASP.NET Core MVC solicitan las dependencias de forma explícita a través de constructores.</span><span class="sxs-lookup"><span data-stu-id="c44a9-134">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="c44a9-135">ASP.NET Core tiene compatibilidad integrada con la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c44a9-135">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c44a9-136">La inserción de dependencias facilita las pruebas y el mantenimiento de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="c44a9-136">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="c44a9-137">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c44a9-137">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="c44a9-138">Inserción de constructores</span><span class="sxs-lookup"><span data-stu-id="c44a9-138">Constructor Injection</span></span>

<span data-ttu-id="c44a9-139">Los servicios se agregan como un parámetro de constructor y el runtime resuelve el servicio desde el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="c44a9-139">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="c44a9-140">Normalmente, los servicios se definen mediante interfaces.</span><span class="sxs-lookup"><span data-stu-id="c44a9-140">Services are typically defined using interfaces.</span></span> <span data-ttu-id="c44a9-141">Por ejemplo, considere una aplicación que requiere la hora actual.</span><span class="sxs-lookup"><span data-stu-id="c44a9-141">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="c44a9-142">En la interfaz siguiente se expone el servicio `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="c44a9-142">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="c44a9-143">En el código siguiente se implementa la interfaz `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="c44a9-143">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="c44a9-144">Agregue el servicio al contenedor de servicios:</span><span class="sxs-lookup"><span data-stu-id="c44a9-144">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="c44a9-145">Para más información sobre <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, vea [Duraciones de servicio de DI](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="c44a9-145">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="c44a9-146">En el código siguiente se muestra un saludo al usuario según la hora del día:</span><span class="sxs-lookup"><span data-stu-id="c44a9-146">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="c44a9-147">Ejecute la aplicación y se mostrará un mensaje en función de la hora.</span><span class="sxs-lookup"><span data-stu-id="c44a9-147">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="c44a9-148">Inserción de acción con FromServices</span><span class="sxs-lookup"><span data-stu-id="c44a9-148">Action injection with FromServices</span></span>

<span data-ttu-id="c44a9-149"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> permite la inserción de un servicio directamente en un método de acción sin usar la inserción de constructores:</span><span class="sxs-lookup"><span data-stu-id="c44a9-149">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="c44a9-150">Acceso a la configuración desde un controlador</span><span class="sxs-lookup"><span data-stu-id="c44a9-150">Access settings from a controller</span></span>

<span data-ttu-id="c44a9-151">El acceso a la configuración de la aplicación o a los valores de configuración desde un controlador es un patrón habitual.</span><span class="sxs-lookup"><span data-stu-id="c44a9-151">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="c44a9-152">El *patrón de opciones* que se describe en <xref:fundamentals/configuration/options> es el enfoque preferido para administrar la configuración.</span><span class="sxs-lookup"><span data-stu-id="c44a9-152">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="c44a9-153">Por lo general, <xref:Microsoft.Extensions.Configuration.IConfiguration> no se inserta directamente en un controlador.</span><span class="sxs-lookup"><span data-stu-id="c44a9-153">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="c44a9-154">Cree una clase que represente las opciones.</span><span class="sxs-lookup"><span data-stu-id="c44a9-154">Create a class that represents the options.</span></span> <span data-ttu-id="c44a9-155">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c44a9-155">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="c44a9-156">Agregue la clase de configuración a la colección de servicios:</span><span class="sxs-lookup"><span data-stu-id="c44a9-156">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="c44a9-157">Configure la aplicación para leer la configuración de un archivo con formato JSON:</span><span class="sxs-lookup"><span data-stu-id="c44a9-157">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="c44a9-158">En el código siguiente se solicita la configuración `IOptions<SampleWebSettings>` del contenedor de servicios y se usa en el método `Index`:</span><span class="sxs-lookup"><span data-stu-id="c44a9-158">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="c44a9-159">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c44a9-159">Additional resources</span></span>

* <span data-ttu-id="c44a9-160">Vea <xref:mvc/controllers/testing> para obtener información sobre cómo solicitar explícitamente dependencias en controladores para facilitar la comprobación del código.</span><span class="sxs-lookup"><span data-stu-id="c44a9-160">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="c44a9-161">[Reemplazo del contenedor de inserción de dependencias predeterminado con una implementación de terceros](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="c44a9-161">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end