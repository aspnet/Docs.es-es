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

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="8ba81-101">Adición de reglas de validación al modelo de película</span><span class="sxs-lookup"><span data-stu-id="8ba81-101">Add validation rules to the movie model</span></span>

<span data-ttu-id="8ba81-102">El espacio de nombres DataAnnotations proporciona un conjunto de atributos de validación integrados que se aplican mediante declaración a una clase o propiedad.</span><span class="sxs-lookup"><span data-stu-id="8ba81-102">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="8ba81-103">DataAnnotations también contiene atributos de formato como `DataType` que ayudan a aplicar formato y no proporcionan ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="8ba81-103">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="8ba81-104">Actualice la clase `Movie` para aprovechar los atributos de validación integrados `Required`, `StringLength`, `RegularExpression` y `Range`.</span><span class="sxs-lookup"><span data-stu-id="8ba81-104">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="8ba81-105">Los atributos de validación especifican el comportamiento que quiere aplicar en las propiedades del modelo al que se aplican:</span><span class="sxs-lookup"><span data-stu-id="8ba81-105">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="8ba81-106">Los atributos `Required` y `MinimumLength` indican que una propiedad debe tener un valor, pero nada impide al usuario escribir espacios en blanco para satisfacer esta validación.</span><span class="sxs-lookup"><span data-stu-id="8ba81-106">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="8ba81-107">El atributo `RegularExpression` se usa para limitar los caracteres que se pueden escribir.</span><span class="sxs-lookup"><span data-stu-id="8ba81-107">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="8ba81-108">En el código anterior, "Género":</span><span class="sxs-lookup"><span data-stu-id="8ba81-108">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="8ba81-109">Solo debe usar letras.</span><span class="sxs-lookup"><span data-stu-id="8ba81-109">Must only use letters.</span></span>
  * <span data-ttu-id="8ba81-110">La primera letra debe estar en mayúsculas.</span><span class="sxs-lookup"><span data-stu-id="8ba81-110">The first letter is required to be uppercase.</span></span> <span data-ttu-id="8ba81-111">No se permiten espacios en blanco, números ni caracteres especiales.</span><span class="sxs-lookup"><span data-stu-id="8ba81-111">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="8ba81-112">La "Clasificación" de `RegularExpression`:</span><span class="sxs-lookup"><span data-stu-id="8ba81-112">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="8ba81-113">Requiere que el primer carácter sea una letra mayúscula.</span><span class="sxs-lookup"><span data-stu-id="8ba81-113">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="8ba81-114">Permite caracteres especiales y números en los espacios posteriores.</span><span class="sxs-lookup"><span data-stu-id="8ba81-114">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="8ba81-115">"PG-13" es válido para una "Clasificación", pero se produce un error en un "Género".</span><span class="sxs-lookup"><span data-stu-id="8ba81-115">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="8ba81-116">El atributo `Range` restringe un valor a un intervalo determinado.</span><span class="sxs-lookup"><span data-stu-id="8ba81-116">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="8ba81-117">El atributo `StringLength` permite establecer la longitud máxima de una propiedad de cadena y, opcionalmente, su longitud mínima.</span><span class="sxs-lookup"><span data-stu-id="8ba81-117">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="8ba81-118">Los tipos de valor (como `decimal`, `int`, `float`, `DateTime`) son intrínsecamente necesarios y no necesitan el atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="8ba81-118">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="8ba81-119">El que ASP.NET Core aplique automáticamente las reglas de validación ayuda a que su aplicación sea más sólida.</span><span class="sxs-lookup"><span data-stu-id="8ba81-119">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="8ba81-120">También nos permite asegurarnos de que todo se valida y que no nos dejamos ningún dato incorrecto en la base de datos accidentalmente.</span><span class="sxs-lookup"><span data-stu-id="8ba81-120">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>
