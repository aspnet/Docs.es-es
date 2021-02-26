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
ms.openlocfilehash: 05c94351ee4747813cfa8dc2318a6fc02c3a46bf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551862"
---
```console
npm run release
```

Este comando genera la entrega de los recursos del lado cliente al ejecutar la aplicación. Los recursos se colocan en la carpeta *wwwroot*.

Webpack ha completado las tareas siguientes:

* Purgar el contenido del directorio *wwwroot*.
* Convertir el lenguaje TypeScript en JavaScript, proceso conocido como *transpilación*.
* Alterar el código JavaScript generado para reducir el tamaño del archivo, proceso conocido como *minificación*.
* Copiar los archivos JavaScript, CSS y HTML procesados desde *src* en el directorio *wwwroot*.
* Insertar los elementos siguientes en el archivo *wwwroot/index.html*:
  * Etiqueta `<link>`, que hace referencia al archivo *wwwroot/main.\<hash\>.css*. Esta etiqueta se coloca inmediatamente antes de la etiqueta `</head>` de cierre.
  * Etiqueta `<script>`, que hace referencia al archivo *wwwroot/main.\<hash\>.js* minificado. Esta etiqueta se coloca inmediatamente antes de la etiqueta `</body>` de cierre.