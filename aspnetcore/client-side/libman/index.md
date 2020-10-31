---
title: Adquisición de bibliotecas del lado cliente en ASP.NET Core con LibMan
author: scottaddie
description: Obtenga información sobre cómo instalar los recursos de bibliotecas del lado cliente en un proyecto de ASP.NET Core mediante Library Manager (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: client-side/libman/index
ms.openlocfilehash: 62b6859b0a8ad0f98a2684f21c0f68dbbd67c4c1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054663"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="01086-103">Adquisición de bibliotecas del lado cliente en ASP.NET Core con LibMan</span><span class="sxs-lookup"><span data-stu-id="01086-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="01086-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="01086-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="01086-105">Library Manager (LibMan) es una herramienta ligera para la adquisición de bibliotecas del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="01086-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="01086-106">Asimismo, LibMan permite descargar bibliotecas y marcos populares de sistemas de archivos o [redes de entrega de contenido (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span><span class="sxs-lookup"><span data-stu-id="01086-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="01086-107">Entre las CDN admitidas se incluyen [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/) y [unpkg](https://unpkg.com/#/).</span><span class="sxs-lookup"><span data-stu-id="01086-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="01086-108">Los archivos de biblioteca seleccionados se capturan y se colocan en la ubicación adecuada dentro del proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="01086-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="01086-109">Casos de uso de LibMan</span><span class="sxs-lookup"><span data-stu-id="01086-109">LibMan use cases</span></span>

<span data-ttu-id="01086-110">LibMan ofrece las ventajas siguientes:</span><span class="sxs-lookup"><span data-stu-id="01086-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="01086-111">Solo se descargan los archivos de biblioteca necesarios.</span><span class="sxs-lookup"><span data-stu-id="01086-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="01086-112">No es necesario usar otras herramientas como [Node.js](https://nodejs.org), [npm](https://www.npmjs.com) o [WebPack](https://webpack.js.org) para adquirir un subconjunto de archivos de una biblioteca.</span><span class="sxs-lookup"><span data-stu-id="01086-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="01086-113">Los archivos se pueden colocar en una ubicación específica sin tener que usar tareas de compilación ni copiar archivos manualmente.</span><span class="sxs-lookup"><span data-stu-id="01086-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="01086-114">Para más información sobre las ventajas de LibMan, consulte [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s) (Desarrollo web de front-end moderno en Visual Studio 2017: segmento LibMan)</span><span class="sxs-lookup"><span data-stu-id="01086-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="01086-115">LibMan no es un sistema de administración de paquetes.</span><span class="sxs-lookup"><span data-stu-id="01086-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="01086-116">Si ya usa un administrador de paquetes, por ejemplo, npm o [yarn](https://yarnpkg.com), le recomendamos que sigua haciéndolo.</span><span class="sxs-lookup"><span data-stu-id="01086-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="01086-117">LibMan no se ha desarrollado para reemplazar dichas herramientas.</span><span class="sxs-lookup"><span data-stu-id="01086-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01086-118">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="01086-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="01086-119">Repositorio de LibMan en GitHub</span><span class="sxs-lookup"><span data-stu-id="01086-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
