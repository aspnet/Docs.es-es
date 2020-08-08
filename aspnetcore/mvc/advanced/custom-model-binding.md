---
title: Enlace de modelos personalizado en ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo el enlace de modelos permite que las acciones de controlador funcionen directamente con tipos de modelos en ASP.NET Core.
ms.author: riande
ms.date: 01/06/2020
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 6e52d9c19a3d9c16f980021000db0bdecca5ed58
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018915"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="3001c-103">Enlace de modelos personalizado en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3001c-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3001c-104">De [Steve Smith](https://ardalis.com/) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="3001c-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="3001c-105">Con el enlace de modelos, las acciones de controlador pueden funcionar directamente con tipos de modelos (pasados como argumentos de método), en lugar de con solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="3001c-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="3001c-106">La asignación entre los datos de solicitudes entrantes y los modelos de aplicaciones se controla por medio de enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="3001c-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="3001c-107">Los desarrolladores pueden ampliar la funcionalidad integrada de enlace de modelos implementando enlazadores de modelos personalizados (si bien, por lo general, no es necesario escribir un proveedor propio).</span><span class="sxs-lookup"><span data-stu-id="3001c-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="3001c-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3001c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="3001c-109">Limitaciones de los enlazadores de modelos predeterminados</span><span class="sxs-lookup"><span data-stu-id="3001c-109">Default model binder limitations</span></span>

<span data-ttu-id="3001c-110">Los enlazadores de modelos predeterminados admiten la mayoría de los tipos de datos de .NET Core comunes y deberían cubrir las necesidades de casi cualquier desarrollador.</span><span class="sxs-lookup"><span data-stu-id="3001c-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="3001c-111">Esperan enlazar entradas basadas en texto desde la solicitud directamente a tipos de modelos.</span><span class="sxs-lookup"><span data-stu-id="3001c-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="3001c-112">Puede que sea necesario transformar la entrada antes de enlazarla.</span><span class="sxs-lookup"><span data-stu-id="3001c-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="3001c-113">Es el caso, por ejemplo, si tiene una clave que se puede usar para buscar datos del modelo.</span><span class="sxs-lookup"><span data-stu-id="3001c-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="3001c-114">Se puede usar un enlazador de modelos personalizado para capturar datos en función de la clave.</span><span class="sxs-lookup"><span data-stu-id="3001c-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="3001c-115">Revisión del enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="3001c-115">Model binding review</span></span>

<span data-ttu-id="3001c-116">El enlace de modelos usa definiciones específicas de los tipos con los que funciona.</span><span class="sxs-lookup"><span data-stu-id="3001c-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="3001c-117">Un *tipo simple* se convierte a partir de una sola cadena de la entrada,</span><span class="sxs-lookup"><span data-stu-id="3001c-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="3001c-118">mientras que un *tipo complejo* se convierte a partir de varios valores de entrada.</span><span class="sxs-lookup"><span data-stu-id="3001c-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="3001c-119">El marco establece la diferencia dependiendo de si existe un `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="3001c-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="3001c-120">Conviene crear un convertidor de tipos si existe una asignación simple `string` -> `SomeType` que no necesita recursos externos.</span><span class="sxs-lookup"><span data-stu-id="3001c-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="3001c-121">Antes de crear su propio enlazador de modelos personalizado, no está de más que repase cómo se implementan los enlazadores de modelos existentes.</span><span class="sxs-lookup"><span data-stu-id="3001c-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="3001c-122">Hay que considerar el uso de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, que sirve para convertir cadenas codificadas con base64 en matrices de bytes.</span><span class="sxs-lookup"><span data-stu-id="3001c-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="3001c-123">Las matrices de bytes se suelen almacenar como archivos o como campos de tipo BLOB de base de datos.</span><span class="sxs-lookup"><span data-stu-id="3001c-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="3001c-124">Trabajar con ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="3001c-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="3001c-125">Las cadenas codificadas con base64 se pueden usar para representar datos binarios.</span><span class="sxs-lookup"><span data-stu-id="3001c-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="3001c-126">Por ejemplo, una imagen se puede codificar como una cadena.</span><span class="sxs-lookup"><span data-stu-id="3001c-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="3001c-127">En el ejemplo se incluye una imagen como una cadena codificada con base64 en [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="3001c-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="3001c-128">ASP.NET Core MVC toma cadenas codificadas con base64 y usa un `ByteArrayModelBinder` para convertirlas en una matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="3001c-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="3001c-129"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> asigna argumentos `byte[]` a `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="3001c-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

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

<span data-ttu-id="3001c-130">Cuando cree su propio enlazador de modelos personalizado, puede implementar su tipo `IModelBinderProvider` particular o usar <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span><span class="sxs-lookup"><span data-stu-id="3001c-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="3001c-131">En el siguiente ejemplo se indica cómo usar `ByteArrayModelBinder` para convertir una cadena codificada con base64 en un `byte[]` y guardar el resultado en un archivo:</span><span class="sxs-lookup"><span data-stu-id="3001c-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="3001c-132">Se puede usar un método API POST en una cadena codificada con base64 con una herramienta como [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="3001c-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="3001c-133">![Postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="3001c-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="3001c-134">Siempre y cuando el enlazador pueda enlazar datos de la solicitud a argumentos o propiedades con el nombre adecuado, el enlace de modelos se realizará correctamente.</span><span class="sxs-lookup"><span data-stu-id="3001c-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="3001c-135">En el siguiente ejemplo se muestra cómo usar `ByteArrayModelBinder` con un modelo de vista:</span><span class="sxs-lookup"><span data-stu-id="3001c-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="3001c-136">Ejemplo de enlazador de modelos personalizado</span><span class="sxs-lookup"><span data-stu-id="3001c-136">Custom model binder sample</span></span>

<span data-ttu-id="3001c-137">En esta sección implementaremos un enlazador de modelos personalizado que haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3001c-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="3001c-138">Convertir los datos de solicitud entrantes en argumentos clave fuertemente tipados</span><span class="sxs-lookup"><span data-stu-id="3001c-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="3001c-139">Usar Entity Framework Core para capturar la entidad asociada</span><span class="sxs-lookup"><span data-stu-id="3001c-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="3001c-140">Pasar la entidad asociada como argumento al método de acción</span><span class="sxs-lookup"><span data-stu-id="3001c-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="3001c-141">En el siguiente ejemplo se usa el atributo `ModelBinder` en el modelo `Author`:</span><span class="sxs-lookup"><span data-stu-id="3001c-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="3001c-142">En el código anterior, el atributo `ModelBinder` especifica el tipo de `IModelBinder` que se debe emplear para enlazar parámetros de acción de `Author`.</span><span class="sxs-lookup"><span data-stu-id="3001c-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="3001c-143">La clase `AuthorEntityBinder` siguiente enlaza un parámetro `Author` capturando la entidad de un origen de datos por medio de Entity Framework Core y un elemento `authorId`:</span><span class="sxs-lookup"><span data-stu-id="3001c-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="3001c-144">La clase `AuthorEntityBinder` anterior está diseñada para ilustrar un enlazador de modelos personalizado.</span><span class="sxs-lookup"><span data-stu-id="3001c-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="3001c-145">La clase no está pensada para ilustrar los procedimientos recomendados para un escenario de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="3001c-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="3001c-146">Para la búsqueda, enlace el valor `authorId` y consulte la base de datos en un método de acción.</span><span class="sxs-lookup"><span data-stu-id="3001c-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="3001c-147">Este enfoque permite diferenciar y separar los errores de enlace de modelo de los casos `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="3001c-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="3001c-148">En el siguiente código se indica cómo usar `AuthorEntityBinder` en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="3001c-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="3001c-149">El atributo `ModelBinder` se puede usar para aplicar `AuthorEntityBinder` a los parámetros que no usan convenciones predeterminadas:</span><span class="sxs-lookup"><span data-stu-id="3001c-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="3001c-150">En este ejemplo, como el nombre del argumento no es el `authorId` predeterminado, se especifica en el parámetro por medio del atributo `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="3001c-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="3001c-151">Tanto el controlador como el método de acción se simplifican, en contraste con tener que buscar la entidad en el método de acción.</span><span class="sxs-lookup"><span data-stu-id="3001c-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="3001c-152">La lógica para capturar el autor a través de Entity Framework Core se traslada al enlazador de modelos.</span><span class="sxs-lookup"><span data-stu-id="3001c-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="3001c-153">Esto puede reducir enormemente la complejidad cuando existen varios métodos que se enlazan al modelo `Author`.</span><span class="sxs-lookup"><span data-stu-id="3001c-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="3001c-154">Puede aplicar el atributo `ModelBinder` a propiedades de modelo individuales (como en un modelo de vista) o a parámetros del método de acción para especificar un determinado nombre de modelo o enlazador de modelos que sea exclusivo de ese tipo o acción en particular.</span><span class="sxs-lookup"><span data-stu-id="3001c-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="3001c-155">Implementar un ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="3001c-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="3001c-156">En lugar de aplicar un atributo, puede implementar `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="3001c-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="3001c-157">Así es como se implementan los enlazadores de marco integrados.</span><span class="sxs-lookup"><span data-stu-id="3001c-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="3001c-158">Cuando se especifica el tipo con el que el enlazador funciona, lo que se está indicando es el tipo de argumento que ese enlazador genera, **no** la entrada que acepta.</span><span class="sxs-lookup"><span data-stu-id="3001c-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="3001c-159">El siguiente proveedor de enlazador funciona con `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="3001c-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="3001c-160">Cuando se agrega a la colección de proveedores de MVC, no es necesario usar el atributo `ModelBinder` en los parámetros con tipo `Author` o `Author`.</span><span class="sxs-lookup"><span data-stu-id="3001c-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="3001c-161">Nota: El código anterior devuelve un `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="3001c-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="3001c-162">`BinderTypeModelBinder` actúa como una fábrica de enlazadores de modelos y proporciona la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="3001c-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="3001c-163">`AuthorEntityBinder` requiere que la inserción de dependencias tenga acceso a Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="3001c-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="3001c-164">Use `BinderTypeModelBinder` si su enlazador de modelos necesita servicios de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="3001c-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="3001c-165">Para usar un proveedor de enlazadores de modelos personalizado, agréguelo a `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3001c-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="3001c-166">Al evaluar enlazadores de modelos, la colección de proveedores se examina en orden.</span><span class="sxs-lookup"><span data-stu-id="3001c-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="3001c-167">Se usará el primer proveedor que devuelva un enlazador.</span><span class="sxs-lookup"><span data-stu-id="3001c-167">The first provider that returns a binder is used.</span></span> <span data-ttu-id="3001c-168">Si su proveedor se agrega al final de la colección, puede ocurrir que se llame a un enlazador de modelos integrado antes que al suyo.</span><span class="sxs-lookup"><span data-stu-id="3001c-168">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="3001c-169">En este ejemplo, el proveedor personalizado se agrega al principio de la colección para procurar que se use en los argumentos de acción de `Author`.</span><span class="sxs-lookup"><span data-stu-id="3001c-169">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="3001c-170">Enlace de modelos polimórfico</span><span class="sxs-lookup"><span data-stu-id="3001c-170">Polymorphic model binding</span></span>

<span data-ttu-id="3001c-171">El enlace a diferentes modelos de tipos derivados se conoce como enlace de modelos polimórfico.</span><span class="sxs-lookup"><span data-stu-id="3001c-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="3001c-172">El enlace de modelos personalizado polimórfico es necesario cuando el valor de la solicitud se debe enlazar al tipo de modelo derivado específico.</span><span class="sxs-lookup"><span data-stu-id="3001c-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="3001c-173">Enlace de modelos polimórfico:</span><span class="sxs-lookup"><span data-stu-id="3001c-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="3001c-174">No es habitual para una API REST diseñada para interoperar con todos los lenguajes.</span><span class="sxs-lookup"><span data-stu-id="3001c-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="3001c-175">Dificulta el razonamiento sobre los modelos enlazados.</span><span class="sxs-lookup"><span data-stu-id="3001c-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="3001c-176">Sin embargo, si una aplicación requiere el enlace de modelos polimórfico, una implementación podría ser similar al código siguiente:</span><span class="sxs-lookup"><span data-stu-id="3001c-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="3001c-177">Sugerencias y procedimientos recomendados</span><span class="sxs-lookup"><span data-stu-id="3001c-177">Recommendations and best practices</span></span>

<span data-ttu-id="3001c-178">Los enlazadores de modelos personalizados deben caracterizarse por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3001c-178">Custom model binders:</span></span>

- <span data-ttu-id="3001c-179">No deben tratar de establecer códigos de estado ni devolver resultados (por ejemplo, 404 No encontrado).</span><span class="sxs-lookup"><span data-stu-id="3001c-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="3001c-180">Los errores que se produzcan en un enlace de modelos se deben controlar con un [filtro de acciones](xref:mvc/controllers/filters) o con la lógica del propio método de acción.</span><span class="sxs-lookup"><span data-stu-id="3001c-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="3001c-181">Son realmente útiles para eliminar el código repetitivo y las cuestiones transversales de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="3001c-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="3001c-182">No se deben usar en general para convertir una cadena en un tipo personalizado. Para ello, <xref:System.ComponentModel.TypeConverter> suele ser una mejor opción.</span><span class="sxs-lookup"><span data-stu-id="3001c-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3001c-183">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3001c-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3001c-184">Con el enlace de modelos, las acciones de controlador pueden funcionar directamente con tipos de modelos (pasados como argumentos de método), en lugar de con solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="3001c-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="3001c-185">La asignación entre los datos de solicitudes entrantes y los modelos de aplicaciones se controla por medio de enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="3001c-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="3001c-186">Los desarrolladores pueden ampliar la funcionalidad integrada de enlace de modelos implementando enlazadores de modelos personalizados (si bien, por lo general, no es necesario escribir un proveedor propio).</span><span class="sxs-lookup"><span data-stu-id="3001c-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="3001c-187">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3001c-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="3001c-188">Limitaciones de los enlazadores de modelos predeterminados</span><span class="sxs-lookup"><span data-stu-id="3001c-188">Default model binder limitations</span></span>

<span data-ttu-id="3001c-189">Los enlazadores de modelos predeterminados admiten la mayoría de los tipos de datos de .NET Core comunes y deberían cubrir las necesidades de casi cualquier desarrollador.</span><span class="sxs-lookup"><span data-stu-id="3001c-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="3001c-190">Esperan enlazar entradas basadas en texto desde la solicitud directamente a tipos de modelos.</span><span class="sxs-lookup"><span data-stu-id="3001c-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="3001c-191">Puede que sea necesario transformar la entrada antes de enlazarla.</span><span class="sxs-lookup"><span data-stu-id="3001c-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="3001c-192">Es el caso, por ejemplo, si tiene una clave que se puede usar para buscar datos del modelo.</span><span class="sxs-lookup"><span data-stu-id="3001c-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="3001c-193">Se puede usar un enlazador de modelos personalizado para capturar datos en función de la clave.</span><span class="sxs-lookup"><span data-stu-id="3001c-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="3001c-194">Revisión del enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="3001c-194">Model binding review</span></span>

<span data-ttu-id="3001c-195">El enlace de modelos usa definiciones específicas de los tipos con los que funciona.</span><span class="sxs-lookup"><span data-stu-id="3001c-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="3001c-196">Un *tipo simple* se convierte a partir de una sola cadena de la entrada,</span><span class="sxs-lookup"><span data-stu-id="3001c-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="3001c-197">mientras que un *tipo complejo* se convierte a partir de varios valores de entrada.</span><span class="sxs-lookup"><span data-stu-id="3001c-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="3001c-198">El marco establece la diferencia dependiendo de si existe un `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="3001c-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="3001c-199">Conviene crear un convertidor de tipos si existe una asignación simple `string` -> `SomeType` que no necesita recursos externos.</span><span class="sxs-lookup"><span data-stu-id="3001c-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="3001c-200">Antes de crear su propio enlazador de modelos personalizado, no está de más que repase cómo se implementan los enlazadores de modelos existentes.</span><span class="sxs-lookup"><span data-stu-id="3001c-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="3001c-201">Hay que considerar el uso de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, que sirve para convertir cadenas codificadas con base64 en matrices de bytes.</span><span class="sxs-lookup"><span data-stu-id="3001c-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="3001c-202">Las matrices de bytes se suelen almacenar como archivos o como campos de tipo BLOB de base de datos.</span><span class="sxs-lookup"><span data-stu-id="3001c-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="3001c-203">Trabajar con ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="3001c-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="3001c-204">Las cadenas codificadas con base64 se pueden usar para representar datos binarios.</span><span class="sxs-lookup"><span data-stu-id="3001c-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="3001c-205">Por ejemplo, una imagen se puede codificar como una cadena.</span><span class="sxs-lookup"><span data-stu-id="3001c-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="3001c-206">En el ejemplo se incluye una imagen como una cadena codificada con base64 en [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="3001c-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="3001c-207">ASP.NET Core MVC toma cadenas codificadas con base64 y usa un `ByteArrayModelBinder` para convertirlas en una matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="3001c-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="3001c-208"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> asigna argumentos `byte[]` a `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="3001c-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

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

<span data-ttu-id="3001c-209">Cuando cree su propio enlazador de modelos personalizado, puede implementar su tipo `IModelBinderProvider` particular o usar <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span><span class="sxs-lookup"><span data-stu-id="3001c-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="3001c-210">En el siguiente ejemplo se indica cómo usar `ByteArrayModelBinder` para convertir una cadena codificada con base64 en un `byte[]` y guardar el resultado en un archivo:</span><span class="sxs-lookup"><span data-stu-id="3001c-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="3001c-211">Se puede usar un método API POST en una cadena codificada con base64 con una herramienta como [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="3001c-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="3001c-212">![Postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="3001c-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="3001c-213">Siempre y cuando el enlazador pueda enlazar datos de la solicitud a argumentos o propiedades con el nombre adecuado, el enlace de modelos se realizará correctamente.</span><span class="sxs-lookup"><span data-stu-id="3001c-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="3001c-214">En el siguiente ejemplo se muestra cómo usar `ByteArrayModelBinder` con un modelo de vista:</span><span class="sxs-lookup"><span data-stu-id="3001c-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="3001c-215">Ejemplo de enlazador de modelos personalizado</span><span class="sxs-lookup"><span data-stu-id="3001c-215">Custom model binder sample</span></span>

<span data-ttu-id="3001c-216">En esta sección implementaremos un enlazador de modelos personalizado que haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3001c-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="3001c-217">Convertir los datos de solicitud entrantes en argumentos clave fuertemente tipados</span><span class="sxs-lookup"><span data-stu-id="3001c-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="3001c-218">Usar Entity Framework Core para capturar la entidad asociada</span><span class="sxs-lookup"><span data-stu-id="3001c-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="3001c-219">Pasar la entidad asociada como argumento al método de acción</span><span class="sxs-lookup"><span data-stu-id="3001c-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="3001c-220">En el siguiente ejemplo se usa el atributo `ModelBinder` en el modelo `Author`:</span><span class="sxs-lookup"><span data-stu-id="3001c-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="3001c-221">En el código anterior, el atributo `ModelBinder` especifica el tipo de `IModelBinder` que se debe emplear para enlazar parámetros de acción de `Author`.</span><span class="sxs-lookup"><span data-stu-id="3001c-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="3001c-222">La clase `AuthorEntityBinder` siguiente enlaza un parámetro `Author` capturando la entidad de un origen de datos por medio de Entity Framework Core y un elemento `authorId`:</span><span class="sxs-lookup"><span data-stu-id="3001c-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="3001c-223">La clase `AuthorEntityBinder` anterior está diseñada para ilustrar un enlazador de modelos personalizado.</span><span class="sxs-lookup"><span data-stu-id="3001c-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="3001c-224">La clase no está pensada para ilustrar los procedimientos recomendados para un escenario de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="3001c-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="3001c-225">Para la búsqueda, enlace el valor `authorId` y consulte la base de datos en un método de acción.</span><span class="sxs-lookup"><span data-stu-id="3001c-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="3001c-226">Este enfoque permite diferenciar y separar los errores de enlace de modelo de los casos `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="3001c-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="3001c-227">En el siguiente código se indica cómo usar `AuthorEntityBinder` en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="3001c-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="3001c-228">El atributo `ModelBinder` se puede usar para aplicar `AuthorEntityBinder` a los parámetros que no usan convenciones predeterminadas:</span><span class="sxs-lookup"><span data-stu-id="3001c-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="3001c-229">En este ejemplo, como el nombre del argumento no es el `authorId` predeterminado, se especifica en el parámetro por medio del atributo `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="3001c-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="3001c-230">Tanto el controlador como el método de acción se simplifican, en contraste con tener que buscar la entidad en el método de acción.</span><span class="sxs-lookup"><span data-stu-id="3001c-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="3001c-231">La lógica para capturar el autor a través de Entity Framework Core se traslada al enlazador de modelos.</span><span class="sxs-lookup"><span data-stu-id="3001c-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="3001c-232">Esto puede reducir enormemente la complejidad cuando existen varios métodos que se enlazan al modelo `Author`.</span><span class="sxs-lookup"><span data-stu-id="3001c-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="3001c-233">Puede aplicar el atributo `ModelBinder` a propiedades de modelo individuales (como en un modelo de vista) o a parámetros del método de acción para especificar un determinado nombre de modelo o enlazador de modelos que sea exclusivo de ese tipo o acción en particular.</span><span class="sxs-lookup"><span data-stu-id="3001c-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="3001c-234">Implementar un ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="3001c-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="3001c-235">En lugar de aplicar un atributo, puede implementar `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="3001c-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="3001c-236">Así es como se implementan los enlazadores de marco integrados.</span><span class="sxs-lookup"><span data-stu-id="3001c-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="3001c-237">Cuando se especifica el tipo con el que el enlazador funciona, lo que se está indicando es el tipo de argumento que ese enlazador genera, **no** la entrada que acepta.</span><span class="sxs-lookup"><span data-stu-id="3001c-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="3001c-238">El siguiente proveedor de enlazador funciona con `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="3001c-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="3001c-239">Cuando se agrega a la colección de proveedores de MVC, no es necesario usar el atributo `ModelBinder` en los parámetros con tipo `Author` o `Author`.</span><span class="sxs-lookup"><span data-stu-id="3001c-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="3001c-240">Nota: El código anterior devuelve un `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="3001c-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="3001c-241">`BinderTypeModelBinder` actúa como una fábrica de enlazadores de modelos y proporciona la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="3001c-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="3001c-242">`AuthorEntityBinder` requiere que la inserción de dependencias tenga acceso a Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="3001c-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="3001c-243">Use `BinderTypeModelBinder` si su enlazador de modelos necesita servicios de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="3001c-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="3001c-244">Para usar un proveedor de enlazadores de modelos personalizado, agréguelo a `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3001c-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="3001c-245">Al evaluar enlazadores de modelos, la colección de proveedores se examina en orden.</span><span class="sxs-lookup"><span data-stu-id="3001c-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="3001c-246">Se usará el primer proveedor que devuelva un enlazador.</span><span class="sxs-lookup"><span data-stu-id="3001c-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="3001c-247">Si su proveedor se agrega al final de la colección, puede ocurrir que se llame a un enlazador de modelos integrado antes que al suyo.</span><span class="sxs-lookup"><span data-stu-id="3001c-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="3001c-248">En este ejemplo, el proveedor personalizado se agrega al principio de la colección para procurar que se use en los argumentos de acción de `Author`.</span><span class="sxs-lookup"><span data-stu-id="3001c-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="3001c-249">Enlace de modelos polimórfico</span><span class="sxs-lookup"><span data-stu-id="3001c-249">Polymorphic model binding</span></span>

<span data-ttu-id="3001c-250">El enlace a diferentes modelos de tipos derivados se conoce como enlace de modelos polimórfico.</span><span class="sxs-lookup"><span data-stu-id="3001c-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="3001c-251">El enlace de modelos personalizado polimórfico es necesario cuando el valor de la solicitud se debe enlazar al tipo de modelo derivado específico.</span><span class="sxs-lookup"><span data-stu-id="3001c-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="3001c-252">Enlace de modelos polimórfico:</span><span class="sxs-lookup"><span data-stu-id="3001c-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="3001c-253">No es habitual para una API REST diseñada para interoperar con todos los lenguajes.</span><span class="sxs-lookup"><span data-stu-id="3001c-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="3001c-254">Dificulta el razonamiento sobre los modelos enlazados.</span><span class="sxs-lookup"><span data-stu-id="3001c-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="3001c-255">Sin embargo, si una aplicación requiere el enlace de modelos polimórfico, una implementación podría ser similar al código siguiente:</span><span class="sxs-lookup"><span data-stu-id="3001c-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="3001c-256">Sugerencias y procedimientos recomendados</span><span class="sxs-lookup"><span data-stu-id="3001c-256">Recommendations and best practices</span></span>

<span data-ttu-id="3001c-257">Los enlazadores de modelos personalizados deben caracterizarse por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3001c-257">Custom model binders:</span></span>

- <span data-ttu-id="3001c-258">No deben tratar de establecer códigos de estado ni devolver resultados (por ejemplo, 404 No encontrado).</span><span class="sxs-lookup"><span data-stu-id="3001c-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="3001c-259">Los errores que se produzcan en un enlace de modelos se deben controlar con un [filtro de acciones](xref:mvc/controllers/filters) o con la lógica del propio método de acción.</span><span class="sxs-lookup"><span data-stu-id="3001c-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="3001c-260">Son realmente útiles para eliminar el código repetitivo y las cuestiones transversales de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="3001c-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="3001c-261">No se deben usar en general para convertir una cadena en un tipo personalizado. Para ello, <xref:System.ComponentModel.TypeConverter> suele ser una mejor opción.</span><span class="sxs-lookup"><span data-stu-id="3001c-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
