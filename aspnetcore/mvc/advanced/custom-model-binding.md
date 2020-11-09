---
title: Enlace de modelos personalizado en ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo el enlace de modelos permite que las acciones de controlador funcionen directamente con tipos de modelos en ASP.NET Core.
ms.author: riande
ms.date: 01/06/2020
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 7675e95c43b9ee428ee5fda86ea3ead9815ed645
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058472"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="c2457-103">Enlace de modelos personalizado en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2457-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2457-104">De [Steve Smith](https://ardalis.com/) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="c2457-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="c2457-105">Con el enlace de modelos, las acciones de controlador pueden funcionar directamente con tipos de modelos (pasados como argumentos de método), en lugar de con solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c2457-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="c2457-106">La asignación entre los datos de solicitudes entrantes y los modelos de aplicaciones se controla por medio de enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="c2457-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="c2457-107">Los desarrolladores pueden ampliar la funcionalidad integrada de enlace de modelos implementando enlazadores de modelos personalizados (si bien, por lo general, no es necesario escribir un proveedor propio).</span><span class="sxs-lookup"><span data-stu-id="c2457-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="c2457-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2457-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="c2457-109">Limitaciones de los enlazadores de modelos predeterminados</span><span class="sxs-lookup"><span data-stu-id="c2457-109">Default model binder limitations</span></span>

<span data-ttu-id="c2457-110">Los enlazadores de modelos predeterminados admiten la mayoría de los tipos de datos de .NET Core comunes y deberían cubrir las necesidades de casi cualquier desarrollador.</span><span class="sxs-lookup"><span data-stu-id="c2457-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="c2457-111">Esperan enlazar entradas basadas en texto desde la solicitud directamente a tipos de modelos.</span><span class="sxs-lookup"><span data-stu-id="c2457-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="c2457-112">Puede que sea necesario transformar la entrada antes de enlazarla.</span><span class="sxs-lookup"><span data-stu-id="c2457-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="c2457-113">Es el caso, por ejemplo, si tiene una clave que se puede usar para buscar datos del modelo.</span><span class="sxs-lookup"><span data-stu-id="c2457-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="c2457-114">Se puede usar un enlazador de modelos personalizado para capturar datos en función de la clave.</span><span class="sxs-lookup"><span data-stu-id="c2457-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="c2457-115">Revisión del enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="c2457-115">Model binding review</span></span>

<span data-ttu-id="c2457-116">El enlace de modelos usa definiciones específicas de los tipos con los que funciona.</span><span class="sxs-lookup"><span data-stu-id="c2457-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="c2457-117">Un *tipo simple* se convierte a partir de una sola cadena de la entrada,</span><span class="sxs-lookup"><span data-stu-id="c2457-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="c2457-118">mientras que un *tipo complejo* se convierte a partir de varios valores de entrada.</span><span class="sxs-lookup"><span data-stu-id="c2457-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="c2457-119">El marco establece la diferencia dependiendo de si existe un `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="c2457-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="c2457-120">Conviene crear un convertidor de tipos si existe una asignación simple `string` -> `SomeType` que no necesita recursos externos.</span><span class="sxs-lookup"><span data-stu-id="c2457-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="c2457-121">Antes de crear su propio enlazador de modelos personalizado, no está de más que repase cómo se implementan los enlazadores de modelos existentes.</span><span class="sxs-lookup"><span data-stu-id="c2457-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="c2457-122">Hay que considerar el uso de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, que sirve para convertir cadenas codificadas con base64 en matrices de bytes.</span><span class="sxs-lookup"><span data-stu-id="c2457-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="c2457-123">Las matrices de bytes se suelen almacenar como archivos o como campos de tipo BLOB de base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2457-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="c2457-124">Trabajar con ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="c2457-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="c2457-125">Las cadenas codificadas con base64 se pueden usar para representar datos binarios.</span><span class="sxs-lookup"><span data-stu-id="c2457-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="c2457-126">Por ejemplo, una imagen se puede codificar como una cadena.</span><span class="sxs-lookup"><span data-stu-id="c2457-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="c2457-127">En el ejemplo se incluye una imagen como una cadena codificada con base64 en [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="c2457-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="c2457-128">ASP.NET Core MVC toma cadenas codificadas con base64 y usa un `ByteArrayModelBinder` para convertirlas en una matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="c2457-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="c2457-129"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> asigna argumentos `byte[]` a `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="c2457-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

<span data-ttu-id="c2457-130">Cuando cree su propio enlazador de modelos personalizado, puede implementar su tipo `IModelBinderProvider` particular o usar <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span><span class="sxs-lookup"><span data-stu-id="c2457-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="c2457-131">En el siguiente ejemplo se indica cómo usar `ByteArrayModelBinder` para convertir una cadena codificada con base64 en un `byte[]` y guardar el resultado en un archivo:</span><span class="sxs-lookup"><span data-stu-id="c2457-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="c2457-132">Se puede usar un método API POST en una cadena codificada con base64 con una herramienta como [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="c2457-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="c2457-133">![Postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="c2457-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="c2457-134">Siempre y cuando el enlazador pueda enlazar datos de la solicitud a argumentos o propiedades con el nombre adecuado, el enlace de modelos se realizará correctamente.</span><span class="sxs-lookup"><span data-stu-id="c2457-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="c2457-135">En el siguiente ejemplo se muestra cómo usar `ByteArrayModelBinder` con un modelo de vista:</span><span class="sxs-lookup"><span data-stu-id="c2457-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="c2457-136">Ejemplo de enlazador de modelos personalizado</span><span class="sxs-lookup"><span data-stu-id="c2457-136">Custom model binder sample</span></span>

<span data-ttu-id="c2457-137">En esta sección implementaremos un enlazador de modelos personalizado que haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2457-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="c2457-138">Convertir los datos de solicitud entrantes en argumentos clave fuertemente tipados</span><span class="sxs-lookup"><span data-stu-id="c2457-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="c2457-139">Usar Entity Framework Core para capturar la entidad asociada</span><span class="sxs-lookup"><span data-stu-id="c2457-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="c2457-140">Pasar la entidad asociada como argumento al método de acción</span><span class="sxs-lookup"><span data-stu-id="c2457-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="c2457-141">En el siguiente ejemplo se usa el atributo `ModelBinder` en el modelo `Author`:</span><span class="sxs-lookup"><span data-stu-id="c2457-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="c2457-142">En el código anterior, el atributo `ModelBinder` especifica el tipo de `IModelBinder` que se debe emplear para enlazar parámetros de acción de `Author`.</span><span class="sxs-lookup"><span data-stu-id="c2457-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="c2457-143">La clase `AuthorEntityBinder` siguiente enlaza un parámetro `Author` capturando la entidad de un origen de datos por medio de Entity Framework Core y un elemento `authorId`:</span><span class="sxs-lookup"><span data-stu-id="c2457-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="c2457-144">La clase `AuthorEntityBinder` anterior está diseñada para ilustrar un enlazador de modelos personalizado.</span><span class="sxs-lookup"><span data-stu-id="c2457-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="c2457-145">La clase no está pensada para ilustrar los procedimientos recomendados para un escenario de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="c2457-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="c2457-146">Para la búsqueda, enlace el valor `authorId` y consulte la base de datos en un método de acción.</span><span class="sxs-lookup"><span data-stu-id="c2457-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="c2457-147">Este enfoque permite diferenciar y separar los errores de enlace de modelo de los casos `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="c2457-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="c2457-148">En el siguiente código se indica cómo usar `AuthorEntityBinder` en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="c2457-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="c2457-149">El atributo `ModelBinder` se puede usar para aplicar `AuthorEntityBinder` a los parámetros que no usan convenciones predeterminadas:</span><span class="sxs-lookup"><span data-stu-id="c2457-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="c2457-150">En este ejemplo, como el nombre del argumento no es el `authorId` predeterminado, se especifica en el parámetro por medio del atributo `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="c2457-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="c2457-151">Tanto el controlador como el método de acción se simplifican, en contraste con tener que buscar la entidad en el método de acción.</span><span class="sxs-lookup"><span data-stu-id="c2457-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="c2457-152">La lógica para capturar el autor a través de Entity Framework Core se traslada al enlazador de modelos.</span><span class="sxs-lookup"><span data-stu-id="c2457-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="c2457-153">Esto puede reducir enormemente la complejidad cuando existen varios métodos que se enlazan al modelo `Author`.</span><span class="sxs-lookup"><span data-stu-id="c2457-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="c2457-154">Puede aplicar el atributo `ModelBinder` a propiedades de modelo individuales (como en un modelo de vista) o a parámetros del método de acción para especificar un determinado nombre de modelo o enlazador de modelos que sea exclusivo de ese tipo o acción en particular.</span><span class="sxs-lookup"><span data-stu-id="c2457-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="c2457-155">Implementar un ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="c2457-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="c2457-156">En lugar de aplicar un atributo, puede implementar `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="c2457-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="c2457-157">Así es como se implementan los enlazadores de marco integrados.</span><span class="sxs-lookup"><span data-stu-id="c2457-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="c2457-158">Cuando se especifica el tipo con el que el enlazador funciona, lo que se está indicando es el tipo de argumento que ese enlazador genera, **no** la entrada que acepta.</span><span class="sxs-lookup"><span data-stu-id="c2457-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="c2457-159">El siguiente proveedor de enlazador funciona con `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="c2457-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="c2457-160">Cuando se agrega a la colección de proveedores de MVC, no es necesario usar el atributo `ModelBinder` en los parámetros con tipo `Author` o `Author`.</span><span class="sxs-lookup"><span data-stu-id="c2457-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="c2457-161">Nota: El código anterior devuelve un `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="c2457-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="c2457-162">`BinderTypeModelBinder` actúa como una fábrica de enlazadores de modelos y proporciona la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2457-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="c2457-163">`AuthorEntityBinder` requiere que la inserción de dependencias tenga acceso a Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c2457-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="c2457-164">Use `BinderTypeModelBinder` si su enlazador de modelos necesita servicios de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2457-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="c2457-165">Para usar un proveedor de enlazadores de modelos personalizado, agréguelo a `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c2457-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="c2457-166">Al evaluar enlazadores de modelos, la colección de proveedores se examina en orden.</span><span class="sxs-lookup"><span data-stu-id="c2457-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="c2457-167">Se utiliza el primer proveedor que devuelve un enlazador que coincide con el modelo de entrada.</span><span class="sxs-lookup"><span data-stu-id="c2457-167">The first provider that returns a binder that matches the input model is used.</span></span> <span data-ttu-id="c2457-168">Al agregar el proveedor al final de la colección, es posible que se llame a un enlazador de modelos integrado antes de que el enlazador personalizado tenga la oportunidad.</span><span class="sxs-lookup"><span data-stu-id="c2457-168">Adding your provider to the end of the collection may thus result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="c2457-169">En este ejemplo, el proveedor personalizado se agrega al principio de la colección para asegurarse de que siempre se usa para los `Author` argumentos de acción.</span><span class="sxs-lookup"><span data-stu-id="c2457-169">In this example, the custom provider is added to the beginning of the collection to ensure it's always used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="c2457-170">Enlace de modelos polimórfico</span><span class="sxs-lookup"><span data-stu-id="c2457-170">Polymorphic model binding</span></span>

<span data-ttu-id="c2457-171">El enlace a diferentes modelos de tipos derivados se conoce como enlace de modelos polimórfico.</span><span class="sxs-lookup"><span data-stu-id="c2457-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="c2457-172">El enlace de modelos personalizado polimórfico es necesario cuando el valor de la solicitud se debe enlazar al tipo de modelo derivado específico.</span><span class="sxs-lookup"><span data-stu-id="c2457-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="c2457-173">Enlace de modelos polimórfico:</span><span class="sxs-lookup"><span data-stu-id="c2457-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="c2457-174">No es habitual para una API REST diseñada para interoperar con todos los lenguajes.</span><span class="sxs-lookup"><span data-stu-id="c2457-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="c2457-175">Dificulta el razonamiento sobre los modelos enlazados.</span><span class="sxs-lookup"><span data-stu-id="c2457-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="c2457-176">Sin embargo, si una aplicación requiere el enlace de modelos polimórfico, una implementación podría ser similar al código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2457-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="c2457-177">Sugerencias y procedimientos recomendados</span><span class="sxs-lookup"><span data-stu-id="c2457-177">Recommendations and best practices</span></span>

<span data-ttu-id="c2457-178">Los enlazadores de modelos personalizados deben caracterizarse por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2457-178">Custom model binders:</span></span>

- <span data-ttu-id="c2457-179">No deben tratar de establecer códigos de estado ni devolver resultados (por ejemplo, 404 No encontrado).</span><span class="sxs-lookup"><span data-stu-id="c2457-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="c2457-180">Los errores que se produzcan en un enlace de modelos se deben controlar con un [filtro de acciones](xref:mvc/controllers/filters) o con la lógica del propio método de acción.</span><span class="sxs-lookup"><span data-stu-id="c2457-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="c2457-181">Son realmente útiles para eliminar el código repetitivo y las cuestiones transversales de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="c2457-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="c2457-182">No se deben usar en general para convertir una cadena en un tipo personalizado. Para ello, <xref:System.ComponentModel.TypeConverter> suele ser una mejor opción.</span><span class="sxs-lookup"><span data-stu-id="c2457-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c2457-183">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c2457-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c2457-184">Con el enlace de modelos, las acciones de controlador pueden funcionar directamente con tipos de modelos (pasados como argumentos de método), en lugar de con solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="c2457-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="c2457-185">La asignación entre los datos de solicitudes entrantes y los modelos de aplicaciones se controla por medio de enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="c2457-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="c2457-186">Los desarrolladores pueden ampliar la funcionalidad integrada de enlace de modelos implementando enlazadores de modelos personalizados (si bien, por lo general, no es necesario escribir un proveedor propio).</span><span class="sxs-lookup"><span data-stu-id="c2457-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="c2457-187">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2457-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="c2457-188">Limitaciones de los enlazadores de modelos predeterminados</span><span class="sxs-lookup"><span data-stu-id="c2457-188">Default model binder limitations</span></span>

<span data-ttu-id="c2457-189">Los enlazadores de modelos predeterminados admiten la mayoría de los tipos de datos de .NET Core comunes y deberían cubrir las necesidades de casi cualquier desarrollador.</span><span class="sxs-lookup"><span data-stu-id="c2457-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="c2457-190">Esperan enlazar entradas basadas en texto desde la solicitud directamente a tipos de modelos.</span><span class="sxs-lookup"><span data-stu-id="c2457-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="c2457-191">Puede que sea necesario transformar la entrada antes de enlazarla.</span><span class="sxs-lookup"><span data-stu-id="c2457-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="c2457-192">Es el caso, por ejemplo, si tiene una clave que se puede usar para buscar datos del modelo.</span><span class="sxs-lookup"><span data-stu-id="c2457-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="c2457-193">Se puede usar un enlazador de modelos personalizado para capturar datos en función de la clave.</span><span class="sxs-lookup"><span data-stu-id="c2457-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="c2457-194">Revisión del enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="c2457-194">Model binding review</span></span>

<span data-ttu-id="c2457-195">El enlace de modelos usa definiciones específicas de los tipos con los que funciona.</span><span class="sxs-lookup"><span data-stu-id="c2457-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="c2457-196">Un *tipo simple* se convierte a partir de una sola cadena de la entrada,</span><span class="sxs-lookup"><span data-stu-id="c2457-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="c2457-197">mientras que un *tipo complejo* se convierte a partir de varios valores de entrada.</span><span class="sxs-lookup"><span data-stu-id="c2457-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="c2457-198">El marco establece la diferencia dependiendo de si existe un `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="c2457-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="c2457-199">Conviene crear un convertidor de tipos si existe una asignación simple `string` -> `SomeType` que no necesita recursos externos.</span><span class="sxs-lookup"><span data-stu-id="c2457-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="c2457-200">Antes de crear su propio enlazador de modelos personalizado, no está de más que repase cómo se implementan los enlazadores de modelos existentes.</span><span class="sxs-lookup"><span data-stu-id="c2457-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="c2457-201">Hay que considerar el uso de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, que sirve para convertir cadenas codificadas con base64 en matrices de bytes.</span><span class="sxs-lookup"><span data-stu-id="c2457-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="c2457-202">Las matrices de bytes se suelen almacenar como archivos o como campos de tipo BLOB de base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2457-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="c2457-203">Trabajar con ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="c2457-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="c2457-204">Las cadenas codificadas con base64 se pueden usar para representar datos binarios.</span><span class="sxs-lookup"><span data-stu-id="c2457-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="c2457-205">Por ejemplo, una imagen se puede codificar como una cadena.</span><span class="sxs-lookup"><span data-stu-id="c2457-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="c2457-206">En el ejemplo se incluye una imagen como una cadena codificada con base64 en [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="c2457-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="c2457-207">ASP.NET Core MVC toma cadenas codificadas con base64 y usa un `ByteArrayModelBinder` para convertirlas en una matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="c2457-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="c2457-208"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> asigna argumentos `byte[]` a `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="c2457-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="c2457-209">Cuando cree su propio enlazador de modelos personalizado, puede implementar su tipo `IModelBinderProvider` particular o usar <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span><span class="sxs-lookup"><span data-stu-id="c2457-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="c2457-210">En el siguiente ejemplo se indica cómo usar `ByteArrayModelBinder` para convertir una cadena codificada con base64 en un `byte[]` y guardar el resultado en un archivo:</span><span class="sxs-lookup"><span data-stu-id="c2457-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="c2457-211">Se puede usar un método API POST en una cadena codificada con base64 con una herramienta como [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="c2457-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="c2457-212">![Postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="c2457-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="c2457-213">Siempre y cuando el enlazador pueda enlazar datos de la solicitud a argumentos o propiedades con el nombre adecuado, el enlace de modelos se realizará correctamente.</span><span class="sxs-lookup"><span data-stu-id="c2457-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="c2457-214">En el siguiente ejemplo se muestra cómo usar `ByteArrayModelBinder` con un modelo de vista:</span><span class="sxs-lookup"><span data-stu-id="c2457-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="c2457-215">Ejemplo de enlazador de modelos personalizado</span><span class="sxs-lookup"><span data-stu-id="c2457-215">Custom model binder sample</span></span>

<span data-ttu-id="c2457-216">En esta sección implementaremos un enlazador de modelos personalizado que haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2457-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="c2457-217">Convertir los datos de solicitud entrantes en argumentos clave fuertemente tipados</span><span class="sxs-lookup"><span data-stu-id="c2457-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="c2457-218">Usar Entity Framework Core para capturar la entidad asociada</span><span class="sxs-lookup"><span data-stu-id="c2457-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="c2457-219">Pasar la entidad asociada como argumento al método de acción</span><span class="sxs-lookup"><span data-stu-id="c2457-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="c2457-220">En el siguiente ejemplo se usa el atributo `ModelBinder` en el modelo `Author`:</span><span class="sxs-lookup"><span data-stu-id="c2457-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="c2457-221">En el código anterior, el atributo `ModelBinder` especifica el tipo de `IModelBinder` que se debe emplear para enlazar parámetros de acción de `Author`.</span><span class="sxs-lookup"><span data-stu-id="c2457-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="c2457-222">La clase `AuthorEntityBinder` siguiente enlaza un parámetro `Author` capturando la entidad de un origen de datos por medio de Entity Framework Core y un elemento `authorId`:</span><span class="sxs-lookup"><span data-stu-id="c2457-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="c2457-223">La clase `AuthorEntityBinder` anterior está diseñada para ilustrar un enlazador de modelos personalizado.</span><span class="sxs-lookup"><span data-stu-id="c2457-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="c2457-224">La clase no está pensada para ilustrar los procedimientos recomendados para un escenario de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="c2457-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="c2457-225">Para la búsqueda, enlace el valor `authorId` y consulte la base de datos en un método de acción.</span><span class="sxs-lookup"><span data-stu-id="c2457-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="c2457-226">Este enfoque permite diferenciar y separar los errores de enlace de modelo de los casos `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="c2457-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="c2457-227">En el siguiente código se indica cómo usar `AuthorEntityBinder` en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="c2457-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="c2457-228">El atributo `ModelBinder` se puede usar para aplicar `AuthorEntityBinder` a los parámetros que no usan convenciones predeterminadas:</span><span class="sxs-lookup"><span data-stu-id="c2457-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="c2457-229">En este ejemplo, como el nombre del argumento no es el `authorId` predeterminado, se especifica en el parámetro por medio del atributo `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="c2457-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="c2457-230">Tanto el controlador como el método de acción se simplifican, en contraste con tener que buscar la entidad en el método de acción.</span><span class="sxs-lookup"><span data-stu-id="c2457-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="c2457-231">La lógica para capturar el autor a través de Entity Framework Core se traslada al enlazador de modelos.</span><span class="sxs-lookup"><span data-stu-id="c2457-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="c2457-232">Esto puede reducir enormemente la complejidad cuando existen varios métodos que se enlazan al modelo `Author`.</span><span class="sxs-lookup"><span data-stu-id="c2457-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="c2457-233">Puede aplicar el atributo `ModelBinder` a propiedades de modelo individuales (como en un modelo de vista) o a parámetros del método de acción para especificar un determinado nombre de modelo o enlazador de modelos que sea exclusivo de ese tipo o acción en particular.</span><span class="sxs-lookup"><span data-stu-id="c2457-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="c2457-234">Implementar un ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="c2457-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="c2457-235">En lugar de aplicar un atributo, puede implementar `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="c2457-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="c2457-236">Así es como se implementan los enlazadores de marco integrados.</span><span class="sxs-lookup"><span data-stu-id="c2457-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="c2457-237">Cuando se especifica el tipo con el que el enlazador funciona, lo que se está indicando es el tipo de argumento que ese enlazador genera, **no** la entrada que acepta.</span><span class="sxs-lookup"><span data-stu-id="c2457-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="c2457-238">El siguiente proveedor de enlazador funciona con `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="c2457-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="c2457-239">Cuando se agrega a la colección de proveedores de MVC, no es necesario usar el atributo `ModelBinder` en los parámetros con tipo `Author` o `Author`.</span><span class="sxs-lookup"><span data-stu-id="c2457-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="c2457-240">Nota: El código anterior devuelve un `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="c2457-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="c2457-241">`BinderTypeModelBinder` actúa como una fábrica de enlazadores de modelos y proporciona la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2457-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="c2457-242">`AuthorEntityBinder` requiere que la inserción de dependencias tenga acceso a Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c2457-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="c2457-243">Use `BinderTypeModelBinder` si su enlazador de modelos necesita servicios de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2457-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="c2457-244">Para usar un proveedor de enlazadores de modelos personalizado, agréguelo a `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c2457-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="c2457-245">Al evaluar enlazadores de modelos, la colección de proveedores se examina en orden.</span><span class="sxs-lookup"><span data-stu-id="c2457-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="c2457-246">Se usará el primer proveedor que devuelva un enlazador.</span><span class="sxs-lookup"><span data-stu-id="c2457-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="c2457-247">Si su proveedor se agrega al final de la colección, puede ocurrir que se llame a un enlazador de modelos integrado antes que al suyo.</span><span class="sxs-lookup"><span data-stu-id="c2457-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="c2457-248">En este ejemplo, el proveedor personalizado se agrega al principio de la colección para procurar que se use en los argumentos de acción de `Author`.</span><span class="sxs-lookup"><span data-stu-id="c2457-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="c2457-249">Enlace de modelos polimórfico</span><span class="sxs-lookup"><span data-stu-id="c2457-249">Polymorphic model binding</span></span>

<span data-ttu-id="c2457-250">El enlace a diferentes modelos de tipos derivados se conoce como enlace de modelos polimórfico.</span><span class="sxs-lookup"><span data-stu-id="c2457-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="c2457-251">El enlace de modelos personalizado polimórfico es necesario cuando el valor de la solicitud se debe enlazar al tipo de modelo derivado específico.</span><span class="sxs-lookup"><span data-stu-id="c2457-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="c2457-252">Enlace de modelos polimórfico:</span><span class="sxs-lookup"><span data-stu-id="c2457-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="c2457-253">No es habitual para una API REST diseñada para interoperar con todos los lenguajes.</span><span class="sxs-lookup"><span data-stu-id="c2457-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="c2457-254">Dificulta el razonamiento sobre los modelos enlazados.</span><span class="sxs-lookup"><span data-stu-id="c2457-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="c2457-255">Sin embargo, si una aplicación requiere el enlace de modelos polimórfico, una implementación podría ser similar al código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2457-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="c2457-256">Sugerencias y procedimientos recomendados</span><span class="sxs-lookup"><span data-stu-id="c2457-256">Recommendations and best practices</span></span>

<span data-ttu-id="c2457-257">Los enlazadores de modelos personalizados deben caracterizarse por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2457-257">Custom model binders:</span></span>

- <span data-ttu-id="c2457-258">No deben tratar de establecer códigos de estado ni devolver resultados (por ejemplo, 404 No encontrado).</span><span class="sxs-lookup"><span data-stu-id="c2457-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="c2457-259">Los errores que se produzcan en un enlace de modelos se deben controlar con un [filtro de acciones](xref:mvc/controllers/filters) o con la lógica del propio método de acción.</span><span class="sxs-lookup"><span data-stu-id="c2457-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="c2457-260">Son realmente útiles para eliminar el código repetitivo y las cuestiones transversales de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="c2457-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="c2457-261">No se deben usar en general para convertir una cadena en un tipo personalizado. Para ello, <xref:System.ComponentModel.TypeConverter> suele ser una mejor opción.</span><span class="sxs-lookup"><span data-stu-id="c2457-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
