---
title: Tipos de valor devuelto de acción del controlador de la API web de ASP.NET Core
author: scottaddie
description: Obtenga información sobre los distintos tipos de valor devuelto de método de acción del controlador de la API web de ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
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
uid: web-api/action-return-types
ms.openlocfilehash: a2866970a20785ae8fa306d484972697817b7f92
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058953"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Tipos de valor devuelto de acción del controlador de la API web de ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

ASP.NET Core ofrece las siguientes opciones relativas a los tipos de valor devuelto de acción del controlador de la API web:

::: moniker range=">= aspnetcore-2.1"

* [Tipo específico](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Tipo específico](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

En este documento se explica cuándo resulta más adecuado usar cada tipo de valor devuelto.

## <a name="specific-type"></a>Tipo específico

La acción más sencilla devuelve un tipo de datos primitivo o complejo (por ejemplo, `string` o un tipo de objeto personalizado). Consideremos la siguiente acción, que devuelve una colección de objetos `Product` personalizados:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Sin condiciones conocidas de las que haya que protegerse durante la ejecución de la acción, bastaría con que se devolviera un tipo específico. La acción anterior no acepta parámetros, por lo que no se necesita ninguna validación de restricciones de parámetros.

Cuando se pueden utilizar varios tipos de valor devuelto, es habitual mezclar un <xref:Microsoft.AspNetCore.Mvc.ActionResult> tipo de valor devuelto con el tipo de valor devuelto primitivo o complejo. [IActionResult](#iactionresult-type) o [ActionResult \<T> ](#actionresultt-type) son necesarios para acomodar este tipo de acción. En este documento se proporcionan varios ejemplos de varios tipos de valor devueltos.

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>Devolver IEnumerable \<T> o IAsyncEnumerable\<T>

En ASP.net Core 2.2 y versiones anteriores, la devolución de <xref:System.Collections.Generic.IEnumerable%601> en una acción da como resultado la iteración de la colección sincrónica por parte del serializador. El resultado es el bloqueo de llamadas y una posibilidad de colapso del grupo de subprocesos. Por poner un ejemplo, imagine que se usa Entity Framework (EF) Core para las necesidades de acceso a los datos de la API Web. El tipo de valor devuelto de la acción siguiente se enumera sincrónicamente durante la serialización:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Para evitar la enumeración sincrónica y las esperas por bloqueo en la base de datos en ASP.NET Core 2.2 y versiones anteriores, invoque `ToListAsync`:

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

En ASP.net Core 3,0 y versiones posteriores, que una acción devuelva `IAsyncEnumerable<T>`:

* Ya no da como resultado iteraciones sincrónicas.
* Es tan eficaz como devolver <xref:System.Collections.Generic.IEnumerable%601>.

Tanto ASP.NET Core 3.0 como las versiones posteriores almacenan en búfer el resultado de la siguiente acción antes de proporcionarlo al serializador:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Considere la posibilidad de declarar el tipo de valor devuelto de la signatura de la acción como `IAsyncEnumerable<T>` para garantizar la iteración asincrónica. En última instancia, el modo de iteración se basa en el tipo concreto subyacente que se va a devolver. MVC almacena en búfer automáticamente cualquier tipo concreto que implemente `IAsyncEnumerable<T>`.

Considere la siguiente acción, que devuelve los registros de producto con precio de venta como `IEnumerable<Product>`:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

El `IAsyncEnumerable<Product>` equivalente de la acción anterior es:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

Las dos acciones anteriores no suponen ningún bloqueo a partir de ASP.NET Core 3.0.

## <a name="iactionresult-type"></a>Tipo IActionResult

El tipo de valor devuelto <xref:Microsoft.AspNetCore.Mvc.IActionResult> resulta adecuado cuando existen varios tipos de valor devuelto `ActionResult` posibles en una acción. Los tipos `ActionResult` representan varios códigos de estado HTTP. Cualquier clase no abstracta derivada de `ActionResult` se considera un tipo de valor devuelto válido. Algunos tipos de valor devueltos comunes en esta categoría son <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) y <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). Como alternativa, se pueden usar métodos de conveniencia en la clase <xref:Microsoft.AspNetCore.Mvc.ControllerBase> para devolver tipos `ActionResult` de una acción. Por ejemplo, `return BadRequest();` es una forma abreviada de `return new BadRequestResult();`.

Dado que hay varios tipos de valor devuelto y rutas de acceso en este tipo de acción, es necesario el uso liberal del [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) atributo. Este atributo genera detalles de respuesta más pormenorizados relativos a las páginas de ayuda de API web generadas por herramientas como [Swagger](xref:tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]` indica los tipos conocidos y los códigos de estado HTTP que la acción va a devolver.

### <a name="synchronous-action"></a>Acción sincrónica

Veamos la siguiente acción sincrónica, en la que pueden darse dos tipos de valor devuelto posibles:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

En la acción anterior:

* Se devuelve un código de estado 404 cuando el producto representado por `id` no existe en el almacén de datos subyacente. El método de conveniencia <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> se invoca como una abreviatura para `return new NotFoundResult();`.
* Se devuelve un código de estado 200 con el objeto `Product` cuando existe el producto. El método de conveniencia <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> se invoca como una abreviatura para `return new OkObjectResult(product);`.

### <a name="asynchronous-action"></a>Acción asincrónica

Veamos la siguiente acción asincrónica, en la que pueden darse dos tipos de valor devuelto posibles:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

En la acción anterior:

* Se devuelve un código de estado 400 cuando la descripción del producto contiene "XYZ Widget". El método de conveniencia <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> se invoca como una abreviatura para `return new BadRequestResult();`.
* Al crear un producto, el método de conveniencia <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> genera un código de estado 201. Una alternativa a la llamada a `CreatedAtAction` es `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`. En esta ruta de acceso de código, el objeto `Product` se proporciona en el cuerpo de la respuesta. Se proporciona un encabezado de respuesta `Location` que contiene la dirección URL del producto recién creada.

Por ejemplo, el siguiente modelo indica que las solicitudes deben incluir las propiedades `Name` y `Description`. Si no se proporcionan `Name` y `Description` en la solicitud, se producirá un error en la validación de los modelos.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Si [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) se aplica el atributo en ASP.NET Core 2,1 o posterior, los errores de validación del modelo dan como resultado un código de estado 400. Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>\<T>Tipo ActionResult

ASP.NET Core 2,1 presentó el tipo de valor devuelto [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) para las acciones del controlador de API Web. Permite devolver un tipo que se deriva de <xref:Microsoft.AspNetCore.Mvc.ActionResult> o bien un [tipo específico](#specific-type). `ActionResult<T>` reporta las siguientes ventajas con frente al [tipo IActionResult](#iactionresult-type):

* [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) `Type` Se puede excluir la propiedad del atributo. Por ejemplo, `[ProducesResponseType(200, Type = typeof(Product))]` se simplifica a `[ProducesResponseType(200)]`. En su lugar, el tipo de valor devuelto esperado de la acción se infiere desde `T` en `ActionResult<T>`.
* Los [operadores de conversión implícitos](/dotnet/csharp/language-reference/keywords/implicit) admiten la conversión tanto de `T` como de `ActionResult` en `ActionResult<T>`. `T` se convierte en <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, lo que significa que `return new ObjectResult(T);` se ha simplificado para `return T;`.

C# no admite operadores de conversión implícitos en las interfaces. Por consiguiente, la conversión de la interfaz a un tipo concreto es necesaria para usar `ActionResult<T>`. Por ejemplo, el uso de `IEnumerable` en el siguiente ejemplo no funciona:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Una opción para corregir el código anterior es devolver `_repository.GetProducts().ToList();`.

La mayoría de las acciones tiene un tipo de valor devuelto específico. Se pueden producir condiciones inesperadas que durante la ejecución de una acción, en cuyo caso no se devuelve el tipo específico. Por ejemplo, puede suceder que el parámetro de entrada de una acción genere un error de validación de modelos. En tal caso, lo normal es que se devuelva el tipo `ActionResult` correspondiente en lugar del tipo específico.

### <a name="synchronous-action"></a>Acción sincrónica

Veamos una acción sincrónica, en la que pueden darse dos tipos de valor devuelto posibles:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

En la acción anterior:

* Se devuelve un código de estado 404 cuando el producto no existe en la base de datos.
* Se devuelve un código de estado 200 con el objeto `Product` correspondiente cuando existe el producto. Antes de ASP.NET Core 2.1, la línea `return product;` tenía que ser `return Ok(product);`.

### <a name="asynchronous-action"></a>Acción asincrónica

Veamos una acción asincrónica, en la que pueden darse dos tipos de valor devuelto posibles:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

En la acción anterior:

* El entorno de ejecución de ASP.NET Core devuelve un código de estado 400 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) en los casos siguientes:
  * Se ha [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) aplicado el atributo y se produce un error en la validación del modelo.
  * La descripción del producto contiene "Widget XYZ".
* Al crear un producto, el método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> genera un código de estado 201. En esta ruta de acceso de código, el objeto `Product` se proporciona en el cuerpo de la respuesta. Se proporciona un encabezado de respuesta `Location` que contiene la dirección URL del producto recién creada.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
