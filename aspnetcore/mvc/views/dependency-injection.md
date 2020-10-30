---
title: Inserción de dependencias en vistas de ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo ASP.NET Core admite la inserción de dependencias en las vistas de MVC.
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
uid: mvc/views/dependency-injection
ms.openlocfilehash: a596d05e3d1d73d4faa2f84d97a40e4a2c1234e4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059291"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="b2e78-103">Inserción de dependencias en vistas de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b2e78-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="b2e78-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b2e78-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b2e78-105">ASP.NET Core admite la [inserción de dependencias](xref:fundamentals/dependency-injection) en vistas.</span><span class="sxs-lookup"><span data-stu-id="b2e78-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="b2e78-106">Esto puede ser útil para servicios específicos de vistas, como la localización o los datos necesarios solamente para rellenar los elementos de vistas.</span><span class="sxs-lookup"><span data-stu-id="b2e78-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="b2e78-107">Debe intentar mantener la [separación de intereses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) entre los controladores y las vistas.</span><span class="sxs-lookup"><span data-stu-id="b2e78-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="b2e78-108">La mayoría de los datos que muestran las vistas deben pasarse desde el controlador.</span><span class="sxs-lookup"><span data-stu-id="b2e78-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="b2e78-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b2e78-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration-injection"></a><span data-ttu-id="b2e78-110">Inserción de configuración</span><span class="sxs-lookup"><span data-stu-id="b2e78-110">Configuration injection</span></span>

<span data-ttu-id="b2e78-111">*:::no-loc(appsettings.json):::* los valores se pueden insertar directamente en una vista.</span><span class="sxs-lookup"><span data-stu-id="b2e78-111">*:::no-loc(appsettings.json):::* values can be injected directly into a view.</span></span>

<span data-ttu-id="b2e78-112">Ejemplo de un *:::no-loc(appsettings.json):::* archivo:</span><span class="sxs-lookup"><span data-stu-id="b2e78-112">Example of an *:::no-loc(appsettings.json):::* file:</span></span>

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

<span data-ttu-id="b2e78-113">La sintaxis de `@inject`: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="b2e78-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="b2e78-114">Un ejemplo que usa `@inject`:</span><span class="sxs-lookup"><span data-stu-id="b2e78-114">An example using `@inject`:</span></span>

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a><span data-ttu-id="b2e78-115">Inserción de un servicio</span><span class="sxs-lookup"><span data-stu-id="b2e78-115">Service injection</span></span>

<span data-ttu-id="b2e78-116">Un servicio puede insertarse en una vista mediante la directiva `@inject`.</span><span class="sxs-lookup"><span data-stu-id="b2e78-116">A service can be injected into a view using the `@inject` directive.</span></span> <span data-ttu-id="b2e78-117">Puede pensar que `@inject` es como si agregara una propiedad a la vista y rellenara la propiedad mediante DI.</span><span class="sxs-lookup"><span data-stu-id="b2e78-117">You can think of `@inject` as adding a property to the view, and populating the property using DI.</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="b2e78-118">Esta vista muestra una lista de instancias `ToDoItem`, junto con un resumen de estadísticas generales.</span><span class="sxs-lookup"><span data-stu-id="b2e78-118">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="b2e78-119">El resumen se rellena a partir de `StatisticsService` insertado.</span><span class="sxs-lookup"><span data-stu-id="b2e78-119">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="b2e78-120">Este servicio está registrado para la inserción de dependencias en `ConfigureServices` en *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="b2e78-120">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs* :</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="b2e78-121">`StatisticsService` realiza algunos cálculos en el conjunto de instancias de `ToDoItem`, al que se accede a través de un repositorio:</span><span class="sxs-lookup"><span data-stu-id="b2e78-121">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="b2e78-122">El repositorio de ejemplo usa una colección en memoria.</span><span class="sxs-lookup"><span data-stu-id="b2e78-122">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="b2e78-123">La implementación que se muestra arriba (que funciona en todos los datos en memoria) no se recomienda para conjuntos de datos grandes, con acceso de forma remota.</span><span class="sxs-lookup"><span data-stu-id="b2e78-123">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="b2e78-124">El ejemplo muestra los datos del modelo enlazado a la vista y del servicio que se inserta en la vista:</span><span class="sxs-lookup"><span data-stu-id="b2e78-124">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Vista de tareas pendientes que enumera los elementos totales, los elementos completados, la prioridad media y una lista de tareas con sus niveles de propiedad y valores booleanos que indican si se han completado.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="b2e78-126">Rellenar datos de búsqueda</span><span class="sxs-lookup"><span data-stu-id="b2e78-126">Populating Lookup Data</span></span>

<span data-ttu-id="b2e78-127">La inserción de vistas puede ser útil para rellenar opciones en elementos de interfaz de usuario, como por ejemplo, listas desplegables.</span><span class="sxs-lookup"><span data-stu-id="b2e78-127">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="b2e78-128">Imagine un formulario de perfil de usuario que incluye opciones para especificar el sexo, el estado y otras preferencias.</span><span class="sxs-lookup"><span data-stu-id="b2e78-128">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="b2e78-129">Para representar este tipo de formulario mediante un enfoque MVC estándar, necesitaría que el controlador solicitara servicios de acceso a datos para cada uno de estos conjuntos de opciones y, después, rellenar un modelo o `ViewBag` con cada conjunto de opciones para enlazar.</span><span class="sxs-lookup"><span data-stu-id="b2e78-129">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="b2e78-130">Un enfoque alternativo consiste en insertar servicios directamente en la vista para obtener las opciones.</span><span class="sxs-lookup"><span data-stu-id="b2e78-130">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="b2e78-131">Esto reduce la cantidad de código necesario para el controlador, ya que mueve esta lógica de construcción del elemento de vista a la propia vista.</span><span class="sxs-lookup"><span data-stu-id="b2e78-131">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="b2e78-132">La acción del controlador para mostrar un formulario de edición de perfil solamente necesita pasar el formulario de la instancia de perfil:</span><span class="sxs-lookup"><span data-stu-id="b2e78-132">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="b2e78-133">El formulario HTML que se utilizó para actualizar estas preferencias incluye listas desplegables para tres de las propiedades:</span><span class="sxs-lookup"><span data-stu-id="b2e78-133">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Vista de actualización de perfil, con un formulario que permite la entrada del nombre, el género, el estado y el color favorito.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="b2e78-135">Estas listas se rellenan mediante un servicio que se ha insertado en la vista:</span><span class="sxs-lookup"><span data-stu-id="b2e78-135">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="b2e78-136">`ProfileOptionsService` es un servicio de nivel de interfaz de usuario diseñado para proporcionar solo los datos necesarios para este formulario:</span><span class="sxs-lookup"><span data-stu-id="b2e78-136">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="b2e78-137">No olvide registrar los tipos que solicite a través de la inserción de dependencias en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b2e78-137">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b2e78-138">Un tipo no registrado produce una excepción en tiempo de ejecución porque el proveedor de servicios se consulta internamente a través de [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span><span class="sxs-lookup"><span data-stu-id="b2e78-138">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="b2e78-139">Reemplazar servicios</span><span class="sxs-lookup"><span data-stu-id="b2e78-139">Overriding Services</span></span>

<span data-ttu-id="b2e78-140">Además de insertar nuevos servicios, esta técnica también puede usarse para reemplazar servicios previamente insertados en una página.</span><span class="sxs-lookup"><span data-stu-id="b2e78-140">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="b2e78-141">En la imagen de abajo se muestran todos los campos disponibles en la página usada en el primer ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b2e78-141">The figure below shows all of the fields available on the page used in the first example:</span></span>

![Menú contextual de IntelliSense de un símbolo @ de tipo que enumera los campos Html, componente, StatsService y URL](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="b2e78-143">Como puede ver, los campos predeterminados incluyen `Html`, `Component` y `Url` (además de `StatsService` que hemos insertado).</span><span class="sxs-lookup"><span data-stu-id="b2e78-143">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="b2e78-144">Si, por ejemplo, quisiera reemplazar los asistentes de HTML predeterminadas con las suyas propias, puede hacerlo fácilmente mediante `@inject`:</span><span class="sxs-lookup"><span data-stu-id="b2e78-144">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="b2e78-145">Si quiere ampliar los servicios existentes, simplemente puede usar esta técnica al heredar o encapsular la implementación existente con la suya propia.</span><span class="sxs-lookup"><span data-stu-id="b2e78-145">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="b2e78-146">Consulte también</span><span class="sxs-lookup"><span data-stu-id="b2e78-146">See Also</span></span>

* <span data-ttu-id="b2e78-147">Blog de Simon Timms: [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/) (Obtener datos de búsqueda en la vista)</span><span class="sxs-lookup"><span data-stu-id="b2e78-147">Simon Timms Blog: [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
