---
title: Uso de analizadores de API web
author: pranavkm
description: Obtenga información sobre el paquete de analizadores de la API web de ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
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
uid: web-api/advanced/analyzers
ms.openlocfilehash: 1bc76f3965009dbdd66c58b197f4c12bd44faf18
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633037"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="e94c0-103">Uso de analizadores de API web</span><span class="sxs-lookup"><span data-stu-id="e94c0-103">Use web API analyzers</span></span>

<span data-ttu-id="e94c0-104">ASP.NET Core 2.2 y versiones posteriores proporcionan un paquete de analizadores de MVC diseñado para su uso con proyectos de API web.</span><span class="sxs-lookup"><span data-stu-id="e94c0-104">ASP.NET Core 2.2 and later provides an MVC analyzers package intended for use with web API projects.</span></span> <span data-ttu-id="e94c0-105">Los analizadores funcionan con los controladores anotados con <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> y se compilan con [convenciones de la API web](xref:web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="e94c0-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [web API conventions](xref:web-api/advanced/conventions).</span></span>

<span data-ttu-id="e94c0-106">El paquete de analizadores le informa de cualquier acción de controlador que:</span><span class="sxs-lookup"><span data-stu-id="e94c0-106">The analyzers package notifies you of any controller action that:</span></span>

* <span data-ttu-id="e94c0-107">Devuelve un código de estado no declarado.</span><span class="sxs-lookup"><span data-stu-id="e94c0-107">Returns an undeclared status code.</span></span>
* <span data-ttu-id="e94c0-108">Devuelve un resultado correcto no declarado.</span><span class="sxs-lookup"><span data-stu-id="e94c0-108">Returns an undeclared success result.</span></span>
* <span data-ttu-id="e94c0-109">Documenta un código de estado que no se devuelve.</span><span class="sxs-lookup"><span data-stu-id="e94c0-109">Documents a status code that isn't returned.</span></span>
* <span data-ttu-id="e94c0-110">Incluye una comprobación de validación del modelo explícita.</span><span class="sxs-lookup"><span data-stu-id="e94c0-110">Includes an explicit model validation check.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a><span data-ttu-id="e94c0-111">Referencia al paquete del analizador</span><span class="sxs-lookup"><span data-stu-id="e94c0-111">Reference the analyzer package</span></span>

<span data-ttu-id="e94c0-112">En ASP.NET Core 3.0 o versiones posteriores, los analizadores se incluyen en el SDK de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e94c0-112">In ASP.NET Core 3.0 or later, the analyzers are included in the .NET Core SDK.</span></span> <span data-ttu-id="e94c0-113">Para habilitar el analizador en el proyecto, incluya la propiedad `IncludeOpenAPIAnalyzers` en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="e94c0-113">To enable the analyzer in your project, include the `IncludeOpenAPIAnalyzers` property in the project file:</span></span>

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a><span data-ttu-id="e94c0-114">Instalación del paquete</span><span class="sxs-lookup"><span data-stu-id="e94c0-114">Package installation</span></span>

<span data-ttu-id="e94c0-115">Instale el paquete de NuGet [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) con uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e94c0-115">Install the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package with one of the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="e94c0-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e94c0-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e94c0-117">En la ventana **Consola del Administrador de paquetes**:</span><span class="sxs-lookup"><span data-stu-id="e94c0-117">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="e94c0-118">Vaya a **Ver** > **otra** > **consola del administrador de paquetes**de Windows.</span><span class="sxs-lookup"><span data-stu-id="e94c0-118">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="e94c0-119">Vaya al directorio en el que esté el archivo *ApiConventions.csproj*.</span><span class="sxs-lookup"><span data-stu-id="e94c0-119">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="e94c0-120">Ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="e94c0-120">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e94c0-121">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e94c0-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e94c0-122">Haga clic con el botón secundario en la carpeta *paquetes* en **Panel de solución** > **agregar paquetes..**..</span><span class="sxs-lookup"><span data-stu-id="e94c0-122">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="e94c0-123">Establezca la lista desplegable **origen** de la ventana **agregar paquetes** en "Nuget.org".</span><span class="sxs-lookup"><span data-stu-id="e94c0-123">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="e94c0-124">En el cuadro de búsqueda, escriba "Microsoft.AspNetCore.Mvc.Api.Analyzers".</span><span class="sxs-lookup"><span data-stu-id="e94c0-124">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="e94c0-125">Seleccione el paquete "Microsoft.AspNetCore.Mvc.Api.Analyzers" en el panel de resultados y haga clic en **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="e94c0-125">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="e94c0-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e94c0-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e94c0-127">Ejecute el siguiente comando en el **terminal integrado**:</span><span class="sxs-lookup"><span data-stu-id="e94c0-127">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[<span data-ttu-id="e94c0-128">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e94c0-128">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e94c0-129">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="e94c0-129">Run the following command:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a><span data-ttu-id="e94c0-130">Analizadores para las convenciones de API web</span><span class="sxs-lookup"><span data-stu-id="e94c0-130">Analyzers for web API conventions</span></span>

<span data-ttu-id="e94c0-131">Los documentos de Open API contienen los códigos de estado y los tipos de respuesta que puede devolver una acción.</span><span class="sxs-lookup"><span data-stu-id="e94c0-131">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="e94c0-132">En ASP.NET Core MVC, los atributos como <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> y <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> se usan para documentar una acción.</span><span class="sxs-lookup"><span data-stu-id="e94c0-132">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="e94c0-133"><xref:tutorials/web-api-help-pages-using-swagger> ofrece información más detallada sobre cómo documentar su API web.</span><span class="sxs-lookup"><span data-stu-id="e94c0-133"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your web API.</span></span>

<span data-ttu-id="e94c0-134">Uno de los analizadores del paquete inspecciona los controladores anotados con <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> e identifica las acciones que no documentan completamente sus respuestas.</span><span class="sxs-lookup"><span data-stu-id="e94c0-134">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="e94c0-135">Considere el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e94c0-135">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

<span data-ttu-id="e94c0-136">La acción anterior documenta el tipo de valor devuelto correcto HTTP 200, pero no documenta el código de estado de error HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="e94c0-136">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="e94c0-137">El analizador informa de la documentación que falta para el código de estado 404 de HTTP como una advertencia.</span><span class="sxs-lookup"><span data-stu-id="e94c0-137">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="e94c0-138">Se proporciona una opción para corregir el problema.</span><span class="sxs-lookup"><span data-stu-id="e94c0-138">An option to fix the problem is provided.</span></span>

![advertencia notificada por el analizador](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="e94c0-140">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e94c0-140">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
