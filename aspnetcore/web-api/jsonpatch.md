---
title: JsonPatch en la API web de ASP.NET Core
author: rick-anderson
description: Aprenda a administrar solicitudes JSON Patch en una API web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: 09da557d678889ba16abe6f9af40ae1b33583d8b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022373"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="e95b7-103">JsonPatch en la API web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e95b7-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="e95b7-104">Por [Tom Dykstra](https://github.com/tdykstra) y [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="e95b7-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e95b7-105">En este artículo se explica cómo administrar solicitudes JSON Patch en una API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e95b7-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="e95b7-106">Instalación del paquete</span><span class="sxs-lookup"><span data-stu-id="e95b7-106">Package installation</span></span>

<span data-ttu-id="e95b7-107">Para habilitar la compatibilidad con la revisión de JSON en la aplicación, complete los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e95b7-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="e95b7-108">Instale el [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="e95b7-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="e95b7-109">Actualice el método del proyecto `Startup.ConfigureServices` para llamar a <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="e95b7-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="e95b7-110">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e95b7-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="e95b7-111">`AddNewtonsoftJson` es compatible con los métodos de registro del servicio MVC:</span><span class="sxs-lookup"><span data-stu-id="e95b7-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="e95b7-112">Revisión de JSON, AddNewtonsoftJson y System.Text.Jsen</span><span class="sxs-lookup"><span data-stu-id="e95b7-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="e95b7-113">`AddNewtonsoftJson`reemplaza los `System.Text.Json` formateadores de entrada y salida basados en que se usan para dar formato a **todo** el contenido JSON.</span><span class="sxs-lookup"><span data-stu-id="e95b7-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="e95b7-114">Para agregar compatibilidad con la revisión de JSON mediante `Newtonsoft.Json` , sin modificar los otros formateadores, actualice el método del proyecto `Startup.ConfigureServices` como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="e95b7-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="e95b7-115">El código anterior requiere el `Microsoft.AspNetCore.Mvc.NewtonsoftJson` paquete y las siguientes `using` instrucciones:</span><span class="sxs-lookup"><span data-stu-id="e95b7-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="e95b7-116">Método de solicitud HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="e95b7-116">PATCH HTTP request method</span></span>

<span data-ttu-id="e95b7-117">Los métodos PUT y [PATCH](https://tools.ietf.org/html/rfc5789) se usan para actualizar un recurso existente.</span><span class="sxs-lookup"><span data-stu-id="e95b7-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="e95b7-118">La diferencia entre ellos es que PUT reemplaza el recurso entero, mientras que PATCH especifica únicamente los cambios.</span><span class="sxs-lookup"><span data-stu-id="e95b7-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="e95b7-119">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="e95b7-119">JSON Patch</span></span>

<span data-ttu-id="e95b7-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) es un formato para especificar las actualizaciones que se aplicarán a un recurso.</span><span class="sxs-lookup"><span data-stu-id="e95b7-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="e95b7-121">Un documento JSON Patch tiene una matriz de *operaciones*.</span><span class="sxs-lookup"><span data-stu-id="e95b7-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="e95b7-122">Cada operación identifica un tipo de cambio determinado.</span><span class="sxs-lookup"><span data-stu-id="e95b7-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="e95b7-123">Algunos ejemplos de estos cambios incluyen agregar un elemento de matriz o reemplazar un valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="e95b7-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="e95b7-124">Por ejemplo, los siguientes documentos JSON representan un recurso, un documento de revisión JSON para el recurso y el resultado de aplicar las operaciones patch.</span><span class="sxs-lookup"><span data-stu-id="e95b7-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="e95b7-125">Ejemplo de recursos</span><span class="sxs-lookup"><span data-stu-id="e95b7-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="e95b7-126">Ejemplo de revisión de JSON</span><span class="sxs-lookup"><span data-stu-id="e95b7-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="e95b7-127">En el código JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="e95b7-127">In the preceding JSON:</span></span>

* <span data-ttu-id="e95b7-128">La propiedad `op` indica el tipo de operación.</span><span class="sxs-lookup"><span data-stu-id="e95b7-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="e95b7-129">La propiedad `path` indica el elemento que se va a actualizar.</span><span class="sxs-lookup"><span data-stu-id="e95b7-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="e95b7-130">La propiedad `value` proporciona el nuevo valor.</span><span class="sxs-lookup"><span data-stu-id="e95b7-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="e95b7-131">Recurso después de la revisión</span><span class="sxs-lookup"><span data-stu-id="e95b7-131">Resource after patch</span></span>

<span data-ttu-id="e95b7-132">Este es el recurso después de aplicar el documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="e95b7-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="e95b7-133">Los cambios realizados al aplicar un documento de revisión de JSON a un recurso son atómicos.</span><span class="sxs-lookup"><span data-stu-id="e95b7-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="e95b7-134">Si se produce un error en cualquier operación de la lista, no se aplica ninguna operación en la lista.</span><span class="sxs-lookup"><span data-stu-id="e95b7-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="e95b7-135">Sintaxis de path</span><span class="sxs-lookup"><span data-stu-id="e95b7-135">Path syntax</span></span>

<span data-ttu-id="e95b7-136">La propiedad [path](https://tools.ietf.org/html/rfc6901) de un objeto de operación tiene barras inversas entre niveles.</span><span class="sxs-lookup"><span data-stu-id="e95b7-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="e95b7-137">Por ejemplo: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="e95b7-138">Para especificar elementos de matriz se usan índices de base cero.</span><span class="sxs-lookup"><span data-stu-id="e95b7-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="e95b7-139">El primer elemento de la matriz `addresses` estaría en `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="e95b7-140">Hasta `add` el final de una matriz, use un guion ( `-` ) en lugar de un número de índice: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="e95b7-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="e95b7-141">Operaciones</span><span class="sxs-lookup"><span data-stu-id="e95b7-141">Operations</span></span>

<span data-ttu-id="e95b7-142">En la siguiente tabla se muestran las operaciones admitidas, como se ha definido en la [especificación de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="e95b7-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="e95b7-143">Operación</span><span class="sxs-lookup"><span data-stu-id="e95b7-143">Operation</span></span>  | <span data-ttu-id="e95b7-144">Notas</span><span class="sxs-lookup"><span data-stu-id="e95b7-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="e95b7-145">Agrega un elemento de propiedad o matriz.</span><span class="sxs-lookup"><span data-stu-id="e95b7-145">Add a property or array element.</span></span> <span data-ttu-id="e95b7-146">Para la propiedad existente: establece el valor.</span><span class="sxs-lookup"><span data-stu-id="e95b7-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="e95b7-147">Quita un elemento de propiedad o matriz.</span><span class="sxs-lookup"><span data-stu-id="e95b7-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="e95b7-148">Lo mismo que `remove` seguido de `add` en la misma ubicación.</span><span class="sxs-lookup"><span data-stu-id="e95b7-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="e95b7-149">Lo mismo que `remove` desde el origen seguido de `add` al destino mediante el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="e95b7-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="e95b7-150">Lo mismo que `add` al destino mediante el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="e95b7-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="e95b7-151">Devuelve el código de estado correcto si el valor en `path` = al `value` proporcionado.</span><span class="sxs-lookup"><span data-stu-id="e95b7-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="e95b7-152">Revisión de JSON en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e95b7-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="e95b7-153">La implementación de ASP.NET Core de JSON Patch se proporciona en el paquete NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="e95b7-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="e95b7-154">Código del método de acción</span><span class="sxs-lookup"><span data-stu-id="e95b7-154">Action method code</span></span>

<span data-ttu-id="e95b7-155">En un controlador de API, un método de acción para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="e95b7-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="e95b7-156">Se anota con el atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="e95b7-157">Acepta `JsonPatchDocument<T>`, normalmente con `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="e95b7-158">Llama a `ApplyTo` en el documento de revisión para aplicar los cambios.</span><span class="sxs-lookup"><span data-stu-id="e95b7-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="e95b7-159">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e95b7-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="e95b7-160">Este código de la aplicación de ejemplo funciona con el siguiente `Customer` modelo:</span><span class="sxs-lookup"><span data-stu-id="e95b7-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="e95b7-161">El método de acción de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e95b7-161">The sample action method:</span></span>

* <span data-ttu-id="e95b7-162">Construye un objeto `Customer`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="e95b7-163">Aplica la revisión.</span><span class="sxs-lookup"><span data-stu-id="e95b7-163">Applies the patch.</span></span>
* <span data-ttu-id="e95b7-164">Devuelve el resultado en el cuerpo de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e95b7-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="e95b7-165">En una aplicación real, el código recuperaría los datos de un almacén como una base de datos y actualizaría la base de datos después de aplicar la revisión.</span><span class="sxs-lookup"><span data-stu-id="e95b7-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="e95b7-166">Estado del modelo</span><span class="sxs-lookup"><span data-stu-id="e95b7-166">Model state</span></span>

<span data-ttu-id="e95b7-167">En el ejemplo anterior del método de acción, se llama a una sobrecarga de `ApplyTo` que toma el estado del modelo como uno de sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="e95b7-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="e95b7-168">Con esta opción, puede obtener mensajes de error en las respuestas.</span><span class="sxs-lookup"><span data-stu-id="e95b7-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="e95b7-169">En el ejemplo siguiente se muestra el cuerpo de una respuesta 400 Solicitud incorrecta de una operación `test`:</span><span class="sxs-lookup"><span data-stu-id="e95b7-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="e95b7-170">Objetos dinámicos</span><span class="sxs-lookup"><span data-stu-id="e95b7-170">Dynamic objects</span></span>

<span data-ttu-id="e95b7-171">En el ejemplo de método de acción siguiente se muestra cómo aplicar una revisión a un objeto dinámico:</span><span class="sxs-lookup"><span data-stu-id="e95b7-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="e95b7-172">La operación add</span><span class="sxs-lookup"><span data-stu-id="e95b7-172">The add operation</span></span>

* <span data-ttu-id="e95b7-173">Si `path` apunta a un elemento de matriz: inserta un nuevo elemento delante del especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="e95b7-174">Si `path` apunta a una propiedad: establece el valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="e95b7-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="e95b7-175">Si `path` apunta a una ubicación que no existe:</span><span class="sxs-lookup"><span data-stu-id="e95b7-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="e95b7-176">Si el recurso para revisar es un objeto dinámico: agrega una propiedad.</span><span class="sxs-lookup"><span data-stu-id="e95b7-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="e95b7-177">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="e95b7-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="e95b7-178">El siguiente documento de revisión de ejemplo establece el valor de `CustomerName` y agrega un objeto `Order` al final de la matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="e95b7-179">La operación remove</span><span class="sxs-lookup"><span data-stu-id="e95b7-179">The remove operation</span></span>

* <span data-ttu-id="e95b7-180">Si `path` apunta a un elemento de matriz: quita el elemento.</span><span class="sxs-lookup"><span data-stu-id="e95b7-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="e95b7-181">Si `path` apunta a una propiedad:</span><span class="sxs-lookup"><span data-stu-id="e95b7-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="e95b7-182">Si el recurso para revisar es un objeto dinámico: quita la propiedad.</span><span class="sxs-lookup"><span data-stu-id="e95b7-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="e95b7-183">Si el recurso para revisar es un objeto estático:</span><span class="sxs-lookup"><span data-stu-id="e95b7-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="e95b7-184">Si la propiedad acepta valores NULL: la establece en null.</span><span class="sxs-lookup"><span data-stu-id="e95b7-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="e95b7-185">Si la propiedad es distinta de null, la establece en `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="e95b7-186">En el documento de revisión de ejemplo siguiente `CustomerName` se establece en NULL y se elimina `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="e95b7-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="e95b7-187">La operación replace</span><span class="sxs-lookup"><span data-stu-id="e95b7-187">The replace operation</span></span>

<span data-ttu-id="e95b7-188">Esta operación es funcionalmente igual que `remove` seguida de `add`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="e95b7-189">En el siguiente documento de revisión de ejemplo se establece el valor de `CustomerName` y se reemplaza `Orders[0]` por un nuevo `Order` objeto:</span><span class="sxs-lookup"><span data-stu-id="e95b7-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="e95b7-190">La operación move</span><span class="sxs-lookup"><span data-stu-id="e95b7-190">The move operation</span></span>

* <span data-ttu-id="e95b7-191">Si `path` apunta a un elemento de matriz: copia el elemento `from` en la ubicación del elemento `path` y, luego, ejecuta una operación `remove` en el elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="e95b7-192">Si `path` apunta a una propiedad: copia el valor de la propiedad `from` en la propiedad `path` y, luego, ejecuta la operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="e95b7-193">Si `path` apunta a una propiedad que no existe:</span><span class="sxs-lookup"><span data-stu-id="e95b7-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="e95b7-194">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="e95b7-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="e95b7-195">Si el recurso para revisar es un objeto dinámico: copia la propiedad `from` en la ubicación indicada por `path` y, luego, ejecuta una operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="e95b7-196">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e95b7-196">The following sample patch document:</span></span>

* <span data-ttu-id="e95b7-197">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e95b7-198">Se establece `Orders[0].OrderName` en null.</span><span class="sxs-lookup"><span data-stu-id="e95b7-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="e95b7-199">Se mueve `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="e95b7-200">La operación copy</span><span class="sxs-lookup"><span data-stu-id="e95b7-200">The copy operation</span></span>

<span data-ttu-id="e95b7-201">Esta operación es funcionalmente igual que la operación `move` sin el paso `remove` final.</span><span class="sxs-lookup"><span data-stu-id="e95b7-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="e95b7-202">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e95b7-202">The following sample patch document:</span></span>

* <span data-ttu-id="e95b7-203">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e95b7-204">Se inserta una copia de `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="e95b7-205">La operación test</span><span class="sxs-lookup"><span data-stu-id="e95b7-205">The test operation</span></span>

<span data-ttu-id="e95b7-206">Si el valor de la ubicación indicada por `path` es diferente del valor proporcionado en `value`, la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="e95b7-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="e95b7-207">En ese caso, la solicitud PATCH entera produce un error incluso si todas las demás operaciones del documento de revisión se realizan correctamente.</span><span class="sxs-lookup"><span data-stu-id="e95b7-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="e95b7-208">La operación `test` se usa habitualmente para impedir una actualización cuando hay un conflicto de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="e95b7-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="e95b7-209">El siguiente documento de revisión de ejemplo no tiene ningún efecto si el valor inicial de `CustomerName` es "John", porque la prueba produce un error:</span><span class="sxs-lookup"><span data-stu-id="e95b7-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="e95b7-210">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="e95b7-210">Get the code</span></span>

<span data-ttu-id="e95b7-211">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="e95b7-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="e95b7-212">([Método de descarga](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e95b7-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="e95b7-213">Para probar el ejemplo, ejecute la aplicación y envíe solicitudes HTTP con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="e95b7-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="e95b7-214">Dirección URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="e95b7-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="e95b7-215">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="e95b7-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="e95b7-216">Encabezado: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="e95b7-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="e95b7-217">Cuerpo: Copie y pegue uno de los ejemplos de documentos de revisión de JSON de la carpeta de proyecto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="e95b7-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e95b7-218">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e95b7-218">Additional resources</span></span>

* [<span data-ttu-id="e95b7-219">Especificación del método PATCH de IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="e95b7-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="e95b7-220">Especificación del método JSON PATCH de IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="e95b7-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="e95b7-221">Especificación del formato de ruta de acceso JSON Patch de IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="e95b7-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="e95b7-222">[Documentación de JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="e95b7-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="e95b7-223">Incluye vínculos a recursos para crear documentos JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="e95b7-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="e95b7-224">Código fuente de JSON Patch para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e95b7-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e95b7-225">En este artículo se explica cómo administrar solicitudes JSON Patch en una API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e95b7-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="e95b7-226">Método de solicitud HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="e95b7-226">PATCH HTTP request method</span></span>

<span data-ttu-id="e95b7-227">Los métodos PUT y [PATCH](https://tools.ietf.org/html/rfc5789) se usan para actualizar un recurso existente.</span><span class="sxs-lookup"><span data-stu-id="e95b7-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="e95b7-228">La diferencia entre ellos es que PUT reemplaza el recurso entero, mientras que PATCH especifica únicamente los cambios.</span><span class="sxs-lookup"><span data-stu-id="e95b7-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="e95b7-229">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="e95b7-229">JSON Patch</span></span>

<span data-ttu-id="e95b7-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) es un formato para especificar las actualizaciones que se aplicarán a un recurso.</span><span class="sxs-lookup"><span data-stu-id="e95b7-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="e95b7-231">Un documento JSON Patch tiene una matriz de *operaciones*.</span><span class="sxs-lookup"><span data-stu-id="e95b7-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="e95b7-232">Cada operación identifica un determinado tipo de cambio, como agregar un elemento de matriz o reemplazar un valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="e95b7-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="e95b7-233">Por ejemplo, los documentos JSON siguientes representan un recurso, un documento de revisión JSON para el recurso y el resultado de aplicar las operaciones de revisión.</span><span class="sxs-lookup"><span data-stu-id="e95b7-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="e95b7-234">Ejemplo de recursos</span><span class="sxs-lookup"><span data-stu-id="e95b7-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="e95b7-235">Ejemplo de revisión de JSON</span><span class="sxs-lookup"><span data-stu-id="e95b7-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="e95b7-236">En el código JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="e95b7-236">In the preceding JSON:</span></span>

* <span data-ttu-id="e95b7-237">La propiedad `op` indica el tipo de operación.</span><span class="sxs-lookup"><span data-stu-id="e95b7-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="e95b7-238">La propiedad `path` indica el elemento que se va a actualizar.</span><span class="sxs-lookup"><span data-stu-id="e95b7-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="e95b7-239">La propiedad `value` proporciona el nuevo valor.</span><span class="sxs-lookup"><span data-stu-id="e95b7-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="e95b7-240">Recurso después de la revisión</span><span class="sxs-lookup"><span data-stu-id="e95b7-240">Resource after patch</span></span>

<span data-ttu-id="e95b7-241">Este es el recurso después de aplicar el documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="e95b7-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="e95b7-242">Los cambios realizados mediante la aplicación de un documento JSON Patch a un recurso son atómicos: si alguna operación de la lista produce un error, no se aplica ninguna operación de la lista.</span><span class="sxs-lookup"><span data-stu-id="e95b7-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="e95b7-243">Sintaxis de path</span><span class="sxs-lookup"><span data-stu-id="e95b7-243">Path syntax</span></span>

<span data-ttu-id="e95b7-244">La propiedad [path](https://tools.ietf.org/html/rfc6901) de un objeto de operación tiene barras inversas entre niveles.</span><span class="sxs-lookup"><span data-stu-id="e95b7-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="e95b7-245">Por ejemplo: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="e95b7-246">Para especificar elementos de matriz se usan índices de base cero.</span><span class="sxs-lookup"><span data-stu-id="e95b7-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="e95b7-247">El primer elemento de la matriz `addresses` estaría en `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="e95b7-248">Para usar `add` al final de una matriz, use un guion (-) en lugar de un número de índice: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="e95b7-249">Operaciones</span><span class="sxs-lookup"><span data-stu-id="e95b7-249">Operations</span></span>

<span data-ttu-id="e95b7-250">En la siguiente tabla se muestran las operaciones admitidas, como se ha definido en la [especificación de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="e95b7-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="e95b7-251">Operación</span><span class="sxs-lookup"><span data-stu-id="e95b7-251">Operation</span></span>  | <span data-ttu-id="e95b7-252">Notas</span><span class="sxs-lookup"><span data-stu-id="e95b7-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="e95b7-253">Agrega un elemento de propiedad o matriz.</span><span class="sxs-lookup"><span data-stu-id="e95b7-253">Add a property or array element.</span></span> <span data-ttu-id="e95b7-254">Para la propiedad existente: establece el valor.</span><span class="sxs-lookup"><span data-stu-id="e95b7-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="e95b7-255">Quita un elemento de propiedad o matriz.</span><span class="sxs-lookup"><span data-stu-id="e95b7-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="e95b7-256">Lo mismo que `remove` seguido de `add` en la misma ubicación.</span><span class="sxs-lookup"><span data-stu-id="e95b7-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="e95b7-257">Lo mismo que `remove` desde el origen seguido de `add` al destino mediante el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="e95b7-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="e95b7-258">Lo mismo que `add` al destino mediante el valor del origen.</span><span class="sxs-lookup"><span data-stu-id="e95b7-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="e95b7-259">Devuelve el código de estado correcto si el valor en `path` = al `value` proporcionado.</span><span class="sxs-lookup"><span data-stu-id="e95b7-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="e95b7-260">JsonPatch en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e95b7-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="e95b7-261">La implementación de ASP.NET Core de JSON Patch se proporciona en el paquete NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="e95b7-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="e95b7-262">El paquete se incluye en el metapaquete [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e95b7-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="e95b7-263">Código del método de acción</span><span class="sxs-lookup"><span data-stu-id="e95b7-263">Action method code</span></span>

<span data-ttu-id="e95b7-264">En un controlador de API, un método de acción para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="e95b7-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="e95b7-265">Se anota con el atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="e95b7-266">Acepta `JsonPatchDocument<T>`, normalmente con `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="e95b7-267">Llama a `ApplyTo` en el documento de revisión para aplicar los cambios.</span><span class="sxs-lookup"><span data-stu-id="e95b7-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="e95b7-268">Este es un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e95b7-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="e95b7-269">Este código de la aplicación de ejemplo funciona con el siguiente modelo `Customer`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="e95b7-270">El método de acción de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e95b7-270">The sample action method:</span></span>

* <span data-ttu-id="e95b7-271">Construye un objeto `Customer`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="e95b7-272">Aplica la revisión.</span><span class="sxs-lookup"><span data-stu-id="e95b7-272">Applies the patch.</span></span>
* <span data-ttu-id="e95b7-273">Devuelve el resultado en el cuerpo de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e95b7-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="e95b7-274">En una aplicación real, el código recuperaría los datos de un almacén como una base de datos y actualizaría la base de datos después de aplicar la revisión.</span><span class="sxs-lookup"><span data-stu-id="e95b7-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="e95b7-275">Estado del modelo</span><span class="sxs-lookup"><span data-stu-id="e95b7-275">Model state</span></span>

<span data-ttu-id="e95b7-276">En el ejemplo anterior del método de acción, se llama a una sobrecarga de `ApplyTo` que toma el estado del modelo como uno de sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="e95b7-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="e95b7-277">Con esta opción, puede obtener mensajes de error en las respuestas.</span><span class="sxs-lookup"><span data-stu-id="e95b7-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="e95b7-278">En el ejemplo siguiente se muestra el cuerpo de una respuesta 400 Solicitud incorrecta de una operación `test`:</span><span class="sxs-lookup"><span data-stu-id="e95b7-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="e95b7-279">Objetos dinámicos</span><span class="sxs-lookup"><span data-stu-id="e95b7-279">Dynamic objects</span></span>

<span data-ttu-id="e95b7-280">En el ejemplo siguiente de método de acción se muestra cómo aplicar una revisión a un objeto dinámico.</span><span class="sxs-lookup"><span data-stu-id="e95b7-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="e95b7-281">La operación add</span><span class="sxs-lookup"><span data-stu-id="e95b7-281">The add operation</span></span>

* <span data-ttu-id="e95b7-282">Si `path` apunta a un elemento de matriz: inserta un nuevo elemento delante del especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="e95b7-283">Si `path` apunta a una propiedad: establece el valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="e95b7-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="e95b7-284">Si `path` apunta a una ubicación que no existe:</span><span class="sxs-lookup"><span data-stu-id="e95b7-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="e95b7-285">Si el recurso para revisar es un objeto dinámico: agrega una propiedad.</span><span class="sxs-lookup"><span data-stu-id="e95b7-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="e95b7-286">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="e95b7-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="e95b7-287">El siguiente documento de revisión de ejemplo establece el valor de `CustomerName` y agrega un objeto `Order` al final de la matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="e95b7-288">La operación remove</span><span class="sxs-lookup"><span data-stu-id="e95b7-288">The remove operation</span></span>

* <span data-ttu-id="e95b7-289">Si `path` apunta a un elemento de matriz: quita el elemento.</span><span class="sxs-lookup"><span data-stu-id="e95b7-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="e95b7-290">Si `path` apunta a una propiedad:</span><span class="sxs-lookup"><span data-stu-id="e95b7-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="e95b7-291">Si el recurso para revisar es un objeto dinámico: quita la propiedad.</span><span class="sxs-lookup"><span data-stu-id="e95b7-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="e95b7-292">Si el recurso para revisar es un objeto estático:</span><span class="sxs-lookup"><span data-stu-id="e95b7-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="e95b7-293">Si la propiedad acepta valores NULL: la establece en null.</span><span class="sxs-lookup"><span data-stu-id="e95b7-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="e95b7-294">Si la propiedad es distinta de null, la establece en `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="e95b7-295">En el siguiente documento de revisión de ejemplo, se establece `CustomerName` en null y se elimina `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="e95b7-296">La operación replace</span><span class="sxs-lookup"><span data-stu-id="e95b7-296">The replace operation</span></span>

<span data-ttu-id="e95b7-297">Esta operación es funcionalmente igual que `remove` seguida de `add`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="e95b7-298">En el siguiente documento de revisión de ejemplo se establece el valor de `CustomerName` y se reemplaza `Orders[0]` por un nuevo objeto `Order`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="e95b7-299">La operación move</span><span class="sxs-lookup"><span data-stu-id="e95b7-299">The move operation</span></span>

* <span data-ttu-id="e95b7-300">Si `path` apunta a un elemento de matriz: copia el elemento `from` en la ubicación del elemento `path` y, luego, ejecuta una operación `remove` en el elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="e95b7-301">Si `path` apunta a una propiedad: copia el valor de la propiedad `from` en la propiedad `path` y, luego, ejecuta la operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="e95b7-302">Si `path` apunta a una propiedad que no existe:</span><span class="sxs-lookup"><span data-stu-id="e95b7-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="e95b7-303">Si el recurso para revisar es un objeto estático: la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="e95b7-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="e95b7-304">Si el recurso para revisar es un objeto dinámico: copia la propiedad `from` en la ubicación indicada por `path` y, luego, ejecuta una operación `remove` en la propiedad `from`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="e95b7-305">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e95b7-305">The following sample patch document:</span></span>

* <span data-ttu-id="e95b7-306">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e95b7-307">Se establece `Orders[0].OrderName` en null.</span><span class="sxs-lookup"><span data-stu-id="e95b7-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="e95b7-308">Se mueve `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="e95b7-309">La operación copy</span><span class="sxs-lookup"><span data-stu-id="e95b7-309">The copy operation</span></span>

<span data-ttu-id="e95b7-310">Esta operación es funcionalmente igual que la operación `move` sin el paso `remove` final.</span><span class="sxs-lookup"><span data-stu-id="e95b7-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="e95b7-311">En el siguiente documento de revisión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e95b7-311">The following sample patch document:</span></span>

* <span data-ttu-id="e95b7-312">Se copia el valor de `Orders[0].OrderName` en `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e95b7-313">Se inserta una copia de `Orders[1]` delante de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e95b7-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="e95b7-314">La operación test</span><span class="sxs-lookup"><span data-stu-id="e95b7-314">The test operation</span></span>

<span data-ttu-id="e95b7-315">Si el valor de la ubicación indicada por `path` es diferente del valor proporcionado en `value`, la solicitud produce un error.</span><span class="sxs-lookup"><span data-stu-id="e95b7-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="e95b7-316">En ese caso, la solicitud PATCH entera produce un error incluso si todas las demás operaciones del documento de revisión se realizan correctamente.</span><span class="sxs-lookup"><span data-stu-id="e95b7-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="e95b7-317">La operación `test` se usa habitualmente para impedir una actualización cuando hay un conflicto de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="e95b7-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="e95b7-318">El siguiente documento de revisión de ejemplo no tiene ningún efecto si el valor inicial de `CustomerName` es "John", porque la prueba produce un error:</span><span class="sxs-lookup"><span data-stu-id="e95b7-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="e95b7-319">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="e95b7-319">Get the code</span></span>

<span data-ttu-id="e95b7-320">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="e95b7-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="e95b7-321">([Método de descarga](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e95b7-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="e95b7-322">Para probar el ejemplo, ejecute la aplicación y envíe solicitudes HTTP con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="e95b7-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="e95b7-323">Dirección URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="e95b7-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="e95b7-324">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="e95b7-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="e95b7-325">Encabezado: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="e95b7-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="e95b7-326">Cuerpo: Copie y pegue uno de los ejemplos de documentos de revisión de JSON de la carpeta de proyecto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="e95b7-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e95b7-327">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e95b7-327">Additional resources</span></span>

* [<span data-ttu-id="e95b7-328">Especificación del método PATCH de IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="e95b7-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="e95b7-329">Especificación del método JSON PATCH de IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="e95b7-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="e95b7-330">Especificación del formato de ruta de acceso JSON Patch de IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="e95b7-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="e95b7-331">[Documentación de JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="e95b7-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="e95b7-332">Incluye vínculos a recursos para crear documentos JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="e95b7-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="e95b7-333">Código fuente de JSON Patch para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e95b7-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
