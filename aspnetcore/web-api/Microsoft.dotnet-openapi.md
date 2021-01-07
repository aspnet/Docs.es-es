---
title: Desarrollo de aplicaciones ASP.NET Core con OpenAPI
author: ryanbrandenburg
description: Muestra cómo usar la herramienta "Microsoft.dotnet-openapi" para agregar referencias a archivos OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 5d9f1684aa333c38c73673138a703b04d318c6df
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972033"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="ff3fe-103">Desarrollo de aplicaciones ASP.NET Core con herramientas de OpenAPI</span><span class="sxs-lookup"><span data-stu-id="ff3fe-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="ff3fe-104">Por Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="ff3fe-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="ff3fe-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) es una [herramienta global de .NET Core](/dotnet/core/tools/global-tools) para administrar las referencias de [OpenAPI](https://github.com/OAI/OpenAPI-Specification) dentro de un proyecto.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="ff3fe-106">Instalación</span><span class="sxs-lookup"><span data-stu-id="ff3fe-106">Installation</span></span>

<span data-ttu-id="ff3fe-107">Para instalar `Microsoft.dotnet-openapi`, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="ff3fe-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="ff3fe-108">Sumar</span><span class="sxs-lookup"><span data-stu-id="ff3fe-108">Add</span></span>

<span data-ttu-id="ff3fe-109">Al agregar una referencia OpenAPI mediante cualquiera de los comandos de esta página, se agrega un `<OpenApiReference />` elemento similar al siguiente al archivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="ff3fe-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="ff3fe-110">La referencia anterior es necesaria para que la aplicación llame al código de cliente generado.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="ff3fe-111">Agregar archivo</span><span class="sxs-lookup"><span data-stu-id="ff3fe-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="ff3fe-112">Opciones</span><span class="sxs-lookup"><span data-stu-id="ff3fe-112">Options</span></span>

| <span data-ttu-id="ff3fe-113">Opción corta</span><span class="sxs-lookup"><span data-stu-id="ff3fe-113">Short option</span></span>| <span data-ttu-id="ff3fe-114">Opción larga</span><span class="sxs-lookup"><span data-stu-id="ff3fe-114">Long option</span></span>| <span data-ttu-id="ff3fe-115">Descripción</span><span class="sxs-lookup"><span data-stu-id="ff3fe-115">Description</span></span> | <span data-ttu-id="ff3fe-116">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="ff3fe-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="ff3fe-117">-p</span><span class="sxs-lookup"><span data-stu-id="ff3fe-117">-p</span></span>|<span data-ttu-id="ff3fe-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="ff3fe-118">--updateProject</span></span> | <span data-ttu-id="ff3fe-119">El proyecto sobre el que actuar.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-119">The project to operate on.</span></span> |<span data-ttu-id="ff3fe-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="ff3fe-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="ff3fe-121">-c</span><span class="sxs-lookup"><span data-stu-id="ff3fe-121">-c</span></span>|<span data-ttu-id="ff3fe-122">--code-generator</span><span class="sxs-lookup"><span data-stu-id="ff3fe-122">--code-generator</span></span>| <span data-ttu-id="ff3fe-123">El generador de código que se va a aplicar a la referencia.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="ff3fe-124">Las opciones son `NSwagCSharp` y `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="ff3fe-125">Si no se especifica `--code-generator`, la herramienta usa `NSwagCSharp` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="ff3fe-126">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="ff3fe-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="ff3fe-127">-H</span><span class="sxs-lookup"><span data-stu-id="ff3fe-127">-h</span></span>|<span data-ttu-id="ff3fe-128">--help</span><span class="sxs-lookup"><span data-stu-id="ff3fe-128">--help</span></span>|<span data-ttu-id="ff3fe-129">Mostrar información de ayuda.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-129">Show help information</span></span>|<span data-ttu-id="ff3fe-130">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="ff3fe-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="ff3fe-131">Argumentos</span><span class="sxs-lookup"><span data-stu-id="ff3fe-131">Arguments</span></span>

|  <span data-ttu-id="ff3fe-132">Argumento</span><span class="sxs-lookup"><span data-stu-id="ff3fe-132">Argument</span></span>  | <span data-ttu-id="ff3fe-133">Descripción</span><span class="sxs-lookup"><span data-stu-id="ff3fe-133">Description</span></span> | <span data-ttu-id="ff3fe-134">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="ff3fe-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="ff3fe-135">source-file</span><span class="sxs-lookup"><span data-stu-id="ff3fe-135">source-file</span></span> | <span data-ttu-id="ff3fe-136">El origen a partir del cual se va a crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-136">The source to create a reference from.</span></span> <span data-ttu-id="ff3fe-137">Debe ser un archivo de OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="ff3fe-138">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="ff3fe-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="ff3fe-139">Agregar dirección URL</span><span class="sxs-lookup"><span data-stu-id="ff3fe-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="ff3fe-140">Opciones</span><span class="sxs-lookup"><span data-stu-id="ff3fe-140">Options</span></span>

| <span data-ttu-id="ff3fe-141">Opción corta</span><span class="sxs-lookup"><span data-stu-id="ff3fe-141">Short option</span></span>| <span data-ttu-id="ff3fe-142">Opción larga</span><span class="sxs-lookup"><span data-stu-id="ff3fe-142">Long option</span></span>| <span data-ttu-id="ff3fe-143">Descripción</span><span class="sxs-lookup"><span data-stu-id="ff3fe-143">Description</span></span> | <span data-ttu-id="ff3fe-144">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="ff3fe-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="ff3fe-145">-p</span><span class="sxs-lookup"><span data-stu-id="ff3fe-145">-p</span></span>|<span data-ttu-id="ff3fe-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="ff3fe-146">--updateProject</span></span> | <span data-ttu-id="ff3fe-147">El proyecto sobre el que actuar.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-147">The project to operate on.</span></span> |<span data-ttu-id="ff3fe-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="ff3fe-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="ff3fe-149">-o</span><span class="sxs-lookup"><span data-stu-id="ff3fe-149">-o</span></span>|<span data-ttu-id="ff3fe-150">--output-file</span><span class="sxs-lookup"><span data-stu-id="ff3fe-150">--output-file</span></span> | <span data-ttu-id="ff3fe-151">Dónde colocar la copia local del archivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="ff3fe-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="ff3fe-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="ff3fe-153">-c</span><span class="sxs-lookup"><span data-stu-id="ff3fe-153">-c</span></span>|<span data-ttu-id="ff3fe-154">--code-generator</span><span class="sxs-lookup"><span data-stu-id="ff3fe-154">--code-generator</span></span>| <span data-ttu-id="ff3fe-155">El generador de código que se va a aplicar a la referencia.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="ff3fe-156">Las opciones son `NSwagCSharp` y `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="ff3fe-157">Agregar dirección URL de dotnet openapi `https://contoso.com/openapi.json` --code-generator</span><span class="sxs-lookup"><span data-stu-id="ff3fe-157">dotnet openapi add url `https://contoso.com/openapi.json` --code-generator</span></span>
| <span data-ttu-id="ff3fe-158">-H</span><span class="sxs-lookup"><span data-stu-id="ff3fe-158">-h</span></span>|<span data-ttu-id="ff3fe-159">--help</span><span class="sxs-lookup"><span data-stu-id="ff3fe-159">--help</span></span>|<span data-ttu-id="ff3fe-160">Mostrar información de ayuda.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-160">Show help information</span></span>|<span data-ttu-id="ff3fe-161">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="ff3fe-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="ff3fe-162">Argumentos</span><span class="sxs-lookup"><span data-stu-id="ff3fe-162">Arguments</span></span>

|  <span data-ttu-id="ff3fe-163">Argumento</span><span class="sxs-lookup"><span data-stu-id="ff3fe-163">Argument</span></span>  | <span data-ttu-id="ff3fe-164">Descripción</span><span class="sxs-lookup"><span data-stu-id="ff3fe-164">Description</span></span> | <span data-ttu-id="ff3fe-165">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="ff3fe-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="ff3fe-166">source-URL</span><span class="sxs-lookup"><span data-stu-id="ff3fe-166">source-URL</span></span> | <span data-ttu-id="ff3fe-167">El origen a partir del cual se va a crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-167">The source to create a reference from.</span></span> <span data-ttu-id="ff3fe-168">Debe ser una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-168">Must be a URL.</span></span> |<span data-ttu-id="ff3fe-169">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="ff3fe-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="ff3fe-170">Quitar</span><span class="sxs-lookup"><span data-stu-id="ff3fe-170">Remove</span></span>

<span data-ttu-id="ff3fe-171">Quita la referencia de OpenAPI que coincide con el nombre de archivo dado del archivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="ff3fe-172">Cuando la referencia de OpenAPI se quita, no se generarán los clientes.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="ff3fe-173">Los archivos *.json* y *.yaml* locales se eliminan.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="ff3fe-174">Opciones</span><span class="sxs-lookup"><span data-stu-id="ff3fe-174">Options</span></span>

| <span data-ttu-id="ff3fe-175">Opción corta</span><span class="sxs-lookup"><span data-stu-id="ff3fe-175">Short option</span></span>| <span data-ttu-id="ff3fe-176">Opción larga</span><span class="sxs-lookup"><span data-stu-id="ff3fe-176">Long option</span></span>| <span data-ttu-id="ff3fe-177">Descripción</span><span class="sxs-lookup"><span data-stu-id="ff3fe-177">Description</span></span>| <span data-ttu-id="ff3fe-178">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="ff3fe-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="ff3fe-179">-p</span><span class="sxs-lookup"><span data-stu-id="ff3fe-179">-p</span></span>|<span data-ttu-id="ff3fe-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="ff3fe-180">--updateProject</span></span> | <span data-ttu-id="ff3fe-181">El proyecto sobre el que actuar.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-181">The project to operate on.</span></span> |<span data-ttu-id="ff3fe-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="ff3fe-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="ff3fe-183">-H</span><span class="sxs-lookup"><span data-stu-id="ff3fe-183">-h</span></span>|<span data-ttu-id="ff3fe-184">--help</span><span class="sxs-lookup"><span data-stu-id="ff3fe-184">--help</span></span>|<span data-ttu-id="ff3fe-185">Mostrar información de ayuda.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-185">Show help information</span></span>|<span data-ttu-id="ff3fe-186">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="ff3fe-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="ff3fe-187">Argumentos</span><span class="sxs-lookup"><span data-stu-id="ff3fe-187">Arguments</span></span>

|  <span data-ttu-id="ff3fe-188">Argumento</span><span class="sxs-lookup"><span data-stu-id="ff3fe-188">Argument</span></span>  | <span data-ttu-id="ff3fe-189">Descripción</span><span class="sxs-lookup"><span data-stu-id="ff3fe-189">Description</span></span>| <span data-ttu-id="ff3fe-190">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="ff3fe-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="ff3fe-191">source-file</span><span class="sxs-lookup"><span data-stu-id="ff3fe-191">source-file</span></span> | <span data-ttu-id="ff3fe-192">Origen al que se va a quitar la referencia.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-192">The source to remove the reference to.</span></span> |<span data-ttu-id="ff3fe-193">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="ff3fe-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="ff3fe-194">Actualizar</span><span class="sxs-lookup"><span data-stu-id="ff3fe-194">Refresh</span></span>

<span data-ttu-id="ff3fe-195">Actualiza la versión local de un archivo que se descargó usando el contenido más reciente de la dirección URL de descarga.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="ff3fe-196">Opciones</span><span class="sxs-lookup"><span data-stu-id="ff3fe-196">Options</span></span>

| <span data-ttu-id="ff3fe-197">Opción corta</span><span class="sxs-lookup"><span data-stu-id="ff3fe-197">Short option</span></span>| <span data-ttu-id="ff3fe-198">Opción larga</span><span class="sxs-lookup"><span data-stu-id="ff3fe-198">Long option</span></span>| <span data-ttu-id="ff3fe-199">Descripción</span><span class="sxs-lookup"><span data-stu-id="ff3fe-199">Description</span></span> | <span data-ttu-id="ff3fe-200">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="ff3fe-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="ff3fe-201">-p</span><span class="sxs-lookup"><span data-stu-id="ff3fe-201">-p</span></span>|<span data-ttu-id="ff3fe-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="ff3fe-202">--updateProject</span></span> | <span data-ttu-id="ff3fe-203">El proyecto sobre el que actuar.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-203">The project to operate on.</span></span> | <span data-ttu-id="ff3fe-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="ff3fe-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="ff3fe-205">-H</span><span class="sxs-lookup"><span data-stu-id="ff3fe-205">-h</span></span>|<span data-ttu-id="ff3fe-206">--help</span><span class="sxs-lookup"><span data-stu-id="ff3fe-206">--help</span></span>|<span data-ttu-id="ff3fe-207">Mostrar información de ayuda.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-207">Show help information</span></span>|<span data-ttu-id="ff3fe-208">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="ff3fe-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="ff3fe-209">Argumentos</span><span class="sxs-lookup"><span data-stu-id="ff3fe-209">Arguments</span></span>

|  <span data-ttu-id="ff3fe-210">Argumento</span><span class="sxs-lookup"><span data-stu-id="ff3fe-210">Argument</span></span>  | <span data-ttu-id="ff3fe-211">Descripción</span><span class="sxs-lookup"><span data-stu-id="ff3fe-211">Description</span></span> | <span data-ttu-id="ff3fe-212">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="ff3fe-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="ff3fe-213">source-URL</span><span class="sxs-lookup"><span data-stu-id="ff3fe-213">source-URL</span></span> | <span data-ttu-id="ff3fe-214">Dirección URL desde la que se va a actualizar la referencia.</span><span class="sxs-lookup"><span data-stu-id="ff3fe-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="ff3fe-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="ff3fe-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
