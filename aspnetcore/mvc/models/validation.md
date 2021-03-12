---
title: Validación de modelos en ASP.NET Core MVC
author: rick-anderson
description: Obtenga información sobre la validación de modelos en ASP.NET Core MVC y Razor páginas.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
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
uid: mvc/models/validation
ms.openlocfilehash: 412f95e67fa35e952a907db328395183f1698b9e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587221"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a>Validación del modelo en ASP.NET Core MVC y Razor páginas

::: moniker range=">= aspnetcore-3.0"

De [Kirk Larkin](https://github.com/serpent5)

En este artículo se explica cómo validar los datos proporcionados por el usuario en una aplicación ASP.NET Core MVC o Razor pages.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Estado del modelo

El estado del modelo representa los errores que proceden de dos subsistemas: el enlace de modelos y la validación de modelos. Los errores que se originan del [enlace de modelos](model-binding.md) suelen ser errores de conversión de datos. Por ejemplo, se escribe una "x" en un campo numérico entero. La validación del modelo se produce después del enlace de modelos y notifica los errores en los que los datos no cumplen las reglas de negocio. Por ejemplo, se especifica un 0 en un campo que espera una clasificación entre 1 y 5.

Tanto el enlace de modelos como la validación de modelos se producen antes de la ejecución de una acción de controlador o un Razor método de controlador de páginas. En el caso de las aplicaciones web, la aplicación es responsable de inspeccionar `ModelState.IsValid` y reaccionar de manera apropiada. Normalmente, las aplicaciones web vuelven a mostrar la página con un mensaje de error:

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

Los controladores de Web API no tienen que comprobar `ModelState.IsValid` si tienen el atributo `[ApiController]`. En ese caso, se devuelve una respuesta HTTP 400 automática que contiene los detalles del error cuando el estado del modelo no es válido. Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Nueva ejecución de la validación

La validación es automática, pero tal vez le interese repetirla manualmente. Por ejemplo, tal vez haya calculado el valor de una propiedad y quiera volver a ejecutar la validación después de establecer la propiedad en el valor calculado. Para volver a ejecutar la validación, llame al método `TryValidateModel`, como se muestra aquí:

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a>Atributos de validación

Los atributos de validación permiten especificar reglas de validación para las propiedades del modelo. En el ejemplo siguiente de la aplicación de ejemplo se muestra una clase de modelo anotada con atributos de validación. El atributo `[ClassicMovie]` es un atributo de validación personalizado y los demás están integrados. `[ClassicMovieWithClientValidator]` no se muestra. `[ClassicMovieWithClientValidator]` muestra una manera alternativa de implementar un atributo personalizado.

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a>Atributos integrados

Estos son algunos de los atributos de validación integrados:

* `[CreditCard]`: Valida que la propiedad tiene un formato de tarjeta de crédito. Requiere [métodos adicionales de validación de jQuery](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).
* `[Compare]`: Valida que dos propiedades de un modelo coincidan.
* `[EmailAddress]`: Valida que la propiedad tiene un formato de correo electrónico.
* `[Phone]`: Valida que la propiedad tiene un formato de número de teléfono.
* `[Range]`: Valida que el valor de la propiedad se encuentra dentro de un intervalo especificado.
* `[RegularExpression]`: Valida que el valor de propiedad coincide con una expresión regular especificada.
* `[Required]`: Valida que el campo no sea NULL. Vea el [ `[Required]` atributo](#required-attribute) para obtener más información sobre el comportamiento de este atributo.
* `[StringLength]`: Valida que un valor de propiedad de cadena no supera un límite de longitud especificado.
* `[Url]`: Valida que la propiedad tiene un formato de dirección URL.
* `[Remote]`: Valida la entrada en el cliente mediante una llamada a un método de acción en el servidor. Vea el [ `[Remote]` atributo](#remote-attribute) para obtener más información sobre el comportamiento de este atributo.

En el espacio de nombres [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) encontrará una lista completa de atributos de validación.

### <a name="error-messages"></a>Mensajes de error

Los atributos de validación permiten especificar el mensaje de error que se mostrará para una entrada no válida. Por ejemplo:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

Internamente, los atributos llaman a `String.Format` con un marcador de posición para el nombre de campo y, en ocasiones, marcadores de posición adicionales. Por ejemplo:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

Cuando se aplica a una propiedad `Name`, el mensaje de error creado por el código anterior sería "La longitud del nombre debe estar entre 6 y 8".

Para averiguar qué parámetros se pasan a `String.Format` para el mensaje de error de un atributo determinado, vea el [código fuente de DataAnnotations](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="non-nullable-reference-types-and-required-attribute"></a>Tipos de referencia que no aceptan valores NULL y atributo [required]

El sistema de validación trata los parámetros que no aceptan valores NULL o las propiedades enlazadas como si tuvieran un `[Required]` atributo. Al [habilitar los `Nullable` contextos](/dotnet/csharp/nullable-references#nullable-contexts), MVC comienza implícitamente la validación de las propiedades o los parámetros que no aceptan valores NULL como si hubieran sido atribuidos con el `[Required]` atributo. Observe el código siguiente:

```csharp
public class Person
{
    public string Name { get; set; }
}
```

Si la aplicación se compiló con `<Nullable>enable</Nullable>` , un valor que falta para `Name` en un archivo JSON o de formulario post produce un error de validación. Use un tipo de referencia que acepte valores NULL para permitir que se especifiquen valores NULL o que faltan para la `Name` propiedad:

```csharp
public class Person
{
    public string? Name { get; set; }
}
```

. Este comportamiento se puede deshabilitar si se configura <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> en `Startup.ConfigureServices`:

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a>Validación de [Required] en el servidor

En el servidor, si la propiedad es NULL, se considera que falta un valor requerido. Un campo que no acepta valores NULL siempre es válido y el mensaje de error del atributo `[Required]` no se muestra nunca.

Aun así, el enlace de modelos para una propiedad que no acepta valores NULL podría fallar, lo que genera un mensaje de error como `The value '' is invalid`. Para especificar un mensaje de error personalizado para la validación del lado servidor de tipos que no aceptan valores NULL, tiene las siguientes opciones:

* Haga que el campo acepte valores NULL (por ejemplo, `decimal?` en lugar de `decimal`). [Acepta \<T> valores NULL](/dotnet/csharp/programming-guide/nullable-types/) los tipos de valor se tratan como los tipos estándar que aceptan valores NULL.
* Especifique el mensaje de error predeterminado que el enlace de modelos va a usar, como se muestra en el ejemplo siguiente:

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  Para obtener más información sobre los errores de enlace de modelos para los que se pueden establecer mensajes predeterminados, vea <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.

### <a name="required-validation-on-the-client"></a>Validación de [Required] en el cliente

Las cadenas y los tipos que no aceptan valores NULL se tratan de forma diferente en el cliente, en comparación con el servidor. En el cliente:

* Un valor se considera presente solo si se especifica una entrada para él. Por lo tanto, la validación del lado cliente controla los tipos que no aceptan valores NULL del mismo modo que los tipos que aceptan valores NULL.
* El método [required](https://jqueryvalidation.org/required-method/) de jQuery Validate considera que un espacio en blanco en un campo de cadena es una entrada válida. La validación del lado servidor considera que un campo de cadena necesario no es válido si solo se especifica un espacio en blanco.

Como se indicó anteriormente, los tipos que no aceptan valores NULL se tratan como si tuvieran un atributo `[Required]`. Esto significa que se obtiene la validación del lado cliente incluso si no se aplica el atributo `[Required]`. Si no usa el atributo, aparecerá un mensaje de error predeterminado. Para especificar un mensaje de error personalizado, use el atributo.

## <a name="remote-attribute"></a>Atributo [Remote]

El atributo `[Remote]` implementa la validación del lado cliente que requiere llamar a un método en el servidor para determinar si la entrada del campo es válida. Por ejemplo, la aplicación podría tener que comprobar si un nombre de usuario ya está en uso.

Para implementar la validación remota:

1. Cree un método de acción para que lo llame JavaScript.  El método [remoto](https://jqueryvalidation.org/remote-method/) de validación de jQuery espera una respuesta JSON:

   * `true` significa que los datos de entrada son válidos.
   * `false`, `undefined` o `null` significan que la entrada no es válida. Muestre el mensaje de error predeterminado.
   * Cualquier otra cadena significa que la entrada no es válida. Muestre la cadena como un mensaje de error personalizado.

   A continuación encontrará un ejemplo de un método de acción que devuelve un mensaje de error personalizado:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. En la clase de modelo, anote la propiedad con un atributo `[Remote]` que apunte al método de acción de validación, como se muestra en el ejemplo siguiente:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   El atributo `[Remote]` está en el espacio de nombres `Microsoft.AspNetCore.Mvc`.
   
### <a name="additional-fields"></a>Campos adicionales

La propiedad `AdditionalFields` del atributo `[Remote]` permite validar combinaciones de campos con los datos del servidor. Por ejemplo, si el modelo `User` tuviera las propiedades `FirstName` y `LastName`, podría interesarle comprobar que ningún usuario actual tuviera ya ese par de nombres. En el siguiente ejemplo se muestra cómo usar `AdditionalFields`:

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

`AdditionalFields` podría configurarse explícitamente para las cadenas "FirstName" y "LastName", pero, al usar el operador [nameof](/dotnet/csharp/language-reference/keywords/nameof), se simplifica la refactorización posterior. El método de acción para esta validación debe aceptar los argumentos `firstName` y `lastName`:

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Cuando el usuario escribe un nombre o un apellido, JavaScript realiza una llamada remota para comprobar si ese par de nombres ya existe.

Para validar dos o más campos adicionales, proporciónelos como una lista delimitada por comas. Por ejemplo, para agregar una propiedad `MiddleName` al modelo, establezca el atributo `[Remote]` tal como se muestra en el ejemplo siguiente:

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, al igual que todos los argumentos de atributo, debe ser una expresión constante. Por lo tanto, no use una [cadena interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) ni llame a <xref:System.String.Join*> para inicializar `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Alternativas a los atributos integrados

Si necesita una validación que no proporcionan los atributos integrados, puede hacer lo siguiente:

* [Crear atributos personalizados](#custom-attributes)
* [Implementar IValidatableObject](#ivalidatableobject)

## <a name="custom-attributes"></a>Atributos personalizados

Para los escenarios que no se controlan mediante los atributos de validación integrados, puede crear atributos de validación personalizados. Cree una clase que herede de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> y reemplace el método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.

El método `IsValid` acepta un objeto denominado *value*, que es la entrada que se va a validar. Una sobrecarga también acepta un objeto `ValidationContext`, que proporciona información adicional, como la instancia del modelo creada por el enlace de modelos.

El siguiente ejemplo valida que la fecha de lanzamiento de una película del género *Classic* no sea posterior a un año especificado. El atributo `[ClassicMovie]`:

* Solo se ejecuta en el servidor.
* En el caso de las películas clásicas, valida la fecha de lanzamiento:

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

La variable `movie` del ejemplo anterior representa un objeto `Movie` que contiene los datos del envío del formulario. Si se produce un error de validación, se devuelve un `ValidationResult` con un mensaje de error.

## <a name="ivalidatableobject"></a>IValidatableObject

El ejemplo anterior solo funciona con tipos `Movie`. Otra opción para la validación del nivel de clase consiste en implementar `IValidatableObject` en la clase de modelo, como se muestra en el ejemplo siguiente:

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Validación de nodo de nivel superior

Los nodos de nivel superior incluyen lo siguiente:

* Parámetros de acción
* Propiedades de controlador
* Parámetros de controlador de página
* Propiedades de modelo de página

Los nodos de nivel superior enlazados al modelo se validan además de la validación de las propiedades del modelo. En el ejemplo siguiente de la aplicación de muestra, el método `VerifyPhone` usa <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar el parámetro de acción `phone`:

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Los nodos de nivel superior pueden usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> con atributos de validación. En el ejemplo siguiente de la aplicación de muestra, el método `CheckAge` especifica que el parámetro `age` debe estar enlazado desde la cadena de consulta al enviar el formulario:

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

En la página Comprobar edad (*CheckAge.cshtml*), hay dos formularios. El primer formulario envía un valor `Age` de `99` como una cadena de parámetro de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.

Al enviar un parámetro `age` con un formato correcto desde la cadena de consulta, el formulario se valida.

El segundo formulario de la página Comprobar edad envía el valor `Age` en el cuerpo de la solicitud, y se produce un error de validación. Se produce un error en el enlace porque el parámetro `age` debe provenir de una cadena de consulta.

## <a name="maximum-errors"></a>Número máximo de errores

La validación se detiene cuando se alcanza el número máximo de errores (200 de forma predeterminada). Puede configurar este número con el siguiente código en `Startup.ConfigureServices`:

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a>Recursividad máxima

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> recorre el gráfico de objetos del modelo que se está validando. En el caso de los modelos profundos o infinitamente recursivos, la validación podría causar un desbordamiento de pila. [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) proporciona una manera de detener pronto la validación si la recursividad del visitante supera la profundidad configurada. El valor predeterminado de `MvcOptions.MaxValidationDepth` es 32.

## <a name="automatic-short-circuit"></a>Cortocircuito automático

La validación cortocircuita (se omite) automáticamente si el gráfico de modelo no requiere validación. Entre los objetos para los que el tiempo de ejecución omite la validación se incluyen las colecciones de elementos primitivos (como `byte[]`, `string[]` y `Dictionary<string, string>`) y gráficos de objeto complejo que no tienen los validadores.

## <a name="disable-validation"></a>Deshabilitación de la validación

Para deshabilitar la validación:

1. Cree una implementación de `IObjectModelValidator` que no marque ningún campo como no válido.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. Agregue el código siguiente a `Startup.ConfigureServices` para reemplazar la implementación predeterminada de `IObjectModelValidator` en el contenedor de inserción de dependencias.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

Es posible que todavía vea errores de estado del modelo originados en el enlace de modelos.

## <a name="client-side-validation"></a>Validación del lado cliente

La validación del lado cliente impide realizar el envío hasta que el formulario sea válido. El botón Enviar ejecuta JavaScript para enviar el formulario o mostrar mensajes de error.

La validación del lado cliente evita un recorrido de ida y vuelta innecesario en el servidor cuando hay errores de entrada en un formulario. Las siguientes referencias de script de *_Layout.cshtml* y *_ValidationScriptsPartial.cshtml* admiten la validación del lado cliente:

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

El script de [validación discreta de jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) es una biblioteca de front-end de Microsoft personalizada que se basa en el conocido complemento de [validación de jQuery](https://jqueryvalidation.org/) . Si no usa Validación discreta de jQuery, deberá codificar la misma lógica de validación dos veces: una vez en los atributos de validación del lado servidor en las propiedades del modelo y luego en los scripts del lado cliente. En su lugar, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan los atributos de validación y escriben metadatos de las propiedades del modelo para representar atributos `data-` HTML 5 para los elementos de formulario que necesitan validación. la validación discreta de jQuery analiza los `data-` atributos y pasa la lógica a la validación de jQuery y "copia" la lógica de validación del lado servidor al cliente. Puede mostrar errores de validación en el cliente mediante el uso de asistentes de etiquetas, como se muestra aquí:

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

Los anteriores asistentes de etiquetas representan el siguiente código HTML:

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

Tenga en cuenta que los atributos `data-` en los resultados HTML corresponden a los atributos de validación para la propiedad `Movie.ReleaseDate`. El atributo `data-val-required` contiene un mensaje de error que se muestra si el usuario no rellena el campo de fecha de estreno. la validación discreta de jQuery pasa este valor al método de validación de jQuery [Required ()](https://jqueryvalidation.org/required-method/) , que después muestra ese mensaje en el **\<span>** elemento correspondiente.

La validación del tipo de datos se basa en el tipo .NET de una propiedad, a menos que lo reemplace un atributo `[DataType]`. Los exploradores tienen sus propios mensajes de error de predeterminados, pero el paquete de Validación discreta de jQuery Validate puede invalidar esos mensajes. Los atributos y las subclases `[DataType]`, como `[EmailAddress]`, permiten especificar el mensaje de error.

## <a name="unobtrusive-validation"></a>Validación discreta

Para obtener información sobre la validación discreta, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/1111).

### <a name="add-validation-to-dynamic-forms"></a>Agregar validación a formularios dinámicos

la validación discreta de jQuery pasa la lógica de validación y los parámetros a la validación de jQuery la primera vez que se carga la página. Por lo tanto, la validación no funciona automáticamente en los formularios generados dinámicamente. Para habilitar la validación, hay que indicarle a Validación discreta de jQuery que analice el formulario dinámico inmediatamente después de su creación. Por ejemplo, en el código siguiente se configura la validación del lado cliente en un formulario agregado mediante AJAX.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

El método `$.validator.unobtrusive.parse()` acepta un selector de jQuery para su único argumento. Este método indica a Validación discreta de jQuery que analice los atributos `data-` de formularios dentro de ese selector. Los valores de esos atributos se pasan al complemento de validación de jQuery.

### <a name="add-validation-to-dynamic-controls"></a>Agregar validación a controles dinámicos

El método `$.validator.unobtrusive.parse()` funciona en todo el formulario, no en los controles individuales generados dinámicamente, como `<input>` y `<select/>`. Para volver a analizar el formulario, quite los datos de validación que se agregaron cuando el formulario se analizó anteriormente, como se muestra en el ejemplo siguiente:

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Validación del lado cliente personalizada

La validación del lado cliente personalizada se realiza mediante `data-` la generación de atributos HTML que funcionan con un adaptador de validación de jQuery personalizado. El siguiente ejemplo de código de adaptador se escribió para los atributos `[ClassicMovie]` y `[ClassicMovieWithClientValidator]` que se introdujeron anteriormente en este artículo:

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

Para obtener información sobre cómo escribir adaptadores, vea la [documentación de validación de jQuery](https://jqueryvalidation.org/documentation/).

El uso de un adaptador para un campo determinado se desencadena mediante atributos `data-` que:

* Marcan que el campo está sujeto a validación (`data-val="true"`).
* Identifican un nombre de regla de validación y un texto de mensaje de error (por ejemplo, `data-val-rulename="Error message."`).
* Proporcionan los parámetros adicionales que necesite el validador (por ejemplo, `data-val-rulename-param1="value"`).

En el ejemplo siguiente se muestran los atributos `data-` para el atributo `ClassicMovie` de la aplicación de ejemplo:

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

Como se indicó anteriormente, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan información procedente de los atributos de validación para representar atributos `data-`. Hay dos opciones para escribir código que dé como resultado la creación de atributos HTML `data-` personalizados:

* Puede crear una clase que derive de `AttributeAdapterBase<TAttribute>` y una clase que implemente `IValidationAttributeAdapterProvider` y, después, registrar el atributo y su adaptador en la inserción de dependencias. Este método sigue el [principio de responsabilidad única](https://wikipedia.org/wiki/Single_responsibility_principle), ya que el código de validación relacionado con servidor y el relacionado con el cliente se encuentran en clases independientes. El adaptador también cuenta con una ventaja: debido a que está registrado en la inserción de dependencias, tiene a su disposición otros servicios de la inserción de dependencias si es necesario.
* Puede implementar `IClientModelValidator` en la clase `ValidationAttribute`. Este método es adecuado si el atributo no realiza ninguna validación en el lado servidor y no necesita ningún servicio de la inserción de dependencias.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter para la validación del lado cliente

Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovie` en la aplicación de ejemplo. Para agregar la validación de cliente mediante este método:

1. Cree una clase de adaptador de atributo para el atributo de validación personalizado. Derive la clase de <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>. Cree un método `AddValidation` que agregue atributos `data-` a la salida representada, tal como se muestra en este ejemplo:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. Cree una clase de proveedor de adaptador que implemente <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. En el método `GetAttributeAdapter`, pase el atributo personalizado al constructor del adaptador, como se muestra en este ejemplo:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. Registre el proveedor del adaptador para la inserción de dependencias en `Startup.ConfigureServices`:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator para la validación del lado cliente

Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovieWithClientValidator` en la aplicación de ejemplo. Para agregar la validación de cliente mediante este método:

* En el atributo de validación personalizado, implemente la interfaz `IClientModelValidator` y cree un método `AddValidation`. En el método `AddValidation`, agregue atributos `data-` para la validación, como se muestra en el ejemplo siguiente:

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a>Deshabilitación de la validación del lado cliente

El código siguiente deshabilita la validación de cliente en Razor las páginas:

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

Otras opciones para deshabilitar la validación del lado cliente:

* Convierta en comentario la referencia a `_ValidationScriptsPartial` en todos los archivos *.cshtml*.
* Quite el contenido del archivo *Pages\Shared\_ValidationScriptsPartial.cshtml*.

El enfoque anterior no impedirá la validación del lado cliente de la ASP.NET Core Identity Razor biblioteca de clases. Para obtener más información, vea <xref:security/authentication/scaffold-identity>.

## <a name="additional-resources"></a>Recursos adicionales

* [Espacio de nombres System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Enlace de modelos](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este artículo se explica cómo validar los datos proporcionados por el usuario en una aplicación ASP.NET Core MVC o Razor pages.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Estado del modelo

El estado del modelo representa los errores que proceden de dos subsistemas: el enlace de modelos y la validación de modelos. Los errores que se originan en el [enlace de modelos](model-binding.md) suelen ser errores de conversión de datos (por ejemplo, se especifica una "x" en un campo que espera un entero). La validación de modelos se produce después de enlace de modelos y notifica errores cuando los datos no se ajustan a las reglas de negocios (por ejemplo, se especifica un 0 en un campo que espera una clasificación entre 1 y 5).

Tanto el enlace de modelos como la validación se producen antes de la ejecución de una acción de controlador o un Razor método de controlador de páginas. En el caso de las aplicaciones web, la aplicación es responsable de inspeccionar `ModelState.IsValid` y reaccionar de manera apropiada. Normalmente, las aplicaciones web vuelven a mostrar la página con un mensaje de error:

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

Los controladores de Web API no tienen que comprobar `ModelState.IsValid` si tienen el atributo `[ApiController]`. En ese caso, se devuelve una respuesta HTTP 400 automática que contiene los detalles del error cuando el estado del modelo no es válido. Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Nueva ejecución de la validación

La validación es automática, pero tal vez le interese repetirla manualmente. Por ejemplo, tal vez haya calculado el valor de una propiedad y quiera volver a ejecutar la validación después de establecer la propiedad en el valor calculado. Para volver a ejecutar la validación, llame al método `TryValidateModel`, como se muestra aquí:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a>Atributos de validación

Los atributos de validación permiten especificar reglas de validación para las propiedades del modelo. En el ejemplo siguiente de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/sample) se muestra una clase de modelo anotada con atributos de validación. El atributo `[ClassicMovie]` es un atributo de validación personalizado y los demás están integrados. No se muestra `[ClassicMovie2]`, que indica una manera alternativa de implementar un atributo personalizado.

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a>Atributos integrados

Entre los atributos de validación integrados se incluyen:

* `[CreditCard]`: Valida que la propiedad tiene un formato de tarjeta de crédito.
* `[Compare]`: Valida que dos propiedades de un modelo coincidan. Por ejemplo, el archivo *Register.cshtml.cs* usa `[Compare]` para validar que ambas contraseñas escritas coincidan. [Scaffolding Identity ](xref:security/authentication/scaffold-identity) para ver el código de registro.
* `[EmailAddress]`: Valida que la propiedad tiene un formato de correo electrónico.
* `[Phone]`: Valida que la propiedad tiene un formato de número de teléfono.
* `[Range]`: Valida que el valor de la propiedad se encuentra dentro de un intervalo especificado.
* `[RegularExpression]`: Valida que el valor de propiedad coincide con una expresión regular especificada.
* `[Required]`: Valida que el campo no sea NULL. Vea el [ `[Required]` atributo](#required-attribute) para obtener más información sobre el comportamiento de este atributo.
* `[StringLength]`: Valida que un valor de propiedad de cadena no supera un límite de longitud especificado.
* `[Url]`: Valida que la propiedad tiene un formato de dirección URL.
* `[Remote]`: Valida la entrada en el cliente mediante una llamada a un método de acción en el servidor. Vea el [ `[Remote]` atributo](#remote-attribute) para obtener más información sobre el comportamiento de este atributo.

Cuando se usa el atributo `[RegularExpression]` con la validación del lado cliente, la regex se ejecuta en JavaScript en el cliente. Esto significa que se usará el comportamiento de coincidencia de [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior). Para más información, consulte [este problema de GitHub](https://github.com/dotnet/corefx/issues/42487).

En el espacio de nombres [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) encontrará una lista completa de atributos de validación.

### <a name="error-messages"></a>Mensajes de error

Los atributos de validación permiten especificar el mensaje de error que se mostrará para una entrada no válida. Por ejemplo:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

Internamente, los atributos llaman a `String.Format` con un marcador de posición para el nombre de campo y, en ocasiones, marcadores de posición adicionales. Por ejemplo:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

Cuando se aplica a una propiedad `Name`, el mensaje de error creado por el código anterior sería "La longitud del nombre debe estar entre 6 y 8".

Para averiguar qué parámetros se pasan a `String.Format` para el mensaje de error de un atributo determinado, vea el [código fuente de DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="required-attribute"></a>Atributo [Required]

De forma predeterminada, el sistema de validación trata las propiedades o los parámetros que no aceptan valores NULL como si tuvieran un atributo `[Required]`. Los [tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) como `decimal` y `int` no aceptan valores NULL.

### <a name="required-validation-on-the-server"></a>Validación de [Required] en el servidor

En el servidor, si la propiedad es NULL, se considera que falta un valor requerido. Un campo que no acepta valores NULL siempre es válido, y el mensaje de error del atributo [Required] no se muestra nunca.

Aun así, el enlace de modelos para una propiedad que no acepta valores NULL podría fallar, lo que genera un mensaje de error como `The value '' is invalid`. Para especificar un mensaje de error personalizado para la validación del lado servidor de tipos que no aceptan valores NULL, tiene las siguientes opciones:

* Haga que el campo acepte valores NULL (por ejemplo, `decimal?` en lugar de `decimal`). [Acepta \<T> valores NULL](/dotnet/csharp/programming-guide/nullable-types/) los tipos de valor se tratan como los tipos estándar que aceptan valores NULL.
* Especifique el mensaje de error predeterminado que el enlace de modelos va a usar, como se muestra en el ejemplo siguiente:

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  Para obtener más información sobre los errores de enlace de modelos para los que se pueden establecer mensajes predeterminados, vea <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.

### <a name="required-validation-on-the-client"></a>Validación de [Required] en el cliente

Las cadenas y los tipos que no aceptan valores NULL se tratan de forma diferente en el cliente, en comparación con el servidor. En el cliente:

* Un valor se considera presente solo si se especifica una entrada para él. Por lo tanto, la validación del lado cliente controla los tipos que no aceptan valores NULL del mismo modo que los tipos que aceptan valores NULL.
* El método [required](https://jqueryvalidation.org/required-method/) de jQuery Validate considera que un espacio en blanco en un campo de cadena es una entrada válida. La validación del lado servidor considera que un campo de cadena necesario no es válido si solo se especifica un espacio en blanco.

Como se indicó anteriormente, los tipos que no aceptan valores NULL se tratan como si tuvieran un atributo `[Required]`. Esto significa que se obtiene la validación del lado cliente incluso si no se aplica el atributo `[Required]`. Si no usa el atributo, aparecerá un mensaje de error predeterminado. Para especificar un mensaje de error personalizado, use el atributo.

## <a name="remote-attribute"></a>Atributo [Remote]

El atributo `[Remote]` implementa la validación del lado cliente que requiere llamar a un método en el servidor para determinar si la entrada del campo es válida. Por ejemplo, la aplicación podría tener que comprobar si un nombre de usuario ya está en uso.

Para implementar la validación remota:

1. Cree un método de acción para que lo llame JavaScript.  El método [remote](https://jqueryvalidation.org/remote-method/) de jQuery Validate espera una respuesta JSON:

   * `"true"` significa que los datos de entrada son válidos.
   * `"false"`, `undefined` o `null` significan que la entrada no es válida.  Muestre el mensaje de error predeterminado.
   * Cualquier otra cadena significa que la entrada no es válida. Muestre la cadena como un mensaje de error personalizado.

   A continuación encontrará un ejemplo de un método de acción que devuelve un mensaje de error personalizado:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. En la clase de modelo, anote la propiedad con un atributo `[Remote]` que apunte al método de acción de validación, como se muestra en el ejemplo siguiente:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   El atributo `[Remote]` está en el espacio de nombres `Microsoft.AspNetCore.Mvc`. Instale el paquete de NuGet [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) si no usa `Microsoft.AspNetCore.App` o el metapaquete `Microsoft.AspNetCore.All`.
   
### <a name="additional-fields"></a>Campos adicionales

La propiedad `AdditionalFields` del atributo `[Remote]` permite validar combinaciones de campos con los datos del servidor. Por ejemplo, si el modelo `User` tuviera las propiedades `FirstName` y `LastName`, podría interesarle comprobar que ningún usuario actual tuviera ya ese par de nombres. En el siguiente ejemplo se muestra cómo usar `AdditionalFields`:

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields` podría configurarse explícitamente para las cadenas `"FirstName"` y `"LastName"`, pero, al usar el operador [nameof](/dotnet/csharp/language-reference/keywords/nameof) se simplifica la refactorización posterior. El método de acción para esta validación debe aceptar los argumentos de nombre y apellido:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Cuando el usuario escribe un nombre o un apellido, JavaScript realiza una llamada remota para comprobar si ese par de nombres ya existe.

Para validar dos o más campos adicionales, proporciónelos como una lista delimitada por comas. Por ejemplo, para agregar una propiedad `MiddleName` al modelo, establezca el atributo `[Remote]` tal como se muestra en el ejemplo siguiente:

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, al igual que todos los argumentos de atributo, debe ser una expresión constante. Por lo tanto, no use una [cadena interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) ni llame a <xref:System.String.Join*> para inicializar `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Alternativas a los atributos integrados

Si necesita una validación que no proporcionan los atributos integrados, puede hacer lo siguiente:

* [Crear atributos personalizados](#custom-attributes)
* [Implementar IValidatableObject](#ivalidatableobject)

## <a name="custom-attributes"></a>Atributos personalizados

Para los escenarios que no se controlan mediante los atributos de validación integrados, puede crear atributos de validación personalizados. Cree una clase que herede de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> y reemplace el método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.

El método `IsValid` acepta un objeto denominado *value*, que es la entrada que se va a validar. Una sobrecarga también acepta un objeto `ValidationContext`, que proporciona información adicional, como la instancia del modelo creada por el enlace de modelos.

El siguiente ejemplo valida que la fecha de lanzamiento de una película del género *Classic* no sea posterior a un año especificado. El atributo `[ClassicMovie2]` comprueba primero el género y continúa únicamente si es *Classic*. Para las películas que se identifican como clásicos, comprueba la fecha de lanzamiento a fin de asegurarse de que no sea posterior al límite que se ha pasado al constructor de atributo.

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

La variable `movie` del ejemplo anterior representa un objeto `Movie` que contiene los datos del envío del formulario. El método `IsValid` comprueba la fecha y el género. Si la validación es correcta, `IsValid` devuelve un código `ValidationResult.Success`. Si se produce un error de validación, se devuelve un `ValidationResult` con un mensaje de error.

## <a name="ivalidatableobject"></a>IValidatableObject

El ejemplo anterior solo funciona con tipos `Movie`. Otra opción para la validación del nivel de clase consiste en implementar `IValidatableObject` en la clase de modelo, como se muestra en el ejemplo siguiente:

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Validación de nodo de nivel superior

Los nodos de nivel superior incluyen lo siguiente:

* Parámetros de acción
* Propiedades de controlador
* Parámetros de controlador de página
* Propiedades de modelo de página

Los nodos de nivel superior enlazados al modelo se validan además de la validación de las propiedades del modelo. En el ejemplo siguiente de la aplicación de muestra, el método `VerifyPhone` usa <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar el parámetro de acción `phone`:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Los nodos de nivel superior pueden usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> con atributos de validación. En el ejemplo siguiente de la aplicación de muestra, el método `CheckAge` especifica que el parámetro `age` debe estar enlazado desde la cadena de consulta al enviar el formulario:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

En la página Comprobar edad (*CheckAge.cshtml*), hay dos formularios. El primer formulario envía un valor `Age` de `99` como una cadena de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.

Al enviar un parámetro `age` con un formato correcto desde la cadena de consulta, el formulario se valida.

El segundo formulario de la página Comprobar edad envía el valor `Age` en el cuerpo de la solicitud, y se produce un error de validación. Se produce un error en el enlace porque el parámetro `age` debe provenir de una cadena de consulta.

Cuando se ejecuta con `CompatibilityVersion.Version_2_1` o versiones posteriores, la validación de nodo de nivel superior está habilitada de forma predeterminada. En caso contrario, la validación del nodo de nivel superior está deshabilitada. La opción predeterminada se puede invalidar si se establece la propiedad <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> en (`Startup.ConfigureServices`), como se muestra aquí:

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a>Número máximo de errores

La validación se detiene cuando se alcanza el número máximo de errores (200 de forma predeterminada). Puede configurar este número con el siguiente código en `Startup.ConfigureServices`:

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a>Recursividad máxima

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> recorre el gráfico de objetos del modelo que se está validando. En el caso de los modelos muy profundos o infinitamente recursivos, la validación podría causar un desbordamiento de pila. [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) proporciona una manera de detener pronto la validación si la recursividad del visitante supera la profundidad configurada. El valor predeterminado de `MvcOptions.MaxValidationDepth` es 32 cuando se ejecuta con `CompatibilityVersion.Version_2_2` o una versión posterior. Para las versiones anteriores, el valor es NULL, lo que significa que no hay restricción de profundidad.

## <a name="automatic-short-circuit"></a>Cortocircuito automático

La validación cortocircuita (se omite) automáticamente si el gráfico de modelo no requiere validación. Entre los objetos para los que el tiempo de ejecución omite la validación se incluyen las colecciones de elementos primitivos (como `byte[]`, `string[]` y `Dictionary<string, string>`) y gráficos de objeto complejo que no tienen los validadores.

## <a name="disable-validation"></a>Deshabilitación de la validación

Para deshabilitar la validación:

1. Cree una implementación de `IObjectModelValidator` que no marque ningún campo como no válido.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. Agregue el código siguiente a `Startup.ConfigureServices` para reemplazar la implementación predeterminada de `IObjectModelValidator` en el contenedor de inserción de dependencias.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

Es posible que todavía vea errores de estado del modelo originados en el enlace de modelos.

## <a name="client-side-validation"></a>Validación del lado cliente

La validación del lado cliente impide realizar el envío hasta que el formulario sea válido. El botón Enviar ejecuta JavaScript para enviar el formulario o mostrar mensajes de error.

La validación del lado cliente evita un recorrido de ida y vuelta innecesario en el servidor cuando hay errores de entrada en un formulario. Las siguientes referencias de script de *_Layout.cshtml* y *_ValidationScriptsPartial.cshtml* admiten la validación del lado cliente:

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

El script [Validación discreta de jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) es una biblioteca front-end personalizada de Microsoft que se basa en el conocido complemento [Validación de jQuery](https://jqueryvalidation.org/). Si no usa Validación discreta de jQuery, deberá codificar la misma lógica de validación dos veces: una vez en los atributos de validación del lado servidor en las propiedades del modelo y luego en los scripts del lado cliente. En su lugar, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan los atributos de validación y escriben metadatos de las propiedades del modelo para representar atributos `data-` HTML 5 para los elementos de formulario que necesitan validación. Validación discreta de jQuery analiza los atributos `data-` y pasa la lógica a jQuery Validate. De este modo, la lógica de validación del lado servidor se "copia" de manera eficaz en el cliente. Puede mostrar errores de validación en el cliente mediante el uso de asistentes de etiquetas, como se muestra aquí:

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

Los anteriores asistentes de etiquetas representan el siguiente código HTML.

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

Tenga en cuenta que los atributos `data-` en los resultados HTML corresponden a los atributos de validación para la propiedad `ReleaseDate`. El atributo `data-val-required` contiene un mensaje de error que se muestra si el usuario no rellena el campo de fecha de estreno. la validación discreta de jQuery pasa este valor al método de jQuery Validate [Required ()](https://jqueryvalidation.org/required-method/) , que después muestra ese mensaje en el **\<span>** elemento correspondiente.

La validación del tipo de datos se basa en el tipo .NET de una propiedad, a menos que lo reemplace un atributo `[DataType]`. Los exploradores tienen sus propios mensajes de error de predeterminados, pero el paquete de Validación discreta de jQuery Validate puede invalidar esos mensajes. Los atributos y las subclases `[DataType]`, como `[EmailAddress]`, permiten especificar el mensaje de error.

### <a name="add-validation-to-dynamic-forms"></a>Agregar validación a formularios dinámicos

Validación discreta de jQuery pasa los parámetros y la lógica de validación a jQuery Validate cuando la página se carga por primera vez. Por lo tanto, la validación no funciona automáticamente en los formularios generados dinámicamente. Para habilitar la validación, hay que indicarle a Validación discreta de jQuery que analice el formulario dinámico inmediatamente después de su creación. Por ejemplo, en el código siguiente se configura la validación del lado cliente en un formulario agregado mediante AJAX.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

El método `$.validator.unobtrusive.parse()` acepta un selector de jQuery para su único argumento. Este método indica a Validación discreta de jQuery que analice los atributos `data-` de formularios dentro de ese selector. Después, los valores de estos atributos se pasan al complemento de jQuery Validate.

### <a name="add-validation-to-dynamic-controls"></a>Agregar validación a controles dinámicos

El método `$.validator.unobtrusive.parse()` funciona en todo el formulario, no en los controles individuales generados dinámicamente, como `<input>` y `<select/>`. Para volver a analizar el formulario, quite los datos de validación que se agregaron cuando el formulario se analizó anteriormente, como se muestra en el ejemplo siguiente:

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Validación del lado cliente personalizada

Para personalizar la validación del lado cliente, es necesario generar atributos HTML `data-` que funcionen con un adaptador personalizado de jQuery Validate. El siguiente ejemplo de código de adaptador se escribió para los atributos `ClassicMovie` y `ClassicMovie2` que se introdujeron anteriormente en este artículo:

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

Para obtener información sobre cómo escribir adaptadores, vea la [documentación de jQuery Validate](https://jqueryvalidation.org/documentation/).

El uso de un adaptador para un campo determinado se desencadena mediante atributos `data-` que:

* Marcan que el campo está sujeto a validación (`data-val="true"`).
* Identifican un nombre de regla de validación y un texto de mensaje de error (por ejemplo, `data-val-rulename="Error message."`).
* Proporcionan los parámetros adicionales que necesite el validador (por ejemplo, `data-val-rulename-parm1="value"`).

En el ejemplo siguiente se muestran los atributos `data-` para el atributo `ClassicMovie` de la aplicación de ejemplo:

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

Como se indicó anteriormente, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan información procedente de los atributos de validación para representar atributos `data-`. Hay dos opciones para escribir código que dé como resultado la creación de atributos HTML `data-` personalizados:

* Puede crear una clase que derive de `AttributeAdapterBase<TAttribute>` y una clase que implemente `IValidationAttributeAdapterProvider` y, después, registrar el atributo y su adaptador en la inserción de dependencias. Este método sigue el [principio de responsabilidad única](https://wikipedia.org/wiki/Single_responsibility_principle), ya que el código de validación relacionado con servidor y el relacionado con el cliente se encuentran en clases independientes. El adaptador también cuenta con una ventaja: debido a que está registrado en la inserción de dependencias, tiene a su disposición otros servicios de la inserción de dependencias si es necesario.
* Puede implementar `IClientModelValidator` en la clase `ValidationAttribute`. Este método es adecuado si el atributo no realiza ninguna validación en el lado servidor y no necesita ningún servicio de la inserción de dependencias.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter para la validación del lado cliente

Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovie` en la aplicación de ejemplo. Para agregar la validación de cliente mediante este método:

1. Cree una clase de adaptador de atributo para el atributo de validación personalizado. Derive la clase de <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>. Cree un método `AddValidation` que agregue atributos `data-` a la salida representada, tal como se muestra en este ejemplo:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. Cree una clase de proveedor de adaptador que implemente <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. En el método `GetAttributeAdapter`, pase el atributo personalizado al constructor del adaptador, como se muestra en este ejemplo:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. Registre el proveedor del adaptador para la inserción de dependencias en `Startup.ConfigureServices`:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator para la validación del lado cliente

Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovie2` en la aplicación de ejemplo. Para agregar la validación de cliente mediante este método:

* En el atributo de validación personalizado, implemente la interfaz `IClientModelValidator` y cree un método `AddValidation`. En el método `AddValidation`, agregue atributos `data-` para la validación, como se muestra en el ejemplo siguiente:

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a>Deshabilitación de la validación del lado cliente

El código siguiente deshabilita la validación de cliente en las vistas de MVC:

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

Y en Razor las páginas:

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

Otra opción para deshabilitar la validación de cliente consiste en marcar como comentario la referencia a `_ValidationScriptsPartial` en el archivo *.cshtml*.

## <a name="additional-resources"></a>Recursos adicionales

* [Espacio de nombres System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Enlace de modelos](model-binding.md)

::: moniker-end
