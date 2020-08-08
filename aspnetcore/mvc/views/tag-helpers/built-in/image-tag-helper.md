---
title: Asistente de etiquetas de imagen en ASP.NET Core
author: pkellner
description: Muestra cómo trabajar con el asistente de etiquetas de imagen.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: a877078356b54a3d0bba6a1be24307e6e1c075c8
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018603"
---
# <a name="image-tag-helper-in-aspnet-core"></a><span data-ttu-id="bfa8a-103">Asistente de etiquetas de imagen en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bfa8a-103">Image Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="bfa8a-104">Por [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="bfa8a-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="bfa8a-105">El asistente de etiquetas de imagen mejora la etiqueta `<img>` para proporcionar un comportamiento de limpieza de caché para archivos de imagen estática.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-105">The Image Tag Helper enhances the `<img>` tag to provide cache-busting behavior for static image files.</span></span>

<span data-ttu-id="bfa8a-106">Una cadena de limpieza de memoria caché es un valor único que representa el valor hash del archivo de imagen estático anexados a la URL del activo.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-106">A cache-busting string is a unique value representing the hash of the static image file appended to the asset's URL.</span></span> <span data-ttu-id="bfa8a-107">La cadena única pide a los clientes (y algunos servidores proxy) que vuelva a cargar la imagen desde el servidor web del host y no desde la memoria caché del cliente.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-107">The unique string prompts clients (and some proxies) to reload the image from the host web server and not from the client's cache.</span></span>

<span data-ttu-id="bfa8a-108">Si el origen de la imagen (`src`) es un archivo estático en el servidor web del host:</span><span class="sxs-lookup"><span data-stu-id="bfa8a-108">If the image source (`src`) is a static file on the host web server:</span></span>

* <span data-ttu-id="bfa8a-109">Se anexa una cadena única de limpieza de caché como un parámetro de consulta al origen de la imagen.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-109">A unique cache-busting string is appended as a query parameter to the image source.</span></span>
* <span data-ttu-id="bfa8a-110">Si el archivo en el servidor web del host cambia, se genera una dirección URL de solicitud única que incluye el parámetro de solicitud actualizada.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-110">If the file on the host web server changes, a unique request URL is generated that includes the updated request parameter.</span></span>

<span data-ttu-id="bfa8a-111">Para obtener información general sobre asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-111">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="image-tag-helper-attributes"></a><span data-ttu-id="bfa8a-112">Atributos del asistente de etiquetas de imagen</span><span class="sxs-lookup"><span data-stu-id="bfa8a-112">Image Tag Helper Attributes</span></span>

### <a name="src"></a><span data-ttu-id="bfa8a-113">src</span><span class="sxs-lookup"><span data-stu-id="bfa8a-113">src</span></span>

<span data-ttu-id="bfa8a-114">Para activar el asistente de etiquetas de imagen, se necesita el atributo `src` en el elemento `<img>`.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-114">To activate the Image Tag Helper, the `src` attribute is required on the `<img>` element.</span></span>

<span data-ttu-id="bfa8a-115">El origen de la imagen (`src`) debe apuntar a un archivo estático físico en el servidor.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-115">The image source (`src`) must point to a physical static file on the server.</span></span> <span data-ttu-id="bfa8a-116">Si `src` es un identificador URI remoto, el parámetro de cadena de consulta de limpieza de caché no se genera.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-116">If the `src` is a remote URI, the cache-busting query string parameter isn't generated.</span></span>

### <a name="asp-append-version"></a><span data-ttu-id="bfa8a-117">asp-append-version</span><span class="sxs-lookup"><span data-stu-id="bfa8a-117">asp-append-version</span></span>

<span data-ttu-id="bfa8a-118">Cuando se especifica `asp-append-version` con un valor `true` junto con un atributo `src`, se invoca el asistente de etiquetas de imagen.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-118">When `asp-append-version` is specified with a `true` value along with a `src` attribute, the Image Tag Helper is invoked.</span></span>

<span data-ttu-id="bfa8a-119">En este ejemplo se usa un asistente de etiquetas de imagen:</span><span class="sxs-lookup"><span data-stu-id="bfa8a-119">The following example uses an Image Tag Helper:</span></span>

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

<span data-ttu-id="bfa8a-120">Si el archivo estático existe en el directorio */wwwroot/images/*, el código HTML generado es similar al siguiente (el valor hash será diferente):</span><span class="sxs-lookup"><span data-stu-id="bfa8a-120">If the static file exists in the directory */wwwroot/images/*, the generated HTML is similar to the following (the hash will be different):</span></span>

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

<span data-ttu-id="bfa8a-121">El valor asignado al parámetro `v` es el valor hash del archivo */wwwroot/images/* almacenado en disco.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-121">The value assigned to the parameter `v` is the hash value of the *asplogo.png* file on disk.</span></span> <span data-ttu-id="bfa8a-122">Si el servidor web no es capaz de tener acceso de lectura al archivo estático, no se agregará ningún parámetro `v` al atributo `src` en el marcado representado.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-122">If the web server is unable to obtain read access to the static file, no `v` parameter is added to the `src` attribute in the rendered markup.</span></span>

## <a name="hash-caching-behavior"></a><span data-ttu-id="bfa8a-123">Comportamiento de almacenamiento en caché de hash</span><span class="sxs-lookup"><span data-stu-id="bfa8a-123">Hash caching behavior</span></span>

<span data-ttu-id="bfa8a-124">El asistente de etiquetas de imagen usa el proveedor de caché en el servidor web local para almacenar el hash `Sha512` calculado de un archivo determinado.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-124">The Image Tag Helper uses the cache provider on the local web server to store the calculated `Sha512` hash of a given file.</span></span> <span data-ttu-id="bfa8a-125">Si se solicita el archivo varias veces, no se vuelven a calcular el hash.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-125">If the file is requested multiple times, the hash isn't recalculated.</span></span> <span data-ttu-id="bfa8a-126">La memoria caché queda invalidada por un monitor del archivo que se adjunta al archivo cuando se calcula el hash `Sha512` del archivo.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-126">The cache is invalidated by a file watcher that's attached to the file when the file's `Sha512` hash is calculated.</span></span> <span data-ttu-id="bfa8a-127">Cuando el archivo se cambia en el disco, se calcula un nuevo hash y se almacena en caché.</span><span class="sxs-lookup"><span data-stu-id="bfa8a-127">When the file changes on disk, a new hash is calculated and cached.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bfa8a-128">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bfa8a-128">Additional resources</span></span>

* <xref:performance/caching/memory>
