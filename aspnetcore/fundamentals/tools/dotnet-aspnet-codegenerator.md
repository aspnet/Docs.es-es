---
title: Comando dotnet aspnet-codegenerator
author: rick-anderson
description: El comando dotnet aspnet-codegenerator aplica scaffolding a los proyectos de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: d29535ea0b4197443fd884aaa6e5b4b763d04fc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920708"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="5d8bb-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="5d8bb-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="5d8bb-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5d8bb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5d8bb-105">`dotnet aspnet-codegenerator`: ejecuta el motor de scaffolding de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="5d8bb-106">`dotnet aspnet-codegenerator` solo es necesario para aplicar scaffolding desde la línea de comandos, no es necesario para usar la técnica de scaffolding con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

## <a name="install-and-update-aspnet-codegenerator"></a><span data-ttu-id="5d8bb-107">Instalación y actualización de aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="5d8bb-107">Install and update aspnet-codegenerator</span></span>

<span data-ttu-id="5d8bb-108">Instale el [SDK de .NET](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="5d8bb-108">Install the [.NET SDK](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="5d8bb-109">`dotnet-aspnet-codegenerator` es una [herramienta global](/dotnet/core/tools/global-tools) que debe estar instalada.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="5d8bb-110">El comando siguiente instala la versión estable más reciente de la herramienta `dotnet-aspnet-codegenerator`:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="5d8bb-111">El comando siguiente actualiza `dotnet-aspnet-codegenerator` a la versión estable más reciente disponible desde los SDK instalados de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a><span data-ttu-id="5d8bb-112">Desinstalación de aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="5d8bb-112">Uninstall aspnet-codegenerator</span></span>

<span data-ttu-id="5d8bb-113">Puede que sea necesario desinstalar `aspnet-codegenerator` para resolver problemas.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-113">It may be necessary to uninstall the `aspnet-codegenerator` to resolve problems.</span></span> <span data-ttu-id="5d8bb-114">Por ejemplo, si instaló una versión preliminar de `aspnet-codegenerator`, desinstálela antes de instalar la versión de publicación.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-114">For example, if you installed a preview version of `aspnet-codegenerator`, uninstall it before installing the released version.</span></span>

<span data-ttu-id="5d8bb-115">El comando siguiente desinstala la herramienta `dotnet-aspnet-codegenerator` e instala la versión estable más reciente:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-115">The following commands uninstall the `dotnet-aspnet-codegenerator` tool and installs the latest stable version:</span></span>

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="5d8bb-116">Sinopsis</span><span class="sxs-lookup"><span data-stu-id="5d8bb-116">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="5d8bb-117">Descripción</span><span class="sxs-lookup"><span data-stu-id="5d8bb-117">Description</span></span>

<span data-ttu-id="5d8bb-118">El comando `dotnet aspnet-codegenerator` global ejecuta el generador de código de ASP.NET Core y el motor de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-118">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="5d8bb-119">Argumentos</span><span class="sxs-lookup"><span data-stu-id="5d8bb-119">Arguments</span></span>

`generator`

<span data-ttu-id="5d8bb-120">El generador de código que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-120">The code generator to run.</span></span> <span data-ttu-id="5d8bb-121">Estos generadores están disponibles:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-121">The following generators are available:</span></span>

| <span data-ttu-id="5d8bb-122">Generator</span><span class="sxs-lookup"><span data-stu-id="5d8bb-122">Generator</span></span>  | <span data-ttu-id="5d8bb-123">Operación</span><span class="sxs-lookup"><span data-stu-id="5d8bb-123">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="5d8bb-124">área</span><span class="sxs-lookup"><span data-stu-id="5d8bb-124">area</span></span>       | [<span data-ttu-id="5d8bb-125">Aplica scaffolding a un área</span><span class="sxs-lookup"><span data-stu-id="5d8bb-125">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="5d8bb-126">controlador</span><span class="sxs-lookup"><span data-stu-id="5d8bb-126">controller</span></span> | [<span data-ttu-id="5d8bb-127">Aplica scaffolding a un controlador</span><span class="sxs-lookup"><span data-stu-id="5d8bb-127">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="5d8bb-128">identidad</span><span class="sxs-lookup"><span data-stu-id="5d8bb-128">identity</span></span>   | [<span data-ttu-id="5d8bb-129">Aplica scaffolding a Identity</span><span class="sxs-lookup"><span data-stu-id="5d8bb-129">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="5d8bb-130">razorpage</span><span class="sxs-lookup"><span data-stu-id="5d8bb-130">razorpage</span></span>  | [<span data-ttu-id="5d8bb-131">Aplica scaffolding a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5d8bb-131">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="5d8bb-132">vista</span><span class="sxs-lookup"><span data-stu-id="5d8bb-132">view</span></span>       | [<span data-ttu-id="5d8bb-133">Aplica scaffolding a una vista</span><span class="sxs-lookup"><span data-stu-id="5d8bb-133">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="5d8bb-134">Opciones</span><span class="sxs-lookup"><span data-stu-id="5d8bb-134">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="5d8bb-135">Especifica el directorio del paquete de NuGet.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-135">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="5d8bb-136">Define la configuración de compilación.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-136">Defines the build configuration.</span></span> <span data-ttu-id="5d8bb-137">El valor predeterminado es `Debug`.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-137">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="5d8bb-138">Establece como destino el [marco](/dotnet/standard/frameworks) que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-138">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="5d8bb-139">Por ejemplo: `net46`.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-139">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="5d8bb-140">La ruta de acceso de la base de compilación.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-140">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="5d8bb-141">Imprime una corta ayuda para el comando.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-141">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="5d8bb-142">No compila el proyecto antes de ejecutarlo.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-142">Doesn't build the project before running.</span></span> <span data-ttu-id="5d8bb-143">También establece la marca `--no-restore` de forma implícita.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-143">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="5d8bb-144">Especifica la ruta de acceso del archivo del proyecto que se va a ejecutar (nombre de la carpeta o ruta de acceso completa).</span><span class="sxs-lookup"><span data-stu-id="5d8bb-144">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="5d8bb-145">Si no se especifica, se toma como predeterminado el directorio actual.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-145">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="5d8bb-146">Opciones del generador</span><span class="sxs-lookup"><span data-stu-id="5d8bb-146">Generator options</span></span>

<span data-ttu-id="5d8bb-147">En las secciones siguientes se detallan las opciones disponibles para los generadores compatibles:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-147">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="5d8bb-148">Área</span><span class="sxs-lookup"><span data-stu-id="5d8bb-148">Area</span></span>
* <span data-ttu-id="5d8bb-149">Controlador</span><span class="sxs-lookup"><span data-stu-id="5d8bb-149">Controller</span></span>
* Identity  
* <span data-ttu-id="5d8bb-150">Razorpage</span><span class="sxs-lookup"><span data-stu-id="5d8bb-150">Razorpage</span></span>
* <span data-ttu-id="5d8bb-151">Ver</span><span class="sxs-lookup"><span data-stu-id="5d8bb-151">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="5d8bb-152">Opciones del área</span><span class="sxs-lookup"><span data-stu-id="5d8bb-152">Area options</span></span>

<span data-ttu-id="5d8bb-153">Esta herramienta está diseñada para los proyectos web de ASP.NET Core con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-153">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="5d8bb-154">No está pensada para las aplicaciones de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-154">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="5d8bb-155">Uso: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="5d8bb-155">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="5d8bb-156">El comando anterior genera estas carpetas:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-156">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="5d8bb-157">*Áreas*</span><span class="sxs-lookup"><span data-stu-id="5d8bb-157">*Areas*</span></span>
  * <span data-ttu-id="5d8bb-158">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="5d8bb-158">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="5d8bb-159">*Controladores*</span><span class="sxs-lookup"><span data-stu-id="5d8bb-159">*Controllers*</span></span>
    * <span data-ttu-id="5d8bb-160">*Data*</span><span class="sxs-lookup"><span data-stu-id="5d8bb-160">*Data*</span></span>
    * <span data-ttu-id="5d8bb-161">*Models*</span><span class="sxs-lookup"><span data-stu-id="5d8bb-161">*Models*</span></span>
    * <span data-ttu-id="5d8bb-162">*Vistas*</span><span class="sxs-lookup"><span data-stu-id="5d8bb-162">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="5d8bb-163">Opciones del controlador</span><span class="sxs-lookup"><span data-stu-id="5d8bb-163">Controller options</span></span>

<span data-ttu-id="5d8bb-164">En la tabla siguiente se indican las opciones para `aspnet-codegenerator` `controller` y `razorpage`:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-164">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="5d8bb-165">En la tabla siguiente se muestran las opciones únicas para `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-165">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="5d8bb-166">Opción</span><span class="sxs-lookup"><span data-stu-id="5d8bb-166">Option</span></span>                         | <span data-ttu-id="5d8bb-167">Descripción</span><span class="sxs-lookup"><span data-stu-id="5d8bb-167">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="5d8bb-168">--controllerName o -name</span><span class="sxs-lookup"><span data-stu-id="5d8bb-168">--controllerName or -name</span></span>      | <span data-ttu-id="5d8bb-169">El nombre del controlador.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-169">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="5d8bb-170">--useAsyncActions o -async</span><span class="sxs-lookup"><span data-stu-id="5d8bb-170">--useAsyncActions or -async</span></span>    | <span data-ttu-id="5d8bb-171">Genera acciones asincrónicas del controlador.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-171">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="5d8bb-172">--noViews o -nv</span><span class="sxs-lookup"><span data-stu-id="5d8bb-172">--noViews or -nv</span></span>               | <span data-ttu-id="5d8bb-173">**No** genera vistas.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-173">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="5d8bb-174">--restWithNoViews o -api</span><span class="sxs-lookup"><span data-stu-id="5d8bb-174">--restWithNoViews or -api</span></span>      | <span data-ttu-id="5d8bb-175">Genera un controlador con una API de estilo REST.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-175">Generate a Controller with REST style API.</span></span> <span data-ttu-id="5d8bb-176">Se supone `noViews` y todas las opciones relacionadas con la vista se omiten.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-176">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="5d8bb-177">--readWriteActions o -actions</span><span class="sxs-lookup"><span data-stu-id="5d8bb-177">--readWriteActions or -actions</span></span> | <span data-ttu-id="5d8bb-178">Genera un controlador con acciones de lectura/escritura sin un modelo.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-178">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="5d8bb-179">Use el modificador `-h` para obtener ayuda sobre el comando `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-179">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="5d8bb-180">Consulte [Aplicar scaffolding al modelo de película](xref:tutorials/first-mvc-app/adding-model) para ver un ejemplo de `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-180">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="5d8bb-181">Razorpage</span><span class="sxs-lookup"><span data-stu-id="5d8bb-181">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="5d8bb-182">Es posible aplicar scaffolding a Razor Pages de manera individual si se especifica el nombre de la página nueva y la plantilla a usar.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-182">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="5d8bb-183">Las plantillas admitidas son:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-183">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="5d8bb-184">Por ejemplo, el comando siguiente usa la plantilla de edición para generar *MyEdit.cshtml* y *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-184">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="5d8bb-185">Por lo general, no se especifica la plantilla ni el nombre del archivo generado y se crean estas plantillas:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-185">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="5d8bb-186">En la tabla siguiente se indican las opciones para `aspnet-codegenerator` `razorpage` y `controller`:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-186">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="5d8bb-187">En la tabla siguiente se muestran las opciones únicas para `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-187">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="5d8bb-188">Opción</span><span class="sxs-lookup"><span data-stu-id="5d8bb-188">Option</span></span>                        | <span data-ttu-id="5d8bb-189">Descripción</span><span class="sxs-lookup"><span data-stu-id="5d8bb-189">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="5d8bb-190">--namespaceName o -namespace</span><span class="sxs-lookup"><span data-stu-id="5d8bb-190">--namespaceName or -namespace</span></span> | <span data-ttu-id="5d8bb-191">El nombre del espacio de nombres que se usará para la clase PageModel generada.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-191">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="5d8bb-192">--partialView o -partial</span><span class="sxs-lookup"><span data-stu-id="5d8bb-192">--partialView or -partial</span></span>     | <span data-ttu-id="5d8bb-193">Genera una vista parcial.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-193">Generate a partial view.</span></span> <span data-ttu-id="5d8bb-194">Si se especifica, las opciones de diseño -l y -udl se ignoran.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-194">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="5d8bb-195">--noPageModel o -npm</span><span class="sxs-lookup"><span data-stu-id="5d8bb-195">--noPageModel or -npm</span></span>         | <span data-ttu-id="5d8bb-196">Cambia a no generar una clase PageModel para una plantilla vacía.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-196">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="5d8bb-197">Use el modificador `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="5d8bb-197">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="5d8bb-198">Consulte [Aplicar scaffolding al modelo de película](xref:tutorials/razor-pages/model) para ver un ejemplo de `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="5d8bb-198">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="5d8bb-199">Vea [Scaffolding Identity](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="5d8bb-199">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
