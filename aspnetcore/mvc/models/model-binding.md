---
title: Enlace de modelos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo funciona el enlace de modelos en ASP.NET Core y cómo personalizar su comportamiento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: 4de34a75da932b41190caa8434ac5be8cc0710fd
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981939"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="bbb14-103">Enlace de modelos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbb14-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bbb14-104">En este artículo se explica qué es el enlace de modelos, cómo funciona y cómo personalizar su comportamiento.</span><span class="sxs-lookup"><span data-stu-id="bbb14-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="bbb14-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bbb14-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="bbb14-106">Qué es el enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="bbb14-106">What is Model binding</span></span>

<span data-ttu-id="bbb14-107">Los controladores y Razor las páginas funcionan con los datos que proceden de las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bbb14-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="bbb14-108">Por ejemplo, los datos de ruta pueden proporcionar una clave de registro y los campos de formulario publicados pueden proporcionar valores para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="bbb14-109">La escritura de código para recuperar cada uno de estos valores y convertirlos de cadenas a tipos de .NET sería tediosa y propensa a errores.</span><span class="sxs-lookup"><span data-stu-id="bbb14-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="bbb14-110">El enlace de modelos automatiza este proceso.</span><span class="sxs-lookup"><span data-stu-id="bbb14-110">Model binding automates this process.</span></span> <span data-ttu-id="bbb14-111">El sistema de enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="bbb14-111">The model binding system:</span></span>

* <span data-ttu-id="bbb14-112">Recupera datos de diversos orígenes, como datos de ruta, campos de formulario y cadenas de consulta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="bbb14-113">Proporciona los datos a los controladores y Razor las páginas de los parámetros de método y las propiedades públicas.</span><span class="sxs-lookup"><span data-stu-id="bbb14-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="bbb14-114">Convierte datos de cadena en tipos de .NET.</span><span class="sxs-lookup"><span data-stu-id="bbb14-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="bbb14-115">Actualiza las propiedades de tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="bbb14-116">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="bbb14-116">Example</span></span>

<span data-ttu-id="bbb14-117">Imagine que tiene el siguiente método de acción:</span><span class="sxs-lookup"><span data-stu-id="bbb14-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="bbb14-118">Y que la aplicación recibe una solicitud con esta dirección URL:</span><span class="sxs-lookup"><span data-stu-id="bbb14-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="bbb14-119">El enlace de modelos realiza los pasos siguientes después de que el sistema de enrutamiento selecciona el método de acción:</span><span class="sxs-lookup"><span data-stu-id="bbb14-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="bbb14-120">Busca el primer parámetro de `GetByID`, un entero denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="bbb14-121">Examina los orígenes disponibles en la solicitud HTTP y busca `id` = "2" en los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="bbb14-122">Convierte la cadena "2" en el entero 2.</span><span class="sxs-lookup"><span data-stu-id="bbb14-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="bbb14-123">Busca el siguiente parámetro de `GetByID`, un valor booleano denominado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="bbb14-124">Examina los orígenes y busca "DogsOnly=true" en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="bbb14-125">La coincidencia de nombres no distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="bbb14-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="bbb14-126">Convierte la cadena "true" en un valor booleano `true`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="bbb14-127">Después, el marco llama al método `GetById` y pasa 2 para el parámetro `id` y `true` para el parámetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="bbb14-128">En el ejemplo anterior, los destinos de enlace de modelos son parámetros de método que son tipos simples.</span><span class="sxs-lookup"><span data-stu-id="bbb14-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="bbb14-129">Los destinos también pueden ser las propiedades de un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="bbb14-130">Después de que cada propiedad se haya enlazado correctamente, se produce la [validación de modelos](xref:mvc/models/validation) para esa propiedad.</span><span class="sxs-lookup"><span data-stu-id="bbb14-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="bbb14-131">El registro de qué datos se han enlazado al modelo y los errores de enlace o validación se almacenan en [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) o [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="bbb14-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="bbb14-132">Para averiguar si este proceso se ha realizado correctamente, la aplicación comprueba la marca [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="bbb14-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="bbb14-133">Destinos</span><span class="sxs-lookup"><span data-stu-id="bbb14-133">Targets</span></span>

<span data-ttu-id="bbb14-134">El enlace de modelos intenta encontrar valores para los tipos de destinos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bbb14-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="bbb14-135">Parámetros del método de acción de controlador al que se enruta una solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="bbb14-136">Parámetros del Razor método de control de páginas al que se enruta una solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="bbb14-137">Propiedades públicas de un controlador o una clase `PageModel`, si se especifican mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="bbb14-138">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="bbb14-138">[BindProperty] attribute</span></span>

<span data-ttu-id="bbb14-139">Se puede aplicar a una propiedad pública de un controlador o una clase `PageModel` para hacer que el enlace de modelos tenga esa propiedad como destino:</span><span class="sxs-lookup"><span data-stu-id="bbb14-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="bbb14-140">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="bbb14-140">[BindProperties] attribute</span></span>

<span data-ttu-id="bbb14-141">Disponible en ASP.NET 2.1 Core y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="bbb14-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="bbb14-142">Se pueden aplicar a un controlador o una clase `PageModel` para indicar al enlace de modelos que seleccione como destino todas las propiedades públicas de la clase:</span><span class="sxs-lookup"><span data-stu-id="bbb14-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="bbb14-143">Enlace de modelos para solicitudes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="bbb14-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="bbb14-144">De forma predeterminada, las propiedades no se enlazan para las solicitudes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="bbb14-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="bbb14-145">Normalmente, todo lo que necesita para una solicitud GET es un parámetro de id. de registro.</span><span class="sxs-lookup"><span data-stu-id="bbb14-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="bbb14-146">El id. de registro se usa para buscar el elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="bbb14-147">Por tanto, no es necesario enlazar una propiedad que contiene una instancia del modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="bbb14-148">En escenarios donde quiera propiedades enlazadas a datos de las solicitudes GET, establezca la propiedad `SupportsGet` en `true`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="bbb14-149">Orígenes</span><span class="sxs-lookup"><span data-stu-id="bbb14-149">Sources</span></span>

<span data-ttu-id="bbb14-150">De forma predeterminada, el enlace de modelos obtiene datos en forma de pares clave-valor de los siguientes orígenes de una solicitud HTTP:</span><span class="sxs-lookup"><span data-stu-id="bbb14-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="bbb14-151">Campos de formulario</span><span class="sxs-lookup"><span data-stu-id="bbb14-151">Form fields</span></span>
1. <span data-ttu-id="bbb14-152">El cuerpo de la solicitud (para [controladores que tienen el atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="bbb14-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="bbb14-153">Datos de ruta</span><span class="sxs-lookup"><span data-stu-id="bbb14-153">Route data</span></span>
1. <span data-ttu-id="bbb14-154">Parámetros de cadena de consulta</span><span class="sxs-lookup"><span data-stu-id="bbb14-154">Query string parameters</span></span>
1. <span data-ttu-id="bbb14-155">Archivos cargados</span><span class="sxs-lookup"><span data-stu-id="bbb14-155">Uploaded files</span></span>

<span data-ttu-id="bbb14-156">Para cada parámetro o propiedad de destino, se examinan los orígenes en el orden indicado en la lista anterior.</span><span class="sxs-lookup"><span data-stu-id="bbb14-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="bbb14-157">Hay algunas excepciones:</span><span class="sxs-lookup"><span data-stu-id="bbb14-157">There are a few exceptions:</span></span>

* <span data-ttu-id="bbb14-158">Los datos de ruta y los valores de cadena de consulta solo se usan para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="bbb14-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="bbb14-159">Los archivos cargados solo se enlazan a tipos de destino que implementan `IFormFile` o `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="bbb14-160">Si el origen predeterminado no es correcto, use uno de los atributos siguientes para especificar el origen:</span><span class="sxs-lookup"><span data-stu-id="bbb14-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="bbb14-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) : Obtiene los valores de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="bbb14-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) : Obtiene los valores de los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="bbb14-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) : Obtiene los valores de los campos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="bbb14-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) : Obtiene valores del cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="bbb14-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) : Obtiene valores de los encabezados HTTP.</span><span class="sxs-lookup"><span data-stu-id="bbb14-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="bbb14-166">Estos atributos:</span><span class="sxs-lookup"><span data-stu-id="bbb14-166">These attributes:</span></span>

* <span data-ttu-id="bbb14-167">Se agregan de forma individual a las propiedades del modelo (no a la clase de modelo), como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bbb14-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="bbb14-168">Opcionalmente, acepte un valor de nombre de modelo en el constructor.</span><span class="sxs-lookup"><span data-stu-id="bbb14-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="bbb14-169">Esta opción se proporciona en caso de que el nombre de la propiedad no coincida con el valor de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="bbb14-170">Por ejemplo, es posible que el valor de la solicitud sea un encabezado con un guion en el nombre, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bbb14-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="bbb14-171">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="bbb14-171">[FromBody] attribute</span></span>

<span data-ttu-id="bbb14-172">Aplique el atributo `[FromBody]` a un parámetro para rellenar sus propiedades desde el cuerpo de una solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bbb14-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="bbb14-173">El runtime de ASP.NET Core delega la responsabilidad de leer el cuerpo al formateador de entrada.</span><span class="sxs-lookup"><span data-stu-id="bbb14-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="bbb14-174">Los formateadores de entrada se explican [más adelante en este artículo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bbb14-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="bbb14-175">Cuando se aplica `[FromBody]` a un parámetro de tipo complejo, se omiten los atributos de origen de enlace aplicados a sus propiedades.</span><span class="sxs-lookup"><span data-stu-id="bbb14-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="bbb14-176">Por ejemplo, la acción `Create` siguiente especifica que su parámetro `pet` se rellena a partir del cuerpo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="bbb14-177">La clase `Pet` especifica que su propiedad `Breed` se rellena a partir de un parámetro de cadena de consulta:</span><span class="sxs-lookup"><span data-stu-id="bbb14-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="bbb14-178">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="bbb14-178">In the preceding example:</span></span>

* <span data-ttu-id="bbb14-179">El atributo `[FromQuery]` se ignora.</span><span class="sxs-lookup"><span data-stu-id="bbb14-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="bbb14-180">La propiedad `Breed` no se rellena desde un parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="bbb14-181">Los formateadores de entrada solo leen el cuerpo y no entienden los atributos de origen de enlace.</span><span class="sxs-lookup"><span data-stu-id="bbb14-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="bbb14-182">Si se encuentra un valor adecuado en el cuerpo, ese valor se usa para rellenar la propiedad `Breed`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="bbb14-183">No aplique `[FromBody]` a más de un parámetro por método de acción.</span><span class="sxs-lookup"><span data-stu-id="bbb14-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="bbb14-184">Una vez que un formateador de entrada ha leído la secuencia de solicitudes, deja de estar disponible para una nueva lectura con el fin de enlazar otros parámetros `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="bbb14-185">Orígenes adicionales</span><span class="sxs-lookup"><span data-stu-id="bbb14-185">Additional sources</span></span>

<span data-ttu-id="bbb14-186">Los *proveedores de valores* proporcionan datos de origen al sistema de enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="bbb14-187">Puede escribir y registrar proveedores de valores personalizados que obtienen datos de otros orígenes para el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="bbb14-188">Por ejemplo, puede que desee datos de cookie o estado de sesión.</span><span class="sxs-lookup"><span data-stu-id="bbb14-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="bbb14-189">Para obtener datos desde un origen nuevo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-189">To get data from a new source:</span></span>

* <span data-ttu-id="bbb14-190">Cree una clase que implemente `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="bbb14-191">Cree una clase que implemente `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="bbb14-192">Registre la clase de generador en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="bbb14-193">La aplicación de ejemplo incluye un [proveedor de valores](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) y un ejemplo de [fábrica](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtiene valores de cookie s.</span><span class="sxs-lookup"><span data-stu-id="bbb14-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="bbb14-194">Este es el código de registro de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="bbb14-195">En el código mostrado, el proveedor de valor personalizado se coloca después de todos los proveedores de valor integrados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="bbb14-196">Para que sea el primero en la lista, llame a `Insert(0, new CookieValueProviderFactory())` en lugar de a `Add`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="bbb14-197">No hay origen para una propiedad de modelo</span><span class="sxs-lookup"><span data-stu-id="bbb14-197">No source for a model property</span></span>

<span data-ttu-id="bbb14-198">De forma predeterminada, si no se encuentra ningún valor para una propiedad de modelo no se crea un error de estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="bbb14-199">La propiedad se establece en NULL o en un valor predeterminado:</span><span class="sxs-lookup"><span data-stu-id="bbb14-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="bbb14-200">Los tipos simples que aceptan valores NULL se establecen en `null`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="bbb14-201">Los tipos de valor que no aceptan valores NULL se establecen en `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="bbb14-202">Por ejemplo, un parámetro `int id` se establece en 0.</span><span class="sxs-lookup"><span data-stu-id="bbb14-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="bbb14-203">Para los tipos complejos, el enlace de modelos crea una instancia mediante el constructor predeterminado, sin establecer propiedades.</span><span class="sxs-lookup"><span data-stu-id="bbb14-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="bbb14-204">Las matrices se establecen en `Array.Empty<T>()`, salvo las matrices `byte[]`, que se establecen en `null`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="bbb14-205">Si el estado del modelo se debe invalidar Cuando no se encuentra nada en los campos de formulario de una propiedad de modelo, utilice el [`[BindRequired]`](#bindrequired-attribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="bbb14-206">Tenga en cuenta que este comportamiento de `[BindRequired]` se aplica al enlace de modelos desde datos de formulario publicados, no a los datos JSON o XML del cuerpo de una solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="bbb14-207">Los datos del cuerpo de la solicitud se controlan mediante [formateadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bbb14-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="bbb14-208">Errores de la conversión de tipos</span><span class="sxs-lookup"><span data-stu-id="bbb14-208">Type conversion errors</span></span>

<span data-ttu-id="bbb14-209">Si se encuentra un origen pero no se puede convertir al tipo de destino, el estado del modelo se marca como no válido.</span><span class="sxs-lookup"><span data-stu-id="bbb14-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="bbb14-210">El parámetro o la propiedad de destino se establece en NULL o en un valor predeterminado, como se ha indicado en la sección anterior.</span><span class="sxs-lookup"><span data-stu-id="bbb14-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="bbb14-211">En un controlador de API que tenga el atributo `[ApiController]`, el estado de modelo no válido genera una respuesta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="bbb14-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="bbb14-212">En una Razor página, vuelva a mostrar la página con un mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="bbb14-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="bbb14-213">La validación del lado cliente detecta la mayoría de los datos incorrectos que de otro modo se enviarían a un Razor formulario de páginas.</span><span class="sxs-lookup"><span data-stu-id="bbb14-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="bbb14-214">Esta validación dificulta que se pueda desencadenar el código resaltado anterior.</span><span class="sxs-lookup"><span data-stu-id="bbb14-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="bbb14-215">En la aplicación de ejemplo se incluye un botón **Submit with Invalid Date** (Enviar con fecha no válida) que agrega datos incorrectos al campo **Hire Date** (Fecha de contratación) y envía el formulario.</span><span class="sxs-lookup"><span data-stu-id="bbb14-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="bbb14-216">Este botón muestra cómo funciona el código para volver a mostrar la página cuando se producen errores de conversión de datos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="bbb14-217">Cuando el código anterior vuelve a mostrar la página, no se muestra la entrada no válida en el campo de formulario.</span><span class="sxs-lookup"><span data-stu-id="bbb14-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="bbb14-218">El motivo es que la propiedad de modelo se ha establecido en NULL o en un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="bbb14-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="bbb14-219">La entrada no válida sí aparece en un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="bbb14-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="bbb14-220">Pero si quiere volver a mostrar los datos incorrectos en el campo de formulario, considere la posibilidad de convertir la propiedad de modelo en una cadena y realizar la conversión de datos de forma manual.</span><span class="sxs-lookup"><span data-stu-id="bbb14-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="bbb14-221">Se recomienda la misma estrategia si no quiere que los errores de conversión de tipo generen errores de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="bbb14-222">En ese caso, convierta la propiedad de modelo en una cadena.</span><span class="sxs-lookup"><span data-stu-id="bbb14-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="bbb14-223">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="bbb14-223">Simple types</span></span>

<span data-ttu-id="bbb14-224">Los tipos simples a los que el enlazador de modelos puede convertir las cadenas de origen incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="bbb14-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="bbb14-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="bbb14-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="bbb14-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="bbb14-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="bbb14-227">Char</span><span class="sxs-lookup"><span data-stu-id="bbb14-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="bbb14-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="bbb14-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="bbb14-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="bbb14-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="bbb14-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="bbb14-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="bbb14-231">Double</span><span class="sxs-lookup"><span data-stu-id="bbb14-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="bbb14-232">Enum</span><span class="sxs-lookup"><span data-stu-id="bbb14-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="bbb14-233">Volumen</span><span class="sxs-lookup"><span data-stu-id="bbb14-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="bbb14-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="bbb14-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="bbb14-235">Single</span><span class="sxs-lookup"><span data-stu-id="bbb14-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="bbb14-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="bbb14-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="bbb14-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="bbb14-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="bbb14-238">Uri</span><span class="sxs-lookup"><span data-stu-id="bbb14-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="bbb14-239">Versión</span><span class="sxs-lookup"><span data-stu-id="bbb14-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="bbb14-240">Tipos complejos</span><span class="sxs-lookup"><span data-stu-id="bbb14-240">Complex types</span></span>

<span data-ttu-id="bbb14-241">Un tipo complejo debe tener un constructor público predeterminado y propiedades grabables públicas para enlazar.</span><span class="sxs-lookup"><span data-stu-id="bbb14-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="bbb14-242">Cuando se produce el enlace de modelos, se crea una instancia de la clase con el constructor predeterminado público.</span><span class="sxs-lookup"><span data-stu-id="bbb14-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="bbb14-243">Para cada propiedad del tipo complejo, el enlace de modelos busca entre los orígenes el patrón de nombre *prefijo.nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="bbb14-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="bbb14-244">Si no se encuentra nada, solo busca *nombre_de_propiedad* sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="bbb14-245">Para el enlace a un parámetro, el prefijo es el nombre del parámetro.</span><span class="sxs-lookup"><span data-stu-id="bbb14-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="bbb14-246">Para el enlace a una propiedad pública `PageModel`, el prefijo es el nombre de la propiedad pública.</span><span class="sxs-lookup"><span data-stu-id="bbb14-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="bbb14-247">Algunos atributos tienen una propiedad `Prefix` que permite invalidar el uso predeterminado del nombre de parámetro o propiedad.</span><span class="sxs-lookup"><span data-stu-id="bbb14-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="bbb14-248">Por ejemplo, imagine que el tipo complejo es la clase `Instructor` siguiente:</span><span class="sxs-lookup"><span data-stu-id="bbb14-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="bbb14-249">Prefijo = nombre del parámetro</span><span class="sxs-lookup"><span data-stu-id="bbb14-249">Prefix = parameter name</span></span>

<span data-ttu-id="bbb14-250">Si el modelo que se va a enlazar es un parámetro denominado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="bbb14-251">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="bbb14-252">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="bbb14-253">Prefijo = nombre de propiedad</span><span class="sxs-lookup"><span data-stu-id="bbb14-253">Prefix = property name</span></span>

<span data-ttu-id="bbb14-254">Si el modelo que se va a enlazar es una propiedad denominada `Instructor` del controlador o la clase `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="bbb14-255">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="bbb14-256">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="bbb14-257">Prefijo personalizado</span><span class="sxs-lookup"><span data-stu-id="bbb14-257">Custom prefix</span></span>

<span data-ttu-id="bbb14-258">Si el modelo que se va a enlazar es un parámetro denominado `instructorToUpdate` y un atributo `Bind`, especifica `Instructor` como el prefijo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="bbb14-259">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="bbb14-260">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="bbb14-261">Atributos para destinos de tipo complejo</span><span class="sxs-lookup"><span data-stu-id="bbb14-261">Attributes for complex type targets</span></span>

<span data-ttu-id="bbb14-262">Existen varios atributos integrados para controlar el enlace de modelos de tipos complejos:</span><span class="sxs-lookup"><span data-stu-id="bbb14-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="bbb14-263">Estos atributos afectan al enlace de modelos cuando el origen de los valores son datos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="bbb14-264">**No afectan** a los formateadores de entrada, que procesan los cuerpos de solicitud JSON y XML publicados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-264">They do \***not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="bbb14-265">Los formateadores de entrada se explican [más adelante en este artículo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bbb14-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="bbb14-266">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="bbb14-266">[Bind] attribute</span></span>

<span data-ttu-id="bbb14-267">Se puede aplicar a una clase o un parámetro de método.</span><span class="sxs-lookup"><span data-stu-id="bbb14-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="bbb14-268">Especifica qué propiedades de un modelo se deben incluir en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="bbb14-269">`[Bind]`_*_no_*_ afecta a los formateadores de entrada.</span><span class="sxs-lookup"><span data-stu-id="bbb14-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="bbb14-270">En el ejemplo siguiente, solo se enlazan las propiedades especificadas del modelo `Instructor` cuando se llama a cualquier método de acción o controlador:</span><span class="sxs-lookup"><span data-stu-id="bbb14-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="bbb14-271">En el ejemplo siguiente, solo se enlazan las propiedades especificadas del modelo `Instructor` cuando se llama al método `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="bbb14-272">El `[Bind]` atributo se puede usar para protegerse frente a la publicación en escenarios _create \*.</span><span class="sxs-lookup"><span data-stu-id="bbb14-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="bbb14-273">No funciona bien en escenarios de edición porque las propiedades excluidas se establecen en NULL o en un valor predeterminado en lugar de mantenerse sin cambios.</span><span class="sxs-lookup"><span data-stu-id="bbb14-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="bbb14-274">Para defenderse de la publicación excesiva, se recomiendan modelos de vista en lugar del atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="bbb14-275">Para más información, vea [Nota de seguridad sobre la publicación excesiva](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="bbb14-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="bbb14-276">Atributo [ModelBinder]</span><span class="sxs-lookup"><span data-stu-id="bbb14-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="bbb14-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> se puede aplicar a tipos, propiedades o parámetros.</span><span class="sxs-lookup"><span data-stu-id="bbb14-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="bbb14-278">Permite especificar el tipo de enlazador de modelos que se usa para enlazar el tipo o la instancia específica.</span><span class="sxs-lookup"><span data-stu-id="bbb14-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="bbb14-279">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="bbb14-280">El `[ModelBinder]` atributo también se puede utilizar para cambiar el nombre de una propiedad o parámetro cuando se enlaza a un modelo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="bbb14-281">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="bbb14-281">[BindRequired] attribute</span></span>

<span data-ttu-id="bbb14-282">Solo se puede aplicar a propiedades del modelo, no a parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="bbb14-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="bbb14-283">Hace que el enlace de modelos agregue un error de estado de modelo si no se puede realizar el enlace para la propiedad de un modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="bbb14-284">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="bbb14-285">Vea también la explicación del atributo `[Required]` en [Validación de modelos](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="bbb14-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="bbb14-286">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="bbb14-286">[BindNever] attribute</span></span>

<span data-ttu-id="bbb14-287">Solo se puede aplicar a propiedades del modelo, no a parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="bbb14-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="bbb14-288">Impide que el enlace de modelos establezca la propiedad de un modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="bbb14-289">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="bbb14-290">Colecciones</span><span class="sxs-lookup"><span data-stu-id="bbb14-290">Collections</span></span>

<span data-ttu-id="bbb14-291">Para los destinos que son colecciones de tipos simples, el enlace de modelos busca coincidencias con *nombre_de_parámetro* o *nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="bbb14-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="bbb14-292">Si no se encuentra ninguna coincidencia, busca uno de los formatos admitidos sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="bbb14-293">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-293">For example:</span></span>

* <span data-ttu-id="bbb14-294">Imagine que el parámetro que se va a enlazar es una matriz llamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="bbb14-295">Los datos de cadena de consulta o formulario pueden estar en uno de los formatos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bbb14-295">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="bbb14-296">El formato siguiente solo se admite en datos de formulario:</span><span class="sxs-lookup"><span data-stu-id="bbb14-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="bbb14-297">Para todos los formatos de ejemplo anteriores, el enlace de modelos pasa una matriz de dos elementos al parámetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="bbb14-298">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="bbb14-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="bbb14-299">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="bbb14-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="bbb14-300">Los formatos de datos que usan números de subíndice (... [0] ... [1] ...) deben asegurarse de que se numeran de forma secuencial a partir de cero.</span><span class="sxs-lookup"><span data-stu-id="bbb14-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="bbb14-301">Si hay algún hueco en la numeración de los subíndices, se omiten todos los elementos que aparecen después del hueco.</span><span class="sxs-lookup"><span data-stu-id="bbb14-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="bbb14-302">Por ejemplo, si los subíndices son 0 y 2 en lugar de 0 y 1, se omite el segundo elemento.</span><span class="sxs-lookup"><span data-stu-id="bbb14-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="bbb14-303">Diccionarios</span><span class="sxs-lookup"><span data-stu-id="bbb14-303">Dictionaries</span></span>

<span data-ttu-id="bbb14-304">Para los destinos `Dictionary`, el enlace de modelos busca coincidencias con *nombre_de_parámetro* o *nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="bbb14-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="bbb14-305">Si no se encuentra ninguna coincidencia, busca uno de los formatos admitidos sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="bbb14-306">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-306">For example:</span></span>

* <span data-ttu-id="bbb14-307">Imagine que el parámetro de destino es un elemento `Dictionary<int, string>` denominado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="bbb14-308">Los datos de cadena de consulta o de formulario publicados pueden ser similares a uno de los ejemplos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bbb14-308">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="bbb14-309">Para todos los formatos de ejemplo anteriores, el enlace de modelos pasa un diccionario de dos elementos al parámetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="bbb14-310">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="bbb14-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="bbb14-311">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="bbb14-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="bbb14-312">Enlazar constructores y tipos de registro</span><span class="sxs-lookup"><span data-stu-id="bbb14-312">Constructor binding and record types</span></span>

<span data-ttu-id="bbb14-313">El enlace de modelos requiere que los tipos complejos tengan un constructor sin parámetros.</span><span class="sxs-lookup"><span data-stu-id="bbb14-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="bbb14-314">Tanto `System.Text.Json` como los `Newtonsoft.Json` formateadores de entrada basados en y admiten la deserialización de clases que no tienen un constructor sin parámetros.</span><span class="sxs-lookup"><span data-stu-id="bbb14-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="bbb14-315">C# 9 incluye tipos de registros, que son una excelente manera de representar los datos en la red.</span><span class="sxs-lookup"><span data-stu-id="bbb14-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="bbb14-316">ASP.NET Core agrega compatibilidad para el enlace de modelos y la validación de tipos de registro con un solo constructor:</span><span class="sxs-lookup"><span data-stu-id="bbb14-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="bbb14-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="bbb14-318">Al validar los tipos de registro, el tiempo de ejecución busca los metadatos de validación específicamente en los parámetros en lugar de en las propiedades.</span><span class="sxs-lookup"><span data-stu-id="bbb14-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="bbb14-319">Comportamiento de globalización del enlace de modelos datos de ruta y cadenas de consulta</span><span class="sxs-lookup"><span data-stu-id="bbb14-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="bbb14-320">El proveedor de valores de ruta de ASP.NET Core y el proveedor de valores de cadena de consulta:</span><span class="sxs-lookup"><span data-stu-id="bbb14-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="bbb14-321">Tratan los valores como referencia cultural de todos los idiomas.</span><span class="sxs-lookup"><span data-stu-id="bbb14-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="bbb14-322">Esperan que las direcciones URL sean independientes de la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="bbb14-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="bbb14-323">Por el contrario, los valores procedentes de datos de formulario se someten a una conversión que tiene en cuenta la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="bbb14-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="bbb14-324">Esto es así por diseño, para que las direcciones URL se puedan compartir entre configuraciones regionales.</span><span class="sxs-lookup"><span data-stu-id="bbb14-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="bbb14-325">Para que el proveedor de valores de ruta de ASP.NET Core y el proveedor de valores de cadena de consulta se sometan a una conversión dependiente de la referencia cultural:</span><span class="sxs-lookup"><span data-stu-id="bbb14-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="bbb14-326">Heredan de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="bbb14-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="bbb14-327">Copie el código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) o [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="bbb14-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="bbb14-328">Reemplace el [valor de referencia cultural](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) pasado al constructor de proveedor de valores con [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="bbb14-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="bbb14-329">Reemplace el generador de proveedores de valor predeterminado en las opciones de MVC por el nuevo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="bbb14-330">Tipos de datos especiales</span><span class="sxs-lookup"><span data-stu-id="bbb14-330">Special data types</span></span>

<span data-ttu-id="bbb14-331">Hay algunos tipos de datos especiales que el enlace de modelos puede controlar.</span><span class="sxs-lookup"><span data-stu-id="bbb14-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="bbb14-332">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="bbb14-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="bbb14-333">Un archivo cargado incluido en la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bbb14-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="bbb14-334">También se admite `IEnumerable<IFormFile>` para varios archivos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="bbb14-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="bbb14-335">CancellationToken</span></span>

<span data-ttu-id="bbb14-336">Opcionalmente, las acciones pueden enlazar un `CancellationToken` como parámetro.</span><span class="sxs-lookup"><span data-stu-id="bbb14-336">Actions can optionally bind a `CancellationToken` as a parameter.</span></span> <span data-ttu-id="bbb14-337">Esto enlaza <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> que señala Cuándo se anula la conexión subyacente a la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bbb14-337">This binds <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> that signals when the connection underlying the HTTP request is aborted.</span></span> <span data-ttu-id="bbb14-338">Las acciones pueden usar este parámetro para cancelar operaciones asincrónicas de larga ejecución que se ejecutan como parte de las acciones de controlador.</span><span class="sxs-lookup"><span data-stu-id="bbb14-338">Actions can use this parameter to cancel long running async operations that are executed as part of the controller actions.</span></span>

### <a name="formcollection"></a><span data-ttu-id="bbb14-339">FormCollection</span><span class="sxs-lookup"><span data-stu-id="bbb14-339">FormCollection</span></span>

<span data-ttu-id="bbb14-340">Se usa para recuperar todos los valores de los datos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-340">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="bbb14-341">Formateadores de entrada</span><span class="sxs-lookup"><span data-stu-id="bbb14-341">Input formatters</span></span>

<span data-ttu-id="bbb14-342">Los datos del cuerpo de la solicitud pueden estar en XML, JSON u otro formato.</span><span class="sxs-lookup"><span data-stu-id="bbb14-342">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="bbb14-343">Para analizar estos datos, el enlace de modelos usa un *formateador de entrada* configurado para controlar un tipo de contenido determinado.</span><span class="sxs-lookup"><span data-stu-id="bbb14-343">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="bbb14-344">De forma predeterminada, en ASP.NET Core se incluyen formateadores de entrada basados en JSON para el control de los datos JSON.</span><span class="sxs-lookup"><span data-stu-id="bbb14-344">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="bbb14-345">Puede agregar otros formateadores para otros tipos de contenido.</span><span class="sxs-lookup"><span data-stu-id="bbb14-345">You can add other formatters for other content types.</span></span>

<span data-ttu-id="bbb14-346">ASP.NET Core selecciona los formateadores de entrada en función del atributo [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="bbb14-346">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="bbb14-347">Si no hay ningún atributo, usa el [encabezado Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="bbb14-347">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="bbb14-348">Para usar los formateadores de entrada XML integrados:</span><span class="sxs-lookup"><span data-stu-id="bbb14-348">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="bbb14-349">Instale el paquete NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-349">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="bbb14-350">En `Startup.ConfigureServices`, llame a <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> o a <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="bbb14-350">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="bbb14-351">Aplique el atributo `Consumes` a las clases de controlador o los métodos de acción que deben esperar XML en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-351">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="bbb14-352">Para más información, vea [Introducción de la serialización XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="bbb14-352">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="bbb14-353">Personalización del enlace de modelos con formateadores de entrada</span><span class="sxs-lookup"><span data-stu-id="bbb14-353">Customize model binding with input formatters</span></span>

<span data-ttu-id="bbb14-354">Un formateador de entrada asume toda la responsabilidad de leer datos del cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-354">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="bbb14-355">Para personalizar este proceso, configure las API que usa el formateador de entrada.</span><span class="sxs-lookup"><span data-stu-id="bbb14-355">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="bbb14-356">En esta sección se describe cómo personalizar el formateador de entrada basado en `System.Text.Json` para entender un tipo personalizado denominado `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-356">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="bbb14-357">Considere el modelo siguiente, que contiene una propiedad `ObjectId` denominada `Id`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-357">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="bbb14-358">Para personalizar el proceso de enlace de modelos al usar `System.Text.Json`, cree una clase derivada de <xref:System.Text.Json.Serialization.JsonConverter%601>:</span><span class="sxs-lookup"><span data-stu-id="bbb14-358">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="bbb14-359">Para usar un convertidor personalizado, aplique el atributo <xref:System.Text.Json.Serialization.JsonConverterAttribute> al tipo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-359">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="bbb14-360">En el ejemplo siguiente, el tipo `ObjectId` se configura con `ObjectIdConverter` como su convertidor personalizado:</span><span class="sxs-lookup"><span data-stu-id="bbb14-360">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="bbb14-361">Para más información, consulte [Procedimientos para escribir convertidores personalizados](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="bbb14-361">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="bbb14-362">Exclusión de tipos especificados del enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="bbb14-362">Exclude specified types from model binding</span></span>

<span data-ttu-id="bbb14-363">El comportamiento del enlace de modelos y los sistemas de validación se controla mediante [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="bbb14-363">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="bbb14-364">Puede personalizar `ModelMetadata` mediante la adición de un proveedor de detalles a [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="bbb14-364">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="bbb14-365">Los proveedores de detalles integrados están disponibles para deshabilitar el enlace de modelos o la validación para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-365">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="bbb14-366">Para deshabilitar el enlace de modelos en todos los modelos de un tipo especificado, agregue una instancia de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-366">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bbb14-367">Por ejemplo, para deshabilitar el enlace de modelos en todos los modelos del tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-367">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="bbb14-368">Para deshabilitar la validación en propiedades de un tipo especificado, agregue una instancia de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-368">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bbb14-369">Por ejemplo, para deshabilitar la validación en las propiedades de tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-369">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="bbb14-370">Enlazadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="bbb14-370">Custom model binders</span></span>

<span data-ttu-id="bbb14-371">Puede ampliar el enlace de modelos si escribe un enlazador de modelos personalizado y usa el atributo `[ModelBinder]` para seleccionarlo para un destino concreto.</span><span class="sxs-lookup"><span data-stu-id="bbb14-371">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="bbb14-372">Más información sobre el [enlace de modelos personalizados](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="bbb14-372">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="bbb14-373">Enlace de modelos manual</span><span class="sxs-lookup"><span data-stu-id="bbb14-373">Manual model binding</span></span> 

<span data-ttu-id="bbb14-374">El enlace de modelos se puede invocar de forma manual mediante el método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="bbb14-374">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="bbb14-375">El método se define en las clases `ControllerBase` y `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-375">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="bbb14-376">Las sobrecargas de método permiten especificar el prefijo y el proveedor de valores que se van a usar.</span><span class="sxs-lookup"><span data-stu-id="bbb14-376">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="bbb14-377">El método devuelve `false` si se produce un error en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-377">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="bbb14-378">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-378">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="bbb14-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> usa proveedores de valor para obtener datos del cuerpo del formulario, la cadena de consulta y los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="bbb14-380">`TryUpdateModelAsync` suele ser:</span><span class="sxs-lookup"><span data-stu-id="bbb14-380">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="bbb14-381">Se usa con Razor las páginas y las aplicaciones MVC con controladores y vistas para evitar la publicación excesiva.</span><span class="sxs-lookup"><span data-stu-id="bbb14-381">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="bbb14-382">No se usa con una API web a menos que se consuma a partir de datos de formulario, cadenas de consulta y datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-382">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="bbb14-383">Los puntos de conexión de la API web que consumen JSON usan [formateadores de entrada](#input-formatters) para deserializar el cuerpo de la solicitud en un objeto.</span><span class="sxs-lookup"><span data-stu-id="bbb14-383">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="bbb14-384">Para más información, consulte [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="bbb14-384">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="bbb14-385">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="bbb14-385">[FromServices] attribute</span></span>

<span data-ttu-id="bbb14-386">El nombre de este atributo sigue el patrón de los atributos de enlace de modelos que especifican un origen de datos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-386">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="bbb14-387">Pero no se trata de enlazar datos desde un proveedor de valores.</span><span class="sxs-lookup"><span data-stu-id="bbb14-387">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="bbb14-388">Obtiene una instancia de un tipo desde el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bbb14-388">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="bbb14-389">Su objetivo es proporcionar una alternativa a la inserción de constructores cuando se necesita un servicio solo si se llama a un método concreto.</span><span class="sxs-lookup"><span data-stu-id="bbb14-389">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bbb14-390">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bbb14-390">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bbb14-391">En este artículo se explica qué es el enlace de modelos, cómo funciona y cómo personalizar su comportamiento.</span><span class="sxs-lookup"><span data-stu-id="bbb14-391">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="bbb14-392">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bbb14-392">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="bbb14-393">Qué es el enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="bbb14-393">What is Model binding</span></span>

<span data-ttu-id="bbb14-394">Los controladores y Razor las páginas funcionan con los datos que proceden de las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bbb14-394">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="bbb14-395">Por ejemplo, los datos de ruta pueden proporcionar una clave de registro y los campos de formulario publicados pueden proporcionar valores para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-395">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="bbb14-396">La escritura de código para recuperar cada uno de estos valores y convertirlos de cadenas a tipos de .NET sería tediosa y propensa a errores.</span><span class="sxs-lookup"><span data-stu-id="bbb14-396">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="bbb14-397">El enlace de modelos automatiza este proceso.</span><span class="sxs-lookup"><span data-stu-id="bbb14-397">Model binding automates this process.</span></span> <span data-ttu-id="bbb14-398">El sistema de enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="bbb14-398">The model binding system:</span></span>

* <span data-ttu-id="bbb14-399">Recupera datos de diversos orígenes, como datos de ruta, campos de formulario y cadenas de consulta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-399">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="bbb14-400">Proporciona los datos a los controladores y Razor las páginas de los parámetros de método y las propiedades públicas.</span><span class="sxs-lookup"><span data-stu-id="bbb14-400">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="bbb14-401">Convierte datos de cadena en tipos de .NET.</span><span class="sxs-lookup"><span data-stu-id="bbb14-401">Converts string data to .NET types.</span></span>
* <span data-ttu-id="bbb14-402">Actualiza las propiedades de tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-402">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="bbb14-403">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="bbb14-403">Example</span></span>

<span data-ttu-id="bbb14-404">Imagine que tiene el siguiente método de acción:</span><span class="sxs-lookup"><span data-stu-id="bbb14-404">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="bbb14-405">Y que la aplicación recibe una solicitud con esta dirección URL:</span><span class="sxs-lookup"><span data-stu-id="bbb14-405">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="bbb14-406">El enlace de modelos realiza los pasos siguientes después de que el sistema de enrutamiento selecciona el método de acción:</span><span class="sxs-lookup"><span data-stu-id="bbb14-406">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="bbb14-407">Busca el primer parámetro de `GetByID`, un entero denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-407">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="bbb14-408">Examina los orígenes disponibles en la solicitud HTTP y busca `id` = "2" en los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-408">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="bbb14-409">Convierte la cadena "2" en el entero 2.</span><span class="sxs-lookup"><span data-stu-id="bbb14-409">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="bbb14-410">Busca el siguiente parámetro de `GetByID`, un valor booleano denominado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-410">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="bbb14-411">Examina los orígenes y busca "DogsOnly=true" en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-411">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="bbb14-412">La coincidencia de nombres no distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="bbb14-412">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="bbb14-413">Convierte la cadena "true" en un valor booleano `true`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-413">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="bbb14-414">Después, el marco llama al método `GetById` y pasa 2 para el parámetro `id` y `true` para el parámetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-414">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="bbb14-415">En el ejemplo anterior, los destinos de enlace de modelos son parámetros de método que son tipos simples.</span><span class="sxs-lookup"><span data-stu-id="bbb14-415">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="bbb14-416">Los destinos también pueden ser las propiedades de un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-416">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="bbb14-417">Después de que cada propiedad se haya enlazado correctamente, se produce la [validación de modelos](xref:mvc/models/validation) para esa propiedad.</span><span class="sxs-lookup"><span data-stu-id="bbb14-417">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="bbb14-418">El registro de qué datos se han enlazado al modelo y los errores de enlace o validación se almacenan en [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) o [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="bbb14-418">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="bbb14-419">Para averiguar si este proceso se ha realizado correctamente, la aplicación comprueba la marca [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="bbb14-419">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="bbb14-420">Destinos</span><span class="sxs-lookup"><span data-stu-id="bbb14-420">Targets</span></span>

<span data-ttu-id="bbb14-421">El enlace de modelos intenta encontrar valores para los tipos de destinos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bbb14-421">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="bbb14-422">Parámetros del método de acción de controlador al que se enruta una solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-422">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="bbb14-423">Parámetros del Razor método de control de páginas al que se enruta una solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-423">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="bbb14-424">Propiedades públicas de un controlador o una clase `PageModel`, si se especifican mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-424">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="bbb14-425">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="bbb14-425">[BindProperty] attribute</span></span>

<span data-ttu-id="bbb14-426">Se puede aplicar a una propiedad pública de un controlador o una clase `PageModel` para hacer que el enlace de modelos tenga esa propiedad como destino:</span><span class="sxs-lookup"><span data-stu-id="bbb14-426">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="bbb14-427">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="bbb14-427">[BindProperties] attribute</span></span>

<span data-ttu-id="bbb14-428">Disponible en ASP.NET 2.1 Core y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="bbb14-428">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="bbb14-429">Se pueden aplicar a un controlador o una clase `PageModel` para indicar al enlace de modelos que seleccione como destino todas las propiedades públicas de la clase:</span><span class="sxs-lookup"><span data-stu-id="bbb14-429">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="bbb14-430">Enlace de modelos para solicitudes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="bbb14-430">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="bbb14-431">De forma predeterminada, las propiedades no se enlazan para las solicitudes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="bbb14-431">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="bbb14-432">Normalmente, todo lo que necesita para una solicitud GET es un parámetro de id. de registro.</span><span class="sxs-lookup"><span data-stu-id="bbb14-432">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="bbb14-433">El id. de registro se usa para buscar el elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-433">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="bbb14-434">Por tanto, no es necesario enlazar una propiedad que contiene una instancia del modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-434">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="bbb14-435">En escenarios donde quiera propiedades enlazadas a datos de las solicitudes GET, establezca la propiedad `SupportsGet` en `true`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-435">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="bbb14-436">Orígenes</span><span class="sxs-lookup"><span data-stu-id="bbb14-436">Sources</span></span>

<span data-ttu-id="bbb14-437">De forma predeterminada, el enlace de modelos obtiene datos en forma de pares clave-valor de los siguientes orígenes de una solicitud HTTP:</span><span class="sxs-lookup"><span data-stu-id="bbb14-437">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="bbb14-438">Campos de formulario</span><span class="sxs-lookup"><span data-stu-id="bbb14-438">Form fields</span></span>
1. <span data-ttu-id="bbb14-439">El cuerpo de la solicitud (para [controladores que tienen el atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="bbb14-439">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="bbb14-440">Datos de ruta</span><span class="sxs-lookup"><span data-stu-id="bbb14-440">Route data</span></span>
1. <span data-ttu-id="bbb14-441">Parámetros de cadena de consulta</span><span class="sxs-lookup"><span data-stu-id="bbb14-441">Query string parameters</span></span>
1. <span data-ttu-id="bbb14-442">Archivos cargados</span><span class="sxs-lookup"><span data-stu-id="bbb14-442">Uploaded files</span></span>

<span data-ttu-id="bbb14-443">Para cada parámetro o propiedad de destino, se examinan los orígenes en el orden indicado en la lista anterior.</span><span class="sxs-lookup"><span data-stu-id="bbb14-443">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="bbb14-444">Hay algunas excepciones:</span><span class="sxs-lookup"><span data-stu-id="bbb14-444">There are a few exceptions:</span></span>

* <span data-ttu-id="bbb14-445">Los datos de ruta y los valores de cadena de consulta solo se usan para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="bbb14-445">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="bbb14-446">Los archivos cargados solo se enlazan a tipos de destino que implementan `IFormFile` o `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-446">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="bbb14-447">Si el origen predeterminado no es correcto, use uno de los atributos siguientes para especificar el origen:</span><span class="sxs-lookup"><span data-stu-id="bbb14-447">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="bbb14-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) : Obtiene los valores de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="bbb14-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) : Obtiene los valores de los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="bbb14-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) : Obtiene los valores de los campos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="bbb14-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) : Obtiene valores del cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="bbb14-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) : Obtiene valores de los encabezados HTTP.</span><span class="sxs-lookup"><span data-stu-id="bbb14-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="bbb14-453">Estos atributos:</span><span class="sxs-lookup"><span data-stu-id="bbb14-453">These attributes:</span></span>

* <span data-ttu-id="bbb14-454">Se agregan de forma individual a las propiedades del modelo (no a la clase de modelo), como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bbb14-454">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="bbb14-455">Opcionalmente, acepte un valor de nombre de modelo en el constructor.</span><span class="sxs-lookup"><span data-stu-id="bbb14-455">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="bbb14-456">Esta opción se proporciona en caso de que el nombre de la propiedad no coincida con el valor de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-456">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="bbb14-457">Por ejemplo, es posible que el valor de la solicitud sea un encabezado con un guion en el nombre, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bbb14-457">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="bbb14-458">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="bbb14-458">[FromBody] attribute</span></span>

<span data-ttu-id="bbb14-459">Aplique el atributo `[FromBody]` a un parámetro para rellenar sus propiedades desde el cuerpo de una solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bbb14-459">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="bbb14-460">El runtime de ASP.NET Core delega la responsabilidad de leer el cuerpo al formateador de entrada.</span><span class="sxs-lookup"><span data-stu-id="bbb14-460">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="bbb14-461">Los formateadores de entrada se explican [más adelante en este artículo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bbb14-461">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="bbb14-462">Cuando se aplica `[FromBody]` a un parámetro de tipo complejo, se omiten los atributos de origen de enlace aplicados a sus propiedades.</span><span class="sxs-lookup"><span data-stu-id="bbb14-462">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="bbb14-463">Por ejemplo, la acción `Create` siguiente especifica que su parámetro `pet` se rellena a partir del cuerpo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-463">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="bbb14-464">La clase `Pet` especifica que su propiedad `Breed` se rellena a partir de un parámetro de cadena de consulta:</span><span class="sxs-lookup"><span data-stu-id="bbb14-464">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="bbb14-465">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="bbb14-465">In the preceding example:</span></span>

* <span data-ttu-id="bbb14-466">El atributo `[FromQuery]` se ignora.</span><span class="sxs-lookup"><span data-stu-id="bbb14-466">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="bbb14-467">La propiedad `Breed` no se rellena desde un parámetro de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="bbb14-467">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="bbb14-468">Los formateadores de entrada solo leen el cuerpo y no entienden los atributos de origen de enlace.</span><span class="sxs-lookup"><span data-stu-id="bbb14-468">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="bbb14-469">Si se encuentra un valor adecuado en el cuerpo, ese valor se usa para rellenar la propiedad `Breed`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-469">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="bbb14-470">No aplique `[FromBody]` a más de un parámetro por método de acción.</span><span class="sxs-lookup"><span data-stu-id="bbb14-470">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="bbb14-471">Una vez que un formateador de entrada ha leído la secuencia de solicitudes, deja de estar disponible para una nueva lectura con el fin de enlazar otros parámetros `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-471">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="bbb14-472">Orígenes adicionales</span><span class="sxs-lookup"><span data-stu-id="bbb14-472">Additional sources</span></span>

<span data-ttu-id="bbb14-473">Los *proveedores de valores* proporcionan datos de origen al sistema de enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-473">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="bbb14-474">Puede escribir y registrar proveedores de valores personalizados que obtienen datos de otros orígenes para el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-474">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="bbb14-475">Por ejemplo, puede que desee datos de cookie o estado de sesión.</span><span class="sxs-lookup"><span data-stu-id="bbb14-475">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="bbb14-476">Para obtener datos desde un origen nuevo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-476">To get data from a new source:</span></span>

* <span data-ttu-id="bbb14-477">Cree una clase que implemente `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-477">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="bbb14-478">Cree una clase que implemente `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-478">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="bbb14-479">Registre la clase de generador en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-479">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="bbb14-480">La aplicación de ejemplo incluye un [proveedor de valores](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) y un ejemplo de [fábrica](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtiene valores de cookie s.</span><span class="sxs-lookup"><span data-stu-id="bbb14-480">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="bbb14-481">Este es el código de registro de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-481">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="bbb14-482">En el código mostrado, el proveedor de valor personalizado se coloca después de todos los proveedores de valor integrados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-482">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="bbb14-483">Para que sea el primero en la lista, llame a `Insert(0, new CookieValueProviderFactory())` en lugar de a `Add`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-483">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="bbb14-484">No hay origen para una propiedad de modelo</span><span class="sxs-lookup"><span data-stu-id="bbb14-484">No source for a model property</span></span>

<span data-ttu-id="bbb14-485">De forma predeterminada, si no se encuentra ningún valor para una propiedad de modelo no se crea un error de estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-485">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="bbb14-486">La propiedad se establece en NULL o en un valor predeterminado:</span><span class="sxs-lookup"><span data-stu-id="bbb14-486">The property is set to null or a default value:</span></span>

* <span data-ttu-id="bbb14-487">Los tipos simples que aceptan valores NULL se establecen en `null`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-487">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="bbb14-488">Los tipos de valor que no aceptan valores NULL se establecen en `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-488">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="bbb14-489">Por ejemplo, un parámetro `int id` se establece en 0.</span><span class="sxs-lookup"><span data-stu-id="bbb14-489">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="bbb14-490">Para los tipos complejos, el enlace de modelos crea una instancia mediante el constructor predeterminado, sin establecer propiedades.</span><span class="sxs-lookup"><span data-stu-id="bbb14-490">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="bbb14-491">Las matrices se establecen en `Array.Empty<T>()`, salvo las matrices `byte[]`, que se establecen en `null`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-491">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="bbb14-492">Si el estado del modelo se debe invalidar Cuando no se encuentra nada en los campos de formulario de una propiedad de modelo, utilice el [`[BindRequired]`](#bindrequired-attribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-492">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="bbb14-493">Tenga en cuenta que este comportamiento de `[BindRequired]` se aplica al enlace de modelos desde datos de formulario publicados, no a los datos JSON o XML del cuerpo de una solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-493">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="bbb14-494">Los datos del cuerpo de la solicitud se controlan mediante [formateadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bbb14-494">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="bbb14-495">Errores de la conversión de tipos</span><span class="sxs-lookup"><span data-stu-id="bbb14-495">Type conversion errors</span></span>

<span data-ttu-id="bbb14-496">Si se encuentra un origen pero no se puede convertir al tipo de destino, el estado del modelo se marca como no válido.</span><span class="sxs-lookup"><span data-stu-id="bbb14-496">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="bbb14-497">El parámetro o la propiedad de destino se establece en NULL o en un valor predeterminado, como se ha indicado en la sección anterior.</span><span class="sxs-lookup"><span data-stu-id="bbb14-497">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="bbb14-498">En un controlador de API que tenga el atributo `[ApiController]`, el estado de modelo no válido genera una respuesta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="bbb14-498">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="bbb14-499">En una Razor página, vuelva a mostrar la página con un mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="bbb14-499">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="bbb14-500">La validación del lado cliente detecta la mayoría de los datos incorrectos que de otro modo se enviarían a un Razor formulario de páginas.</span><span class="sxs-lookup"><span data-stu-id="bbb14-500">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="bbb14-501">Esta validación dificulta que se pueda desencadenar el código resaltado anterior.</span><span class="sxs-lookup"><span data-stu-id="bbb14-501">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="bbb14-502">En la aplicación de ejemplo se incluye un botón **Submit with Invalid Date** (Enviar con fecha no válida) que agrega datos incorrectos al campo **Hire Date** (Fecha de contratación) y envía el formulario.</span><span class="sxs-lookup"><span data-stu-id="bbb14-502">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="bbb14-503">Este botón muestra cómo funciona el código para volver a mostrar la página cuando se producen errores de conversión de datos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-503">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="bbb14-504">Cuando el código anterior vuelve a mostrar la página, no se muestra la entrada no válida en el campo de formulario.</span><span class="sxs-lookup"><span data-stu-id="bbb14-504">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="bbb14-505">El motivo es que la propiedad de modelo se ha establecido en NULL o en un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="bbb14-505">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="bbb14-506">La entrada no válida sí aparece en un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="bbb14-506">The invalid input does appear in an error message.</span></span> <span data-ttu-id="bbb14-507">Pero si quiere volver a mostrar los datos incorrectos en el campo de formulario, considere la posibilidad de convertir la propiedad de modelo en una cadena y realizar la conversión de datos de forma manual.</span><span class="sxs-lookup"><span data-stu-id="bbb14-507">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="bbb14-508">Se recomienda la misma estrategia si no quiere que los errores de conversión de tipo generen errores de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-508">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="bbb14-509">En ese caso, convierta la propiedad de modelo en una cadena.</span><span class="sxs-lookup"><span data-stu-id="bbb14-509">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="bbb14-510">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="bbb14-510">Simple types</span></span>

<span data-ttu-id="bbb14-511">Los tipos simples a los que el enlazador de modelos puede convertir las cadenas de origen incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="bbb14-511">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="bbb14-512">Boolean</span><span class="sxs-lookup"><span data-stu-id="bbb14-512">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="bbb14-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="bbb14-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="bbb14-514">Char</span><span class="sxs-lookup"><span data-stu-id="bbb14-514">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="bbb14-515">DateTime</span><span class="sxs-lookup"><span data-stu-id="bbb14-515">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="bbb14-516">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="bbb14-516">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="bbb14-517">Decimal</span><span class="sxs-lookup"><span data-stu-id="bbb14-517">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="bbb14-518">Double</span><span class="sxs-lookup"><span data-stu-id="bbb14-518">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="bbb14-519">Enum</span><span class="sxs-lookup"><span data-stu-id="bbb14-519">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="bbb14-520">Volumen</span><span class="sxs-lookup"><span data-stu-id="bbb14-520">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="bbb14-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="bbb14-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="bbb14-522">Single</span><span class="sxs-lookup"><span data-stu-id="bbb14-522">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="bbb14-523">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="bbb14-523">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="bbb14-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="bbb14-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="bbb14-525">Uri</span><span class="sxs-lookup"><span data-stu-id="bbb14-525">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="bbb14-526">Versión</span><span class="sxs-lookup"><span data-stu-id="bbb14-526">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="bbb14-527">Tipos complejos</span><span class="sxs-lookup"><span data-stu-id="bbb14-527">Complex types</span></span>

<span data-ttu-id="bbb14-528">Un tipo complejo debe tener un constructor público predeterminado y propiedades grabables públicas para enlazar.</span><span class="sxs-lookup"><span data-stu-id="bbb14-528">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="bbb14-529">Cuando se produce el enlace de modelos, se crea una instancia de la clase con el constructor predeterminado público.</span><span class="sxs-lookup"><span data-stu-id="bbb14-529">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="bbb14-530">Para cada propiedad del tipo complejo, el enlace de modelos busca entre los orígenes el patrón de nombre *prefijo.nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="bbb14-530">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="bbb14-531">Si no se encuentra nada, solo busca *nombre_de_propiedad* sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-531">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="bbb14-532">Para el enlace a un parámetro, el prefijo es el nombre del parámetro.</span><span class="sxs-lookup"><span data-stu-id="bbb14-532">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="bbb14-533">Para el enlace a una propiedad pública `PageModel`, el prefijo es el nombre de la propiedad pública.</span><span class="sxs-lookup"><span data-stu-id="bbb14-533">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="bbb14-534">Algunos atributos tienen una propiedad `Prefix` que permite invalidar el uso predeterminado del nombre de parámetro o propiedad.</span><span class="sxs-lookup"><span data-stu-id="bbb14-534">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="bbb14-535">Por ejemplo, imagine que el tipo complejo es la clase `Instructor` siguiente:</span><span class="sxs-lookup"><span data-stu-id="bbb14-535">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="bbb14-536">Prefijo = nombre del parámetro</span><span class="sxs-lookup"><span data-stu-id="bbb14-536">Prefix = parameter name</span></span>

<span data-ttu-id="bbb14-537">Si el modelo que se va a enlazar es un parámetro denominado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-537">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="bbb14-538">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-538">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="bbb14-539">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-539">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="bbb14-540">Prefijo = nombre de propiedad</span><span class="sxs-lookup"><span data-stu-id="bbb14-540">Prefix = property name</span></span>

<span data-ttu-id="bbb14-541">Si el modelo que se va a enlazar es una propiedad denominada `Instructor` del controlador o la clase `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-541">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="bbb14-542">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-542">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="bbb14-543">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-543">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="bbb14-544">Prefijo personalizado</span><span class="sxs-lookup"><span data-stu-id="bbb14-544">Custom prefix</span></span>

<span data-ttu-id="bbb14-545">Si el modelo que se va a enlazar es un parámetro denominado `instructorToUpdate` y un atributo `Bind`, especifica `Instructor` como el prefijo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-545">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="bbb14-546">El enlace de modelos se inicia mediante el examen de los orígenes para la clave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-546">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="bbb14-547">Si no se encuentra, busca `ID` sin un prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-547">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="bbb14-548">Atributos para destinos de tipo complejo</span><span class="sxs-lookup"><span data-stu-id="bbb14-548">Attributes for complex type targets</span></span>

<span data-ttu-id="bbb14-549">Existen varios atributos integrados para controlar el enlace de modelos de tipos complejos:</span><span class="sxs-lookup"><span data-stu-id="bbb14-549">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="bbb14-550">Estos atributos afectan al enlace de modelos cuando el origen de los valores son datos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-550">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="bbb14-551">No afectan a los formateadores de entrada, que procesan los cuerpos de la solicitud JSON y XML publicados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-551">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="bbb14-552">Los formateadores de entrada se explican [más adelante en este artículo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bbb14-552">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="bbb14-553">Vea también la explicación del atributo `[Required]` en [Validación de modelos](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="bbb14-553">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="bbb14-554">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="bbb14-554">[BindRequired] attribute</span></span>

<span data-ttu-id="bbb14-555">Solo se puede aplicar a propiedades del modelo, no a parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="bbb14-555">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="bbb14-556">Hace que el enlace de modelos agregue un error de estado de modelo si no se puede realizar el enlace para la propiedad de un modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-556">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="bbb14-557">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-557">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="bbb14-558">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="bbb14-558">[BindNever] attribute</span></span>

<span data-ttu-id="bbb14-559">Solo se puede aplicar a propiedades del modelo, no a parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="bbb14-559">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="bbb14-560">Impide que el enlace de modelos establezca la propiedad de un modelo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-560">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="bbb14-561">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-561">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="bbb14-562">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="bbb14-562">[Bind] attribute</span></span>

<span data-ttu-id="bbb14-563">Se puede aplicar a una clase o un parámetro de método.</span><span class="sxs-lookup"><span data-stu-id="bbb14-563">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="bbb14-564">Especifica qué propiedades de un modelo se deben incluir en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-564">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="bbb14-565">En el ejemplo siguiente, solo se enlazan las propiedades especificadas del modelo `Instructor` cuando se llama a cualquier método de acción o controlador:</span><span class="sxs-lookup"><span data-stu-id="bbb14-565">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="bbb14-566">En el ejemplo siguiente, solo se enlazan las propiedades especificadas del modelo `Instructor` cuando se llama al método `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-566">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="bbb14-567">El atributo `[Bind]` se puede usar para protegerse de la publicación excesiva en escenarios de *creación*.</span><span class="sxs-lookup"><span data-stu-id="bbb14-567">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="bbb14-568">No funciona bien en escenarios de edición porque las propiedades excluidas se establecen en NULL o en un valor predeterminado en lugar de mantenerse sin cambios.</span><span class="sxs-lookup"><span data-stu-id="bbb14-568">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="bbb14-569">Para defenderse de la publicación excesiva, se recomiendan modelos de vista en lugar del atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-569">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="bbb14-570">Para más información, vea [Nota de seguridad sobre la publicación excesiva](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="bbb14-570">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="bbb14-571">Colecciones</span><span class="sxs-lookup"><span data-stu-id="bbb14-571">Collections</span></span>

<span data-ttu-id="bbb14-572">Para los destinos que son colecciones de tipos simples, el enlace de modelos busca coincidencias con *nombre_de_parámetro* o *nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="bbb14-572">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="bbb14-573">Si no se encuentra ninguna coincidencia, busca uno de los formatos admitidos sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-573">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="bbb14-574">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-574">For example:</span></span>

* <span data-ttu-id="bbb14-575">Imagine que el parámetro que se va a enlazar es una matriz llamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-575">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="bbb14-576">Los datos de cadena de consulta o formulario pueden estar en uno de los formatos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bbb14-576">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="bbb14-577">El formato siguiente solo se admite en datos de formulario:</span><span class="sxs-lookup"><span data-stu-id="bbb14-577">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="bbb14-578">Para todos los formatos de ejemplo anteriores, el enlace de modelos pasa una matriz de dos elementos al parámetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-578">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="bbb14-579">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="bbb14-579">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="bbb14-580">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="bbb14-580">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="bbb14-581">Los formatos de datos que usan números de subíndice (... [0] ... [1] ...) deben asegurarse de que se numeran de forma secuencial a partir de cero.</span><span class="sxs-lookup"><span data-stu-id="bbb14-581">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="bbb14-582">Si hay algún hueco en la numeración de los subíndices, se omiten todos los elementos que aparecen después del hueco.</span><span class="sxs-lookup"><span data-stu-id="bbb14-582">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="bbb14-583">Por ejemplo, si los subíndices son 0 y 2 en lugar de 0 y 1, se omite el segundo elemento.</span><span class="sxs-lookup"><span data-stu-id="bbb14-583">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="bbb14-584">Diccionarios</span><span class="sxs-lookup"><span data-stu-id="bbb14-584">Dictionaries</span></span>

<span data-ttu-id="bbb14-585">Para los destinos `Dictionary`, el enlace de modelos busca coincidencias con *nombre_de_parámetro* o *nombre_de_propiedad*.</span><span class="sxs-lookup"><span data-stu-id="bbb14-585">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="bbb14-586">Si no se encuentra ninguna coincidencia, busca uno de los formatos admitidos sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="bbb14-586">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="bbb14-587">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-587">For example:</span></span>

* <span data-ttu-id="bbb14-588">Imagine que el parámetro de destino es un elemento `Dictionary<int, string>` denominado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-588">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="bbb14-589">Los datos de cadena de consulta o de formulario publicados pueden ser similares a uno de los ejemplos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bbb14-589">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="bbb14-590">Para todos los formatos de ejemplo anteriores, el enlace de modelos pasa un diccionario de dos elementos al parámetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-590">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="bbb14-591">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="bbb14-591">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="bbb14-592">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="bbb14-592">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="bbb14-593">Comportamiento de globalización del enlace de modelos datos de ruta y cadenas de consulta</span><span class="sxs-lookup"><span data-stu-id="bbb14-593">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="bbb14-594">El proveedor de valores de ruta de ASP.NET Core y el proveedor de valores de cadena de consulta:</span><span class="sxs-lookup"><span data-stu-id="bbb14-594">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="bbb14-595">Tratan los valores como referencia cultural de todos los idiomas.</span><span class="sxs-lookup"><span data-stu-id="bbb14-595">Treat values as invariant culture.</span></span>
* <span data-ttu-id="bbb14-596">Esperan que las direcciones URL sean independientes de la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="bbb14-596">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="bbb14-597">Por el contrario, los valores procedentes de datos de formulario se someten a una conversión que tiene en cuenta la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="bbb14-597">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="bbb14-598">Esto es así por diseño, para que las direcciones URL se puedan compartir entre configuraciones regionales.</span><span class="sxs-lookup"><span data-stu-id="bbb14-598">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="bbb14-599">Para que el proveedor de valores de ruta de ASP.NET Core y el proveedor de valores de cadena de consulta se sometan a una conversión dependiente de la referencia cultural:</span><span class="sxs-lookup"><span data-stu-id="bbb14-599">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="bbb14-600">Heredan de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="bbb14-600">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="bbb14-601">Copie el código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) o [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="bbb14-601">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="bbb14-602">Reemplace el [valor de referencia cultural](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) pasado al constructor de proveedor de valores con [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="bbb14-602">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="bbb14-603">Reemplace el generador de proveedores de valor predeterminado en las opciones de MVC por el nuevo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-603">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="bbb14-604">Tipos de datos especiales</span><span class="sxs-lookup"><span data-stu-id="bbb14-604">Special data types</span></span>

<span data-ttu-id="bbb14-605">Hay algunos tipos de datos especiales que el enlace de modelos puede controlar.</span><span class="sxs-lookup"><span data-stu-id="bbb14-605">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="bbb14-606">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="bbb14-606">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="bbb14-607">Un archivo cargado incluido en la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bbb14-607">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="bbb14-608">También se admite `IEnumerable<IFormFile>` para varios archivos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-608">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="bbb14-609">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="bbb14-609">CancellationToken</span></span>

<span data-ttu-id="bbb14-610">se usa para cancelar la actividad en controladores asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-610">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="bbb14-611">FormCollection</span><span class="sxs-lookup"><span data-stu-id="bbb14-611">FormCollection</span></span>

<span data-ttu-id="bbb14-612">Se usa para recuperar todos los valores de los datos de formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-612">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="bbb14-613">Formateadores de entrada</span><span class="sxs-lookup"><span data-stu-id="bbb14-613">Input formatters</span></span>

<span data-ttu-id="bbb14-614">Los datos del cuerpo de la solicitud pueden estar en XML, JSON u otro formato.</span><span class="sxs-lookup"><span data-stu-id="bbb14-614">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="bbb14-615">Para analizar estos datos, el enlace de modelos usa un *formateador de entrada* configurado para controlar un tipo de contenido determinado.</span><span class="sxs-lookup"><span data-stu-id="bbb14-615">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="bbb14-616">De forma predeterminada, en ASP.NET Core se incluyen formateadores de entrada basados en JSON para el control de los datos JSON.</span><span class="sxs-lookup"><span data-stu-id="bbb14-616">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="bbb14-617">Puede agregar otros formateadores para otros tipos de contenido.</span><span class="sxs-lookup"><span data-stu-id="bbb14-617">You can add other formatters for other content types.</span></span>

<span data-ttu-id="bbb14-618">ASP.NET Core selecciona los formateadores de entrada en función del atributo [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="bbb14-618">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="bbb14-619">Si no hay ningún atributo, usa el [encabezado Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="bbb14-619">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="bbb14-620">Para usar los formateadores de entrada XML integrados:</span><span class="sxs-lookup"><span data-stu-id="bbb14-620">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="bbb14-621">Instale el paquete NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-621">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="bbb14-622">En `Startup.ConfigureServices`, llame a <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> o a <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="bbb14-622">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="bbb14-623">Aplique el atributo `Consumes` a las clases de controlador o los métodos de acción que deben esperar XML en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="bbb14-623">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="bbb14-624">Para más información, vea [Introducción de la serialización XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="bbb14-624">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="bbb14-625">Exclusión de tipos especificados del enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="bbb14-625">Exclude specified types from model binding</span></span>

<span data-ttu-id="bbb14-626">El comportamiento del enlace de modelos y los sistemas de validación se controla mediante [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="bbb14-626">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="bbb14-627">Puede personalizar `ModelMetadata` mediante la adición de un proveedor de detalles a [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="bbb14-627">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="bbb14-628">Los proveedores de detalles integrados están disponibles para deshabilitar el enlace de modelos o la validación para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="bbb14-628">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="bbb14-629">Para deshabilitar el enlace de modelos en todos los modelos de un tipo especificado, agregue una instancia de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-629">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bbb14-630">Por ejemplo, para deshabilitar el enlace de modelos en todos los modelos del tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-630">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="bbb14-631">Para deshabilitar la validación en propiedades de un tipo especificado, agregue una instancia de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-631">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bbb14-632">Por ejemplo, para deshabilitar la validación en las propiedades de tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="bbb14-632">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="bbb14-633">Enlazadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="bbb14-633">Custom model binders</span></span>

<span data-ttu-id="bbb14-634">Puede ampliar el enlace de modelos si escribe un enlazador de modelos personalizado y usa el atributo `[ModelBinder]` para seleccionarlo para un destino concreto.</span><span class="sxs-lookup"><span data-stu-id="bbb14-634">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="bbb14-635">Más información sobre el [enlace de modelos personalizados](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="bbb14-635">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="bbb14-636">Enlace de modelos manual</span><span class="sxs-lookup"><span data-stu-id="bbb14-636">Manual model binding</span></span>

<span data-ttu-id="bbb14-637">El enlace de modelos se puede invocar de forma manual mediante el método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="bbb14-637">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="bbb14-638">El método se define en las clases `ControllerBase` y `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="bbb14-638">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="bbb14-639">Las sobrecargas de método permiten especificar el prefijo y el proveedor de valores que se van a usar.</span><span class="sxs-lookup"><span data-stu-id="bbb14-639">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="bbb14-640">El método devuelve `false` si se produce un error en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-640">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="bbb14-641">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bbb14-641">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="bbb14-642">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="bbb14-642">[FromServices] attribute</span></span>

<span data-ttu-id="bbb14-643">El nombre de este atributo sigue el patrón de los atributos de enlace de modelos que especifican un origen de datos.</span><span class="sxs-lookup"><span data-stu-id="bbb14-643">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="bbb14-644">Pero no se trata de enlazar datos desde un proveedor de valores.</span><span class="sxs-lookup"><span data-stu-id="bbb14-644">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="bbb14-645">Obtiene una instancia de un tipo desde el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bbb14-645">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="bbb14-646">Su objetivo es proporcionar una alternativa a la inserción de constructores cuando se necesita un servicio solo si se llama a un método concreto.</span><span class="sxs-lookup"><span data-stu-id="bbb14-646">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bbb14-647">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bbb14-647">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
