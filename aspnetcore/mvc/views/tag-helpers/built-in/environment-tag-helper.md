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
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="6f749-103">Asistente de etiquetas de entorno en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f749-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="6f749-104">Por [Peter Kellner](https://peterkellner.net) y [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="6f749-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="6f749-105">La aplicación auxiliar de etiquetas de entorno representa condicionalmente el contenido incluido en función del [entorno de hospedaje](xref:fundamentals/environments)actual.</span><span class="sxs-lookup"><span data-stu-id="6f749-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="6f749-106">El único atributo del asistente de etiquetas de entorno, `names`, es una lista de nombres de entorno separados por comas.</span><span class="sxs-lookup"><span data-stu-id="6f749-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="6f749-107">Si alguno de los nombres de entorno proporcionados coincide con el entorno actual, se representa el contenido incluido.</span><span class="sxs-lookup"><span data-stu-id="6f749-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="6f749-108">Para obtener información general sobre asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="6f749-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="6f749-109">Atributos del asistente de etiquetas de entorno</span><span class="sxs-lookup"><span data-stu-id="6f749-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="6f749-110">nombres</span><span class="sxs-lookup"><span data-stu-id="6f749-110">names</span></span>

<span data-ttu-id="6f749-111">`names` acepta un solo nombre de entorno de hospedaje o una lista de nombres de entorno de hospedaje separados por comas que desencadenan la representación del contenido incluido.</span><span class="sxs-lookup"><span data-stu-id="6f749-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="6f749-112">Los valores de entorno se comparan con el valor actual devuelto por [IWebHostEnvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="6f749-112">Environment values are compared to the current value returned by [IWebHostEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="6f749-113">La comparación ignora el uso de mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6f749-113">The comparison ignores case.</span></span>

<span data-ttu-id="6f749-114">En este ejemplo se usa un asistente de etiquetas de entorno.</span><span class="sxs-lookup"><span data-stu-id="6f749-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="6f749-115">El contenido se representa si el entorno de hospedaje es de almacenamiento provisional o de producción:</span><span class="sxs-lookup"><span data-stu-id="6f749-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="6f749-116">Atributos include y exclude</span><span class="sxs-lookup"><span data-stu-id="6f749-116">include and exclude attributes</span></span>

<span data-ttu-id="6f749-117">`include`& `exclude` los atributos controlan la representación del contenido incluido en función de los nombres de entorno de hospedaje incluidos o excluidos.</span><span class="sxs-lookup"><span data-stu-id="6f749-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="6f749-118">include</span><span class="sxs-lookup"><span data-stu-id="6f749-118">include</span></span>

<span data-ttu-id="6f749-119">La propiedad `include` exhibe un comportamiento similar al atributo `names`.</span><span class="sxs-lookup"><span data-stu-id="6f749-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="6f749-120">Un entorno incluido en el `include` valor del atributo debe coincidir con el entorno de hospedaje de la aplicación ([IWebHostEnvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) para representar el contenido de la `<environment>` etiqueta.</span><span class="sxs-lookup"><span data-stu-id="6f749-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IWebHostEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="6f749-121">exclude</span><span class="sxs-lookup"><span data-stu-id="6f749-121">exclude</span></span>

<span data-ttu-id="6f749-122">A diferencia del atributo `include`, el contenido de la etiqueta `<environment>` se representa cuando el entorno de hospedaje no coincide con un entorno que se muestra en el valor de atributo `exclude`.</span><span class="sxs-lookup"><span data-stu-id="6f749-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>IWebHostEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6f749-123">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6f749-123">Additional resources</span></span>

* <xref:fundamentals/environments>
