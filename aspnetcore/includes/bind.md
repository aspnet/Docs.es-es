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
ms.openlocfilehash: 30baab0649268f4abf0dbd6c99dfeef3f43d0054
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536321"
---
<span data-ttu-id="31f21-101">La mejor manera de leer valores de configuración relacionados es usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="31f21-101">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="31f21-102">Por ejemplo, para leer los siguientes valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="31f21-102">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="31f21-103">Cree la siguiente clase `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="31f21-103">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="31f21-104">Una clase de opciones:</span><span class="sxs-lookup"><span data-stu-id="31f21-104">An options class:</span></span>

* <span data-ttu-id="31f21-105">Debe ser no abstracta con un constructor público sin parámetros.</span><span class="sxs-lookup"><span data-stu-id="31f21-105">Must be non-abstract with a public parameterless constructor.</span></span>
* <span data-ttu-id="31f21-106">Todas las propiedades de lectura y escritura públicas del tipo están enlazadas.</span><span class="sxs-lookup"><span data-stu-id="31f21-106">All public read-write properties of the type are bound.</span></span>
* <span data-ttu-id="31f21-107">Los campos ***no*** se enlazan.</span><span class="sxs-lookup"><span data-stu-id="31f21-107">Fields are ***not*** bound.</span></span> <span data-ttu-id="31f21-108">En el código anterior, `Position` no está enlazado.</span><span class="sxs-lookup"><span data-stu-id="31f21-108">In the preceding code, `Position` is not bound.</span></span> <span data-ttu-id="31f21-109">La propiedad `Position` se usa para que la cadena `"Position"` no tenga que estar codificada de forma rígida en la aplicación al enlazar la clase a un proveedor de configuración.</span><span class="sxs-lookup"><span data-stu-id="31f21-109">The `Position` property is used so the string `"Position"` doesn't need to be hard coded in the app when binding the class to a configuration provider.</span></span>

<span data-ttu-id="31f21-110">El código siguiente:</span><span class="sxs-lookup"><span data-stu-id="31f21-110">The following code:</span></span>

* <span data-ttu-id="31f21-111">Llama a [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) para enlazar la clase `PositionOptions` a la sección `Position`.</span><span class="sxs-lookup"><span data-stu-id="31f21-111">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="31f21-112">Muestra los datos de configuración de `Position`.</span><span class="sxs-lookup"><span data-stu-id="31f21-112">Displays the `Position` configuration data.</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="31f21-113">En el código anterior, de forma predeterminada, los cambios en el archivo de configuración de JSON producidos una vez iniciada la aplicación se leen.</span><span class="sxs-lookup"><span data-stu-id="31f21-113">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="31f21-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) enlaza y devuelve el tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="31f21-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="31f21-115">Puede ser más conveniente usar `ConfigurationBinder.Get<T>` que `ConfigurationBinder.Bind`.</span><span class="sxs-lookup"><span data-stu-id="31f21-115">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="31f21-116">En el código siguiente se muestra cómo puede usar `ConfigurationBinder.Get<T>` con la clase `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="31f21-116">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="31f21-117">En el código anterior, de forma predeterminada, los cambios en el archivo de configuración de JSON producidos una vez iniciada la aplicación se leen.</span><span class="sxs-lookup"><span data-stu-id="31f21-117">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="31f21-118">Un enfoque alternativo a la hora de usar el \***patrón de opciones** _ consiste en enlazar la sección `Position` y agregarla al [contenedor del servicio de inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="31f21-118">An alternative approach when using the \***options pattern** _ is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="31f21-119">En el siguiente código se agrega `PositionOptions` al contenedor de servicios con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> y se enlaza a la configuración:</span><span class="sxs-lookup"><span data-stu-id="31f21-119">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="31f21-120">A partir del código anterior, el siguiente código lee las opciones de posición:</span><span class="sxs-lookup"><span data-stu-id="31f21-120">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="31f21-121">En el código anterior, los cambios en el archivo de configuración de JSON producidos una vez iniciada la aplicación ***no*** se leen.</span><span class="sxs-lookup"><span data-stu-id="31f21-121">In the preceding code, changes to the JSON configuration file after the app has started are ***not*** read.</span></span> <span data-ttu-id="31f21-122">Para leer los cambios una vez iniciada la aplicación, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span><span class="sxs-lookup"><span data-stu-id="31f21-122">To read changes after the app has started, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span></span>
