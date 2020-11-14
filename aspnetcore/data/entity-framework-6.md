---
title: ASP.NET Core y Entity Framework 6
author: rick-anderson
description: Entity Framework 6.3 y versiones posteriores funcionan con ASP.NET Core 3.1 y versiones posteriores.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: data/entity-framework-6
ms.openlocfilehash: 086418c161677f585b08ed360555c93d8575e701
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059460"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="8ceb0-103">ASP.NET Core y Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="8ceb0-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8ceb0-104">Por [Patrick Goode](https://github.com/attrib75)</span><span class="sxs-lookup"><span data-stu-id="8ceb0-104">By [Patrick Goode](https://github.com/attrib75)</span></span>

## <a name="using-entity-framework-6-with-aspnet-core"></a><span data-ttu-id="8ceb0-105">Uso de Entity Framework 6 con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8ceb0-105">Using Entity Framework 6 with ASP.NET Core</span></span>

<span data-ttu-id="8ceb0-106">[Entity Framework Core](/ef/) se debe usar para desarrollos nuevo.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-106">[Entity Framework Core](/ef/) should be used for new development.</span></span> <span data-ttu-id="8ceb0-107">En el [ejemplo para descargar](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) se usa [Entity Framework 6 (EF6)](/ef/ef6), que se puede utilizar para migrar aplicaciones existentes a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-107">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) uses [Entity Framework 6 (EF6)](/ef/ef6), which can be used to migrate exiting apps to ASP.NET Core.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8ceb0-108">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8ceb0-108">Additional resources</span></span>

* <span data-ttu-id="8ceb0-109">[Entity Framework - Code-Based Configuration](/ef/ef6/fundamentals/configuring/code-based) (Entity Framework: configuración basada en código)</span><span class="sxs-lookup"><span data-stu-id="8ceb0-109">[Entity Framework - Code-Based Configuration](/ef/ef6/fundamentals/configuring/code-based)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8ceb0-110">Por [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) y [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="8ceb0-110">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="8ceb0-111">En este artículo se muestra cómo usar Entity Framework 6 en una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-111">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="8ceb0-112">Información general</span><span class="sxs-lookup"><span data-stu-id="8ceb0-112">Overview</span></span> 

<span data-ttu-id="8ceb0-113">Para usar Entity Framework 6, el proyecto se tiene que compilar con .NET Framework, dado que Entity Framework 6 no es compatible con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-113">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="8ceb0-114">Si necesita usar características multiplataforma, debe actualizar a [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="8ceb0-114">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="8ceb0-115">La manera recomendada de usar Entity Framework 6 en una aplicación ASP.NET Core es colocar el contexto y las clases de modelo de EF6 en un proyecto de biblioteca de clases cuyo destino sea .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-115">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="8ceb0-116">Agregue una referencia a la biblioteca de clases desde el proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-116">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="8ceb0-117">Vea el ejemplo [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) (Solución de Visual Studio con proyectos de EF6 y ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="8ceb0-117">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>  

<span data-ttu-id="8ceb0-118">No se puede colocar un contexto de EF6 en un proyecto de ASP.NET Core porque los proyectos de .NET Core no admiten toda la funcionalidad que requieren los comandos de EF6 como *Enable-Migrations*.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-118">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="8ceb0-119">Independientemente del tipo de proyecto en el que localice el contexto de EF6, solo las herramientas de línea de comandos de EF6 funcionan con un contexto de EF6.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-119">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="8ceb0-120">Por ejemplo, `Scaffold-DbContext` solo está disponible en Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-120">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="8ceb0-121">Si necesita la utilización de técnicas de ingeniería inversa para una base de datos en un modelo de EF6, vea <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database> (Code First para una base de datos existente).</span><span class="sxs-lookup"><span data-stu-id="8ceb0-121">If you need to do reverse engineering of a database into an EF6 model, see <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.</span></span>    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="8ceb0-122">Marco de referencia completo y EF6 en el proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8ceb0-122">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="8ceb0-123">El proyecto de ASP.NET Core debe destinarse a .NET Framework y hacer referencia a EF6.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-123">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="8ceb0-124">Por ejemplo, el archivo *.csproj* del proyecto ASP.NET Core tendrá un aspecto similar al ejemplo siguiente (solo se muestran las partes relevantes del archivo).</span><span class="sxs-lookup"><span data-stu-id="8ceb0-124">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="8ceb0-125">Al crear un proyecto, use la plantilla **Aplicación web ASP.NET Core (.NET Framework)** .</span><span class="sxs-lookup"><span data-stu-id="8ceb0-125">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="8ceb0-126">Controlar las cadenas de conexión</span><span class="sxs-lookup"><span data-stu-id="8ceb0-126">Handle connection strings</span></span>    

<span data-ttu-id="8ceb0-127">Las herramientas de línea de comandos de EF6 que se van a usar en el proyecto de biblioteca de clases de EF6 requieren un constructor predeterminado para poder crear instancias del contexto.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-127">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="8ceb0-128">Pero, probablemente querrá especificar la cadena de conexión que se va a usar en el proyecto de ASP.NET Core, en cuyo caso el constructor de contexto debe tener un parámetro que permita pasar la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-128">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="8ceb0-129">Este es un ejemplo.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-129">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="8ceb0-130">Como el contexto de EF6 no tiene un constructor sin parámetros, el proyecto de EF6 tiene que proporcionar una implementación de <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-130">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>.</span></span> <span data-ttu-id="8ceb0-131">Las herramientas de línea de comandos de EF6 buscarán y usarán esa implementación para poder crear instancias del contexto.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-131">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="8ceb0-132">Este es un ejemplo.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-132">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="8ceb0-133">En este ejemplo de código, la implementación de `IDbContextFactory` pasa una cadena de conexión codificada de forma rígida.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-133">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="8ceb0-134">Se trata de la cadena de conexión que van a usar las herramientas de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-134">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="8ceb0-135">Querrá implementar una estrategia para asegurarse de que la biblioteca de clases usa la misma cadena de conexión que la aplicación que realiza la llamada.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-135">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="8ceb0-136">Por ejemplo, podría obtener el valor de una variable de entorno en los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-136">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="8ceb0-137">Configurar la inserción de dependencias en el proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8ceb0-137">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="8ceb0-138">En el archivo *Startup.cs* del proyecto de Core, establezca el contexto de EF6 para la inserción de dependencias (DI) en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-138">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="8ceb0-139">La duración de cada solicitud se debe configurar como ámbito de los objetos de contexto de EF.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-139">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="8ceb0-140">Después, puede obtener una instancia del contexto en los controladores mediante DI.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-140">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="8ceb0-141">El código es similar al que escribiría para un contexto de EF Core:</span><span class="sxs-lookup"><span data-stu-id="8ceb0-141">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="8ceb0-142">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="8ceb0-142">Sample application</span></span>   

<span data-ttu-id="8ceb0-143">Para obtener una aplicación de ejemplo funcional, vea la [solución de Visual Studio de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) que se incluye en este artículo.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-143">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>  

<span data-ttu-id="8ceb0-144">Este ejemplo se puede crear desde cero mediante los pasos siguientes en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8ceb0-144">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="8ceb0-145">Cree una solución.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-145">Create a solution.</span></span>    

* <span data-ttu-id="8ceb0-146">**Agregar** > **Nuevo proyecto** > **Web** > **Aplicación web ASP.NET Core**</span><span class="sxs-lookup"><span data-stu-id="8ceb0-146">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="8ceb0-147">En el cuadro de diálogo de selección de la plantilla de proyecto, seleccione la API y .NET Framework en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-147">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="8ceb0-148">**Agregar** > **Nuevo proyecto** > **Escritorio de Windows** > **Biblioteca de clases (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="8ceb0-148">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="8ceb0-149">En la **Consola del Administrador de paquetes** (PMC) para ambos proyectos, ejecute el comando `Install-Package Entityframework`.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-149">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="8ceb0-150">En el proyecto de biblioteca de clases, cree clases de modelo de datos, una clase de contexto y una implementación de `IDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-150">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="8ceb0-151">En PMC para el proyecto de biblioteca de clases, ejecute los comandos `Enable-Migrations` y `Add-Migration Initial`.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-151">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="8ceb0-152">Si ha configurado el proyecto de ASP.NET Core como proyecto de inicio, agregue `-StartupProjectName EF6` a estos comandos.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-152">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="8ceb0-153">En el proyecto de Core, agregue una referencia de proyecto al proyecto de biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-153">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="8ceb0-154">En el proyecto de Core, en *Startup.cs* , registre el contexto para DI.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-154">In the Core project, in *Startup.cs* , register the context for DI.</span></span>    

* <span data-ttu-id="8ceb0-155">En el proyecto de Core, en *appsettings.json* , agregue la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-155">In the Core project, in *appsettings.json* , add the connection string.</span></span>  

* <span data-ttu-id="8ceb0-156">En el proyecto de Core, agregue un controlador y vistas para comprobar que puede leer y escribir datos.</span><span class="sxs-lookup"><span data-stu-id="8ceb0-156">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="8ceb0-157">(Tenga en cuenta que el scaffolding de ASP.NET Core MVC no funcionará con el contexto de EF6 al que se hace referencia desde la biblioteca de clases).</span><span class="sxs-lookup"><span data-stu-id="8ceb0-157">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
