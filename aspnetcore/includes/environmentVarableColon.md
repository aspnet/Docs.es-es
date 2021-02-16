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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536313"
---
El separador `:` no funciona con claves jerárquicas de variables de entorno en todas las plataformas. `__`, el carácter de subrayado doble, tiene las siguientes características:

* Es compatible con todas las plataformas. Por ejemplo, el separador `:` no es compatible con [Bash](https://linuxhint.com/bash-environment-variables/), pero `__` sí.
* Se reemplaza automáticamente por un signo `:`.