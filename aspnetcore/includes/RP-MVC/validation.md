---
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
ms.openlocfilehash: c96c5e66d2e15db03c321d239a64b98119a7543a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551277"
---
<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>Adición de reglas de validación al modelo de película

El espacio de nombres DataAnnotations proporciona un conjunto de atributos de validación integrados que se aplican mediante declaración a una clase o propiedad. DataAnnotations también contiene atributos de formato como `DataType` que ayudan a aplicar formato y no proporcionan ninguna validación.

Actualice la clase `Movie` para aprovechar los atributos de validación integrados `Required`, `StringLength`, `RegularExpression` y `Range`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Los atributos de validación especifican el comportamiento que quiere aplicar en las propiedades del modelo al que se aplican:

* Los atributos `Required` y `MinimumLength` indican que una propiedad debe tener un valor, pero nada impide al usuario escribir espacios en blanco para satisfacer esta validación.
* El atributo `RegularExpression` se usa para limitar los caracteres que se pueden escribir. En el código anterior, "Género":

  * Solo debe usar letras.
  * La primera letra debe estar en mayúsculas. No se permiten espacios en blanco, números ni caracteres especiales.

* La "Clasificación" de `RegularExpression`:

  * Requiere que el primer carácter sea una letra mayúscula.
  * Permite caracteres especiales y números en los espacios posteriores. "PG-13" es válido para una "Clasificación", pero se produce un error en un "Género".

* El atributo `Range` restringe un valor a un intervalo determinado.
* El atributo `StringLength` permite establecer la longitud máxima de una propiedad de cadena y, opcionalmente, su longitud mínima.
* Los tipos de valor (como `decimal`, `int`, `float`, `DateTime`) son intrínsecamente necesarios y no necesitan el atributo `[Required]`.

El que ASP.NET Core aplique automáticamente las reglas de validación ayuda a que su aplicación sea más sólida. También nos permite asegurarnos de que todo se valida y que no nos dejamos ningún dato incorrecto en la base de datos accidentalmente.
