---
title: Tipos de valor devuelto de acción del controlador de la API web de ASP.NET Core
author: scottaddie
description: Obtenga información sobre los distintos tipos de valor devuelto de método de acción del controlador de la API web de ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
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
uid: web-api/action-return-types
ms.openlocfilehash: 3058fabb0c08ac62956c18f3c294692d35122e12
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022165"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="3588d-103">Tipos de valor devuelto de acción del controlador de la API web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3588d-103">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="3588d-104">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="3588d-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="3588d-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3588d-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3588d-106">ASP.NET Core ofrece las siguientes opciones relativas a los tipos de valor devuelto de acción del controlador de la API web:</span><span class="sxs-lookup"><span data-stu-id="3588d-106">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="3588d-107">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="3588d-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="3588d-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="3588d-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="3588d-109">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="3588d-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="3588d-110">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="3588d-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="3588d-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="3588d-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="3588d-112">En este documento se explica cuándo resulta más adecuado usar cada tipo de valor devuelto.</span><span class="sxs-lookup"><span data-stu-id="3588d-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="3588d-113">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="3588d-113">Specific type</span></span>

<span data-ttu-id="3588d-114">La acción más sencilla devuelve un tipo de datos primitivo o complejo (por ejemplo, `string` o un tipo de objeto personalizado).</span><span class="sxs-lookup"><span data-stu-id="3588d-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="3588d-115">Consideremos la siguiente acción, que devuelve una colección de objetos `Product` personalizados:</span><span class="sxs-lookup"><span data-stu-id="3588d-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="3588d-116">Sin condiciones conocidas de las que haya que protegerse durante la ejecución de la acción, bastaría con que se devolviera un tipo específico.</span><span class="sxs-lookup"><span data-stu-id="3588d-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="3588d-117">La acción anterior no acepta parámetros, por lo que no se necesita ninguna validación de restricciones de parámetros.</span><span class="sxs-lookup"><span data-stu-id="3588d-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="3588d-118">Cuando se pueden utilizar varios tipos de valor devuelto, es habitual mezclar un <xref:Microsoft.AspNetCore.Mvc.ActionResult> tipo de valor devuelto con el tipo de valor devuelto primitivo o complejo.</span><span class="sxs-lookup"><span data-stu-id="3588d-118">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="3588d-119">[IActionResult](#iactionresult-type) o [ActionResult \<T> ](#actionresultt-type) son necesarios para acomodar este tipo de acción.</span><span class="sxs-lookup"><span data-stu-id="3588d-119">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="3588d-120">En este documento se proporcionan varios ejemplos de varios tipos de valor devueltos.</span><span class="sxs-lookup"><span data-stu-id="3588d-120">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="3588d-121">Devolver IEnumerable \<T> o IAsyncEnumerable\<T></span><span class="sxs-lookup"><span data-stu-id="3588d-121">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="3588d-122">En ASP.net Core 2.2 y versiones anteriores, la devolución de <xref:System.Collections.Generic.IEnumerable%601> en una acción da como resultado la iteración de la colección sincrónica por parte del serializador.</span><span class="sxs-lookup"><span data-stu-id="3588d-122">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="3588d-123">El resultado es el bloqueo de llamadas y una posibilidad de colapso del grupo de subprocesos.</span><span class="sxs-lookup"><span data-stu-id="3588d-123">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="3588d-124">Por poner un ejemplo, imagine que se usa Entity Framework (EF) Core para las necesidades de acceso a los datos de la API Web.</span><span class="sxs-lookup"><span data-stu-id="3588d-124">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="3588d-125">El tipo de valor devuelto de la acción siguiente se enumera sincrónicamente durante la serialización:</span><span class="sxs-lookup"><span data-stu-id="3588d-125">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="3588d-126">Para evitar la enumeración sincrónica y las esperas por bloqueo en la base de datos en ASP.NET Core 2.2 y versiones anteriores, invoque `ToListAsync`:</span><span class="sxs-lookup"><span data-stu-id="3588d-126">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="3588d-127">En ASP.net Core 3,0 y versiones posteriores, que una acción devuelva `IAsyncEnumerable<T>`:</span><span class="sxs-lookup"><span data-stu-id="3588d-127">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="3588d-128">Ya no da como resultado iteraciones sincrónicas.</span><span class="sxs-lookup"><span data-stu-id="3588d-128">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="3588d-129">Es tan eficaz como devolver <xref:System.Collections.Generic.IEnumerable%601>.</span><span class="sxs-lookup"><span data-stu-id="3588d-129">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="3588d-130">Tanto ASP.NET Core 3.0 como las versiones posteriores almacenan en búfer el resultado de la siguiente acción antes de proporcionarlo al serializador:</span><span class="sxs-lookup"><span data-stu-id="3588d-130">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="3588d-131">Considere la posibilidad de declarar el tipo de valor devuelto de la signatura de la acción como `IAsyncEnumerable<T>` para garantizar la iteración asincrónica.</span><span class="sxs-lookup"><span data-stu-id="3588d-131">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="3588d-132">En última instancia, el modo de iteración se basa en el tipo concreto subyacente que se va a devolver.</span><span class="sxs-lookup"><span data-stu-id="3588d-132">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="3588d-133">MVC almacena en búfer automáticamente cualquier tipo concreto que implemente `IAsyncEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="3588d-133">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="3588d-134">Considere la siguiente acción, que devuelve los registros de producto con precio de venta como `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="3588d-134">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="3588d-135">El `IAsyncEnumerable<Product>` equivalente de la acción anterior es:</span><span class="sxs-lookup"><span data-stu-id="3588d-135">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="3588d-136">Las dos acciones anteriores no suponen ningún bloqueo a partir de ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3588d-136">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="3588d-137">Tipo IActionResult</span><span class="sxs-lookup"><span data-stu-id="3588d-137">IActionResult type</span></span>

<span data-ttu-id="3588d-138">El tipo de valor devuelto <xref:Microsoft.AspNetCore.Mvc.IActionResult> resulta adecuado cuando existen varios tipos de valor devuelto `ActionResult` posibles en una acción.</span><span class="sxs-lookup"><span data-stu-id="3588d-138">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="3588d-139">Los tipos `ActionResult` representan varios códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="3588d-139">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="3588d-140">Cualquier clase no abstracta derivada de `ActionResult` se considera un tipo de valor devuelto válido.</span><span class="sxs-lookup"><span data-stu-id="3588d-140">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="3588d-141">Algunos tipos de valor devueltos comunes en esta categoría son <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) y <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="3588d-141">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="3588d-142">Como alternativa, se pueden usar métodos de conveniencia en la clase <xref:Microsoft.AspNetCore.Mvc.ControllerBase> para devolver tipos `ActionResult` de una acción.</span><span class="sxs-lookup"><span data-stu-id="3588d-142">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="3588d-143">Por ejemplo, `return BadRequest();` es una forma abreviada de `return new BadRequestResult();`.</span><span class="sxs-lookup"><span data-stu-id="3588d-143">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="3588d-144">Dado que hay varios tipos de valor devuelto y rutas de acceso en este tipo de acción, es necesario el uso liberal del [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="3588d-144">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="3588d-145">Este atributo genera detalles de respuesta más pormenorizados relativos a las páginas de ayuda de API web generadas por herramientas como [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="3588d-145">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="3588d-146">`[ProducesResponseType]` indica los tipos conocidos y los códigos de estado HTTP que la acción va a devolver.</span><span class="sxs-lookup"><span data-stu-id="3588d-146">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="3588d-147">Acción sincrónica</span><span class="sxs-lookup"><span data-stu-id="3588d-147">Synchronous action</span></span>

<span data-ttu-id="3588d-148">Veamos la siguiente acción sincrónica, en la que pueden darse dos tipos de valor devuelto posibles:</span><span class="sxs-lookup"><span data-stu-id="3588d-148">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="3588d-149">En la acción anterior:</span><span class="sxs-lookup"><span data-stu-id="3588d-149">In the preceding action:</span></span>

* <span data-ttu-id="3588d-150">Se devuelve un código de estado 404 cuando el producto representado por `id` no existe en el almacén de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="3588d-150">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="3588d-151">El método de conveniencia <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> se invoca como una abreviatura para `return new NotFoundResult();`.</span><span class="sxs-lookup"><span data-stu-id="3588d-151">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="3588d-152">Se devuelve un código de estado 200 con el objeto `Product` cuando existe el producto.</span><span class="sxs-lookup"><span data-stu-id="3588d-152">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="3588d-153">El método de conveniencia <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> se invoca como una abreviatura para `return new OkObjectResult(product);`.</span><span class="sxs-lookup"><span data-stu-id="3588d-153">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="3588d-154">Acción asincrónica</span><span class="sxs-lookup"><span data-stu-id="3588d-154">Asynchronous action</span></span>

<span data-ttu-id="3588d-155">Veamos la siguiente acción asincrónica, en la que pueden darse dos tipos de valor devuelto posibles:</span><span class="sxs-lookup"><span data-stu-id="3588d-155">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="3588d-156">En la acción anterior:</span><span class="sxs-lookup"><span data-stu-id="3588d-156">In the preceding action:</span></span>

* <span data-ttu-id="3588d-157">Se devuelve un código de estado 400 cuando la descripción del producto contiene "XYZ Widget".</span><span class="sxs-lookup"><span data-stu-id="3588d-157">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="3588d-158">El método de conveniencia <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> se invoca como una abreviatura para `return new BadRequestResult();`.</span><span class="sxs-lookup"><span data-stu-id="3588d-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="3588d-159">Al crear un producto, el método de conveniencia <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> genera un código de estado 201.</span><span class="sxs-lookup"><span data-stu-id="3588d-159">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="3588d-160">Una alternativa a la llamada a `CreatedAtAction` es `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span><span class="sxs-lookup"><span data-stu-id="3588d-160">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="3588d-161">En esta ruta de acceso de código, el objeto `Product` se proporciona en el cuerpo de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3588d-161">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="3588d-162">Se proporciona un encabezado de respuesta `Location` que contiene la dirección URL del producto recién creada.</span><span class="sxs-lookup"><span data-stu-id="3588d-162">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="3588d-163">Por ejemplo, el siguiente modelo indica que las solicitudes deben incluir las propiedades `Name` y `Description`.</span><span class="sxs-lookup"><span data-stu-id="3588d-163">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="3588d-164">Si no se proporcionan `Name` y `Description` en la solicitud, se producirá un error en la validación de los modelos.</span><span class="sxs-lookup"><span data-stu-id="3588d-164">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3588d-165">Si [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) se aplica el atributo en ASP.NET Core 2,1 o posterior, los errores de validación del modelo dan como resultado un código de estado 400.</span><span class="sxs-lookup"><span data-stu-id="3588d-165">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="3588d-166">Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="3588d-166">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="3588d-167">\<T>Tipo ActionResult</span><span class="sxs-lookup"><span data-stu-id="3588d-167">ActionResult\<T> type</span></span>

<span data-ttu-id="3588d-168">ASP.NET Core 2,1 presentó el tipo de valor devuelto [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) para las acciones del controlador de API Web.</span><span class="sxs-lookup"><span data-stu-id="3588d-168">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="3588d-169">Permite devolver un tipo que se deriva de <xref:Microsoft.AspNetCore.Mvc.ActionResult> o bien un [tipo específico](#specific-type).</span><span class="sxs-lookup"><span data-stu-id="3588d-169">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="3588d-170">`ActionResult<T>` reporta las siguientes ventajas con frente al [tipo IActionResult](#iactionresult-type):</span><span class="sxs-lookup"><span data-stu-id="3588d-170">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="3588d-171">[`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) `Type` Se puede excluir la propiedad del atributo.</span><span class="sxs-lookup"><span data-stu-id="3588d-171">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="3588d-172">Por ejemplo, `[ProducesResponseType(200, Type = typeof(Product))]` se simplifica a `[ProducesResponseType(200)]`.</span><span class="sxs-lookup"><span data-stu-id="3588d-172">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="3588d-173">En su lugar, el tipo de valor devuelto esperado de la acción se infiere desde `T` en `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="3588d-173">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="3588d-174">Los [operadores de conversión implícitos](/dotnet/csharp/language-reference/keywords/implicit) admiten la conversión tanto de `T` como de `ActionResult` en `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="3588d-174">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="3588d-175">`T` se convierte en <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, lo que significa que `return new ObjectResult(T);` se ha simplificado para `return T;`.</span><span class="sxs-lookup"><span data-stu-id="3588d-175">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="3588d-176">C# no admite operadores de conversión implícitos en las interfaces.</span><span class="sxs-lookup"><span data-stu-id="3588d-176">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="3588d-177">Por consiguiente, la conversión de la interfaz a un tipo concreto es necesaria para usar `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="3588d-177">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="3588d-178">Por ejemplo, el uso de `IEnumerable` en el siguiente ejemplo no funciona:</span><span class="sxs-lookup"><span data-stu-id="3588d-178">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="3588d-179">Una opción para corregir el código anterior es devolver `_repository.GetProducts().ToList();`.</span><span class="sxs-lookup"><span data-stu-id="3588d-179">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="3588d-180">La mayoría de las acciones tiene un tipo de valor devuelto específico.</span><span class="sxs-lookup"><span data-stu-id="3588d-180">Most actions have a specific return type.</span></span> <span data-ttu-id="3588d-181">Se pueden producir condiciones inesperadas que durante la ejecución de una acción, en cuyo caso no se devuelve el tipo específico.</span><span class="sxs-lookup"><span data-stu-id="3588d-181">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="3588d-182">Por ejemplo, puede suceder que el parámetro de entrada de una acción genere un error de validación de modelos.</span><span class="sxs-lookup"><span data-stu-id="3588d-182">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="3588d-183">En tal caso, lo normal es que se devuelva el tipo `ActionResult` correspondiente en lugar del tipo específico.</span><span class="sxs-lookup"><span data-stu-id="3588d-183">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="3588d-184">Acción sincrónica</span><span class="sxs-lookup"><span data-stu-id="3588d-184">Synchronous action</span></span>

<span data-ttu-id="3588d-185">Veamos una acción sincrónica, en la que pueden darse dos tipos de valor devuelto posibles:</span><span class="sxs-lookup"><span data-stu-id="3588d-185">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="3588d-186">En la acción anterior:</span><span class="sxs-lookup"><span data-stu-id="3588d-186">In the preceding action:</span></span>

* <span data-ttu-id="3588d-187">Se devuelve un código de estado 404 cuando el producto no existe en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3588d-187">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="3588d-188">Se devuelve un código de estado 200 con el objeto `Product` correspondiente cuando existe el producto.</span><span class="sxs-lookup"><span data-stu-id="3588d-188">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="3588d-189">Antes de ASP.NET Core 2.1, la línea `return product;` tenía que ser `return Ok(product);`.</span><span class="sxs-lookup"><span data-stu-id="3588d-189">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="3588d-190">Acción asincrónica</span><span class="sxs-lookup"><span data-stu-id="3588d-190">Asynchronous action</span></span>

<span data-ttu-id="3588d-191">Veamos una acción asincrónica, en la que pueden darse dos tipos de valor devuelto posibles:</span><span class="sxs-lookup"><span data-stu-id="3588d-191">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="3588d-192">En la acción anterior:</span><span class="sxs-lookup"><span data-stu-id="3588d-192">In the preceding action:</span></span>

* <span data-ttu-id="3588d-193">El entorno de ejecución de ASP.NET Core devuelve un código de estado 400 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) en los casos siguientes:</span><span class="sxs-lookup"><span data-stu-id="3588d-193">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="3588d-194">Se ha [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) aplicado el atributo y se produce un error en la validación del modelo.</span><span class="sxs-lookup"><span data-stu-id="3588d-194">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="3588d-195">La descripción del producto contiene "Widget XYZ".</span><span class="sxs-lookup"><span data-stu-id="3588d-195">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="3588d-196">Al crear un producto, el método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> genera un código de estado 201.</span><span class="sxs-lookup"><span data-stu-id="3588d-196">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="3588d-197">En esta ruta de acceso de código, el objeto `Product` se proporciona en el cuerpo de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3588d-197">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="3588d-198">Se proporciona un encabezado de respuesta `Location` que contiene la dirección URL del producto recién creada.</span><span class="sxs-lookup"><span data-stu-id="3588d-198">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3588d-199">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3588d-199">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
