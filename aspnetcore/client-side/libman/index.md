---
title: Adquisición de bibliotecas del lado cliente en ASP.NET Core con LibMan
author: scottaddie
description: Obtenga información sobre cómo instalar los recursos de bibliotecas del lado cliente en un proyecto de ASP.NET Core mediante Library Manager (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
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
uid: client-side/libman/index
ms.openlocfilehash: 8458f2fe1cdc139a3f22c7d73675301d89ad368e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625640"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>Adquisición de bibliotecas del lado cliente en ASP.NET Core con LibMan

Por [Scott Addie](https://twitter.com/Scott_Addie)

Library Manager (LibMan) es una herramienta ligera para la adquisición de bibliotecas del lado cliente. Asimismo, LibMan permite descargar bibliotecas y marcos populares de sistemas de archivos o [redes de entrega de contenido (CDN)](https://wikipedia.org/wiki/Content_delivery_network). Entre las CDN admitidas se incluyen [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/) y [unpkg](https://unpkg.com/#/). Los archivos de biblioteca seleccionados se capturan y se colocan en la ubicación adecuada dentro del proyecto de ASP.NET Core.

## <a name="libman-use-cases"></a>Casos de uso de LibMan

LibMan ofrece las ventajas siguientes:

* Solo se descargan los archivos de biblioteca necesarios.
* No es necesario usar otras herramientas como [Node.js](https://nodejs.org), [npm](https://www.npmjs.com) o [WebPack](https://webpack.js.org) para adquirir un subconjunto de archivos de una biblioteca.
* Los archivos se pueden colocar en una ubicación específica sin tener que usar tareas de compilación ni copiar archivos manualmente.

Para más información sobre las ventajas de LibMan, consulte [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s) (Desarrollo web de front-end moderno en Visual Studio 2017: segmento LibMan)

LibMan no es un sistema de administración de paquetes. Si ya usa un administrador de paquetes, por ejemplo, npm o [yarn](https://yarnpkg.com), le recomendamos que sigua haciéndolo. LibMan no se ha desarrollado para reemplazar dichas herramientas.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Repositorio de LibMan en GitHub](https://github.com/aspnet/LibraryManager)
