---
title: Información general de ASP.NET Core MVC
author: ardalis
description: Conozca ASP.NET Core MVC, un marco completo para crear aplicaciones web y varias API mediante el patrón de diseño del controlador de vista de modelos.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/overview
ms.openlocfilehash: 29701506d9f6c1ff90081a548d524bc0deb65cda
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633622"
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="6baae-103">Información general de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6baae-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="6baae-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6baae-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6baae-105">ASP.NET Core MVC es un completo marco de trabajo para compilar aplicaciones web y API mediante el patrón de diseño Modelo-Vista-Controlador.</span><span class="sxs-lookup"><span data-stu-id="6baae-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="6baae-106">¿Qué es el patrón de MVC?</span><span class="sxs-lookup"><span data-stu-id="6baae-106">What is the MVC pattern?</span></span>

<span data-ttu-id="6baae-107">El patrón de arquitectura del controlador de vista de modelos (MVC) separa una aplicación en tres grupos de componentes principales: modelos, vistas y controladores.</span><span class="sxs-lookup"><span data-stu-id="6baae-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="6baae-108">Este patrón permite lograr la [separación de intereses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="6baae-108">This pattern helps to achieve [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span> <span data-ttu-id="6baae-109">Con este patrón, las solicitudes del usuario se enrutan a un controlador que se encarga de trabajar con el modelo para realizar las acciones del usuario o recuperar los resultados de consultas.</span><span class="sxs-lookup"><span data-stu-id="6baae-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="6baae-110">El controlador elige la vista para mostrar al usuario y proporciona cualquier dato de modelo que sea necesario.</span><span class="sxs-lookup"><span data-stu-id="6baae-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="6baae-111">En el siguiente diagrama se muestran los tres componentes principales y cuál hace referencia a los demás:</span><span class="sxs-lookup"><span data-stu-id="6baae-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![Patrón de MVC](overview/_static/mvc.png)

<span data-ttu-id="6baae-113">Con esta delineación de responsabilidades es más sencillo escalar la aplicación, porque resulta más fácil codificar, depurar y probar algo (modelo, vista o controlador) que tenga un solo trabajo.</span><span class="sxs-lookup"><span data-stu-id="6baae-113">This delineation of responsibilities helps you scale the application in terms of complexity because it's easier to code, debug, and test something (model, view, or controller) that has a single job.</span></span> <span data-ttu-id="6baae-114">Es más difícil actualizar, probar y depurar código que tenga dependencias repartidas entre dos o más de estas tres áreas.</span><span class="sxs-lookup"><span data-stu-id="6baae-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="6baae-115">Por ejemplo, la lógica de la interfaz de usuario tiende a cambiar con mayor frecuencia que la lógica de negocios.</span><span class="sxs-lookup"><span data-stu-id="6baae-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="6baae-116">Si el código de presentación y la lógica de negocios se combinan en un solo objeto, un objeto que contenga lógica de negocios deberá modificarse cada vez que cambie la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="6baae-116">If presentation code and business logic are combined in a single object, an object containing business logic must be modified every time the user interface is changed.</span></span> <span data-ttu-id="6baae-117">A menudo esto genera errores y es necesario volver a probar la lógica de negocio después de cada cambio mínimo en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="6baae-117">This often introduces errors and requires the retesting of business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="6baae-118">La vista y el controlador dependen del modelo.</span><span class="sxs-lookup"><span data-stu-id="6baae-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="6baae-119">Sin embargo, el modelo no depende de la vista ni del controlador. </span><span class="sxs-lookup"><span data-stu-id="6baae-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="6baae-120">Esta es una de las ventajas principales de la separación.</span><span class="sxs-lookup"><span data-stu-id="6baae-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="6baae-121">Esta separación permite compilar y probar el modelo con independencia de la presentación visual.</span><span class="sxs-lookup"><span data-stu-id="6baae-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="6baae-122">Responsabilidades del modelo</span><span class="sxs-lookup"><span data-stu-id="6baae-122">Model Responsibilities</span></span>

<span data-ttu-id="6baae-123">El modelo en una aplicación de MVC representa el estado de la aplicación y cualquier lógica de negocios u operaciones que esta deba realizar.</span><span class="sxs-lookup"><span data-stu-id="6baae-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="6baae-124">La lógica de negocios debe encapsularse en el modelo, junto con cualquier lógica de implementación para conservar el estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6baae-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="6baae-125">Las vistas fuertemente tipadas normalmente usan tipos ViewModel diseñados para que contengan los datos para mostrar en esa vista.</span><span class="sxs-lookup"><span data-stu-id="6baae-125">Strongly-typed views typically use ViewModel types designed to contain the data to display on that view.</span></span> <span data-ttu-id="6baae-126">El controlador crea y rellena estas instancias de ViewModel desde el modelo.</span><span class="sxs-lookup"><span data-stu-id="6baae-126">The controller creates and populates these ViewModel instances from the model.</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="6baae-127">Responsabilidades de las vistas</span><span class="sxs-lookup"><span data-stu-id="6baae-127">View Responsibilities</span></span>

<span data-ttu-id="6baae-128">Las vistas se encargan de presentar el contenido a través de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="6baae-128">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="6baae-129">Usan el [ Razor motor de vistas](#razor-view-engine) para insertar código .net en el marcado HTML.</span><span class="sxs-lookup"><span data-stu-id="6baae-129">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="6baae-130">Debería haber la mínima lógica entre las vistas y cualquier lógica en ellas debe estar relacionada con la presentación de contenido.</span><span class="sxs-lookup"><span data-stu-id="6baae-130">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="6baae-131">Si ve que necesita realizar una gran cantidad de lógica en los archivos de vistas para mostrar datos de un modelo complejo, considere la opción de usar un [componente de vista](views/view-components.md), ViewModel, o una plantilla de vista para simplificar la vista.</span><span class="sxs-lookup"><span data-stu-id="6baae-131">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="6baae-132">Responsabilidades del controlador</span><span class="sxs-lookup"><span data-stu-id="6baae-132">Controller Responsibilities</span></span>

<span data-ttu-id="6baae-133">Los controladores son los componentes que controlan la interacción del usuario, trabajan con el modelo y, en última instancia, seleccionan una vista para representarla.</span><span class="sxs-lookup"><span data-stu-id="6baae-133">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="6baae-134">En una aplicación de MVC, la vista solo muestra información; el controlador controla y responde a la interacción y los datos que introducen los usuarios.</span><span class="sxs-lookup"><span data-stu-id="6baae-134">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="6baae-135">En el patrón de MVC, el controlador es el punto de entrada inicial que se encarga de seleccionar con qué tipos de modelo trabajar y qué vistas representar (de ahí su nombre, ya que controla el modo en que la aplicación responde a una determinada solicitud).</span><span class="sxs-lookup"><span data-stu-id="6baae-135">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="6baae-136">Los controladores no deberían complicarse con demasiadas responsabilidades.</span><span class="sxs-lookup"><span data-stu-id="6baae-136">Controllers shouldn't be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="6baae-137">Para evitar que la lógica del controlador se vuelva demasiado compleja, saque la lógica de negocios fuera el controlador y llévela al modelo de dominio.</span><span class="sxs-lookup"><span data-stu-id="6baae-137">To keep controller logic from becoming overly complex, push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="6baae-138">Si piensa que el controlador realiza con frecuencia los mismos tipos de acciones, mueva estas acciones comunes en [filtros](#filters).</span><span class="sxs-lookup"><span data-stu-id="6baae-138">If you find that your controller actions frequently perform the same kinds of actions, move these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="6baae-139">Qué es ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6baae-139">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="6baae-140">El marco de ASP.NET Core MVC es un marco de presentación ligero, de código abierto y con gran capacidad de prueba, que está optimizado para usarlo con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6baae-140">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="6baae-141">ASP.NET Core MVC ofrece una manera basada en patrones de crear sitios web dinámicos que permitan una clara separación de intereses.</span><span class="sxs-lookup"><span data-stu-id="6baae-141">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="6baae-142">Proporciona control total sobre el marcado, admite el desarrollo controlado por pruebas (TDD) y usa los estándares web más recientes.</span><span class="sxs-lookup"><span data-stu-id="6baae-142">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="6baae-143">Características</span><span class="sxs-lookup"><span data-stu-id="6baae-143">Features</span></span>

<span data-ttu-id="6baae-144">ASP.NET Core MVC incluye lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6baae-144">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="6baae-145">Enrutamiento</span><span class="sxs-lookup"><span data-stu-id="6baae-145">Routing</span></span>](#routing)
* [<span data-ttu-id="6baae-146">Enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="6baae-146">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="6baae-147">Validación de modelos</span><span class="sxs-lookup"><span data-stu-id="6baae-147">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="6baae-148">Inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="6baae-148">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="6baae-149">Filtros</span><span class="sxs-lookup"><span data-stu-id="6baae-149">Filters</span></span>](#filters)
* [<span data-ttu-id="6baae-150">Áreas</span><span class="sxs-lookup"><span data-stu-id="6baae-150">Areas</span></span>](#areas)
* [<span data-ttu-id="6baae-151">API web</span><span class="sxs-lookup"><span data-stu-id="6baae-151">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="6baae-152">Capacidad de prueba</span><span class="sxs-lookup"><span data-stu-id="6baae-152">Testability</span></span>](#testability)
* [<span data-ttu-id="6baae-153">Razor motor de vista</span><span class="sxs-lookup"><span data-stu-id="6baae-153">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="6baae-154">Vistas fuertemente tipadas</span><span class="sxs-lookup"><span data-stu-id="6baae-154">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="6baae-155">Aplicaciones auxiliares de etiquetas</span><span class="sxs-lookup"><span data-stu-id="6baae-155">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="6baae-156">Ver componentes</span><span class="sxs-lookup"><span data-stu-id="6baae-156">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="6baae-157">Enrutamiento</span><span class="sxs-lookup"><span data-stu-id="6baae-157">Routing</span></span>

<span data-ttu-id="6baae-158">ASP.NET Core MVC se basa en el [enrutamiento de ASP.NET Core](../fundamentals/routing.md), un eficaz componente de asignación de URL que permite compilar aplicaciones que tengan direcciones URL comprensibles y que admitan búsquedas.</span><span class="sxs-lookup"><span data-stu-id="6baae-158">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="6baae-159">Esto permite definir patrones de nomenclatura de URL de la aplicación que funcionen bien para la optimización del motor de búsqueda (SEO) y para la generación de vínculos, sin tener en cuenta cómo se organizan los archivos en el servidor web.</span><span class="sxs-lookup"><span data-stu-id="6baae-159">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="6baae-160">Puede definir las rutas mediante una sintaxis de plantilla de ruta adecuada que admita las restricciones de valores de ruta, los valores predeterminados y los valores opcionales.</span><span class="sxs-lookup"><span data-stu-id="6baae-160">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="6baae-161">El *enrutamiento basado en la convención* permite definir globalmente los formatos de URL que acepta la aplicación y cómo cada uno de esos formatos se asigna a un método de acción específico en un determinado controlador.</span><span class="sxs-lookup"><span data-stu-id="6baae-161">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="6baae-162">Cuando se recibe una solicitud entrante, el motor de enrutamiento analiza la URL y la hace coincidir con uno de los formatos de URL definidos. Después, llama al método de acción del controlador asociado.</span><span class="sxs-lookup"><span data-stu-id="6baae-162">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="6baae-163">El *enrutamiento de atributos* permite especificar información de enrutamiento mediante la asignación de atributos a los controladores y las acciones, que permiten definir las rutas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6baae-163">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="6baae-164">Esto significa que las definiciones de ruta se colocan junto al controlador y la acción con la que están asociados.</span><span class="sxs-lookup"><span data-stu-id="6baae-164">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}
```

### <a name="model-binding"></a><span data-ttu-id="6baae-165">Enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="6baae-165">Model binding</span></span>

<span data-ttu-id="6baae-166">El [enlace de modelo](models/model-binding.md) de ASP.NET Core MVC convierte los datos de solicitud del cliente (valores de formulario, datos de ruta, parámetros de cadena de consulta, encabezados HTTP) en objetos que el controlador puede controlar.</span><span class="sxs-lookup"><span data-stu-id="6baae-166">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="6baae-167">Como resultado, la lógica del controlador no tiene que realizar el trabajo de pensar en los datos de la solicitud entrante; simplemente tiene los datos como parámetros para los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="6baae-167">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a><span data-ttu-id="6baae-168">Validación de modelos</span><span class="sxs-lookup"><span data-stu-id="6baae-168">Model validation</span></span>

<span data-ttu-id="6baae-169">ASP.NET Core MVC admite la [validación](models/validation.md) decorando el objeto de modelo con los atributos de validación de anotación de datos.</span><span class="sxs-lookup"><span data-stu-id="6baae-169">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="6baae-170">Los atributos de validación se comprueban en el lado cliente antes de que los valores se registren en el servidor, y también en el servidor antes de llamar a la acción del controlador.</span><span class="sxs-lookup"><span data-stu-id="6baae-170">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

<span data-ttu-id="6baae-171">Una acción del controlador:</span><span class="sxs-lookup"><span data-stu-id="6baae-171">A controller action:</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

<span data-ttu-id="6baae-172">El marco administra los datos de la solicitud de validación en el cliente y en el servidor.</span><span class="sxs-lookup"><span data-stu-id="6baae-172">The framework handles validating request data both on the client and on the server.</span></span> <span data-ttu-id="6baae-173">La lógica de validación especificada en tipos de modelo se agrega a las vistas representadas como anotaciones discretas y se aplica en el explorador con [Validación de jQuery](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="6baae-173">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="6baae-174">Inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="6baae-174">Dependency injection</span></span>

<span data-ttu-id="6baae-175">ASP.NET Core tiene compatibilidad integrada con la [inserción de dependencias](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="6baae-175">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="6baae-176">En ASP.NET Core MVC, los [controladores](controllers/dependency-injection.md) pueden solicitar los servicios que necesiten a través de sus constructores, lo que les permite seguir el [principio de dependencias explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="6baae-176">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

<span data-ttu-id="6baae-177">La aplicación también puede usar la [inserción de dependencias en archivos de vistas](views/dependency-injection.md), usando la directiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="6baae-177">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

```cshtml
@inject SomeService ServiceName

<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a><span data-ttu-id="6baae-178">Filtros</span><span class="sxs-lookup"><span data-stu-id="6baae-178">Filters</span></span>

<span data-ttu-id="6baae-179">Los [filtros](controllers/filters.md) ayudan a los desarrolladores a encapsular ciertas preocupaciones transversales, como el control de excepciones o la autorización.</span><span class="sxs-lookup"><span data-stu-id="6baae-179">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="6baae-180">Los filtros habilitan la ejecución de lógica de procesamiento previo y posterior para métodos de acción y pueden configurarse para ejecutarse en ciertos puntos dentro de la canalización de ejecución para una solicitud determinada.</span><span class="sxs-lookup"><span data-stu-id="6baae-180">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="6baae-181">Los filtros pueden aplicarse a controladores o acciones como atributos (o pueden ejecutarse globalmente).</span><span class="sxs-lookup"><span data-stu-id="6baae-181">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="6baae-182">En el marco se incluyen varios filtros (como `Authorize`).</span><span class="sxs-lookup"><span data-stu-id="6baae-182">Several filters (such as `Authorize`) are included in the framework.</span></span> <span data-ttu-id="6baae-183">`[Authorize]` es el atributo que se usa para crear filtros de autorización MVC.</span><span class="sxs-lookup"><span data-stu-id="6baae-183">`[Authorize]` is the attribute that is used to create MVC authorization filters.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a><span data-ttu-id="6baae-184">Áreas</span><span class="sxs-lookup"><span data-stu-id="6baae-184">Areas</span></span>

<span data-ttu-id="6baae-185">Las [áreas](controllers/areas.md) proporcionan una manera de crear particiones de una aplicación web MVC de gran ASP.net Core en agrupaciones funcionales más pequeñas.</span><span class="sxs-lookup"><span data-stu-id="6baae-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="6baae-186">Un área es una estructura de MVC dentro de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="6baae-186">An area is an MVC structure inside an application.</span></span> <span data-ttu-id="6baae-187">En un proyecto de MVC, los componentes lógicos como el modelo, el controlador y la vista se guardan en carpetas diferentes, y MVC usa las convenciones de nomenclatura para crear la relación entre estos componentes.</span><span class="sxs-lookup"><span data-stu-id="6baae-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="6baae-188">Para una aplicación grande, puede ser conveniente dividir la aplicación en distintas áreas de funciones de alto nivel.</span><span class="sxs-lookup"><span data-stu-id="6baae-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="6baae-189">Por ejemplo, una aplicación de comercio electrónico con varias unidades de negocio, como la retirada, la facturación y la búsqueda, etc. Cada una de estas unidades tiene sus propias vistas de componentes lógicas, controladores y modelos.</span><span class="sxs-lookup"><span data-stu-id="6baae-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="6baae-190">API web</span><span class="sxs-lookup"><span data-stu-id="6baae-190">Web APIs</span></span>

<span data-ttu-id="6baae-191">Además de ser una plataforma excelente para crear sitios web, ASP.NET Core MVC es muy compatible con la creación de las API web.</span><span class="sxs-lookup"><span data-stu-id="6baae-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="6baae-192">Puede crear servicios que lleguen con facilidad a una amplia gama de clientes, incluidos exploradores y dispositivos móviles.</span><span class="sxs-lookup"><span data-stu-id="6baae-192">You can build services that reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="6baae-193">El marco incluye compatibilidad con la negociación de contenido HTTP y compatibilidad integrada para [aplicar formato a datos](xref:web-api/advanced/formatting) como JSON o XML.</span><span class="sxs-lookup"><span data-stu-id="6baae-193">The framework includes support for HTTP content-negotiation with built-in support to [format data](xref:web-api/advanced/formatting) as JSON or XML.</span></span> <span data-ttu-id="6baae-194">Escriba [formateadores personalizados](xref:web-api/advanced/custom-formatters) para agregar compatibilidad con sus propios formatos.</span><span class="sxs-lookup"><span data-stu-id="6baae-194">Write [custom formatters](xref:web-api/advanced/custom-formatters) to add support for your own formats.</span></span>

<span data-ttu-id="6baae-195">Use la generación de vínculos para habilitar la compatibilidad con hipermedios.</span><span class="sxs-lookup"><span data-stu-id="6baae-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="6baae-196">Habilite fácilmente la compatibilidad con el [uso compartido de recursos entre orígenes (CORS)](https://www.w3.org/TR/cors/) para que las API web se pueden compartir entre varias aplicaciones web.</span><span class="sxs-lookup"><span data-stu-id="6baae-196">Easily enable support for [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="6baae-197">Capacidad de prueba</span><span class="sxs-lookup"><span data-stu-id="6baae-197">Testability</span></span>

<span data-ttu-id="6baae-198">Al usar interfaces e inserción de dependencias, el marco es adecuado para realizar pruebas unitarias. También incluye características (por ejemplo, un proveedor TestHost e InMemory para Entity Framework) con las que resulta muy fácil y rápido realizar [pruebas de integración](xref:test/integration-tests).</span><span class="sxs-lookup"><span data-stu-id="6baae-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration tests](xref:test/integration-tests) quick and easy as well.</span></span> <span data-ttu-id="6baae-199">Obtenga más información sobre [cómo probar la lógica del controlador](controllers/testing.md).</span><span class="sxs-lookup"><span data-stu-id="6baae-199">Learn more about [how to test controller logic](controllers/testing.md).</span></span>

### <a name="no-locrazor-view-engine"></a><span data-ttu-id="6baae-200">Razor motor de vista</span><span class="sxs-lookup"><span data-stu-id="6baae-200">Razor view engine</span></span>

<span data-ttu-id="6baae-201">[ASP.net Core vistas de MVC](views/overview.md) usan el [ Razor motor de vistas](views/razor.md) para representar las vistas.</span><span class="sxs-lookup"><span data-stu-id="6baae-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="6baae-202">Razor es un lenguaje de marcado de plantilla compacto, expresivo y fluido para definir vistas mediante código C# incrustado.</span><span class="sxs-lookup"><span data-stu-id="6baae-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="6baae-203">Razor se utiliza para generar dinámicamente contenido web en el servidor.</span><span class="sxs-lookup"><span data-stu-id="6baae-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="6baae-204">Permite combinar de manera limpia el código del servidor con el código y el contenido del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="6baae-204">You can cleanly mix server code with client side content and code.</span></span>

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

<span data-ttu-id="6baae-205">Con el Razor motor de vistas puede definir [diseños](views/layout.md), [vistas parciales](views/partial.md) y secciones reemplazables.</span><span class="sxs-lookup"><span data-stu-id="6baae-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="6baae-206">Vistas fuertemente tipadas</span><span class="sxs-lookup"><span data-stu-id="6baae-206">Strongly typed views</span></span>

<span data-ttu-id="6baae-207">Razor las vistas de MVC se pueden fuertemente tipadas en función del modelo.</span><span class="sxs-lookup"><span data-stu-id="6baae-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="6baae-208">Los controladores pueden pasar un modelo fuertemente tipado a las vistas para que estas admitan IntelliSense y la comprobación de tipos.</span><span class="sxs-lookup"><span data-stu-id="6baae-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="6baae-209">Por ejemplo, en esta vista se representa un modelo de tipo `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="6baae-209">For example, the following view renders a model of type `IEnumerable<Product>`:</span></span>

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="6baae-210">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="6baae-210">Tag Helpers</span></span>

<span data-ttu-id="6baae-211">Las [aplicaciones auxiliares de etiquetas](views/tag-helpers/intro.md) permiten que el código del lado servidor participe en la creación y representación de elementos HTML en Razor archivos.</span><span class="sxs-lookup"><span data-stu-id="6baae-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="6baae-212">Puede usar asistentes de etiquetas para definir etiquetas personalizadas (por ejemplo, `<environment>`) o para modificar el comportamiento de etiquetas existentes (por ejemplo, `<label>`).</span><span class="sxs-lookup"><span data-stu-id="6baae-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="6baae-213">Los asistentes de etiquetas enlazan con elementos específicos, en función del nombre del elemento y sus atributos.</span><span class="sxs-lookup"><span data-stu-id="6baae-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="6baae-214">Proporcionan las ventajas de la representación del lado servidor, al tiempo que se mantiene una experiencia de edición HTML.</span><span class="sxs-lookup"><span data-stu-id="6baae-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="6baae-215">Hay muchos asistentes de etiquetas integradas para tareas comunes (como la creación de formularios, vínculos, carga de activos, etc.) y existen muchos más a disposición en repositorios públicos de GitHub y como paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="6baae-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="6baae-216">Los asistentes de etiquetas se crean en C# y tienen como destino elementos HTML en función del nombre de elemento, el nombre de atributo o la etiqueta principal.</span><span class="sxs-lookup"><span data-stu-id="6baae-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="6baae-217">Por ejemplo, la aplicación auxiliar de etiquetas integrada LinkTagHelper puede usarse para crear un vínculo a la acción `Login` de `AccountsController`:</span><span class="sxs-lookup"><span data-stu-id="6baae-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="6baae-218">La aplicación auxiliar de etiquetas `EnvironmentTagHelper` puede usarse para incluir distintos scripts en las vistas (por ejemplo, sin formato o reducida) según el entorno en tiempo de ejecución, como desarrollo, ensayo o producción:</span><span class="sxs-lookup"><span data-stu-id="6baae-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

<span data-ttu-id="6baae-219">Las aplicaciones auxiliares de etiquetas proporcionan una experiencia de desarrollo compatible con HTML y un completo entorno de IntelliSense para crear HTML y Razor marcado.</span><span class="sxs-lookup"><span data-stu-id="6baae-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="6baae-220">La mayoría de los asistentes de etiquetas integrados tienen como destino elementos HTML existentes y proporcionan atributos del lado servidor para el elemento.</span><span class="sxs-lookup"><span data-stu-id="6baae-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="6baae-221">Componentes de vista</span><span class="sxs-lookup"><span data-stu-id="6baae-221">View Components</span></span>

<span data-ttu-id="6baae-222">Los [componentes de vista](views/view-components.md) permiten empaquetar la lógica de representación y reutilizarla en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6baae-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="6baae-223">Son similares a las [vistas parciales](views/partial.md), pero con lógica asociada.</span><span class="sxs-lookup"><span data-stu-id="6baae-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

## <a name="compatibility-version"></a><span data-ttu-id="6baae-224">Versión de compatibilidad</span><span class="sxs-lookup"><span data-stu-id="6baae-224">Compatibility version</span></span>

<span data-ttu-id="6baae-225">El método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite a una aplicación participar o no en los cambios de comportamiento importantes incorporados en ASP.NET Core MVC 2.1 o una versión posterior.</span><span class="sxs-lookup"><span data-stu-id="6baae-225">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="6baae-226">Para obtener más información, vea <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="6baae-226">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6baae-227">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6baae-227">Additional resources</span></span>

* <span data-ttu-id="6baae-228">[MyTest. AspNetCore. Mvc: biblioteca de pruebas fluidas para ASP.net Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): biblioteca de pruebas unitarias fuertemente tipadas, que proporciona una interfaz fluida para probar MVC y aplicaciones de API Web.</span><span class="sxs-lookup"><span data-stu-id="6baae-228">[MyTested.AspNetCore.Mvc - Fluent Testing Library for ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Strongly-typed unit testing library, providing a fluent interface for testing MVC and web API apps.</span></span> <span data-ttu-id="6baae-229">(*Microsoft no realiza su mantenimiento ni su soporte técnico.* )</span><span class="sxs-lookup"><span data-stu-id="6baae-229">(*Not maintained or supported by Microsoft.*)</span></span>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>
