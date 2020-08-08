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
# <a name="custom-model-binding-in-aspnet-core"></a>Enlace de modelos personalizado en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

De [Steve Smith](https://ardalis.com/) y [Kirk Larkin](https://twitter.com/serpent5)

Con el enlace de modelos, las acciones de controlador pueden funcionar directamente con tipos de modelos (pasados como argumentos de método), en lugar de con solicitudes HTTP. La asignación entre los datos de solicitudes entrantes y los modelos de aplicaciones se controla por medio de enlazadores de modelos. Los desarrolladores pueden ampliar la funcionalidad integrada de enlace de modelos implementando enlazadores de modelos personalizados (si bien, por lo general, no es necesario escribir un proveedor propio).

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Limitaciones de los enlazadores de modelos predeterminados

Los enlazadores de modelos predeterminados admiten la mayoría de los tipos de datos de .NET Core comunes y deberían cubrir las necesidades de casi cualquier desarrollador. Esperan enlazar entradas basadas en texto desde la solicitud directamente a tipos de modelos. Puede que sea necesario transformar la entrada antes de enlazarla. Es el caso, por ejemplo, si tiene una clave que se puede usar para buscar datos del modelo. Se puede usar un enlazador de modelos personalizado para capturar datos en función de la clave.

## <a name="model-binding-review"></a>Revisión del enlace de modelos

El enlace de modelos usa definiciones específicas de los tipos con los que funciona. Un *tipo simple* se convierte a partir de una sola cadena de la entrada, mientras que un *tipo complejo* se convierte a partir de varios valores de entrada. El marco establece la diferencia dependiendo de si existe un `TypeConverter`. Conviene crear un convertidor de tipos si existe una asignación simple `string` -> `SomeType` que no necesita recursos externos.

Antes de crear su propio enlazador de modelos personalizado, no está de más que repase cómo se implementan los enlazadores de modelos existentes. Hay que considerar el uso de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, que sirve para convertir cadenas codificadas con base64 en matrices de bytes. Las matrices de bytes se suelen almacenar como archivos o como campos de tipo BLOB de base de datos.

### <a name="working-with-the-bytearraymodelbinder"></a>Trabajar con ByteArrayModelBinder

Las cadenas codificadas con base64 se pueden usar para representar datos binarios. Por ejemplo, una imagen se puede codificar como una cadena. En el ejemplo se incluye una imagen como una cadena codificada con base64 en [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).

ASP.NET Core MVC toma cadenas codificadas con base64 y usa un `ByteArrayModelBinder` para convertirlas en una matriz de bytes. <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> asigna argumentos `byte[]` a `ByteArrayModelBinder`:

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

Cuando cree su propio enlazador de modelos personalizado, puede implementar su tipo `IModelBinderProvider` particular o usar <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.

En el siguiente ejemplo se indica cómo usar `ByteArrayModelBinder` para convertir una cadena codificada con base64 en un `byte[]` y guardar el resultado en un archivo:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Se puede usar un método API POST en una cadena codificada con base64 con una herramienta como [Postman](https://www.getpostman.com/):

![Postman](custom-model-binding/images/postman.png "postman")

Siempre y cuando el enlazador pueda enlazar datos de la solicitud a argumentos o propiedades con el nombre adecuado, el enlace de modelos se realizará correctamente. En el siguiente ejemplo se muestra cómo usar `ByteArrayModelBinder` con un modelo de vista:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a>Ejemplo de enlazador de modelos personalizado

En esta sección implementaremos un enlazador de modelos personalizado que haga lo siguiente:

- Convertir los datos de solicitud entrantes en argumentos clave fuertemente tipados
- Usar Entity Framework Core para capturar la entidad asociada
- Pasar la entidad asociada como argumento al método de acción

En el siguiente ejemplo se usa el atributo `ModelBinder` en el modelo `Author`:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

En el código anterior, el atributo `ModelBinder` especifica el tipo de `IModelBinder` que se debe emplear para enlazar parámetros de acción de `Author`.

La clase `AuthorEntityBinder` siguiente enlaza un parámetro `Author` capturando la entidad de un origen de datos por medio de Entity Framework Core y un elemento `authorId`:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> La clase `AuthorEntityBinder` anterior está diseñada para ilustrar un enlazador de modelos personalizado. La clase no está pensada para ilustrar los procedimientos recomendados para un escenario de búsqueda. Para la búsqueda, enlace el valor `authorId` y consulte la base de datos en un método de acción. Este enfoque permite diferenciar y separar los errores de enlace de modelo de los casos `NotFound`.

En el siguiente código se indica cómo usar `AuthorEntityBinder` en un método de acción:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

El atributo `ModelBinder` se puede usar para aplicar `AuthorEntityBinder` a los parámetros que no usan convenciones predeterminadas:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

En este ejemplo, como el nombre del argumento no es el `authorId` predeterminado, se especifica en el parámetro por medio del atributo `ModelBinder`. Tanto el controlador como el método de acción se simplifican, en contraste con tener que buscar la entidad en el método de acción. La lógica para capturar el autor a través de Entity Framework Core se traslada al enlazador de modelos. Esto puede reducir enormemente la complejidad cuando existen varios métodos que se enlazan al modelo `Author`.

Puede aplicar el atributo `ModelBinder` a propiedades de modelo individuales (como en un modelo de vista) o a parámetros del método de acción para especificar un determinado nombre de modelo o enlazador de modelos que sea exclusivo de ese tipo o acción en particular.

### <a name="implementing-a-modelbinderprovider"></a>Implementar un ModelBinderProvider

En lugar de aplicar un atributo, puede implementar `IModelBinderProvider`. Así es como se implementan los enlazadores de marco integrados. Cuando se especifica el tipo con el que el enlazador funciona, lo que se está indicando es el tipo de argumento que ese enlazador genera, **no** la entrada que acepta. El siguiente proveedor de enlazador funciona con `AuthorEntityBinder`. Cuando se agrega a la colección de proveedores de MVC, no es necesario usar el atributo `ModelBinder` en los parámetros con tipo `Author` o `Author`.

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Nota: El código anterior devuelve un `BinderTypeModelBinder`. `BinderTypeModelBinder` actúa como una fábrica de enlazadores de modelos y proporciona la inserción de dependencias. `AuthorEntityBinder` requiere que la inserción de dependencias tenga acceso a Entity Framework Core. Use `BinderTypeModelBinder` si su enlazador de modelos necesita servicios de inserción de dependencias.

Para usar un proveedor de enlazadores de modelos personalizado, agréguelo a `ConfigureServices`:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

Al evaluar enlazadores de modelos, la colección de proveedores se examina en orden. Se usará el primer proveedor que devuelva un enlazador. Si su proveedor se agrega al final de la colección, puede ocurrir que se llame a un enlazador de modelos integrado antes que al suyo. En este ejemplo, el proveedor personalizado se agrega al principio de la colección para procurar que se use en los argumentos de acción de `Author`.

### <a name="polymorphic-model-binding"></a>Enlace de modelos polimórfico

El enlace a diferentes modelos de tipos derivados se conoce como enlace de modelos polimórfico. El enlace de modelos personalizado polimórfico es necesario cuando el valor de la solicitud se debe enlazar al tipo de modelo derivado específico. Enlace de modelos polimórfico:

* No es habitual para una API REST diseñada para interoperar con todos los lenguajes.
* Dificulta el razonamiento sobre los modelos enlazados.

Sin embargo, si una aplicación requiere el enlace de modelos polimórfico, una implementación podría ser similar al código siguiente:

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Sugerencias y procedimientos recomendados

Los enlazadores de modelos personalizados deben caracterizarse por lo siguiente:

- No deben tratar de establecer códigos de estado ni devolver resultados (por ejemplo, 404 No encontrado). Los errores que se produzcan en un enlace de modelos se deben controlar con un [filtro de acciones](xref:mvc/controllers/filters) o con la lógica del propio método de acción.
- Son realmente útiles para eliminar el código repetitivo y las cuestiones transversales de los métodos de acción.
- No se deben usar en general para convertir una cadena en un tipo personalizado. Para ello, <xref:System.ComponentModel.TypeConverter> suele ser una mejor opción.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Por [Steve Smith](https://ardalis.com/)

Con el enlace de modelos, las acciones de controlador pueden funcionar directamente con tipos de modelos (pasados como argumentos de método), en lugar de con solicitudes HTTP. La asignación entre los datos de solicitudes entrantes y los modelos de aplicaciones se controla por medio de enlazadores de modelos. Los desarrolladores pueden ampliar la funcionalidad integrada de enlace de modelos implementando enlazadores de modelos personalizados (si bien, por lo general, no es necesario escribir un proveedor propio).

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Limitaciones de los enlazadores de modelos predeterminados

Los enlazadores de modelos predeterminados admiten la mayoría de los tipos de datos de .NET Core comunes y deberían cubrir las necesidades de casi cualquier desarrollador. Esperan enlazar entradas basadas en texto desde la solicitud directamente a tipos de modelos. Puede que sea necesario transformar la entrada antes de enlazarla. Es el caso, por ejemplo, si tiene una clave que se puede usar para buscar datos del modelo. Se puede usar un enlazador de modelos personalizado para capturar datos en función de la clave.

## <a name="model-binding-review"></a>Revisión del enlace de modelos

El enlace de modelos usa definiciones específicas de los tipos con los que funciona. Un *tipo simple* se convierte a partir de una sola cadena de la entrada, mientras que un *tipo complejo* se convierte a partir de varios valores de entrada. El marco establece la diferencia dependiendo de si existe un `TypeConverter`. Conviene crear un convertidor de tipos si existe una asignación simple `string` -> `SomeType` que no necesita recursos externos.

Antes de crear su propio enlazador de modelos personalizado, no está de más que repase cómo se implementan los enlazadores de modelos existentes. Hay que considerar el uso de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, que sirve para convertir cadenas codificadas con base64 en matrices de bytes. Las matrices de bytes se suelen almacenar como archivos o como campos de tipo BLOB de base de datos.

### <a name="working-with-the-bytearraymodelbinder"></a>Trabajar con ByteArrayModelBinder

Las cadenas codificadas con base64 se pueden usar para representar datos binarios. Por ejemplo, una imagen se puede codificar como una cadena. En el ejemplo se incluye una imagen como una cadena codificada con base64 en [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).

ASP.NET Core MVC toma cadenas codificadas con base64 y usa un `ByteArrayModelBinder` para convertirlas en una matriz de bytes. <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> asigna argumentos `byte[]` a `ByteArrayModelBinder`:

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

Cuando cree su propio enlazador de modelos personalizado, puede implementar su tipo `IModelBinderProvider` particular o usar <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.

En el siguiente ejemplo se indica cómo usar `ByteArrayModelBinder` para convertir una cadena codificada con base64 en un `byte[]` y guardar el resultado en un archivo:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

Se puede usar un método API POST en una cadena codificada con base64 con una herramienta como [Postman](https://www.getpostman.com/):

![Postman](custom-model-binding/images/postman.png "postman")

Siempre y cuando el enlazador pueda enlazar datos de la solicitud a argumentos o propiedades con el nombre adecuado, el enlace de modelos se realizará correctamente. En el siguiente ejemplo se muestra cómo usar `ByteArrayModelBinder` con un modelo de vista:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>Ejemplo de enlazador de modelos personalizado

En esta sección implementaremos un enlazador de modelos personalizado que haga lo siguiente:

- Convertir los datos de solicitud entrantes en argumentos clave fuertemente tipados
- Usar Entity Framework Core para capturar la entidad asociada
- Pasar la entidad asociada como argumento al método de acción

En el siguiente ejemplo se usa el atributo `ModelBinder` en el modelo `Author`:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

En el código anterior, el atributo `ModelBinder` especifica el tipo de `IModelBinder` que se debe emplear para enlazar parámetros de acción de `Author`.

La clase `AuthorEntityBinder` siguiente enlaza un parámetro `Author` capturando la entidad de un origen de datos por medio de Entity Framework Core y un elemento `authorId`:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> La clase `AuthorEntityBinder` anterior está diseñada para ilustrar un enlazador de modelos personalizado. La clase no está pensada para ilustrar los procedimientos recomendados para un escenario de búsqueda. Para la búsqueda, enlace el valor `authorId` y consulte la base de datos en un método de acción. Este enfoque permite diferenciar y separar los errores de enlace de modelo de los casos `NotFound`.

En el siguiente código se indica cómo usar `AuthorEntityBinder` en un método de acción:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

El atributo `ModelBinder` se puede usar para aplicar `AuthorEntityBinder` a los parámetros que no usan convenciones predeterminadas:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

En este ejemplo, como el nombre del argumento no es el `authorId` predeterminado, se especifica en el parámetro por medio del atributo `ModelBinder`. Tanto el controlador como el método de acción se simplifican, en contraste con tener que buscar la entidad en el método de acción. La lógica para capturar el autor a través de Entity Framework Core se traslada al enlazador de modelos. Esto puede reducir enormemente la complejidad cuando existen varios métodos que se enlazan al modelo `Author`.

Puede aplicar el atributo `ModelBinder` a propiedades de modelo individuales (como en un modelo de vista) o a parámetros del método de acción para especificar un determinado nombre de modelo o enlazador de modelos que sea exclusivo de ese tipo o acción en particular.

### <a name="implementing-a-modelbinderprovider"></a>Implementar un ModelBinderProvider

En lugar de aplicar un atributo, puede implementar `IModelBinderProvider`. Así es como se implementan los enlazadores de marco integrados. Cuando se especifica el tipo con el que el enlazador funciona, lo que se está indicando es el tipo de argumento que ese enlazador genera, **no** la entrada que acepta. El siguiente proveedor de enlazador funciona con `AuthorEntityBinder`. Cuando se agrega a la colección de proveedores de MVC, no es necesario usar el atributo `ModelBinder` en los parámetros con tipo `Author` o `Author`.

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Nota: El código anterior devuelve un `BinderTypeModelBinder`. `BinderTypeModelBinder` actúa como una fábrica de enlazadores de modelos y proporciona la inserción de dependencias. `AuthorEntityBinder` requiere que la inserción de dependencias tenga acceso a Entity Framework Core. Use `BinderTypeModelBinder` si su enlazador de modelos necesita servicios de inserción de dependencias.

Para usar un proveedor de enlazadores de modelos personalizado, agréguelo a `ConfigureServices`:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

Al evaluar enlazadores de modelos, la colección de proveedores se examina en orden. Se usará el primer proveedor que devuelva un enlazador. Si su proveedor se agrega al final de la colección, puede ocurrir que se llame a un enlazador de modelos integrado antes que al suyo. En este ejemplo, el proveedor personalizado se agrega al principio de la colección para procurar que se use en los argumentos de acción de `Author`.

### <a name="polymorphic-model-binding"></a>Enlace de modelos polimórfico

El enlace a diferentes modelos de tipos derivados se conoce como enlace de modelos polimórfico. El enlace de modelos personalizado polimórfico es necesario cuando el valor de la solicitud se debe enlazar al tipo de modelo derivado específico. Enlace de modelos polimórfico:

* No es habitual para una API REST diseñada para interoperar con todos los lenguajes.
* Dificulta el razonamiento sobre los modelos enlazados.

Sin embargo, si una aplicación requiere el enlace de modelos polimórfico, una implementación podría ser similar al código siguiente:

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Sugerencias y procedimientos recomendados

Los enlazadores de modelos personalizados deben caracterizarse por lo siguiente:

- No deben tratar de establecer códigos de estado ni devolver resultados (por ejemplo, 404 No encontrado). Los errores que se produzcan en un enlace de modelos se deben controlar con un [filtro de acciones](xref:mvc/controllers/filters) o con la lógica del propio método de acción.
- Son realmente útiles para eliminar el código repetitivo y las cuestiones transversales de los métodos de acción.
- No se deben usar en general para convertir una cadena en un tipo personalizado. Para ello, <xref:System.ComponentModel.TypeConverter> suele ser una mejor opción.

::: moniker-end
