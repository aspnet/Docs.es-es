---
title: Comando dotnet aspnet-codegenerator
author: rick-anderson
description: El comando dotnet aspnet-codegenerator aplica scaffolding a los proyectos de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: af151b65d3fefb5863529de1cd97c6315f11ca82
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444128"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="7f0c4-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="7f0c4-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="7f0c4-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7f0c4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7f0c4-105">`dotnet aspnet-codegenerator`: ejecuta el motor de scaffolding de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="7f0c4-106">`dotnet aspnet-codegenerator` solo es necesario para aplicar scaffolding desde la línea de comandos, no es necesario para usar la técnica de scaffolding con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="7f0c4-107">Este artículo se aplica al [SDK de .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="7f0c4-108">Instalación de aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="7f0c4-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="7f0c4-109">`dotnet-aspnet-codegenerator` es una [herramienta global](/dotnet/core/tools/global-tools) que debe estar instalada.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="7f0c4-110">El comando siguiente instala la versión estable más reciente de la herramienta `dotnet-aspnet-codegenerator`:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="7f0c4-111">El comando siguiente actualiza `dotnet-aspnet-codegenerator` a la versión estable más reciente disponible desde los SDK instalados de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="7f0c4-112">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="7f0c4-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="7f0c4-113">Descripción</span><span class="sxs-lookup"><span data-stu-id="7f0c4-113">Description</span></span>

<span data-ttu-id="7f0c4-114">El comando `dotnet aspnet-codegenerator` global ejecuta el generador de código de ASP.NET Core y el motor de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="7f0c4-115">Argumentos</span><span class="sxs-lookup"><span data-stu-id="7f0c4-115">Arguments</span></span>

`generator`

<span data-ttu-id="7f0c4-116">El generador de código que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-116">The code generator to run.</span></span> <span data-ttu-id="7f0c4-117">Estos generadores están disponibles:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-117">The following generators are available:</span></span>

| <span data-ttu-id="7f0c4-118">Generator</span><span class="sxs-lookup"><span data-stu-id="7f0c4-118">Generator</span></span>  | <span data-ttu-id="7f0c4-119">Operación</span><span class="sxs-lookup"><span data-stu-id="7f0c4-119">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="7f0c4-120">área</span><span class="sxs-lookup"><span data-stu-id="7f0c4-120">area</span></span>       | [<span data-ttu-id="7f0c4-121">Aplica scaffolding a un área</span><span class="sxs-lookup"><span data-stu-id="7f0c4-121">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="7f0c4-122">controlador</span><span class="sxs-lookup"><span data-stu-id="7f0c4-122">controller</span></span> | [<span data-ttu-id="7f0c4-123">Aplica scaffolding a un controlador</span><span class="sxs-lookup"><span data-stu-id="7f0c4-123">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="7f0c4-124">identidad</span><span class="sxs-lookup"><span data-stu-id="7f0c4-124">identity</span></span>   | [<span data-ttu-id="7f0c4-125">Aplica scaffolding a Identity</span><span class="sxs-lookup"><span data-stu-id="7f0c4-125">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="7f0c4-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="7f0c4-126">razorpage</span></span>  | [<span data-ttu-id="7f0c4-127">Aplica scaffolding a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="7f0c4-127">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="7f0c4-128">vista</span><span class="sxs-lookup"><span data-stu-id="7f0c4-128">view</span></span>       | [<span data-ttu-id="7f0c4-129">Aplica scaffolding a una vista</span><span class="sxs-lookup"><span data-stu-id="7f0c4-129">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="7f0c4-130">Opciones</span><span class="sxs-lookup"><span data-stu-id="7f0c4-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="7f0c4-131">Especifica el directorio del paquete de NuGet.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="7f0c4-132">Define la configuración de compilación.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-132">Defines the build configuration.</span></span> <span data-ttu-id="7f0c4-133">El valor predeterminado es `Debug`.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="7f0c4-134">Establece como destino el [marco](/dotnet/standard/frameworks) que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="7f0c4-135">Por ejemplo: `net46`.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="7f0c4-136">La ruta de acceso de la base de compilación.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="7f0c4-137">Imprime una corta ayuda para el comando.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="7f0c4-138">No compila el proyecto antes de ejecutarlo.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-138">Doesn't build the project before running.</span></span> <span data-ttu-id="7f0c4-139">También establece la marca `--no-restore` de forma implícita.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="7f0c4-140">Especifica la ruta de acceso del archivo del proyecto que se va a ejecutar (nombre de la carpeta o ruta de acceso completa).</span><span class="sxs-lookup"><span data-stu-id="7f0c4-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="7f0c4-141">Si no se especifica, se toma como predeterminado el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="7f0c4-142">Opciones del generador</span><span class="sxs-lookup"><span data-stu-id="7f0c4-142">Generator options</span></span>

<span data-ttu-id="7f0c4-143">En las secciones siguientes se detallan las opciones disponibles para los generadores compatibles:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="7f0c4-144">Área</span><span class="sxs-lookup"><span data-stu-id="7f0c4-144">Area</span></span>
* <span data-ttu-id="7f0c4-145">Controlador</span><span class="sxs-lookup"><span data-stu-id="7f0c4-145">Controller</span></span>
* Identity  
* <span data-ttu-id="7f0c4-146">Razorpage</span><span class="sxs-lookup"><span data-stu-id="7f0c4-146">Razorpage</span></span>
* <span data-ttu-id="7f0c4-147">Ver</span><span class="sxs-lookup"><span data-stu-id="7f0c4-147">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="7f0c4-148">Opciones del área</span><span class="sxs-lookup"><span data-stu-id="7f0c4-148">Area options</span></span>

<span data-ttu-id="7f0c4-149">Esta herramienta está diseñada para los proyectos web de ASP.NET Core con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-149">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="7f0c4-150">No está pensada para las aplicaciones de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-150">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="7f0c4-151">Uso: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="7f0c4-151">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="7f0c4-152">El comando anterior genera estas carpetas:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-152">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="7f0c4-153">*Áreas*</span><span class="sxs-lookup"><span data-stu-id="7f0c4-153">*Areas*</span></span>
  * <span data-ttu-id="7f0c4-154">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="7f0c4-154">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="7f0c4-155">*Controladores*</span><span class="sxs-lookup"><span data-stu-id="7f0c4-155">*Controllers*</span></span>
    * <span data-ttu-id="7f0c4-156">*Data*</span><span class="sxs-lookup"><span data-stu-id="7f0c4-156">*Data*</span></span>
    * <span data-ttu-id="7f0c4-157">*Models*</span><span class="sxs-lookup"><span data-stu-id="7f0c4-157">*Models*</span></span>
    * <span data-ttu-id="7f0c4-158">*Vistas*</span><span class="sxs-lookup"><span data-stu-id="7f0c4-158">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="7f0c4-159">Opciones del controlador</span><span class="sxs-lookup"><span data-stu-id="7f0c4-159">Controller options</span></span>

<span data-ttu-id="7f0c4-160">En la tabla siguiente se indican las opciones para `aspnet-codegenerator` `controller` y `razorpage`:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-160">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="7f0c4-161">En la tabla siguiente se muestran las opciones únicas para `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-161">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="7f0c4-162">Opción</span><span class="sxs-lookup"><span data-stu-id="7f0c4-162">Option</span></span>                         | <span data-ttu-id="7f0c4-163">Descripción</span><span class="sxs-lookup"><span data-stu-id="7f0c4-163">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="7f0c4-164">--controllerName o -name</span><span class="sxs-lookup"><span data-stu-id="7f0c4-164">--controllerName or -name</span></span>      | <span data-ttu-id="7f0c4-165">El nombre del controlador.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-165">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="7f0c4-166">--useAsyncActions o -async</span><span class="sxs-lookup"><span data-stu-id="7f0c4-166">--useAsyncActions or -async</span></span>    | <span data-ttu-id="7f0c4-167">Genera acciones asincrónicas del controlador.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-167">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="7f0c4-168">--noViews o -nv</span><span class="sxs-lookup"><span data-stu-id="7f0c4-168">--noViews or -nv</span></span>               | <span data-ttu-id="7f0c4-169">**No** genera vistas.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-169">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="7f0c4-170">--restWithNoViews o -api</span><span class="sxs-lookup"><span data-stu-id="7f0c4-170">--restWithNoViews or -api</span></span>      | <span data-ttu-id="7f0c4-171">Genera un controlador con una API de estilo REST.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-171">Generate a Controller with REST style API.</span></span> <span data-ttu-id="7f0c4-172">Se supone `noViews` y todas las opciones relacionadas con la vista se omiten.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-172">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="7f0c4-173">--readWriteActions o -actions</span><span class="sxs-lookup"><span data-stu-id="7f0c4-173">--readWriteActions or -actions</span></span> | <span data-ttu-id="7f0c4-174">Genera un controlador con acciones de lectura/escritura sin un modelo.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-174">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="7f0c4-175">Use el modificador `-h` para obtener ayuda sobre el comando `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-175">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="7f0c4-176">Consulte [Aplicar scaffolding al modelo de película](xref:tutorials/first-mvc-app/adding-model) para ver un ejemplo de `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-176">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="7f0c4-177">Razorpage</span><span class="sxs-lookup"><span data-stu-id="7f0c4-177">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="7f0c4-178">Es posible aplicar scaffolding a Razor Pages de manera individual si se especifica el nombre de la página nueva y la plantilla a usar.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-178">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="7f0c4-179">Las plantillas admitidas son:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-179">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="7f0c4-180">Por ejemplo, el comando siguiente usa la plantilla de edición para generar *MyEdit.cshtml* y *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-180">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="7f0c4-181">Por lo general, no se especifica la plantilla ni el nombre del archivo generado y se crean estas plantillas:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-181">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="7f0c4-182">En la tabla siguiente se indican las opciones para `aspnet-codegenerator` `razorpage` y `controller`:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-182">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="7f0c4-183">En la tabla siguiente se muestran las opciones únicas para `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-183">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="7f0c4-184">Opción</span><span class="sxs-lookup"><span data-stu-id="7f0c4-184">Option</span></span>                        | <span data-ttu-id="7f0c4-185">Descripción</span><span class="sxs-lookup"><span data-stu-id="7f0c4-185">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="7f0c4-186">--namespaceName o -namespace</span><span class="sxs-lookup"><span data-stu-id="7f0c4-186">--namespaceName or -namespace</span></span> | <span data-ttu-id="7f0c4-187">El nombre del espacio de nombres que se usará para la clase PageModel generada.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-187">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="7f0c4-188">--partialView o -partial</span><span class="sxs-lookup"><span data-stu-id="7f0c4-188">--partialView or -partial</span></span>     | <span data-ttu-id="7f0c4-189">Genera una vista parcial.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-189">Generate a partial view.</span></span> <span data-ttu-id="7f0c4-190">Si se especifica, las opciones de diseño -l y -udl se ignoran.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-190">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="7f0c4-191">--noPageModel o -npm</span><span class="sxs-lookup"><span data-stu-id="7f0c4-191">--noPageModel or -npm</span></span>         | <span data-ttu-id="7f0c4-192">Cambia a no generar una clase PageModel para una plantilla vacía.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-192">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="7f0c4-193">Use el modificador `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="7f0c4-193">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="7f0c4-194">Consulte [Aplicar scaffolding al modelo de película](xref:tutorials/razor-pages/model) para ver un ejemplo de `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="7f0c4-194">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="7f0c4-195">Vea [Scaffolding Identity](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="7f0c4-195">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
