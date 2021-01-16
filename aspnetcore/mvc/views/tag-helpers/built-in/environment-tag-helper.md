---
title: Asistente de etiquetas de entorno en ASP.NET Core
author: pkellner
description: Asistente de etiquetas de entorno de ASP.NET Core definida con todas las propiedades
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: d63364b0c052ba7f9e745e1ad829b8d1ca9122d2
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253129"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Asistente de etiquetas de entorno en ASP.NET Core

Por [Peter Kellner](https://peterkellner.net) y [Hisham Bin Ateya](https://twitter.com/hishambinateya)

La aplicación auxiliar de etiquetas de entorno representa condicionalmente el contenido incluido en función del [entorno de hospedaje](xref:fundamentals/environments)actual. El único atributo del asistente de etiquetas de entorno, `names`, es una lista de nombres de entorno separados por comas. Si alguno de los nombres de entorno proporcionados coincide con el entorno actual, se representa el contenido incluido.

Para obtener información general sobre asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.

## <a name="environment-tag-helper-attributes"></a>Atributos del asistente de etiquetas de entorno

### <a name="names"></a>nombres

`names` acepta un solo nombre de entorno de hospedaje o una lista de nombres de entorno de hospedaje separados por comas que desencadenan la representación del contenido incluido.

Los valores de entorno se comparan con el valor actual devuelto por [IWebHostEnvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*). La comparación ignora el uso de mayúsculas y minúsculas.

En este ejemplo se usa un asistente de etiquetas de entorno. El contenido se representa si el entorno de hospedaje es de almacenamiento provisional o de producción:

```cshtml
<environment names="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>Atributos include y exclude

`include`& `exclude` los atributos controlan la representación del contenido incluido en función de los nombres de entorno de hospedaje incluidos o excluidos.

### <a name="include"></a>include

La propiedad `include` exhibe un comportamiento similar al atributo `names`. Un entorno incluido en el `include` valor del atributo debe coincidir con el entorno de hospedaje de la aplicación ([IWebHostEnvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) para representar el contenido de la `<environment>` etiqueta.

```cshtml
<environment include="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>exclude

A diferencia del atributo `include`, el contenido de la etiqueta `<environment>` se representa cuando el entorno de hospedaje no coincide con un entorno que se muestra en el valor de atributo `exclude`.

```cshtml
<environment exclude="Development">
    <strong>IWebHostEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/environments>
