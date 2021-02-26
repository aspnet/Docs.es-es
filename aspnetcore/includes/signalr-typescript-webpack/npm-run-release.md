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

<span data-ttu-id="79644-101">Este comando genera la entrega de los recursos del lado cliente al ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="79644-101">This command generates the client-side assets to be served when running the app.</span></span> <span data-ttu-id="79644-102">Los recursos se colocan en la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="79644-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="79644-103">Webpack ha completado las tareas siguientes:</span><span class="sxs-lookup"><span data-stu-id="79644-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="79644-104">Purgar el contenido del directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="79644-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="79644-105">Convertir el lenguaje TypeScript en JavaScript, proceso conocido como *transpilación*.</span><span class="sxs-lookup"><span data-stu-id="79644-105">Converted the TypeScript to JavaScript in a process known as *transpilation*.</span></span>
* <span data-ttu-id="79644-106">Alterar el código JavaScript generado para reducir el tamaño del archivo, proceso conocido como *minificación*.</span><span class="sxs-lookup"><span data-stu-id="79644-106">Mangled the generated JavaScript to reduce file size in a process known as *minification*.</span></span>
* <span data-ttu-id="79644-107">Copiar los archivos JavaScript, CSS y HTML procesados desde *src* en el directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="79644-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="79644-108">Insertar los elementos siguientes en el archivo *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="79644-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="79644-109">Etiqueta `<link>`, que hace referencia al archivo *wwwroot/main.\<hash\>.css*.</span><span class="sxs-lookup"><span data-stu-id="79644-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="79644-110">Esta etiqueta se coloca inmediatamente antes de la etiqueta `</head>` de cierre.</span><span class="sxs-lookup"><span data-stu-id="79644-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="79644-111">Etiqueta `<script>`, que hace referencia al archivo *wwwroot/main.\<hash\>.js* minificado.</span><span class="sxs-lookup"><span data-stu-id="79644-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="79644-112">Esta etiqueta se coloca inmediatamente antes de la etiqueta `</body>` de cierre.</span><span class="sxs-lookup"><span data-stu-id="79644-112">This tag is placed immediately before the closing `</body>` tag.</span></span>