---
title: Desarrollo de aplicaciones ASP.NET Core con OpenAPI
author: ryanbrandenburg
description: Muestra cómo usar la herramienta "Microsoft.dotnet-openapi" para agregar referencias a archivos OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 45921deb35452876b0a92a8731da68539a880c1d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626563"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="b6655-103">Desarrollo de aplicaciones ASP.NET Core con herramientas de OpenAPI</span><span class="sxs-lookup"><span data-stu-id="b6655-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="b6655-104">Por Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="b6655-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="b6655-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) es una [herramienta global de .NET Core](/dotnet/core/tools/global-tools) para administrar las referencias de [OpenAPI](https://github.com/OAI/OpenAPI-Specification) dentro de un proyecto.</span><span class="sxs-lookup"><span data-stu-id="b6655-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="b6655-106">Instalación</span><span class="sxs-lookup"><span data-stu-id="b6655-106">Installation</span></span>

<span data-ttu-id="b6655-107">Para instalar `Microsoft.dotnet-openapi`, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="b6655-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="b6655-108">Sumar</span><span class="sxs-lookup"><span data-stu-id="b6655-108">Add</span></span>

<span data-ttu-id="b6655-109">Al agregar una referencia OpenAPI mediante cualquiera de los comandos de esta página, se agrega un `<OpenApiReference />` elemento similar al siguiente al archivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="b6655-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="b6655-110">La referencia anterior es necesaria para que la aplicación llame al código de cliente generado.</span><span class="sxs-lookup"><span data-stu-id="b6655-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="b6655-111">Agregar archivo</span><span class="sxs-lookup"><span data-stu-id="b6655-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="b6655-112">Opciones</span><span class="sxs-lookup"><span data-stu-id="b6655-112">Options</span></span>

| <span data-ttu-id="b6655-113">Opción corta</span><span class="sxs-lookup"><span data-stu-id="b6655-113">Short option</span></span>| <span data-ttu-id="b6655-114">Opción larga</span><span class="sxs-lookup"><span data-stu-id="b6655-114">Long option</span></span>| <span data-ttu-id="b6655-115">Descripción</span><span class="sxs-lookup"><span data-stu-id="b6655-115">Description</span></span> | <span data-ttu-id="b6655-116">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b6655-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="b6655-117">-p</span><span class="sxs-lookup"><span data-stu-id="b6655-117">-p</span></span>|<span data-ttu-id="b6655-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b6655-118">--updateProject</span></span> | <span data-ttu-id="b6655-119">El proyecto sobre el que actuar.</span><span class="sxs-lookup"><span data-stu-id="b6655-119">The project to operate on.</span></span> |<span data-ttu-id="b6655-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="b6655-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="b6655-121">-c</span><span class="sxs-lookup"><span data-stu-id="b6655-121">-c</span></span>|<span data-ttu-id="b6655-122">--code-generator</span><span class="sxs-lookup"><span data-stu-id="b6655-122">--code-generator</span></span>| <span data-ttu-id="b6655-123">El generador de código que se va a aplicar a la referencia.</span><span class="sxs-lookup"><span data-stu-id="b6655-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="b6655-124">Las opciones son `NSwagCSharp` y `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="b6655-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="b6655-125">Si no se especifica `--code-generator`, la herramienta usa `NSwagCSharp` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b6655-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="b6655-126">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="b6655-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="b6655-127">-H</span><span class="sxs-lookup"><span data-stu-id="b6655-127">-h</span></span>|<span data-ttu-id="b6655-128">--help</span><span class="sxs-lookup"><span data-stu-id="b6655-128">--help</span></span>|<span data-ttu-id="b6655-129">Mostrar información de ayuda.</span><span class="sxs-lookup"><span data-stu-id="b6655-129">Show help information</span></span>|<span data-ttu-id="b6655-130">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="b6655-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="b6655-131">Argumentos</span><span class="sxs-lookup"><span data-stu-id="b6655-131">Arguments</span></span>

|  <span data-ttu-id="b6655-132">Argumento</span><span class="sxs-lookup"><span data-stu-id="b6655-132">Argument</span></span>  | <span data-ttu-id="b6655-133">Descripción</span><span class="sxs-lookup"><span data-stu-id="b6655-133">Description</span></span> | <span data-ttu-id="b6655-134">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b6655-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="b6655-135">source-file</span><span class="sxs-lookup"><span data-stu-id="b6655-135">source-file</span></span> | <span data-ttu-id="b6655-136">El origen a partir del cual se va a crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="b6655-136">The source to create a reference from.</span></span> <span data-ttu-id="b6655-137">Debe ser un archivo de OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="b6655-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="b6655-138">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="b6655-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="b6655-139">Agregar dirección URL</span><span class="sxs-lookup"><span data-stu-id="b6655-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="b6655-140">Opciones</span><span class="sxs-lookup"><span data-stu-id="b6655-140">Options</span></span>

| <span data-ttu-id="b6655-141">Opción corta</span><span class="sxs-lookup"><span data-stu-id="b6655-141">Short option</span></span>| <span data-ttu-id="b6655-142">Opción larga</span><span class="sxs-lookup"><span data-stu-id="b6655-142">Long option</span></span>| <span data-ttu-id="b6655-143">Descripción</span><span class="sxs-lookup"><span data-stu-id="b6655-143">Description</span></span> | <span data-ttu-id="b6655-144">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b6655-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="b6655-145">-p</span><span class="sxs-lookup"><span data-stu-id="b6655-145">-p</span></span>|<span data-ttu-id="b6655-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b6655-146">--updateProject</span></span> | <span data-ttu-id="b6655-147">El proyecto sobre el que actuar.</span><span class="sxs-lookup"><span data-stu-id="b6655-147">The project to operate on.</span></span> |<span data-ttu-id="b6655-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b6655-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="b6655-149">-o</span><span class="sxs-lookup"><span data-stu-id="b6655-149">-o</span></span>|<span data-ttu-id="b6655-150">--output-file</span><span class="sxs-lookup"><span data-stu-id="b6655-150">--output-file</span></span> | <span data-ttu-id="b6655-151">Dónde colocar la copia local del archivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="b6655-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="b6655-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="b6655-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="b6655-153">-c</span><span class="sxs-lookup"><span data-stu-id="b6655-153">-c</span></span>|<span data-ttu-id="b6655-154">--code-generator</span><span class="sxs-lookup"><span data-stu-id="b6655-154">--code-generator</span></span>| <span data-ttu-id="b6655-155">El generador de código que se va a aplicar a la referencia.</span><span class="sxs-lookup"><span data-stu-id="b6655-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="b6655-156">Las opciones son `NSwagCSharp` y `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="b6655-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="b6655-157">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="b6655-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="b6655-158">-H</span><span class="sxs-lookup"><span data-stu-id="b6655-158">-h</span></span>|<span data-ttu-id="b6655-159">--help</span><span class="sxs-lookup"><span data-stu-id="b6655-159">--help</span></span>|<span data-ttu-id="b6655-160">Mostrar información de ayuda.</span><span class="sxs-lookup"><span data-stu-id="b6655-160">Show help information</span></span>|<span data-ttu-id="b6655-161">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="b6655-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="b6655-162">Argumentos</span><span class="sxs-lookup"><span data-stu-id="b6655-162">Arguments</span></span>

|  <span data-ttu-id="b6655-163">Argumento</span><span class="sxs-lookup"><span data-stu-id="b6655-163">Argument</span></span>  | <span data-ttu-id="b6655-164">Descripción</span><span class="sxs-lookup"><span data-stu-id="b6655-164">Description</span></span> | <span data-ttu-id="b6655-165">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b6655-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="b6655-166">source-URL</span><span class="sxs-lookup"><span data-stu-id="b6655-166">source-URL</span></span> | <span data-ttu-id="b6655-167">El origen a partir del cual se va a crear una referencia.</span><span class="sxs-lookup"><span data-stu-id="b6655-167">The source to create a reference from.</span></span> <span data-ttu-id="b6655-168">Debe ser una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="b6655-168">Must be a URL.</span></span> |<span data-ttu-id="b6655-169">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b6655-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="b6655-170">Remove</span><span class="sxs-lookup"><span data-stu-id="b6655-170">Remove</span></span>

<span data-ttu-id="b6655-171">Quita la referencia de OpenAPI que coincide con el nombre de archivo dado del archivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="b6655-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="b6655-172">Cuando la referencia de OpenAPI se quita, no se generarán los clientes.</span><span class="sxs-lookup"><span data-stu-id="b6655-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="b6655-173">Los archivos *.json* y *.yaml* locales se eliminan.</span><span class="sxs-lookup"><span data-stu-id="b6655-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="b6655-174">Opciones</span><span class="sxs-lookup"><span data-stu-id="b6655-174">Options</span></span>

| <span data-ttu-id="b6655-175">Opción corta</span><span class="sxs-lookup"><span data-stu-id="b6655-175">Short option</span></span>| <span data-ttu-id="b6655-176">Opción larga</span><span class="sxs-lookup"><span data-stu-id="b6655-176">Long option</span></span>| <span data-ttu-id="b6655-177">Descripción</span><span class="sxs-lookup"><span data-stu-id="b6655-177">Description</span></span>| <span data-ttu-id="b6655-178">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b6655-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="b6655-179">-p</span><span class="sxs-lookup"><span data-stu-id="b6655-179">-p</span></span>|<span data-ttu-id="b6655-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b6655-180">--updateProject</span></span> | <span data-ttu-id="b6655-181">El proyecto sobre el que actuar.</span><span class="sxs-lookup"><span data-stu-id="b6655-181">The project to operate on.</span></span> |<span data-ttu-id="b6655-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="b6655-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="b6655-183">-H</span><span class="sxs-lookup"><span data-stu-id="b6655-183">-h</span></span>|<span data-ttu-id="b6655-184">--help</span><span class="sxs-lookup"><span data-stu-id="b6655-184">--help</span></span>|<span data-ttu-id="b6655-185">Mostrar información de ayuda.</span><span class="sxs-lookup"><span data-stu-id="b6655-185">Show help information</span></span>|<span data-ttu-id="b6655-186">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="b6655-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="b6655-187">Argumentos</span><span class="sxs-lookup"><span data-stu-id="b6655-187">Arguments</span></span>

|  <span data-ttu-id="b6655-188">Argumento</span><span class="sxs-lookup"><span data-stu-id="b6655-188">Argument</span></span>  | <span data-ttu-id="b6655-189">Descripción</span><span class="sxs-lookup"><span data-stu-id="b6655-189">Description</span></span>| <span data-ttu-id="b6655-190">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b6655-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="b6655-191">source-file</span><span class="sxs-lookup"><span data-stu-id="b6655-191">source-file</span></span> | <span data-ttu-id="b6655-192">Origen al que se va a quitar la referencia.</span><span class="sxs-lookup"><span data-stu-id="b6655-192">The source to remove the reference to.</span></span> |<span data-ttu-id="b6655-193">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="b6655-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="b6655-194">Actualizar</span><span class="sxs-lookup"><span data-stu-id="b6655-194">Refresh</span></span>

<span data-ttu-id="b6655-195">Actualiza la versión local de un archivo que se descargó usando el contenido más reciente de la dirección URL de descarga.</span><span class="sxs-lookup"><span data-stu-id="b6655-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="b6655-196">Opciones</span><span class="sxs-lookup"><span data-stu-id="b6655-196">Options</span></span>

| <span data-ttu-id="b6655-197">Opción corta</span><span class="sxs-lookup"><span data-stu-id="b6655-197">Short option</span></span>| <span data-ttu-id="b6655-198">Opción larga</span><span class="sxs-lookup"><span data-stu-id="b6655-198">Long option</span></span>| <span data-ttu-id="b6655-199">Descripción</span><span class="sxs-lookup"><span data-stu-id="b6655-199">Description</span></span> | <span data-ttu-id="b6655-200">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b6655-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="b6655-201">-p</span><span class="sxs-lookup"><span data-stu-id="b6655-201">-p</span></span>|<span data-ttu-id="b6655-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b6655-202">--updateProject</span></span> | <span data-ttu-id="b6655-203">El proyecto sobre el que actuar.</span><span class="sxs-lookup"><span data-stu-id="b6655-203">The project to operate on.</span></span> | <span data-ttu-id="b6655-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b6655-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="b6655-205">-H</span><span class="sxs-lookup"><span data-stu-id="b6655-205">-h</span></span>|<span data-ttu-id="b6655-206">--help</span><span class="sxs-lookup"><span data-stu-id="b6655-206">--help</span></span>|<span data-ttu-id="b6655-207">Mostrar información de ayuda.</span><span class="sxs-lookup"><span data-stu-id="b6655-207">Show help information</span></span>|<span data-ttu-id="b6655-208">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="b6655-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="b6655-209">Argumentos</span><span class="sxs-lookup"><span data-stu-id="b6655-209">Arguments</span></span>

|  <span data-ttu-id="b6655-210">Argumento</span><span class="sxs-lookup"><span data-stu-id="b6655-210">Argument</span></span>  | <span data-ttu-id="b6655-211">Descripción</span><span class="sxs-lookup"><span data-stu-id="b6655-211">Description</span></span> | <span data-ttu-id="b6655-212">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b6655-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="b6655-213">source-URL</span><span class="sxs-lookup"><span data-stu-id="b6655-213">source-URL</span></span> | <span data-ttu-id="b6655-214">Dirección URL desde la que se va a actualizar la referencia.</span><span class="sxs-lookup"><span data-stu-id="b6655-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="b6655-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b6655-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
