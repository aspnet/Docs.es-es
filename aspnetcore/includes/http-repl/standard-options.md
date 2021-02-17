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
ms.openlocfilehash: 30c4c469453f0202fe5310dbe00b3d7214f49b5a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552037"
---
* `-F|--no-formatting`

  Marca cuya presencia suprime el formato de respuesta HTTP.

* `-h|--header`

  Establece un encabezado de solicitud HTTP. Se admiten los dos siguientes formatos de valor:

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  Especifica un archivo en el que se debe escribir el cuerpo de respuesta HTTP. Por ejemplo, `--response:body "C:\response.json"`. Si el archivo no existe, se creará.

* `--response:headers`

  Especifica un archivo en el que se deben escribir los encabezados de respuesta HTTP. Por ejemplo, `--response:headers "C:\response.txt"`. Si el archivo no existe, se creará.

* `-s|--streaming`

  Marca cuya presencia habilita la transmisión en secuencias de la respuesta HTTP.
