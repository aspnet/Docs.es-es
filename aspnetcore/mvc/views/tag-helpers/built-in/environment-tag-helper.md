---
title: Asistente de etiquetas de entorno en ASP.NET Core
author: pkellner
description: Asistente de etiquetas de entorno de ASP.NET Core definida con todas las propiedades
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 01fa5409136557dcbf53dbdd051bf35803e8b71f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018655"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Asistente de etiquetas de entorno en ASP.NET Core

Por [Peter Kellner](https://peterkellner.net) y [Hisham Bin Ateya](https://twitter.com/hishambinateya)

La aplicación auxiliar de etiquetas de entorno representa condicionalmente el contenido incluido en función del [entorno de hospedaje](xref:fundamentals/environments)actual. El único atributo del asistente de etiquetas de entorno, `names`, es una lista de nombres de entorno separados por comas. Si alguno de los nombres de entorno proporcionados coincide con el entorno actual, se representa el contenido incluido.

Para obtener información general sobre asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.

## <a name="environment-tag-helper-attributes"></a>Atributos del asistente de etiquetas de entorno

### <a name="names"></a>nombres

`names` acepta un solo nombre de entorno de hospedaje o una lista de nombres de entorno de hospedaje separados por comas que desencadenan la representación del contenido incluido.

Los valores de entorno se comparan con el valor actual devuelto por [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*). La comparación ignora el uso de mayúsculas y minúsculas.

En este ejemplo se usa un asistente de etiquetas de entorno. El contenido se representa si el entorno de hospedaje es de almacenamiento provisional o de producción:

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>Atributos include y exclude

`include`& `exclude` los atributos controlan la representación del contenido incluido en función de los nombres de entorno de hospedaje incluidos o excluidos.

### <a name="include"></a>include

La propiedad `include` exhibe un comportamiento similar al atributo `names`. Un entorno que se muestra en el valor de atributo `include` debe coincidir con el entorno de hospedaje de la aplicación ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) para representar el contenido de la etiqueta `<environment>`.

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>exclude

A diferencia del atributo `include`, el contenido de la etiqueta `<environment>` se representa cuando el entorno de hospedaje no coincide con un entorno que se muestra en el valor de atributo `exclude`.

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/environments>
