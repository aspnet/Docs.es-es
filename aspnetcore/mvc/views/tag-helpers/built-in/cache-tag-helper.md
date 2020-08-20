---
title: Asistente de etiquetas de caché en ASP.NET Core MVC
author: pkellner
description: Obtenga información sobre cómo usar el asistente de etiquetas de caché.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
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
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: b1cab7ab8b491529ee4208d92fb30082be795eda
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635065"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>Asistente de etiquetas de caché en ASP.NET Core MVC

Por [Peter Kellner](https://peterkellner.net)

El asistente de etiquetas de caché proporciona la capacidad para mejorar el rendimiento de la aplicación de ASP.NET Core al permitir almacenar en memoria caché su contenido en el proveedor de caché interno de ASP.NET Core.

Para obtener información general sobre asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.

El siguiente Razor marcado almacena en caché la fecha actual:

```cshtml
<cache>@DateTime.Now</cache>
```

La primera solicitud a la página que contiene el asistente de etiquetas muestra la fecha actual. Las solicitudes adicionales muestran el valor almacenado en caché hasta que la memoria caché expira (el valor predeterminado es 20 minutos) o hasta que la fecha almacenada en caché se expulsa de la memoria caché.

## <a name="cache-tag-helper-attributes"></a>Atributos del asistente de etiqueta de caché

### <a name="enabled"></a>enabled

| Tipo de atributo  | Ejemplos        | Valor predeterminado |
| --------------- | --------------- | ------- |
| Boolean         | `true`, `false` | `true`  |

`enabled` determina si se almacena en caché el contenido incluido por el asistente de etiquetas de caché. El valor predeterminado es `true`. Si establece en `false`, el resultado representado **no** se almacena en caché.

Ejemplo:

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a>expires-on

| Tipo de atributo   | Ejemplo                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

`expires-on` establece una fecha de expiración absoluta para el elemento almacenado en caché.

En este ejemplo se almacena en caché el contenido del asistente de etiquetas de caché hasta las 17:02 del 29 de enero de 2025.

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a>expires-after

| Tipo de atributo | Ejemplo                      | Valor predeterminado    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | 20 minutos |

`expires-after` establece el período de tiempo desde el momento de la primera solicitud para almacenar en caché el contenido.

Ejemplo:

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

El Razor motor de vista establece el `expires-after` valor predeterminado en veinte minutos.

### <a name="expires-sliding"></a>expires-sliding

| Tipo de atributo | Ejemplo                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

Establece la hora en que se debe expulsar una entrada de caché si no se ha accedido a su valor.

Ejemplo:

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a>vary-by-header

| Tipo de atributo | Ejemplos                                    |
| -------------- | ------------------------------------------- |
| String         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header` acepta una lista de los valores de encabezado separados por comas que desencadenan una actualización de la caché cuando cambian.

En el ejemplo siguiente se supervisa el valor del encabezado `User-Agent`. En el ejemplo se almacena en caché el contenido de cada `User-Agent` diferente que se presenta al servidor web:

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>vary-by-query

| Tipo de atributo | Ejemplos             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-query` acepta una lista de valores separados por comas de <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> en una cadena de consulta (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) que desencadenan una actualización de la caché cuando cambia el valor de cualquiera de las claves.

En este ejemplo se supervisan los valores de `Make` y `Model`. En el ejemplo se almacena en caché el contenido de cada `Make` y `Model`diferente que se presenta al servidor web:

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>vary-by-route

| Tipo de atributo | Ejemplos             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-route` acepta una lista delimitada por comas de nombres de parámetros de ruta que desencadenan una actualización de la caché cuando el valor del parámetro de datos de ruta cambia.

Ejemplo:

*Startup.cs*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

*Index. cshtml*:

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-no-loccookie"></a>variar porcookie

| Tipo de atributo | Ejemplos                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| String         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie` acepta una lista delimitada por comas de cookie nombres que desencadenan una actualización de la memoria caché cuando cookie cambian los valores.

En el siguiente ejemplo se supervisa el cookie asociado a ASP.NET Core Identity . Cuando se autentica un usuario, un cambio en Identity cookie desencadena una actualización de la memoria caché:

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>vary-by-user

| Tipo de atributo  | Ejemplos        | Valor predeterminado |
| --------------- | --------------- | ------- |
| Boolean         | `true`, `false` | `true`  |

`vary-by-user` especifica la memoria caché se restablece o no cuando cambia el usuario que ha iniciado la sesión (o la entidad de seguridad del contexto). El usuario actual también se conoce como la entidad de seguridad del contexto de la solicitud y se puede ver en una Razor vista haciendo referencia a `@User.Identity.Name` .

En este ejemplo se supervisa el usuario actual que ha iniciado sesión para desencadenar una actualización de caché:

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Con este atributo, se mantiene el contenido en caché a través de un ciclo de inicio y cierre de sesión. Cuando el valor se establece en `true`, un ciclo de autenticación invalida la memoria caché para el usuario autenticado. La memoria caché se invalida porque se genera un nuevo cookie valor único cuando se autentica un usuario. La memoria caché se mantiene para el estado anónimo cuando no cookie existe o cookie ha expirado. Si **no** se autentica el usuario, se mantiene la memoria caché.

### <a name="vary-by"></a>vary-by

| Tipo de atributo | Ejemplo  |
| -------------- | -------- |
| String         | `@Model` |

`vary-by` permite personalizar qué datos se almacenan en caché. Cuando el objeto al que hace referencia el valor de cadena del atributo cambia, el contenido del asistente de etiqueta de caché se actualiza. A menudo se asignan a este atributo una concatenación de cadenas de valores del modelo. De hecho, esto provoca una situación en la que una actualización de cualquiera de los valores concatenados invalida la memoria caché.

En este ejemplo se supone que el método de controlador que representa la vista suma el valor del entero de los dos parámetros de ruta, `myParam1` y `myParam2`, y devuelve el resultado de la suma como la propiedad de modelo simple. Cuando se cambia esta suma, el contenido del asistente de etiqueta de caché se representa y almacena en caché de nuevo.  

Acción:

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index. cshtml*:

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a>priority

| Tipo de atributo      | Ejemplos                               | Valor predeterminado  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`, `Low`, `NeverRemove`, `Normal` | `Normal` |

`priority` proporciona instrucciones de expulsión de caché para el proveedor de caché integrado. El servidor web expulsa primero las entradas de caché `Low` cuando está bajo presión de memoria.

Ejemplo:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

El atributo `priority` no garantiza un nivel específico de retención de la memoria caché. `CacheItemPriority` es solo una sugerencia. Establecer este atributo en `NeverRemove` no garantiza que siempre se conserven los elementos en la memoria caché. Para más información, eche un vistazo a los temas de la sección [Recursos adicionales](#additional-resources).

El asistente de etiquetas de caché es dependiente del [servicio de caché de memoria](xref:performance/caching/memory). El asistente de etiquetas de caché agrega el servicio si no se ha agregado.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
