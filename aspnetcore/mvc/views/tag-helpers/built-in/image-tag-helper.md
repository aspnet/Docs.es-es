---
title: Asistente de etiquetas de imagen en ASP.NET Core
author: pkellner
description: Muestra cómo trabajar con el asistente de etiquetas de imagen.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: edc6971ac42756504b5b371ba509d7a657a0c396
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061124"
---
# <a name="image-tag-helper-in-aspnet-core"></a>Asistente de etiquetas de imagen en ASP.NET Core

Por [Peter Kellner](https://peterkellner.net)

El asistente de etiquetas de imagen mejora la etiqueta `<img>` para proporcionar un comportamiento de limpieza de caché para archivos de imagen estática.

Una cadena de limpieza de memoria caché es un valor único que representa el valor hash del archivo de imagen estático anexados a la URL del activo. La cadena única pide a los clientes (y algunos servidores proxy) que vuelva a cargar la imagen desde el servidor web del host y no desde la memoria caché del cliente.

Si el origen de la imagen (`src`) es un archivo estático en el servidor web del host:

* Se anexa una cadena única de limpieza de caché como un parámetro de consulta al origen de la imagen.
* Si el archivo en el servidor web del host cambia, se genera una dirección URL de solicitud única que incluye el parámetro de solicitud actualizada.

Para obtener información general sobre asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.

## <a name="image-tag-helper-attributes"></a>Atributos del asistente de etiquetas de imagen

### <a name="src"></a>src

Para activar el asistente de etiquetas de imagen, se necesita el atributo `src` en el elemento `<img>`.

El origen de la imagen (`src`) debe apuntar a un archivo estático físico en el servidor. Si `src` es un identificador URI remoto, el parámetro de cadena de consulta de limpieza de caché no se genera.

### <a name="asp-append-version"></a>asp-append-version

Cuando se especifica `asp-append-version` con un valor `true` junto con un atributo `src`, se invoca el asistente de etiquetas de imagen.

En este ejemplo se usa un asistente de etiquetas de imagen:

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

Si el archivo estático existe en el directorio */wwwroot/images/* , el código HTML generado es similar al siguiente (el valor hash será diferente):

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

El valor asignado al parámetro `v` es el valor hash del archivo */wwwroot/images/* almacenado en disco. Si el servidor web no es capaz de tener acceso de lectura al archivo estático, no se agregará ningún parámetro `v` al atributo `src` en el marcado representado.

## <a name="hash-caching-behavior"></a>Comportamiento de almacenamiento en caché de hash

El asistente de etiquetas de imagen usa el proveedor de caché en el servidor web local para almacenar el hash `Sha512` calculado de un archivo determinado. Si se solicita el archivo varias veces, no se vuelven a calcular el hash. La memoria caché queda invalidada por un monitor del archivo que se adjunta al archivo cuando se calcula el hash `Sha512` del archivo. Cuando el archivo se cambia en el disco, se calcula un nuevo hash y se almacena en caché.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:performance/caching/memory>
