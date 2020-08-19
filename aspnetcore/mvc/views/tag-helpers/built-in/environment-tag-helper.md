---
title: Asistente de etiquetas de entorno en ASP.NET Core
author: pkellner
description: Asistente de etiquetas de entorno de ASP.NET Core definida con todas las propiedades
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: de37bf704ac377a15ef600b96ba78056c51ea7be
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633869"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="b062e-103">Asistente de etiquetas de entorno en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b062e-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="b062e-104">Por [Peter Kellner](https://peterkellner.net) y [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="b062e-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="b062e-105">La aplicación auxiliar de etiquetas de entorno representa condicionalmente el contenido incluido en función del [entorno de hospedaje](xref:fundamentals/environments)actual.</span><span class="sxs-lookup"><span data-stu-id="b062e-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="b062e-106">El único atributo del asistente de etiquetas de entorno, `names`, es una lista de nombres de entorno separados por comas.</span><span class="sxs-lookup"><span data-stu-id="b062e-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="b062e-107">Si alguno de los nombres de entorno proporcionados coincide con el entorno actual, se representa el contenido incluido.</span><span class="sxs-lookup"><span data-stu-id="b062e-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="b062e-108">Para obtener información general sobre asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="b062e-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="b062e-109">Atributos del asistente de etiquetas de entorno</span><span class="sxs-lookup"><span data-stu-id="b062e-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="b062e-110">nombres</span><span class="sxs-lookup"><span data-stu-id="b062e-110">names</span></span>

<span data-ttu-id="b062e-111">`names` acepta un solo nombre de entorno de hospedaje o una lista de nombres de entorno de hospedaje separados por comas que desencadenan la representación del contenido incluido.</span><span class="sxs-lookup"><span data-stu-id="b062e-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="b062e-112">Los valores de entorno se comparan con el valor actual devuelto por [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="b062e-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="b062e-113">La comparación ignora el uso de mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="b062e-113">The comparison ignores case.</span></span>

<span data-ttu-id="b062e-114">En este ejemplo se usa un asistente de etiquetas de entorno.</span><span class="sxs-lookup"><span data-stu-id="b062e-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="b062e-115">El contenido se representa si el entorno de hospedaje es de almacenamiento provisional o de producción:</span><span class="sxs-lookup"><span data-stu-id="b062e-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="b062e-116">Atributos include y exclude</span><span class="sxs-lookup"><span data-stu-id="b062e-116">include and exclude attributes</span></span>

<span data-ttu-id="b062e-117">`include`& `exclude` los atributos controlan la representación del contenido incluido en función de los nombres de entorno de hospedaje incluidos o excluidos.</span><span class="sxs-lookup"><span data-stu-id="b062e-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="b062e-118">include</span><span class="sxs-lookup"><span data-stu-id="b062e-118">include</span></span>

<span data-ttu-id="b062e-119">La propiedad `include` exhibe un comportamiento similar al atributo `names`.</span><span class="sxs-lookup"><span data-stu-id="b062e-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="b062e-120">Un entorno que se muestra en el valor de atributo `include` debe coincidir con el entorno de hospedaje de la aplicación ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) para representar el contenido de la etiqueta `<environment>`.</span><span class="sxs-lookup"><span data-stu-id="b062e-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="b062e-121">exclude</span><span class="sxs-lookup"><span data-stu-id="b062e-121">exclude</span></span>

<span data-ttu-id="b062e-122">A diferencia del atributo `include`, el contenido de la etiqueta `<environment>` se representa cuando el entorno de hospedaje no coincide con un entorno que se muestra en el valor de atributo `exclude`.</span><span class="sxs-lookup"><span data-stu-id="b062e-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b062e-123">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b062e-123">Additional resources</span></span>

* <xref:fundamentals/environments>
