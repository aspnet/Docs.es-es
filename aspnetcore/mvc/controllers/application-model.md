---
title: Trabajar con el modelo de aplicación en ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo leer y manipular el modelo de aplicación para modificar la forma en que los elementos de MVC se comportan en ASP.NET Core.
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/controllers/application-model
ms.openlocfilehash: de831a8b5012a2fe61642887ad816466df7a1bcb
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019903"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a><span data-ttu-id="82312-103">Trabajar con el modelo de aplicación en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82312-103">Work with the application model in ASP.NET Core</span></span>

<span data-ttu-id="82312-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="82312-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="82312-105">ASP.NET Core MVC define un *modelo de aplicación* que representa los componentes de una aplicación MVC.</span><span class="sxs-lookup"><span data-stu-id="82312-105">ASP.NET Core MVC defines an *application model* representing the components of an MVC app.</span></span> <span data-ttu-id="82312-106">Puede leer y manipular este modelo para modificar la manera en que se comportan los elementos de MVC.</span><span class="sxs-lookup"><span data-stu-id="82312-106">You can read and manipulate this model to modify how MVC elements behave.</span></span> <span data-ttu-id="82312-107">De forma predeterminada, MVC sigue ciertas convenciones para determinar qué clases se consideran controladores, qué métodos de esas clases son acciones y cómo se comportan los parámetros y el enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="82312-107">By default, MVC follows certain conventions to determine which classes are considered to be controllers, which methods on those classes are actions, and how parameters and routing behave.</span></span> <span data-ttu-id="82312-108">Puede personalizar este comportamiento para adaptarlo a las necesidades de su aplicación. Para ello, basta con que cree sus propias convenciones y las aplique globalmente o como atributos.</span><span class="sxs-lookup"><span data-stu-id="82312-108">You can customize this behavior to suit your app's needs by creating your own conventions and applying them globally or as attributes.</span></span>

## <a name="models-and-providers"></a><span data-ttu-id="82312-109">Modelos y proveedores</span><span class="sxs-lookup"><span data-stu-id="82312-109">Models and Providers</span></span>

<span data-ttu-id="82312-110">El modelo de aplicación de ASP.NET Core MVC incluye interfaces abstractas y clases de implementación concretas que describen una aplicación MVC.</span><span class="sxs-lookup"><span data-stu-id="82312-110">The ASP.NET Core MVC application model include both abstract interfaces and concrete implementation classes that describe an MVC application.</span></span> <span data-ttu-id="82312-111">Este modelo es el resultado de la detección por parte de MVC de los controladores, las acciones, los parámetros de acción, las rutas y los filtros de la aplicación de acuerdo con las convenciones predeterminadas.</span><span class="sxs-lookup"><span data-stu-id="82312-111">This model is the result of MVC discovering the app's controllers, actions, action parameters, routes, and filters according to default conventions.</span></span> <span data-ttu-id="82312-112">Cuando trabaje con el modelo de aplicación, puede modificar la aplicación para que siga convenciones diferentes del comportamiento predeterminado de MVC.</span><span class="sxs-lookup"><span data-stu-id="82312-112">By working with the application model, you can modify your app to follow different conventions from the default MVC behavior.</span></span> <span data-ttu-id="82312-113">Los parámetros, nombres, rutas y filtros se usan como datos de configuración para las acciones y los controladores.</span><span class="sxs-lookup"><span data-stu-id="82312-113">The parameters, names, routes, and filters are all used as configuration data for actions and controllers.</span></span>

<span data-ttu-id="82312-114">El modelo de aplicación de ASP.NET Core MVC tiene la estructura siguiente:</span><span class="sxs-lookup"><span data-stu-id="82312-114">The ASP.NET Core MVC Application Model has the following structure:</span></span>

* <span data-ttu-id="82312-115">ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="82312-115">ApplicationModel</span></span>
  * <span data-ttu-id="82312-116">Controladores (ControllerModel)</span><span class="sxs-lookup"><span data-stu-id="82312-116">Controllers (ControllerModel)</span></span>
    * <span data-ttu-id="82312-117">Acciones (ActionModel)</span><span class="sxs-lookup"><span data-stu-id="82312-117">Actions (ActionModel)</span></span>
      * <span data-ttu-id="82312-118">Parámetros (ParameterModel)</span><span class="sxs-lookup"><span data-stu-id="82312-118">Parameters (ParameterModel)</span></span>

<span data-ttu-id="82312-119">Cada nivel del modelo tiene acceso a una colección `Properties` común, y los niveles inferiores pueden tener acceso a los valores de propiedad establecidos por los niveles superiores de la jerarquía y sobrescribirlos.</span><span class="sxs-lookup"><span data-stu-id="82312-119">Each level of the model has access to a common `Properties` collection, and lower levels can access and overwrite property values set by higher levels in the hierarchy.</span></span> <span data-ttu-id="82312-120">Las propiedades se conservan en `ActionDescriptor.Properties` cuando se crean las acciones.</span><span class="sxs-lookup"><span data-stu-id="82312-120">The properties are persisted to the `ActionDescriptor.Properties` when the actions are created.</span></span> <span data-ttu-id="82312-121">Después, cuando se controla una solicitud, se puede obtener acceso a través de `ActionContext.ActionDescriptor.Properties` a todas las propiedades que agregue o modifique una convención.</span><span class="sxs-lookup"><span data-stu-id="82312-121">Then when a request is being handled, any properties a convention added or modified can be accessed through `ActionContext.ActionDescriptor.Properties`.</span></span> <span data-ttu-id="82312-122">El uso de propiedades es una manera excelente de configurar por acción los filtros, los enlazadores de modelos, etc.</span><span class="sxs-lookup"><span data-stu-id="82312-122">Using properties is a great way to configure your filters, model binders, etc. on a per-action basis.</span></span>

> [!NOTE]
> <span data-ttu-id="82312-123">La colección `ActionDescriptor.Properties` no es segura para subprocesos (para escrituras) una vez que el inicio de la aplicación haya finalizado.</span><span class="sxs-lookup"><span data-stu-id="82312-123">The `ActionDescriptor.Properties` collection isn't thread safe (for writes) once app startup has finished.</span></span> <span data-ttu-id="82312-124">Las convenciones son la mejor manera de agregar datos de forma segura a esta colección.</span><span class="sxs-lookup"><span data-stu-id="82312-124">Conventions are the best way to safely add data to this collection.</span></span>

### <a name="iapplicationmodelprovider"></a><span data-ttu-id="82312-125">IApplicationModelProvider</span><span class="sxs-lookup"><span data-stu-id="82312-125">IApplicationModelProvider</span></span>

<span data-ttu-id="82312-126">ASP.NET Core MVC carga el modelo de aplicación mediante un patrón de proveedor definido por la interfaz [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider).</span><span class="sxs-lookup"><span data-stu-id="82312-126">ASP.NET Core MVC loads the application model using a provider pattern, defined by the [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) interface.</span></span> <span data-ttu-id="82312-127">En esta sección se describen algunos detalles de implementación interna relacionados con el funcionamiento de este proveedor.</span><span class="sxs-lookup"><span data-stu-id="82312-127">This section covers some of the internal implementation details of how this provider functions.</span></span> <span data-ttu-id="82312-128">Se trata de un tema avanzado, ya que la mayoría de las aplicaciones que aprovechan el modelo de aplicación deberían hacerlo mediante convenciones.</span><span class="sxs-lookup"><span data-stu-id="82312-128">This is an advanced topic - most apps that leverage the application model should do so by working with conventions.</span></span>

<span data-ttu-id="82312-129">Las implementaciones de la interfaz `IApplicationModelProvider` "se encapsulan" entre sí, y cada implementación llama a `OnProvidersExecuting` en orden ascendente en función de su propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="82312-129">Implementations of the `IApplicationModelProvider` interface "wrap" one another, with each implementation calling `OnProvidersExecuting` in ascending order based on its `Order` property.</span></span> <span data-ttu-id="82312-130">Después, se llama al método `OnProvidersExecuted` en orden inverso.</span><span class="sxs-lookup"><span data-stu-id="82312-130">The `OnProvidersExecuted` method is then called in reverse order.</span></span> <span data-ttu-id="82312-131">El marco de trabajo define varios proveedores:</span><span class="sxs-lookup"><span data-stu-id="82312-131">The framework defines several providers:</span></span>

<span data-ttu-id="82312-132">Primero, (`Order=-1000`):</span><span class="sxs-lookup"><span data-stu-id="82312-132">First (`Order=-1000`):</span></span>

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

<span data-ttu-id="82312-133">Después, (`Order=-990`):</span><span class="sxs-lookup"><span data-stu-id="82312-133">Then (`Order=-990`):</span></span>

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> <span data-ttu-id="82312-134">El orden en que se llama a dos proveedores con el mismo valor para `Order` no está definido y, por tanto, no se debe confiar en él.</span><span class="sxs-lookup"><span data-stu-id="82312-134">The order in which two providers with the same value for `Order` are called is undefined, and therefore shouldn't be relied upon.</span></span>

> [!NOTE]
> <span data-ttu-id="82312-135">`IApplicationModelProvider` es un concepto avanzado pensado para que los autores del marco de trabajo lo extiendan.</span><span class="sxs-lookup"><span data-stu-id="82312-135">`IApplicationModelProvider` is an advanced concept for framework authors to extend.</span></span> <span data-ttu-id="82312-136">En general, las aplicaciones deben usar convenciones y los marcos de trabajo deben usar proveedores.</span><span class="sxs-lookup"><span data-stu-id="82312-136">In general, apps should use conventions and frameworks should use providers.</span></span> <span data-ttu-id="82312-137">La diferencia clave es que los proveedores siempre se ejecutan antes que las convenciones.</span><span class="sxs-lookup"><span data-stu-id="82312-137">The key distinction is that providers always run before conventions.</span></span>

<span data-ttu-id="82312-138">`DefaultApplicationModelProvider` establece muchos de los comportamientos predeterminados que usa ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="82312-138">The `DefaultApplicationModelProvider` establishes many of the default behaviors used by ASP.NET Core MVC.</span></span> <span data-ttu-id="82312-139">Entre sus responsabilidades se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="82312-139">Its responsibilities include:</span></span>

* <span data-ttu-id="82312-140">Agregar filtros globales al contexto</span><span class="sxs-lookup"><span data-stu-id="82312-140">Adding global filters to the context</span></span>
* <span data-ttu-id="82312-141">Agregar controladores al contexto</span><span class="sxs-lookup"><span data-stu-id="82312-141">Adding controllers to the context</span></span>
* <span data-ttu-id="82312-142">Agregar métodos de controlador públicos como acciones</span><span class="sxs-lookup"><span data-stu-id="82312-142">Adding public controller methods as actions</span></span>
* <span data-ttu-id="82312-143">Agregar parámetros de métodos de acción al contexto</span><span class="sxs-lookup"><span data-stu-id="82312-143">Adding action method parameters to the context</span></span>
* <span data-ttu-id="82312-144">Aplicar la ruta y otros atributos</span><span class="sxs-lookup"><span data-stu-id="82312-144">Applying route and other attributes</span></span>

<span data-ttu-id="82312-145">Algunos comportamientos integrados se implementan mediante `DefaultApplicationModelProvider`.</span><span class="sxs-lookup"><span data-stu-id="82312-145">Some built-in behaviors are implemented by the `DefaultApplicationModelProvider`.</span></span> <span data-ttu-id="82312-146">Este proveedor es responsable de construir [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel) , que a su vez hace referencia a las [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel) instancias de, [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) y [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) .</span><span class="sxs-lookup"><span data-stu-id="82312-146">This provider is responsible for constructing the [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), which in turn references [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel), [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), and [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) instances.</span></span> <span data-ttu-id="82312-147">La clase `DefaultApplicationModelProvider` es un detalle de implementación del marco de trabajo interno que cambiará en el futuro.</span><span class="sxs-lookup"><span data-stu-id="82312-147">The `DefaultApplicationModelProvider` class is an internal framework implementation detail that can and will change in the future.</span></span> 

<span data-ttu-id="82312-148">`AuthorizationApplicationModelProvider` se encarga de aplicar el comportamiento asociado a los atributos `AuthorizeFilter` y `AllowAnonymousFilter`.</span><span class="sxs-lookup"><span data-stu-id="82312-148">The `AuthorizationApplicationModelProvider` is responsible for applying the behavior associated with the `AuthorizeFilter` and `AllowAnonymousFilter` attributes.</span></span> <span data-ttu-id="82312-149">[Más información sobre estos atributos](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="82312-149">[Learn more about these attributes](xref:security/authorization/simple).</span></span>

<span data-ttu-id="82312-150">`CorsApplicationModelProvider` implementa el comportamiento asociado a `IEnableCorsAttribute`, `IDisableCorsAttribute` y `DisableCorsAuthorizationFilter`.</span><span class="sxs-lookup"><span data-stu-id="82312-150">The `CorsApplicationModelProvider` implements behavior associated with the `IEnableCorsAttribute` and `IDisableCorsAttribute`, and the `DisableCorsAuthorizationFilter`.</span></span> <span data-ttu-id="82312-151">[Más información sobre CORS](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="82312-151">[Learn more about CORS](xref:security/cors).</span></span>

## <a name="conventions"></a><span data-ttu-id="82312-152">Convenciones</span><span class="sxs-lookup"><span data-stu-id="82312-152">Conventions</span></span>

<span data-ttu-id="82312-153">El modelo de aplicación define abstracciones de convenciones que proporcionan una forma más sencilla de personalizar el comportamiento de los modelos que invalidan el modelo completo o el proveedor.</span><span class="sxs-lookup"><span data-stu-id="82312-153">The application model defines convention abstractions that provide a simpler way to customize the behavior of the models than overriding the entire model or provider.</span></span> <span data-ttu-id="82312-154">Se recomienda el uso de estas abstracciones para modificar el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="82312-154">These abstractions are the recommended way to modify your app's behavior.</span></span> <span data-ttu-id="82312-155">Las convenciones ofrecen una manera de escribir código que aplica dinámicamente las personalizaciones.</span><span class="sxs-lookup"><span data-stu-id="82312-155">Conventions provide a way for you to write code that will dynamically apply customizations.</span></span> <span data-ttu-id="82312-156">Mientras los [filtros](xref:mvc/controllers/filters) proporcionan una forma de modificar el comportamiento del marco de trabajo, las personalizaciones permiten controlar cómo funciona la aplicación en su conjunto.</span><span class="sxs-lookup"><span data-stu-id="82312-156">While [filters](xref:mvc/controllers/filters) provide a means of modifying the framework's behavior, customizations let you control how the whole app works together.</span></span>

<span data-ttu-id="82312-157">Están disponibles las convenciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="82312-157">The following conventions are available:</span></span>

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

<span data-ttu-id="82312-158">Las convenciones se aplican agregándolas a las opciones de MVC o implementando `Attribute` s y aplicándolos a los controladores, acciones o parámetros de acción (similar a [`Filters`](xref:mvc/controllers/filters) ).</span><span class="sxs-lookup"><span data-stu-id="82312-158">Conventions are applied by adding them to MVC options or by implementing `Attribute`s and applying them to controllers, actions, or action parameters (similar to [`Filters`](xref:mvc/controllers/filters)).</span></span> <span data-ttu-id="82312-159">A diferencia de los filtros, las convenciones solo se ejecutan cuando se inicia la aplicación, no como parte de cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="82312-159">Unlike filters, conventions are only executed when the app is starting, not as part of each request.</span></span>

### <a name="sample-modifying-the-applicationmodel"></a><span data-ttu-id="82312-160">Ejemplo: modificar ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="82312-160">Sample: Modifying the ApplicationModel</span></span>

<span data-ttu-id="82312-161">La siguiente convención se usa para agregar una propiedad al modelo de aplicación.</span><span class="sxs-lookup"><span data-stu-id="82312-161">The following convention is used to add a property to the application model.</span></span> 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

<span data-ttu-id="82312-162">Las convenciones del modelo de aplicación se aplican como opciones cuando se agrega MVC en `ConfigureServices` en `Startup`.</span><span class="sxs-lookup"><span data-stu-id="82312-162">Application model conventions are applied as options when MVC is added in `ConfigureServices` in `Startup`.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

<span data-ttu-id="82312-163">Las propiedades son accesibles desde la colección de propiedades `ActionDescriptor` dentro de las acciones de controlador:</span><span class="sxs-lookup"><span data-stu-id="82312-163">Properties are accessible from the `ActionDescriptor` properties collection within controller actions:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a><span data-ttu-id="82312-164">Ejemplo: modificar la descripción de ControllerModel</span><span class="sxs-lookup"><span data-stu-id="82312-164">Sample: Modifying the ControllerModel Description</span></span>

<span data-ttu-id="82312-165">Como en el ejemplo anterior, el modelo de controlador también se puede modificar para incluir propiedades personalizadas.</span><span class="sxs-lookup"><span data-stu-id="82312-165">As in the previous example, the controller model can also be modified to include custom properties.</span></span> <span data-ttu-id="82312-166">Estos invalidará las propiedades existentes con el mismo nombre especificado en el modelo de aplicación.</span><span class="sxs-lookup"><span data-stu-id="82312-166">These will override existing properties with the same name specified in the application model.</span></span> <span data-ttu-id="82312-167">El atributo de convención siguiente agrega una descripción en el nivel de controlador:</span><span class="sxs-lookup"><span data-stu-id="82312-167">The following convention attribute adds a description at the controller level:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

<span data-ttu-id="82312-168">Esta convención se aplica como un atributo en un controlador.</span><span class="sxs-lookup"><span data-stu-id="82312-168">This convention is applied as an attribute on a controller.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

<span data-ttu-id="82312-169">Se tiene acceso a la propiedad "description" de la misma manera que en ejemplos anteriores.</span><span class="sxs-lookup"><span data-stu-id="82312-169">The "description" property is accessed in the same manner as in previous examples.</span></span>

### <a name="sample-modifying-the-actionmodel-description"></a><span data-ttu-id="82312-170">Ejemplo: modificar la descripción de ActionModel</span><span class="sxs-lookup"><span data-stu-id="82312-170">Sample: Modifying the ActionModel Description</span></span>

<span data-ttu-id="82312-171">Se puede aplicar una convención de atributo independiente a acciones individuales, con lo que se invalida el comportamiento que ya se haya aplicado en el nivel de aplicación o controlador.</span><span class="sxs-lookup"><span data-stu-id="82312-171">A separate attribute convention can be applied to individual actions, overriding behavior already applied at the application or controller level.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

<span data-ttu-id="82312-172">Si se aplica a una acción dentro del controlador del ejemplo anterior se puede ver cómo invalida la convención en el nivel de controlador:</span><span class="sxs-lookup"><span data-stu-id="82312-172">Applying this to an action within the previous example's controller demonstrates how it overrides the controller-level convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a><span data-ttu-id="82312-173">Ejemplo: modificar ParameterModel</span><span class="sxs-lookup"><span data-stu-id="82312-173">Sample: Modifying the ParameterModel</span></span>

<span data-ttu-id="82312-174">La convención siguiente se puede aplicar a parámetros de acción para modificar su `BindingInfo`.</span><span class="sxs-lookup"><span data-stu-id="82312-174">The following convention can be applied to action parameters to modify their `BindingInfo`.</span></span> <span data-ttu-id="82312-175">La convención siguiente requiere que el parámetro sea un parámetro de ruta. Se ignorarán todos los demás orígenes de enlace posibles (por ejemplo, valores de cadena de consulta).</span><span class="sxs-lookup"><span data-stu-id="82312-175">The following convention requires that the parameter be a route parameter; other potential binding sources (such as query string values) are ignored.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

<span data-ttu-id="82312-176">El atributo se puede aplicar a cualquier parámetro de acción:</span><span class="sxs-lookup"><span data-stu-id="82312-176">The attribute may be applied to any action parameter:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a><span data-ttu-id="82312-177">Ejemplo: modificar el nombre de ActionModel</span><span class="sxs-lookup"><span data-stu-id="82312-177">Sample: Modifying the ActionModel Name</span></span>

<span data-ttu-id="82312-178">La convención siguiente modifica el valor de `ActionModel` para actualizar el *nombre* de la acción a la que se aplica.</span><span class="sxs-lookup"><span data-stu-id="82312-178">The following convention modifies the `ActionModel` to update the *name* of the action to which it's applied.</span></span> <span data-ttu-id="82312-179">El nuevo nombre se proporciona como un parámetro al atributo.</span><span class="sxs-lookup"><span data-stu-id="82312-179">The new name is provided as a parameter to the attribute.</span></span> <span data-ttu-id="82312-180">El enrutamiento usará este nuevo nombre, lo que afectará a la ruta usada para llegar a este método de acción.</span><span class="sxs-lookup"><span data-stu-id="82312-180">This new name is used by routing, so it will affect the route used to reach this action method.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

<span data-ttu-id="82312-181">Este atributo se aplica a un método de acción en `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="82312-181">This attribute is applied to an action method in the `HomeController`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

<span data-ttu-id="82312-182">Aunque el nombre del método es `SomeName`, el atributo invalida la convención de MVC de usar el nombre del método y reemplaza el nombre de la acción por `MyCoolAction`.</span><span class="sxs-lookup"><span data-stu-id="82312-182">Even though the method name is `SomeName`, the attribute overrides the MVC convention of using the method name and replaces the action name with `MyCoolAction`.</span></span> <span data-ttu-id="82312-183">De este modo, la ruta usada para llegar a esta acción es `/Home/MyCoolAction`.</span><span class="sxs-lookup"><span data-stu-id="82312-183">Thus, the route used to reach this action is `/Home/MyCoolAction`.</span></span>

> [!NOTE]
> <span data-ttu-id="82312-184">Este ejemplo básicamente equivale a usar el atributo [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) integrado.</span><span class="sxs-lookup"><span data-stu-id="82312-184">This example is essentially the same as using the built-in [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) attribute.</span></span>

### <a name="sample-custom-routing-convention"></a><span data-ttu-id="82312-185">Ejemplo: convención de enrutamiento personalizado</span><span class="sxs-lookup"><span data-stu-id="82312-185">Sample: Custom Routing Convention</span></span>

<span data-ttu-id="82312-186">Puede usar `IApplicationModelConvention` para personalizar cómo funciona el enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="82312-186">You can use an `IApplicationModelConvention` to customize how routing works.</span></span> <span data-ttu-id="82312-187">Por ejemplo, la convención siguiente incorporará espacios de nombres de controladores en sus rutas, al reemplazar `.` en el espacio de nombres por `/` en la ruta:</span><span class="sxs-lookup"><span data-stu-id="82312-187">For example, the following convention will incorporate Controllers' namespaces into their routes, replacing `.` in the namespace with `/` in the route:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

<span data-ttu-id="82312-188">La convención se agrega como una opción en Startup.</span><span class="sxs-lookup"><span data-stu-id="82312-188">The convention is added as an option in Startup.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> <span data-ttu-id="82312-189">Para agregar convenciones a su [software intermedio](xref:fundamentals/middleware/index), obtenga acceso a `MvcOptions` con `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`.</span><span class="sxs-lookup"><span data-stu-id="82312-189">You can add conventions to your [middleware](xref:fundamentals/middleware/index) by accessing `MvcOptions` using `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span></span>

<span data-ttu-id="82312-190">En este ejemplo se aplica esta convención a las rutas que no usan el enrutamiento de atributos donde el controlador contiene "Namespace" en su nombre.</span><span class="sxs-lookup"><span data-stu-id="82312-190">This sample applies this convention to routes that are not using attribute routing where the controller has  "Namespace" in its name.</span></span> <span data-ttu-id="82312-191">En el controlador siguiente se muestra esta convención:</span><span class="sxs-lookup"><span data-stu-id="82312-191">The following controller demonstrates this convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a><span data-ttu-id="82312-192">Uso del modelo de aplicación en WebApiCompatShim</span><span class="sxs-lookup"><span data-stu-id="82312-192">Application Model Usage in WebApiCompatShim</span></span>

<span data-ttu-id="82312-193">ASP.NET Core MVC usa un conjunto diferente de convenciones de ASP.NET Web API 2.</span><span class="sxs-lookup"><span data-stu-id="82312-193">ASP.NET Core MVC uses a different set of conventions from ASP.NET Web API 2.</span></span> <span data-ttu-id="82312-194">Mediante el uso de convenciones personalizadas, puede modificar el comportamiento de una aplicación ASP.NET Core MVC para que sea coherente con el de una aplicación Web API.</span><span class="sxs-lookup"><span data-stu-id="82312-194">Using custom conventions, you can modify an ASP.NET Core MVC app's behavior to be consistent with that of a Web API app.</span></span> <span data-ttu-id="82312-195">Microsoft suministra [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) específicamente para este propósito.</span><span class="sxs-lookup"><span data-stu-id="82312-195">Microsoft ships the [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) specifically for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="82312-196">Obtenga más información sobre la [migración desde ASP.NET Web API](xref:migration/webapi).</span><span class="sxs-lookup"><span data-stu-id="82312-196">Learn more about [migration from ASP.NET Web API](xref:migration/webapi).</span></span>

<span data-ttu-id="82312-197">Para usar las correcciones de compatibilidad (shim) de Web API, debe agregar el paquete al proyecto y, después, agregar las convenciones a MVC mediante una llamada a `AddWebApiConventions` en `Startup`:</span><span class="sxs-lookup"><span data-stu-id="82312-197">To use the Web API Compatibility Shim, you need to add the package to your project and then add the conventions to MVC by calling `AddWebApiConventions` in `Startup`:</span></span>

```csharp
services.AddMvc().AddWebApiConventions();
```

<span data-ttu-id="82312-198">Las convenciones proporcionadas por las correcciones de compatibilidad solo se aplican a las partes de la aplicación a las que se han aplicado ciertos atributos.</span><span class="sxs-lookup"><span data-stu-id="82312-198">The conventions provided by the shim are only applied to parts of the app that have had certain attributes applied to them.</span></span> <span data-ttu-id="82312-199">Los cuatro atributos siguientes se usan para controlar en qué controladores se deben modificar las convenciones mediante las convenciones de las correcciones de compatibilidad:</span><span class="sxs-lookup"><span data-stu-id="82312-199">The following four attributes are used to control which controllers should have their conventions modified by the shim's conventions:</span></span>

* [<span data-ttu-id="82312-200">UseWebApiActionConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="82312-200">UseWebApiActionConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [<span data-ttu-id="82312-201">UseWebApiOverloadingAttribute</span><span class="sxs-lookup"><span data-stu-id="82312-201">UseWebApiOverloadingAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [<span data-ttu-id="82312-202">UseWebApiParameterConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="82312-202">UseWebApiParameterConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [<span data-ttu-id="82312-203">UseWebApiRoutesAttribute</span><span class="sxs-lookup"><span data-stu-id="82312-203">UseWebApiRoutesAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a><span data-ttu-id="82312-204">Convenciones de acción</span><span class="sxs-lookup"><span data-stu-id="82312-204">Action Conventions</span></span>

<span data-ttu-id="82312-205">`UseWebApiActionConventionsAttribute` se usa para asignar el método HTTP a las acciones según su nombre (por ejemplo, `Get` se asignaría a `HttpGet`).</span><span class="sxs-lookup"><span data-stu-id="82312-205">The `UseWebApiActionConventionsAttribute` is used to map the HTTP method to actions based on their name (for instance, `Get` would map to `HttpGet`).</span></span> <span data-ttu-id="82312-206">Solo se aplica a las acciones que no usan el enrutamiento de atributos.</span><span class="sxs-lookup"><span data-stu-id="82312-206">It only applies to actions that don't use attribute routing.</span></span>

### <a name="overloading"></a><span data-ttu-id="82312-207">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="82312-207">Overloading</span></span>

<span data-ttu-id="82312-208">`UseWebApiOverloadingAttribute` se usa para aplicar la convención `WebApiOverloadingApplicationModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="82312-208">The `UseWebApiOverloadingAttribute` is used to apply the `WebApiOverloadingApplicationModelConvention` convention.</span></span> <span data-ttu-id="82312-209">Esta convención agrega `OverloadActionConstraint` al proceso de selección de acciones, que limita las acciones candidatas a aquellas en las que la solicitud cumple todos los parámetros no opcionales.</span><span class="sxs-lookup"><span data-stu-id="82312-209">This convention adds an `OverloadActionConstraint` to the action selection process, which limits candidate actions to those for which the request satisfies all non-optional parameters.</span></span>

### <a name="parameter-conventions"></a><span data-ttu-id="82312-210">Convenciones de parámetro</span><span class="sxs-lookup"><span data-stu-id="82312-210">Parameter Conventions</span></span>

<span data-ttu-id="82312-211">`UseWebApiParameterConventionsAttribute` se usa para aplicar la convención de acción `WebApiParameterConventionsApplicationModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="82312-211">The `UseWebApiParameterConventionsAttribute` is used to apply the `WebApiParameterConventionsApplicationModelConvention` action convention.</span></span> <span data-ttu-id="82312-212">Esta convención especifica que los tipos simples usados como parámetros de acción se enlazan desde el URI de forma predeterminada, mientras que los tipos complejos se enlazan desde el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="82312-212">This convention specifies that simple types used as action parameters are bound from the URI by default, while complex types are bound from the request body.</span></span>

### <a name="routes"></a><span data-ttu-id="82312-213">Rutas</span><span class="sxs-lookup"><span data-stu-id="82312-213">Routes</span></span>

<span data-ttu-id="82312-214">`UseWebApiRoutesAttribute` controla si se ha aplicado la convención de controlador `WebApiApplicationModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="82312-214">The `UseWebApiRoutesAttribute` controls whether the `WebApiApplicationModelConvention` controller convention is applied.</span></span> <span data-ttu-id="82312-215">Cuando se habilita, esta convención se usa para agregar a la ruta compatibilidad con [áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="82312-215">When enabled, this convention is used to add support for [areas](xref:mvc/controllers/areas) to the route.</span></span>

<span data-ttu-id="82312-216">Además de un conjunto de convenciones, el paquete de compatibilidad incluye una clase base `System.Web.Http.ApiController` que reemplaza la que proporciona Web API.</span><span class="sxs-lookup"><span data-stu-id="82312-216">In addition to a set of conventions, the compatibility package includes a `System.Web.Http.ApiController` base class that replaces the one provided by Web API.</span></span> <span data-ttu-id="82312-217">Esto permite que los controladores escritos para Web API y que heredan de `ApiController` funcionen de conformidad con su diseño, mientras se ejecutan en ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="82312-217">This allows your controllers written for Web API and inheriting from its `ApiController` to work as they were designed, while running on ASP.NET Core MVC.</span></span> <span data-ttu-id="82312-218">Todos los atributos `UseWebApi*` enumerados anteriormente se aplican a la clase de controlador base.</span><span class="sxs-lookup"><span data-stu-id="82312-218">All of the `UseWebApi*` attributes listed earlier are applied to the base controller class.</span></span> <span data-ttu-id="82312-219">`ApiController` expone propiedades, métodos y tipos de resultados compatibles con los que se encuentran en Web API.</span><span class="sxs-lookup"><span data-stu-id="82312-219">The `ApiController` exposes properties, methods, and result types that are compatible with those found in Web API.</span></span>

## <a name="using-apiexplorer-to-document-your-app"></a><span data-ttu-id="82312-220">Uso de ApiExplorer para documentar la aplicación</span><span class="sxs-lookup"><span data-stu-id="82312-220">Using ApiExplorer to Document Your App</span></span>

<span data-ttu-id="82312-221">El modelo de aplicación expone una [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) propiedad en cada nivel que se puede usar para recorrer la estructura de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="82312-221">The application model exposes an [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) property at each level that can be used to traverse the app's structure.</span></span> <span data-ttu-id="82312-222">Esto se puede usar para [generar páginas de ayuda para las Web API mediante el uso de herramientas como Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="82312-222">This can be used to [generate help pages for your Web APIs using tools like Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="82312-223">La propiedad `ApiExplorer` expone una propiedad `IsVisible` que se puede establecer para especificar qué partes del modelo de la aplicación deben exponerse.</span><span class="sxs-lookup"><span data-stu-id="82312-223">The `ApiExplorer` property exposes an `IsVisible` property that can be set to specify which parts of your app's model should be exposed.</span></span> <span data-ttu-id="82312-224">Puede configurar esta opción mediante una convención:</span><span class="sxs-lookup"><span data-stu-id="82312-224">You can configure this setting using a convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

<span data-ttu-id="82312-225">Mediante el uso de este enfoque (y de convenciones adicionales si es necesario), puede habilitar o deshabilitar las visibilidad de API en cualquier nivel dentro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="82312-225">Using this approach (and additional conventions if required), you can enable or disable API visibility at any level within your app.</span></span> 
