---
title: Introducción a Razor Pages en ASP.NET Core
author: Rick-Anderson
description: Explica cómo Razor Pages en ASP.NET Core facilita la programación en escenarios centrados en páginas y mejora la productividad en comparación con el uso de MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: 78b192cb2240046d16b1b766954ed4ca5229d888
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586714"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="36e0b-103">Introducción a Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36e0b-103">Introduction to Razor Pages in ASP.NET Core</span></span>


<span data-ttu-id="36e0b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="36e0b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="36e0b-105">Razor Pages facilita la programación de escenarios centrados en páginas y hace que resulte más productiva que con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="36e0b-106">Si busca un tutorial que use el enfoque Model-View-Controller, consulte [Introducción a ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="36e0b-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="36e0b-107">En este documento se proporciona una introducción a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="36e0b-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="36e0b-108">No es un tutorial paso a paso.</span><span class="sxs-lookup"><span data-stu-id="36e0b-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="36e0b-109">Si encuentra que alguna sección es demasiado avanzada, consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="36e0b-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="36e0b-110">Para obtener información general de ASP.NET Core, vea [Introducción a ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="36e0b-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="36e0b-111">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="36e0b-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="36e0b-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36e0b-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="36e0b-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36e0b-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="36e0b-114">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36e0b-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="36e0b-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36e0b-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="36e0b-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36e0b-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="36e0b-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36e0b-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="36e0b-118">Crear un proyecto de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="36e0b-118">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36e0b-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36e0b-119">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="36e0b-120">Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="36e0b-120">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36e0b-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36e0b-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="36e0b-122">Ejecute `dotnet new webapp` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-122">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="36e0b-123">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36e0b-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="36e0b-124">Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="36e0b-124">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="36e0b-125">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="36e0b-125">Razor Pages</span></span>

<span data-ttu-id="36e0b-126">Razor Pages está habilitado en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="36e0b-127">Considere la posibilidad de una página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="36e0b-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="36e0b-128">El código anterior se parece mucho a un [archivo de vista de Razor](xref:tutorials/first-mvc-app/adding-view) que se utiliza en una aplicación ASP.NET Core con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="36e0b-129">La directiva [`@page`](xref:mvc/views/razor#page) lo hace diferente.</span><span class="sxs-lookup"><span data-stu-id="36e0b-129">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="36e0b-130">`@page` transforma el archivo en una acción de MVC, lo que significa que administra las solicitudes directamente, sin tener que pasar a través de un controlador.</span><span class="sxs-lookup"><span data-stu-id="36e0b-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="36e0b-131">`@page` debe ser la primera directiva de Razorde una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="36e0b-132">`@page` afecta al comportamiento de otras construcciones de [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="36e0b-132">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="36e0b-133">Los nombres de archivo de Razor Pages tienen el sufijo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-133">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="36e0b-134">Una página similar, con una clase `PageModel`, se muestra en los dos archivos siguientes.</span><span class="sxs-lookup"><span data-stu-id="36e0b-134">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="36e0b-135">El archivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-135">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="36e0b-136">Modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-136">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="36e0b-137">Por convención, el archivo de clase `PageModel` tiene el mismo nombre que el archivo de Razor Pages con *.cs* anexado.</span><span class="sxs-lookup"><span data-stu-id="36e0b-137">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="36e0b-138">Por ejemplo, la instancia de Razor Pages anterior es *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-138">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="36e0b-139">El archivo que contiene la clase `PageModel` se denomina *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-139">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="36e0b-140">Las asociaciones de rutas de dirección URL a páginas se determinan según la ubicación de la página en el sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-140">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="36e0b-141">En la tabla siguiente, se muestra una ruta de acceso Razor Pages y la dirección URL correspondiente:</span><span class="sxs-lookup"><span data-stu-id="36e0b-141">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="36e0b-142">Ruta de acceso y nombre de archivo</span><span class="sxs-lookup"><span data-stu-id="36e0b-142">File name and path</span></span>               | <span data-ttu-id="36e0b-143">URL correspondiente</span><span class="sxs-lookup"><span data-stu-id="36e0b-143">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="36e0b-144">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-144">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="36e0b-145">`/` o `/Index`</span><span class="sxs-lookup"><span data-stu-id="36e0b-145">`/` or `/Index`</span></span> |
| <span data-ttu-id="36e0b-146">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-146">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="36e0b-147">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-147">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="36e0b-148">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-148">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="36e0b-149">`/Store` o `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="36e0b-149">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="36e0b-150">Notas:</span><span class="sxs-lookup"><span data-stu-id="36e0b-150">Notes:</span></span>

* <span data-ttu-id="36e0b-151">El entorno de ejecución busca archivos de páginas de Razor en la carpeta *Pages* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="36e0b-151">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="36e0b-152">`Index` es la página predeterminada cuando una URL no incluye una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-152">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="36e0b-153">Escritura de un formulario básico</span><span class="sxs-lookup"><span data-stu-id="36e0b-153">Write a basic form</span></span>

<span data-ttu-id="36e0b-154">Razor Pages está diseñado para facilitar la implementación de patrones comunes que se usan con exploradores web al compilar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-154">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="36e0b-155">Los [enlaces de modelos](xref:mvc/models/model-binding), los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los asistentes de HTML *simplemente funcionan* con las propiedades definidas en una clase de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="36e0b-155">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="36e0b-156">Considere la posibilidad de una página que implementa un formulario básico del estilo "Póngase en contacto con nosotros" para el modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-156">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="36e0b-157">Para los ejemplos de este documento, `DbContext` se inicializa en el archivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="36e0b-157">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="36e0b-158">La base de datos en memoria requiere el paquete NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-158">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="36e0b-159">El modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="36e0b-159">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="36e0b-160">El contexto de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="36e0b-160">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="36e0b-161">El archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-161">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="36e0b-162">Modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-162">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="36e0b-163">Por convención, la clase `PageModel` se denomina `<PageName>Model` y se encuentra en el mismo espacio de nombres que la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-163">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="36e0b-164">La clase `PageModel` permite la separación de la lógica de una página de su presentación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-164">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="36e0b-165">Define los controladores de página para solicitudes que se envían a la página y los datos que usan para representar la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-165">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="36e0b-166">Esta separación permite lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="36e0b-166">This separation allows:</span></span>

* <span data-ttu-id="36e0b-167">Administración de dependencias de página mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="36e0b-167">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="36e0b-168">Pruebas unitarias</span><span class="sxs-lookup"><span data-stu-id="36e0b-168">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="36e0b-169">La página tiene un *método de controlador* `OnPostAsync`, que se ejecuta en solicitudes `POST` (cuando un usuario envía el formulario).</span><span class="sxs-lookup"><span data-stu-id="36e0b-169">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="36e0b-170">Se pueden agregar métodos de controlador para cualquier verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="36e0b-170">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="36e0b-171">Los controladores más comunes son:</span><span class="sxs-lookup"><span data-stu-id="36e0b-171">The most common handlers are:</span></span>

* <span data-ttu-id="36e0b-172">`OnGet` para inicializar el estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-172">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="36e0b-173">En el código anterior, el método `OnGet` muestra la instancia *CreateModel.cshtml* de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="36e0b-173">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="36e0b-174">`OnPost` para controlar los envíos del formulario.</span><span class="sxs-lookup"><span data-stu-id="36e0b-174">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="36e0b-175">El sufijo de nombre `Async` es opcional, pero se usa a menudo por convención para funciones asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-175">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="36e0b-176">El código anterior es típico de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="36e0b-176">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="36e0b-177">Si está familiarizado con las aplicaciones de ASP.NET con controladores y vistas:</span><span class="sxs-lookup"><span data-stu-id="36e0b-177">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="36e0b-178">El código `OnPostAsync` del ejemplo anterior es similar al típico código de controlador.</span><span class="sxs-lookup"><span data-stu-id="36e0b-178">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="36e0b-179">La mayoría de los primitivos de MVC, como el [enlace de modelos](xref:mvc/models/model-binding), la [validación](xref:mvc/models/validation) y los resultados de acciones, funcionan del mismo modo con los controladores y Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="36e0b-179">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="36e0b-180">El método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="36e0b-180">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="36e0b-181">El flujo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-181">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="36e0b-182">Compruebe los errores de validación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-182">Check for validation errors.</span></span>

* <span data-ttu-id="36e0b-183">Si no hay ningún error, guarde los datos y redirija.</span><span class="sxs-lookup"><span data-stu-id="36e0b-183">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="36e0b-184">Si hay errores, muestre la página de nuevo con mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-184">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="36e0b-185">En muchos casos, los errores de validación se detectan en el cliente y nunca se envían al servidor.</span><span class="sxs-lookup"><span data-stu-id="36e0b-185">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="36e0b-186">El archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-186">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="36e0b-187">El código HTML representado de *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-187">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="36e0b-188">En el código anterior, la publicación del formulario:</span><span class="sxs-lookup"><span data-stu-id="36e0b-188">In the previous code, posting the form:</span></span>

* <span data-ttu-id="36e0b-189">Con datos válidos:</span><span class="sxs-lookup"><span data-stu-id="36e0b-189">With valid data:</span></span>

  * <span data-ttu-id="36e0b-190">El método del controlador `OnPostAsync` llama al método auxiliar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>.</span><span class="sxs-lookup"><span data-stu-id="36e0b-190">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="36e0b-191">`RedirectToPage` devuelve una instancia de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="36e0b-191">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="36e0b-192">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-192">`RedirectToPage`:</span></span>

    * <span data-ttu-id="36e0b-193">Es el resultado de una acción.</span><span class="sxs-lookup"><span data-stu-id="36e0b-193">Is an action result.</span></span>
    * <span data-ttu-id="36e0b-194">Es similar a `RedirectToAction` o `RedirectToRoute` (se usa en controladores y vistas).</span><span class="sxs-lookup"><span data-stu-id="36e0b-194">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="36e0b-195">Se ha personalizado para las páginas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-195">Is customized for pages.</span></span> <span data-ttu-id="36e0b-196">En el ejemplo anterior, redirige a la página de índice raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="36e0b-196">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="36e0b-197">`RedirectToPage` se detalla en la sección [Generación de direcciones URL para las páginas](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="36e0b-197">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="36e0b-198">Con errores de validación que se pasan al servidor:</span><span class="sxs-lookup"><span data-stu-id="36e0b-198">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="36e0b-199">El método del controlador `OnPostAsync` llama al método auxiliar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>.</span><span class="sxs-lookup"><span data-stu-id="36e0b-199">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="36e0b-200">`Page` devuelve una instancia de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="36e0b-200">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="36e0b-201">Devolver `Page` es similar a cómo las acciones en los controladores devuelven `View`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-201">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="36e0b-202">`PageResult` es el tipo de valor devuelto predeterminado para un método de controlador.</span><span class="sxs-lookup"><span data-stu-id="36e0b-202">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="36e0b-203">Un método de controlador que devuelve `void` representa la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-203">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="36e0b-204">En el ejemplo anterior, la publicación del formulario sin valores hace que se devuelva [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) como false.</span><span class="sxs-lookup"><span data-stu-id="36e0b-204">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="36e0b-205">En este ejemplo, no se muestra ningún error de validación en el cliente.</span><span class="sxs-lookup"><span data-stu-id="36e0b-205">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="36e0b-206">La entrega de errores de validación se trata más adelante en este documento.</span><span class="sxs-lookup"><span data-stu-id="36e0b-206">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="36e0b-207">Con errores de validación detectados mediante la validación del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="36e0b-207">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="36e0b-208">Los datos **no** se publican en el servidor.</span><span class="sxs-lookup"><span data-stu-id="36e0b-208">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="36e0b-209">La validación del lado cliente se explica más adelante en este documento.</span><span class="sxs-lookup"><span data-stu-id="36e0b-209">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="36e0b-210">La propiedad `Customer` usa el atributo [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) para participar en el enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="36e0b-210">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="36e0b-211">`[BindProperty]`**no** debe usarse en modelos que contengan propiedades que el cliente no debe cambiar.</span><span class="sxs-lookup"><span data-stu-id="36e0b-211">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="36e0b-212">Para más información, consulte [Publicación excesiva](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="36e0b-212">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="36e0b-213">De forma predeterminada, Razor Pages enlaza propiedades solo con verbos que no sean `GET`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-213">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="36e0b-214">El enlace a propiedades elimina la necesidad de escribir código para convertir los datos HTTP en el tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="36e0b-214">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="36e0b-215">Enlazar reduce el código al usar la misma propiedad para representar los campos de formulario (`<input asp-for="Customer.Name">`) y aceptar la entrada.</span><span class="sxs-lookup"><span data-stu-id="36e0b-215">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="36e0b-216">Revisión del archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-216">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="36e0b-217">En el código anterior, el [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` enlaza el elemento `<input>` HTML con la expresión del modelo `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-217">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="36e0b-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) hace que los asistentes de etiquetas estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="36e0b-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="36e0b-219">La página principal</span><span class="sxs-lookup"><span data-stu-id="36e0b-219">The home page</span></span>

<span data-ttu-id="36e0b-220">*Index.cshtml* es la página principal:</span><span class="sxs-lookup"><span data-stu-id="36e0b-220">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="36e0b-221">La clase `PageModel` asociada (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="36e0b-221">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="36e0b-222">El archivo *Index.cshtml* contiene el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="36e0b-222">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="36e0b-223">El `<a /a>` [asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usado el atributo `asp-route-{value}` para generar un vínculo a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="36e0b-223">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="36e0b-224">El vínculo contiene datos de ruta con el identificador del contacto.</span><span class="sxs-lookup"><span data-stu-id="36e0b-224">The link contains route data with the contact ID.</span></span> <span data-ttu-id="36e0b-225">Por ejemplo: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-225">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="36e0b-226">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="36e0b-226">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="36e0b-227">El archivo *index.cshtml* contiene marcado para crear un botón de eliminar para cada contacto de cliente:</span><span class="sxs-lookup"><span data-stu-id="36e0b-227">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="36e0b-228">El código HTML representado:</span><span class="sxs-lookup"><span data-stu-id="36e0b-228">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="36e0b-229">Al representar el botón de eliminar en HTML, el elemento [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) incluye parámetros para los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="36e0b-229">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="36e0b-230">Id. de contacto de cliente especificado mediante el atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-230">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="36e0b-231">`handler` especificado mediante el atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-231">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="36e0b-232">Al seleccionar el botón, se envía una solicitud de formulario `POST` al servidor.</span><span class="sxs-lookup"><span data-stu-id="36e0b-232">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="36e0b-233">De forma predeterminada, el nombre del método de control se selecciona de acuerdo con el valor del parámetro `handler` y según el esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-233">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="36e0b-234">Como en este ejemplo `handler` es `delete`, el método de control `OnPostDeleteAsync` se usa para procesar la solicitud `POST`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-234">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="36e0b-235">Si `asp-page-handler` se establece en otro valor, como `remove`, se seleccionará un método de controlador llamado `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-235">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="36e0b-236">El método `OnPostDeleteAsync` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="36e0b-236">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="36e0b-237">Obtiene el elemento `id` de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="36e0b-237">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="36e0b-238">Realiza una consulta a la base de datos del contacto de cliente con `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-238">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="36e0b-239">Si se encuentra el contacto del cliente, este se quita y se actualiza la base de datos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-239">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="36e0b-240">Llama a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> para redirigir la página Index raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="36e0b-240">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="36e0b-241">El archivo Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="36e0b-241">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="36e0b-242">La primera línea contiene la directiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-242">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="36e0b-243">La restricción de enrutamiento `"{id:int}"` indica a la página que acepte las solicitudes a la página que contienen datos de ruta `int`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-243">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="36e0b-244">Si una solicitud a la página no contiene datos de ruta que se puedan convertir en `int`, el tiempo de ejecución devuelve un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="36e0b-244">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="36e0b-245">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="36e0b-245">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="36e0b-246">El archivo *Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-246">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="36e0b-247">Validación</span><span class="sxs-lookup"><span data-stu-id="36e0b-247">Validation</span></span>

<span data-ttu-id="36e0b-248">Reglas de validación:</span><span class="sxs-lookup"><span data-stu-id="36e0b-248">Validation rules:</span></span>

* <span data-ttu-id="36e0b-249">Se especifican mediante declaración en la clase de modelo.</span><span class="sxs-lookup"><span data-stu-id="36e0b-249">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="36e0b-250">Se aplican en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-250">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="36e0b-251">El espacio de nombres <xref:System.ComponentModel.DataAnnotations> proporciona un conjunto de atributos de validación integrados que se aplican mediante declaración a una clase o propiedad.</span><span class="sxs-lookup"><span data-stu-id="36e0b-251">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="36e0b-252">DataAnnotations también contiene atributos de formato como [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute), que ayudan a aplicar formato y no proporcionan ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-252">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="36e0b-253">Considere el modelo `Customer`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-253">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="36e0b-254">Con el siguiente archivo de vista *Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-254">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="36e0b-255">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="36e0b-255">The preceding code:</span></span>

* <span data-ttu-id="36e0b-256">Incluye scripts de validación de jQuery y jQuery.</span><span class="sxs-lookup"><span data-stu-id="36e0b-256">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="36e0b-257">Usa los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) `<div />` y `<span />` para habilitar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="36e0b-257">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="36e0b-258">Validación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="36e0b-258">Client-side validation.</span></span>
  * <span data-ttu-id="36e0b-259">Representación del error de validación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-259">Validation error rendering.</span></span>

* <span data-ttu-id="36e0b-260">Se genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="36e0b-260">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="36e0b-261">Al publicar el formulario de creación sin un valor de nombre, se muestra el mensaje de error "El campo Nombre es obligatorio".</span><span class="sxs-lookup"><span data-stu-id="36e0b-261">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="36e0b-262">en el formulario.</span><span class="sxs-lookup"><span data-stu-id="36e0b-262">on the form.</span></span> <span data-ttu-id="36e0b-263">Si JavaScript está habilitado en el cliente, el explorador muestra el error sin realizar la publicación en el servidor.</span><span class="sxs-lookup"><span data-stu-id="36e0b-263">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="36e0b-264">El atributo `[StringLength(10)]` genera `data-val-length-max="10"` en el código HTML representado.</span><span class="sxs-lookup"><span data-stu-id="36e0b-264">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="36e0b-265">`data-val-length-max` impide que los exploradores superen la longitud máxima especificada al escribir.</span><span class="sxs-lookup"><span data-stu-id="36e0b-265">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="36e0b-266">Si se usa una herramienta como [Fiddler](https://www.telerik.com/fiddler) para editar y reproducir la publicación:</span><span class="sxs-lookup"><span data-stu-id="36e0b-266">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="36e0b-267">Con el nombre de más de 10 caracteres.</span><span class="sxs-lookup"><span data-stu-id="36e0b-267">With the name longer than 10.</span></span>
* <span data-ttu-id="36e0b-268">Se devolverá el mensaje de error "El nombre del campo debe ser una cadena con una longitud máxima de 10 caracteres".</span><span class="sxs-lookup"><span data-stu-id="36e0b-268">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="36e0b-269">.</span><span class="sxs-lookup"><span data-stu-id="36e0b-269">is returned.</span></span>

<span data-ttu-id="36e0b-270">Considere el modelo `Movie` siguiente:</span><span class="sxs-lookup"><span data-stu-id="36e0b-270">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="36e0b-271">Los atributos de validación especifican el comportamiento que se exigirá a las propiedades del modelo al que se aplican:</span><span class="sxs-lookup"><span data-stu-id="36e0b-271">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="36e0b-272">Los atributos `Required` y `MinimumLength` indican que una propiedad debe tener un valor, pero nada impide al usuario escribir espacios en blanco para satisfacer esta validación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-272">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="36e0b-273">El atributo `RegularExpression` se usa para limitar los caracteres que se pueden escribir.</span><span class="sxs-lookup"><span data-stu-id="36e0b-273">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="36e0b-274">En el código anterior, "Género":</span><span class="sxs-lookup"><span data-stu-id="36e0b-274">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="36e0b-275">Solo debe usar letras.</span><span class="sxs-lookup"><span data-stu-id="36e0b-275">Must only use letters.</span></span>
  * <span data-ttu-id="36e0b-276">La primera letra debe estar en mayúsculas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-276">The first letter is required to be uppercase.</span></span> <span data-ttu-id="36e0b-277">No se permiten espacios en blanco, números ni caracteres especiales.</span><span class="sxs-lookup"><span data-stu-id="36e0b-277">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="36e0b-278">La "Clasificación" de `RegularExpression`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-278">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="36e0b-279">Requiere que el primer carácter sea una letra mayúscula.</span><span class="sxs-lookup"><span data-stu-id="36e0b-279">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="36e0b-280">Permite caracteres especiales y números en los espacios posteriores.</span><span class="sxs-lookup"><span data-stu-id="36e0b-280">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="36e0b-281">"PG-13" es válido para una "Clasificación", pero se produce un error en un "Género".</span><span class="sxs-lookup"><span data-stu-id="36e0b-281">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="36e0b-282">El atributo `Range` restringe un valor a un intervalo determinado.</span><span class="sxs-lookup"><span data-stu-id="36e0b-282">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="36e0b-283">El atributo `StringLength` establece la longitud máxima de una propiedad de cadena y, opcionalmente, su longitud mínima.</span><span class="sxs-lookup"><span data-stu-id="36e0b-283">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="36e0b-284">Los tipos de valor (como `decimal`, `int`, `float`, `DateTime`) son intrínsecamente necesarios y no necesitan el atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-284">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="36e0b-285">La página de creación del modelo `Movie` muestra errores de visualización con valores no válidos:</span><span class="sxs-lookup"><span data-stu-id="36e0b-285">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulario de vista de película con varios errores de validación de cliente de jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="36e0b-287">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="36e0b-287">For more information, see:</span></span>

* [<span data-ttu-id="36e0b-288">Adición de validación a la aplicación Película</span><span class="sxs-lookup"><span data-stu-id="36e0b-288">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="36e0b-289">[Validación de modelos en ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="36e0b-289">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="36e0b-290">Control de solicitudes HEAD con un controlador OnGet de reserva</span><span class="sxs-lookup"><span data-stu-id="36e0b-290">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="36e0b-291">Las solicitudes `HEAD` permiten recuperar los encabezados de un recurso específico.</span><span class="sxs-lookup"><span data-stu-id="36e0b-291">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="36e0b-292">A diferencia de las solicitudes `GET`, las solicitudes `HEAD` no devuelven un cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="36e0b-292">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="36e0b-293">Normalmente, se crea un controlador `OnHead` al que se llama para las solicitudes `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-293">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="36e0b-294">Razor Pages recurre a una llamada al controlador `OnGet` si no se define ningún controlador `OnHead`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-294">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="36e0b-295">XSRF/CSRF y Razor Pages</span><span class="sxs-lookup"><span data-stu-id="36e0b-295">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="36e0b-296">Razor Pages está protegido mediante [validación antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="36e0b-296">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="36e0b-297">El elemento [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) inserta tokens antifalsificación en los elementos de formulario HTML.</span><span class="sxs-lookup"><span data-stu-id="36e0b-297">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="36e0b-298">Usar diseños, parciales, plantillas y asistentes de etiquetas con Razor Pages</span><span class="sxs-lookup"><span data-stu-id="36e0b-298">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="36e0b-299">Las páginas funcionan con todas las características del motor de vista de Razor.</span><span class="sxs-lookup"><span data-stu-id="36e0b-299">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="36e0b-300">Los diseños, parciales, plantillas, asistentes de etiquetas, *_ViewStart.cshtml* y *_ViewImports.cshtml* funcionan de la misma manera que lo hacen con las vistas de Razor convencionales.</span><span class="sxs-lookup"><span data-stu-id="36e0b-300">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="36e0b-301">Para simplificar esta página, aprovecharemos algunas de esas características.</span><span class="sxs-lookup"><span data-stu-id="36e0b-301">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="36e0b-302">Agregue una [página de diseño](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-302">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="36e0b-303">El [diseño](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="36e0b-303">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="36e0b-304">Controla el diseño de cada página (a no ser que la página no tenga diseño).</span><span class="sxs-lookup"><span data-stu-id="36e0b-304">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="36e0b-305">Importa las estructuras HTML como JavaScript y hojas de estilos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-305">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="36e0b-306">El contenido de la página de Razor se representa donde se llama a `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-306">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="36e0b-307">Para más información, consulte la [página de diseño](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="36e0b-307">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="36e0b-308">La propiedad [Layout](xref:mvc/views/layout#specifying-a-layout) se establece en *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-308">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="36e0b-309">El diseño está en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-309">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="36e0b-310">Las páginas buscan otras vistas (diseños, plantillas, parciales) de forma jerárquica, a partir de la misma carpeta que la página actual.</span><span class="sxs-lookup"><span data-stu-id="36e0b-310">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="36e0b-311">Un diseño en la carpeta *Pages/Shared* se puede usar desde cualquier página de Razor en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-311">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="36e0b-312">El archivo de diseño debería ir en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-312">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="36e0b-313">Le recomendamos que **no** coloque el archivo de diseño en la carpeta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-313">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="36e0b-314">*Views/Shared* es un patrón de vistas de MVC.</span><span class="sxs-lookup"><span data-stu-id="36e0b-314">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="36e0b-315">Razor Pages está diseñado para basarse en la jerarquía de carpetas, no en las convenciones de ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="36e0b-315">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="36e0b-316">La búsqueda de vistas de una instancia de Razor Pages incluye la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-316">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="36e0b-317">Los diseños, plantillas y parciales que se usan con los controladores de MVC y las vistas de Razor convencionales *simplemente funcionan*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-317">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="36e0b-318">Agregue un archivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-318">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="36e0b-319">`@namespace` se explica más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="36e0b-319">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="36e0b-320">La directiva `@addTagHelper` pone los [asistentes de etiquetas integradas](xref:mvc/views/tag-helpers/builtin-th/Index) en todas las páginas de la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-320">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="36e0b-321">La directiva `@namespace` establecida en una página:</span><span class="sxs-lookup"><span data-stu-id="36e0b-321">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="36e0b-322">La directiva `@namespace` establece el espacio de nombres de la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-322">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="36e0b-323">La directiva `@model` no necesita incluir el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="36e0b-323">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="36e0b-324">Cuando la directiva `@namespace` se encuentra en *_ViewImports.cshtml*, el espacio de nombres especificado proporciona el prefijo del espacio de nombres generado en la página que importa la directiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-324">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="36e0b-325">El resto del espacio de nombres generado (la parte del sufijo) es la ruta de acceso relativa separada por puntos entre la carpeta que contiene *_ViewImports.cshtml* y la carpeta que contiene la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-325">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="36e0b-326">Por ejemplo, la clase `PageModel`*Pages/Customers/Edit.cshtml.cs* establece explícitamente el espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="36e0b-326">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="36e0b-327">El archivo *Pages/_ViewImports.cshtml* establece el espacio de nombres siguiente:</span><span class="sxs-lookup"><span data-stu-id="36e0b-327">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="36e0b-328">El espacio de nombres generado para la página de Razor *Pages/Customers/Edit.cshtml* es el mismo que la clase `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-328">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="36e0b-329">`@namespace` *también funciona con vistas de Razor convencionales.*</span><span class="sxs-lookup"><span data-stu-id="36e0b-329">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="36e0b-330">Considere el archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-330">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="36e0b-331">Archivo de vista actualizado *Pages/Create.cshtml* con *_ViewImports.cshtml* y el archivo de distribución anterior:</span><span class="sxs-lookup"><span data-stu-id="36e0b-331">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="36e0b-332">En el código anterior, el elemento *_ViewImports. cshtml* importó el espacio de nombres y los asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-332">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="36e0b-333">El archivo de distribución importó los archivos JavaScript.</span><span class="sxs-lookup"><span data-stu-id="36e0b-333">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="36e0b-334">El [proyecto de inicio de Razor Pages](#rpvs17) contiene *Pages/_ValidationScriptsPartial.cshtml*, que enlaza la validación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="36e0b-334">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="36e0b-335">Para más información sobre las vistas parciales, vea <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="36e0b-335">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="36e0b-336">Generación de direcciones URL para las páginas</span><span class="sxs-lookup"><span data-stu-id="36e0b-336">URL generation for Pages</span></span>

<span data-ttu-id="36e0b-337">La página `Create`, mostrada anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-337">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="36e0b-338">La aplicación tiene la siguiente estructura de archivos o carpetas:</span><span class="sxs-lookup"><span data-stu-id="36e0b-338">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="36e0b-339">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="36e0b-339">*/Pages*</span></span>

  * <span data-ttu-id="36e0b-340">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-340">*Index.cshtml*</span></span>
  * <span data-ttu-id="36e0b-341">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-341">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="36e0b-342">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="36e0b-342">*/Customers*</span></span>

    * <span data-ttu-id="36e0b-343">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-343">*Create.cshtml*</span></span>
    * <span data-ttu-id="36e0b-344">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-344">*Edit.cshtml*</span></span>
    * <span data-ttu-id="36e0b-345">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-345">*Index.cshtml*</span></span>

<span data-ttu-id="36e0b-346">Las páginas *Pages/Customers/Create.cshtml* y *Pages/Customers/Edit.cshtml* redirigen a *Pages/Customers/Index.cshtml* si la operación se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="36e0b-346">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="36e0b-347">La cadena `./Index` es un nombre de página relativo que se usa para acceder a la página anterior.</span><span class="sxs-lookup"><span data-stu-id="36e0b-347">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="36e0b-348">Se usa para generar direcciones URL a la página *Pages/Customers/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-348">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="36e0b-349">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="36e0b-349">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="36e0b-350">El nombre de página absoluto `/Index` se usa para generar direcciones URL a la página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-350">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="36e0b-351">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="36e0b-351">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="36e0b-352">El nombre de página es la ruta de acceso a la página de la carpeta raíz */Pages*, incluido un `/` inicial, por ejemplo `/Index`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-352">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="36e0b-353">Los ejemplos anteriores de generación de URL ofrecen opciones mejoradas y capacidades funcionales en comparación con la escritura a mano de estas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-353">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="36e0b-354">La generación de direcciones URL usa el [enrutamiento](xref:mvc/controllers/routing) y puede generar y codificar parámetros según cómo se defina la ruta en la ruta de acceso de destino.</span><span class="sxs-lookup"><span data-stu-id="36e0b-354">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="36e0b-355">La generación de direcciones URL para las páginas admite nombres relativos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-355">URL generation for pages supports relative names.</span></span> <span data-ttu-id="36e0b-356">En la siguiente tabla, se muestra qué página de índice está seleccionada con diferentes parámetros `RedirectToPage` en *Pages/Customers/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-356">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="36e0b-357">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="36e0b-357">RedirectToPage(x)</span></span>| <span data-ttu-id="36e0b-358">Página</span><span class="sxs-lookup"><span data-stu-id="36e0b-358">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="36e0b-359">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="36e0b-359">RedirectToPage("/Index")</span></span> | <span data-ttu-id="36e0b-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="36e0b-360">*Pages/Index*</span></span> |
| <span data-ttu-id="36e0b-361">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="36e0b-361">RedirectToPage("./Index");</span></span> | <span data-ttu-id="36e0b-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="36e0b-362">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="36e0b-363">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="36e0b-363">RedirectToPage("../Index")</span></span> | <span data-ttu-id="36e0b-364">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="36e0b-364">*Pages/Index*</span></span> |
| <span data-ttu-id="36e0b-365">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="36e0b-365">RedirectToPage("Index")</span></span>  | <span data-ttu-id="36e0b-366">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="36e0b-366">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="36e0b-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")` y `RedirectToPage("../Index")` son *nombres relativos*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="36e0b-368">El parámetro `RedirectToPage` se *combina* con la ruta de acceso de la página actual para calcular el nombre de la página de destino.</span><span class="sxs-lookup"><span data-stu-id="36e0b-368">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="36e0b-369">Vincular el nombre relativo es útil al crear sitios con una estructura compleja.</span><span class="sxs-lookup"><span data-stu-id="36e0b-369">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="36e0b-370">Cuando se usan nombres relativos para el vínculo entre las páginas de una carpeta:</span><span class="sxs-lookup"><span data-stu-id="36e0b-370">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="36e0b-371">Al cambiar el nombre de una carpeta, no se rompen los vínculos relativos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-371">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="36e0b-372">Los vínculos no se rompen porque no incluyen el nombre de la carpeta.</span><span class="sxs-lookup"><span data-stu-id="36e0b-372">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="36e0b-373">Para redirigir a una página en otra [área](xref:mvc/controllers/areas), especifique el área:</span><span class="sxs-lookup"><span data-stu-id="36e0b-373">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="36e0b-374">Para obtener más información, vea <xref:mvc/controllers/areas> y <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="36e0b-374">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="36e0b-375">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="36e0b-375">ViewData attribute</span></span>

<span data-ttu-id="36e0b-376">Se pueden pasar datos a una página con <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span><span class="sxs-lookup"><span data-stu-id="36e0b-376">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="36e0b-377">Las propiedades con el atributo `[ViewData]` tienen sus valores almacenados y cargados desde el elemento <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span><span class="sxs-lookup"><span data-stu-id="36e0b-377">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="36e0b-378">En el ejemplo siguiente, el elemento `AboutModel` aplica el atributo `[ViewData]` a la propiedad `Title`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-378">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="36e0b-379">En la página Acerca de, acceda a la propiedad `Title` como propiedad de modelo:</span><span class="sxs-lookup"><span data-stu-id="36e0b-379">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="36e0b-380">En el diseño, el título se lee desde el diccionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="36e0b-380">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="36e0b-381">TempData</span><span class="sxs-lookup"><span data-stu-id="36e0b-381">TempData</span></span>

<span data-ttu-id="36e0b-382">ASP.NET Core expone el elemento <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span><span class="sxs-lookup"><span data-stu-id="36e0b-382">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="36e0b-383">Esta propiedad almacena datos hasta que se leen.</span><span class="sxs-lookup"><span data-stu-id="36e0b-383">This property stores data until it's read.</span></span> <span data-ttu-id="36e0b-384">Los métodos <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> y <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> se pueden usar para examinar los datos sin que se eliminen.</span><span class="sxs-lookup"><span data-stu-id="36e0b-384">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="36e0b-385">`TempData` es útil para el redireccionamiento cuando se necesitan los datos de más de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="36e0b-385">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="36e0b-386">El siguiente código establece el valor de `Message` mediante `TempData`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-386">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="36e0b-387">El siguiente marcado en el archivo *Pages/Customers/Index.cshtml* muestra el valor de `Message` mediante `TempData`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-387">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="36e0b-388">El modelo de página *Pages/Customers/Index.cshtml.cs* aplica el atributo `[TempData]` a la propiedad `Message`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-388">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="36e0b-389">Para más información, consulte [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="36e0b-389">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="36e0b-390">Varios controladores por página</span><span class="sxs-lookup"><span data-stu-id="36e0b-390">Multiple handlers per page</span></span>

<span data-ttu-id="36e0b-391">En la página siguiente se usa el asistente de etiquetas `asp-page-handler` para generar marcado para dos controladores:</span><span class="sxs-lookup"><span data-stu-id="36e0b-391">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="36e0b-392">El formulario del ejemplo anterior tiene dos botones de envío, y cada uno de ellos usa `FormActionTagHelper` para enviar a una dirección URL diferente.</span><span class="sxs-lookup"><span data-stu-id="36e0b-392">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="36e0b-393">El atributo `asp-page-handler` es un complemento de `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-393">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="36e0b-394">`asp-page-handler` genera direcciones URL que envían a cada uno de los métodos de controlador definidos por una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-394">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="36e0b-395">`asp-page` no se especifica porque el ejemplo se vincula a la página actual.</span><span class="sxs-lookup"><span data-stu-id="36e0b-395">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="36e0b-396">Modelo de página:</span><span class="sxs-lookup"><span data-stu-id="36e0b-396">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="36e0b-397">El código anterior usa *métodos de controlador con nombre*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-397">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="36e0b-398">Los métodos de controlador con nombre se crean tomando el texto en el nombre después de `On<HTTP Verb>` y antes de `Async` (si existe).</span><span class="sxs-lookup"><span data-stu-id="36e0b-398">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="36e0b-399">En el ejemplo anterior, los métodos de página son OnPost **JoinList** Async y OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="36e0b-399">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="36e0b-400">Quitando *OnPost* y *Async*, los nombres de controlador son `JoinList` y `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-400">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="36e0b-401">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-401">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="36e0b-402">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-402">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="36e0b-403">Rutas personalizadas</span><span class="sxs-lookup"><span data-stu-id="36e0b-403">Custom routes</span></span>

<span data-ttu-id="36e0b-404">Use la directiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="36e0b-404">Use the `@page` directive to:</span></span>

* <span data-ttu-id="36e0b-405">Especificar una ruta personalizada a una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-405">Specify a custom route to a page.</span></span> <span data-ttu-id="36e0b-406">Por ejemplo, la ruta a la página Acerca de se puede establecer en `/Some/Other/Path` con `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-406">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="36e0b-407">Anexar segmentos a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-407">Append segments to a page's default route.</span></span> <span data-ttu-id="36e0b-408">Por ejemplo, se puede agregar un segmento "item" a la ruta predeterminada de una página con `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-408">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="36e0b-409">Anexar parámetros a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-409">Append parameters to a page's default route.</span></span> <span data-ttu-id="36e0b-410">Por ejemplo, un parámetro de identificador, `id`, puede ser necesario para una página con `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-410">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="36e0b-411">Se admite una ruta de acceso relativa raíz designada por una tilde (`~`) al principio de la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="36e0b-411">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="36e0b-412">Por ejemplo, `@page "~/Some/Other/Path"` es lo mismo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-412">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="36e0b-413">Si no le gusta la cadena de consulta `?handler=JoinList` en la dirección URL, puede cambiar la ruta para poner el nombre del controlador en la parte de la ruta de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="36e0b-413">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="36e0b-414">Para personalizar la ruta, se puede agregar una plantilla de ruta entre comillas dobles después de la directiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-414">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="36e0b-415">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-415">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="36e0b-416">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-416">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="36e0b-417">El signo `?` que sigue a `handler` significa que el parámetro de ruta es opcional.</span><span class="sxs-lookup"><span data-stu-id="36e0b-417">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="36e0b-418">Valores de configuración avanzados</span><span class="sxs-lookup"><span data-stu-id="36e0b-418">Advanced configuration and settings</span></span>

<span data-ttu-id="36e0b-419">La mayoría de las aplicaciones no requieren la configuración de las siguientes secciones.</span><span class="sxs-lookup"><span data-stu-id="36e0b-419">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="36e0b-420">Para configurar las opciones avanzadas, use la sobrecarga <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> que configura <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span><span class="sxs-lookup"><span data-stu-id="36e0b-420">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="36e0b-421">Use el elemento <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> para establecer el directorio raíz de páginas o agregar convenciones de modelo de aplicación para las páginas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-421">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="36e0b-422">Para obtener más información sobre las convenciones, vea [Convenciones de autorización de Razor Pages](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="36e0b-422">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="36e0b-423">Para precompilar vistas, consulte la sección sobre la [compilación de vistas de Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="36e0b-423">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="36e0b-424">Especificación de Razor Pages en la raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="36e0b-424">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="36e0b-425">De forma predeterminada, Razor Pages se encuentra en la raíz del directorio */Pages*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-425">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="36e0b-426">Agregue <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> para especificar que sus instancias de Razor Pages se encuentran en la [raíz de contenido](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="36e0b-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="36e0b-427">Especificación de Razor Pages en un directorio raíz personalizado</span><span class="sxs-lookup"><span data-stu-id="36e0b-427">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="36e0b-428">Agregue <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> para especificar que Razor Pages se encuentra en un directorio raíz personalizado en la aplicación (proporcione una ruta de acceso relativa):</span><span class="sxs-lookup"><span data-stu-id="36e0b-428">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="36e0b-429">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="36e0b-429">Additional resources</span></span>

* <span data-ttu-id="36e0b-430">Consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start), que se basa en esta introducción.</span><span class="sxs-lookup"><span data-stu-id="36e0b-430">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="36e0b-431">Autorización de atributos y Razor Pages</span><span class="sxs-lookup"><span data-stu-id="36e0b-431">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <span data-ttu-id="36e0b-432">[Descargue o vea el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/index/3.0sample).</span><span class="sxs-lookup"><span data-stu-id="36e0b-432">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/index/3.0sample)</span></span>
* <xref:index>
* [<span data-ttu-id="36e0b-433">Referencia sobre la sintaxis de Razor para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36e0b-433">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[<span data-ttu-id="36e0b-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36e0b-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="36e0b-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36e0b-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="36e0b-436">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36e0b-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="36e0b-437">Crear un proyecto de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="36e0b-437">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="36e0b-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36e0b-438">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="36e0b-439">Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="36e0b-439">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="36e0b-440">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36e0b-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="36e0b-441">Ejecute `dotnet new webapp` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-441">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="36e0b-442">Abra el archivo *.csproj* generado desde Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="36e0b-442">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="36e0b-443">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36e0b-443">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="36e0b-444">Ejecute `dotnet new webapp` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-444">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="36e0b-445">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="36e0b-445">Razor Pages</span></span>

<span data-ttu-id="36e0b-446">Razor Pages está habilitado en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-446">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="36e0b-447">Considere la posibilidad de una página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="36e0b-447">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="36e0b-448">El código anterior se parece mucho a un [archivo de vista de Razor](xref:tutorials/first-mvc-app/adding-view) que se utiliza en una aplicación ASP.NET Core con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-448">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="36e0b-449">La directiva `@page` lo hace diferente.</span><span class="sxs-lookup"><span data-stu-id="36e0b-449">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="36e0b-450">`@page` transforma el archivo en una acción de MVC, lo que significa que administra las solicitudes directamente, sin tener que pasar a través de un controlador.</span><span class="sxs-lookup"><span data-stu-id="36e0b-450">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="36e0b-451">`@page` debe ser la primera directiva de Razor de una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-451">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="36e0b-452">`@page` afecta al comportamiento de otras construcciones de Razor.</span><span class="sxs-lookup"><span data-stu-id="36e0b-452">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="36e0b-453">Una página similar, con una clase `PageModel`, se muestra en los dos archivos siguientes.</span><span class="sxs-lookup"><span data-stu-id="36e0b-453">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="36e0b-454">El archivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-454">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="36e0b-455">Modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-455">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="36e0b-456">Por convención, el archivo de clase `PageModel` tiene el mismo nombre que el archivo de Razor Pages con *.cs* anexado.</span><span class="sxs-lookup"><span data-stu-id="36e0b-456">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="36e0b-457">Por ejemplo, la instancia de Razor Pages anterior es *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-457">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="36e0b-458">El archivo que contiene la clase `PageModel` se denomina *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-458">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="36e0b-459">Las asociaciones de rutas de dirección URL a páginas se determinan según la ubicación de la página en el sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-459">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="36e0b-460">En la tabla siguiente, se muestra una ruta de acceso Razor Pages y la dirección URL correspondiente:</span><span class="sxs-lookup"><span data-stu-id="36e0b-460">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="36e0b-461">Ruta de acceso y nombre de archivo</span><span class="sxs-lookup"><span data-stu-id="36e0b-461">File name and path</span></span>               | <span data-ttu-id="36e0b-462">URL correspondiente</span><span class="sxs-lookup"><span data-stu-id="36e0b-462">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="36e0b-463">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-463">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="36e0b-464">`/` o `/Index`</span><span class="sxs-lookup"><span data-stu-id="36e0b-464">`/` or `/Index`</span></span> |
| <span data-ttu-id="36e0b-465">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-465">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="36e0b-466">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-466">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="36e0b-467">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-467">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="36e0b-468">`/Store` o `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="36e0b-468">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="36e0b-469">Notas:</span><span class="sxs-lookup"><span data-stu-id="36e0b-469">Notes:</span></span>

* <span data-ttu-id="36e0b-470">El entorno de ejecución busca archivos de páginas de Razor en la carpeta *Pages* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="36e0b-470">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="36e0b-471">`Index` es la página predeterminada cuando una URL no incluye una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-471">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="36e0b-472">Escritura de un formulario básico</span><span class="sxs-lookup"><span data-stu-id="36e0b-472">Write a basic form</span></span>

<span data-ttu-id="36e0b-473">Razor Pages está diseñado para facilitar la implementación de patrones comunes que se usan con exploradores web al compilar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-473">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="36e0b-474">Los [enlaces de modelos](xref:mvc/models/model-binding), los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los asistentes de HTML *simplemente funcionan* con las propiedades definidas en una clase de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="36e0b-474">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="36e0b-475">Considere la posibilidad de una página que implementa un formulario básico del estilo "Póngase en contacto con nosotros" para el modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-475">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="36e0b-476">Para los ejemplos de este documento, `DbContext` se inicializa en el archivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="36e0b-476">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="36e0b-477">El modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="36e0b-477">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="36e0b-478">El contexto de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="36e0b-478">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="36e0b-479">El archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-479">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="36e0b-480">Modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-480">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="36e0b-481">Por convención, la clase `PageModel` se denomina `<PageName>Model` y se encuentra en el mismo espacio de nombres que la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-481">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="36e0b-482">La clase `PageModel` permite la separación de la lógica de una página de su presentación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-482">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="36e0b-483">Define los controladores de página para solicitudes que se envían a la página y los datos que usan para representar la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-483">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="36e0b-484">Esta separación permite lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="36e0b-484">This separation allows:</span></span>

* <span data-ttu-id="36e0b-485">Administración de dependencias de página mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="36e0b-485">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="36e0b-486">[Pruebas](xref:test/razor-pages-tests) unitarias de las páginas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-486">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="36e0b-487">La página tiene un *método de controlador* `OnPostAsync`, que se ejecuta en solicitudes `POST` (cuando un usuario envía el formulario).</span><span class="sxs-lookup"><span data-stu-id="36e0b-487">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="36e0b-488">Puede agregar métodos de controlador para cualquier verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="36e0b-488">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="36e0b-489">Los controladores más comunes son:</span><span class="sxs-lookup"><span data-stu-id="36e0b-489">The most common handlers are:</span></span>

* <span data-ttu-id="36e0b-490">`OnGet` para inicializar el estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-490">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="36e0b-491">Ejemplo [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="36e0b-491">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="36e0b-492">`OnPost` para controlar los envíos del formulario.</span><span class="sxs-lookup"><span data-stu-id="36e0b-492">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="36e0b-493">El sufijo de nombre `Async` es opcional, pero se usa a menudo por convención para funciones asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-493">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="36e0b-494">El código anterior es típico de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="36e0b-494">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="36e0b-495">Si está familiarizado con las aplicaciones de ASP.NET con controladores y vistas:</span><span class="sxs-lookup"><span data-stu-id="36e0b-495">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="36e0b-496">El código `OnPostAsync` del ejemplo anterior es similar al típico código de controlador.</span><span class="sxs-lookup"><span data-stu-id="36e0b-496">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="36e0b-497">La mayoría de los primitivos MVC como el [enlace de modelos](xref:mvc/models/model-binding), la [validación](xref:mvc/models/validation), la [validación](xref:mvc/models/validation) y los resultados de acciones se comparten.</span><span class="sxs-lookup"><span data-stu-id="36e0b-497">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="36e0b-498">El método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="36e0b-498">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="36e0b-499">El flujo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-499">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="36e0b-500">Compruebe los errores de validación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-500">Check for validation errors.</span></span>

* <span data-ttu-id="36e0b-501">Si no hay ningún error, guarde los datos y redirija.</span><span class="sxs-lookup"><span data-stu-id="36e0b-501">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="36e0b-502">Si hay errores, muestre la página de nuevo con mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="36e0b-502">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="36e0b-503">La validación del lado cliente es idéntica a las aplicaciones de ASP.NET Core MVC tradicionales.</span><span class="sxs-lookup"><span data-stu-id="36e0b-503">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="36e0b-504">En muchos casos, los errores de validación se detectan en el cliente y nunca se envían al servidor.</span><span class="sxs-lookup"><span data-stu-id="36e0b-504">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="36e0b-505">Cuando los datos se escriben correctamente, el método del controlador `OnPostAsync` llama al método del asistente `RedirectToPage` para devolver una instancia de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-505">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="36e0b-506">`RedirectToPage` es un resultado de acción nueva, similar a `RedirectToAction` o `RedirectToRoute`, pero personalizada para las páginas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-506">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="36e0b-507">En el ejemplo anterior, redirige a la página de índice raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="36e0b-507">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="36e0b-508">`RedirectToPage` se detalla en la sección [Generación de direcciones URL para las páginas](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="36e0b-508">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="36e0b-509">Cuando el formulario enviado tiene errores de validación (que se pasan al servidor), el método del controlador `OnPostAsync` llama al método del asistente `Page`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-509">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="36e0b-510">`Page` devuelve una instancia de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-510">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="36e0b-511">Devolver `Page` es similar a cómo las acciones en los controladores devuelven `View`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-511">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="36e0b-512">`PageResult` es el tipo de valor devuelto predeterminado para un método de controlador.</span><span class="sxs-lookup"><span data-stu-id="36e0b-512">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="36e0b-513">Un método de controlador que devuelve `void` representa la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-513">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="36e0b-514">La propiedad `Customer` usa el atributo `[BindProperty]` para participar en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-514">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="36e0b-515">De forma predeterminada, Razor Pages enlaza propiedades solo con verbos que no sean `GET`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-515">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="36e0b-516">Enlazar a propiedades puede reducir la cantidad de código que se debe escribir.</span><span class="sxs-lookup"><span data-stu-id="36e0b-516">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="36e0b-517">Enlazar reduce el código al usar la misma propiedad para representar los campos de formulario (`<input asp-for="Customer.Name">`) y aceptar la entrada.</span><span class="sxs-lookup"><span data-stu-id="36e0b-517">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="36e0b-518">La página principal (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="36e0b-518">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="36e0b-519">La clase `PageModel` asociada (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="36e0b-519">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="36e0b-520">El archivo *Index.cshtml* contiene el siguiente marcado para crear un vínculo de edición para cada contacto:</span><span class="sxs-lookup"><span data-stu-id="36e0b-520">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="36e0b-521">El `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usado el atributo `asp-route-{value}` para generar un vínculo a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="36e0b-521">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="36e0b-522">El vínculo contiene datos de ruta con el identificador del contacto.</span><span class="sxs-lookup"><span data-stu-id="36e0b-522">The link contains route data with the contact ID.</span></span> <span data-ttu-id="36e0b-523">Por ejemplo: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-523">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="36e0b-524">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="36e0b-524">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="36e0b-525">Los asistentes de etiquetas se habilitan mediante `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="36e0b-525">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="36e0b-526">El archivo *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-526">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="36e0b-527">La primera línea contiene la directiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-527">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="36e0b-528">La restricción de enrutamiento `"{id:int}"` indica a la página que acepte las solicitudes a la página que contienen datos de ruta `int`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-528">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="36e0b-529">Si una solicitud a la página no contiene datos de ruta que se puedan convertir en `int`, el tiempo de ejecución devuelve un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="36e0b-529">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="36e0b-530">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="36e0b-530">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="36e0b-531">El archivo *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-531">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="36e0b-532">El archivo *index.cshtml* también contiene una marca para crear un botón de eliminar para cada contacto de cliente:</span><span class="sxs-lookup"><span data-stu-id="36e0b-532">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="36e0b-533">Al representar dicho botón de eliminar en HTML, `formaction` incluye parámetros para:</span><span class="sxs-lookup"><span data-stu-id="36e0b-533">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="36e0b-534">Id. de contacto de cliente especificado mediante el atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-534">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="36e0b-535">`handler` especificado mediante el atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-535">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="36e0b-536">Aquí tiene un ejemplo de un botón de eliminar representado con un id. de contacto de cliente de `1`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-536">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="36e0b-537">Al seleccionar el botón, se envía una solicitud de formulario `POST` al servidor.</span><span class="sxs-lookup"><span data-stu-id="36e0b-537">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="36e0b-538">De forma predeterminada, el nombre del método de control se selecciona de acuerdo con el valor del parámetro `handler` y según el esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-538">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="36e0b-539">Como en este ejemplo `handler` es `delete`, el método de control `OnPostDeleteAsync` se usa para procesar la solicitud `POST`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-539">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="36e0b-540">Si `asp-page-handler` se establece en otro valor, como `remove`, se seleccionará un método de controlador llamado `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-540">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="36e0b-541">En el código siguiente se muestra el controlador `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-541">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="36e0b-542">El método `OnPostDeleteAsync` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="36e0b-542">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="36e0b-543">Acepta el elemento `id` de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="36e0b-543">Accepts the `id` from the query string.</span></span> <span data-ttu-id="36e0b-544">Si la directiva de página *Index.cshtml* contuviera la restricción de enrutamiento `"{id:int?}"`, `id` provendría de los datos de la ruta.</span><span class="sxs-lookup"><span data-stu-id="36e0b-544">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="36e0b-545">Los datos de la ruta de `id` se especifican en el URI. Por ejemplo, `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-545">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="36e0b-546">Realiza una consulta a la base de datos del contacto de cliente con `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-546">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="36e0b-547">Si encuentra dicho contacto de cliente, se quita de la lista correspondiente.</span><span class="sxs-lookup"><span data-stu-id="36e0b-547">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="36e0b-548">Luego, se actualiza la base de datos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-548">The database is updated.</span></span>
* <span data-ttu-id="36e0b-549">Llama a `RedirectToPage` para redirigir la página Index raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="36e0b-549">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="36e0b-550">Marcado de las propiedades de página según sea necesario</span><span class="sxs-lookup"><span data-stu-id="36e0b-550">Mark page properties as required</span></span>

<span data-ttu-id="36e0b-551">Las propiedades de un valor `PageModel` se pueden marcar con el atributo [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):</span><span class="sxs-lookup"><span data-stu-id="36e0b-551">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="36e0b-552">Para obtener más información, vea [Validación de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="36e0b-552">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="36e0b-553">Control de solicitudes HEAD con un controlador OnGet de reserva</span><span class="sxs-lookup"><span data-stu-id="36e0b-553">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="36e0b-554">Las solicitudes `HEAD` permiten recuperar los encabezados de un recurso específico.</span><span class="sxs-lookup"><span data-stu-id="36e0b-554">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="36e0b-555">A diferencia de las solicitudes `GET`, las solicitudes `HEAD` no devuelven un cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="36e0b-555">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="36e0b-556">Normalmente, se crea un controlador `OnHead` al que se llama para las solicitudes `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-556">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="36e0b-557">En ASP.NET Core 2.1 o versiones posteriores, Razor Pages recurre a una llamada al controlador `OnGet` si no se define ningún controlador `OnHead`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-557">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="36e0b-558">Este comportamiento se habilita mediante la llamada a [SetCompatibilityVersion](xref:mvc/compatibility-version) en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-558">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="36e0b-559">Las plantillas predeterminadas generan la llamada `SetCompatibilityVersion` en ASP.NET Core 2.1 y 2.2.</span><span class="sxs-lookup"><span data-stu-id="36e0b-559">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="36e0b-560">`SetCompatibilityVersion` define con eficacia la opción de Razor Pages `true` como `AllowMappingHeadRequestsToGetHandler`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-560">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="36e0b-561">En lugar de aceptar todos los comportamientos con `SetCompatibilityVersion`, puede aceptar explícitamente comportamientos *específicos*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-561">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="36e0b-562">El código siguiente permite que las solicitudes `HEAD` se asignen al controlador `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-562">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="36e0b-563">XSRF/CSRF y Razor Pages</span><span class="sxs-lookup"><span data-stu-id="36e0b-563">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="36e0b-564">No tiene que escribir ningún código para la [validación antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="36e0b-564">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="36e0b-565">La validación y generación de tokens antifalsificación se incluyen automáticamente en Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="36e0b-565">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="36e0b-566">Usar diseños, parciales, plantillas y asistentes de etiquetas con Razor Pages</span><span class="sxs-lookup"><span data-stu-id="36e0b-566">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="36e0b-567">Las páginas funcionan con todas las características del motor de vista de Razor.</span><span class="sxs-lookup"><span data-stu-id="36e0b-567">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="36e0b-568">Los diseños, parciales, plantillas, asistentes de etiquetas, *_ViewStart.cshtml*, *_ViewImports.cshtml* funcionan de la misma manera que lo hacen con las vistas de Razor convencionales.</span><span class="sxs-lookup"><span data-stu-id="36e0b-568">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="36e0b-569">Para simplificar esta página, aprovecharemos algunas de esas características.</span><span class="sxs-lookup"><span data-stu-id="36e0b-569">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="36e0b-570">Agregue una [página de diseño](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-570">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="36e0b-571">El [diseño](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="36e0b-571">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="36e0b-572">Controla el diseño de cada página (a no ser que la página no tenga diseño).</span><span class="sxs-lookup"><span data-stu-id="36e0b-572">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="36e0b-573">Importa las estructuras HTML como JavaScript y hojas de estilos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-573">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="36e0b-574">Vea [Layout page](xref:mvc/views/layout) (Página de diseño) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="36e0b-574">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="36e0b-575">La propiedad [Layout](xref:mvc/views/layout#specifying-a-layout) se establece en *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-575">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="36e0b-576">El diseño está en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-576">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="36e0b-577">Las páginas buscan otras vistas (diseños, plantillas, parciales) de forma jerárquica, a partir de la misma carpeta que la página actual.</span><span class="sxs-lookup"><span data-stu-id="36e0b-577">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="36e0b-578">Un diseño en la carpeta *Pages/Shared* se puede usar desde cualquier página de Razor en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-578">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="36e0b-579">El archivo de diseño debería ir en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-579">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="36e0b-580">Le recomendamos que **no** coloque el archivo de diseño en la carpeta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-580">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="36e0b-581">*Views/Shared* es un patrón de vistas de MVC.</span><span class="sxs-lookup"><span data-stu-id="36e0b-581">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="36e0b-582">Razor Pages está diseñado para basarse en la jerarquía de carpetas, no en las convenciones de ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="36e0b-582">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="36e0b-583">La búsqueda de vistas de instancia de Razor Pages incluye la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-583">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="36e0b-584">Los diseños, plantillas y parciales que usa con los controladores de MVC y las vistas de Razor convencionales *simplemente funcionan*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-584">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="36e0b-585">Agregue un archivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-585">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="36e0b-586">`@namespace` se explica más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="36e0b-586">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="36e0b-587">La directiva `@addTagHelper` pone los [asistentes de etiquetas integradas](xref:mvc/views/tag-helpers/builtin-th/Index) en todas las páginas de la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-587">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="36e0b-588">Cuando la directiva `@namespace` se usa explícitamente en una página:</span><span class="sxs-lookup"><span data-stu-id="36e0b-588">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="36e0b-589">La directiva establece el espacio de nombres de la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-589">The directive sets the namespace for the page.</span></span> <span data-ttu-id="36e0b-590">La directiva `@model` no necesita incluir el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="36e0b-590">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="36e0b-591">Cuando la directiva `@namespace` se encuentra en *_ViewImports.cshtml*, el espacio de nombres especificado proporciona el prefijo del espacio de nombres generado en la página que importa la directiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-591">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="36e0b-592">El resto del espacio de nombres generado (la parte del sufijo) es la ruta de acceso relativa separada por puntos entre la carpeta que contiene *_ViewImports.cshtml* y la carpeta que contiene la página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-592">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="36e0b-593">Por ejemplo, la clase `PageModel`*Pages/Customers/Edit.cshtml.cs* establece explícitamente el espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="36e0b-593">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="36e0b-594">El archivo *Pages/_ViewImports.cshtml* establece el espacio de nombres siguiente:</span><span class="sxs-lookup"><span data-stu-id="36e0b-594">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="36e0b-595">El espacio de nombres generado para la página de Razor *Pages/Customers/Edit.cshtml* es el mismo que la clase `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-595">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="36e0b-596">`@namespace` *también funciona con vistas de Razor convencionales.*</span><span class="sxs-lookup"><span data-stu-id="36e0b-596">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="36e0b-597">El archivo de vista *Pages/Create.cshtml* original:</span><span class="sxs-lookup"><span data-stu-id="36e0b-597">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="36e0b-598">El archivo de vista *Pages/Create.cshtml* actualizado:</span><span class="sxs-lookup"><span data-stu-id="36e0b-598">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="36e0b-599">El [proyecto de inicio de Razor Pages](#rpvs17) contiene *Pages/_ValidationScriptsPartial.cshtml*, que enlaza la validación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="36e0b-599">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="36e0b-600">Para más información sobre las vistas parciales, vea <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="36e0b-600">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="36e0b-601">Generación de direcciones URL para las páginas</span><span class="sxs-lookup"><span data-stu-id="36e0b-601">URL generation for Pages</span></span>

<span data-ttu-id="36e0b-602">La página `Create`, mostrada anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-602">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="36e0b-603">La aplicación tiene la siguiente estructura de archivos o carpetas:</span><span class="sxs-lookup"><span data-stu-id="36e0b-603">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="36e0b-604">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="36e0b-604">*/Pages*</span></span>

  * <span data-ttu-id="36e0b-605">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-605">*Index.cshtml*</span></span>
  * <span data-ttu-id="36e0b-606">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="36e0b-606">*/Customers*</span></span>

    * <span data-ttu-id="36e0b-607">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-607">*Create.cshtml*</span></span>
    * <span data-ttu-id="36e0b-608">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-608">*Edit.cshtml*</span></span>
    * <span data-ttu-id="36e0b-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="36e0b-609">*Index.cshtml*</span></span>

<span data-ttu-id="36e0b-610">Las páginas *Pages/Customers/Create.cshtml* y *Pages/Customers/Edit.cshtml* redirigen a *Pages/Index.cshtml* si se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="36e0b-610">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="36e0b-611">La cadena `/Index` forma parte del URI para tener acceso a la página anterior.</span><span class="sxs-lookup"><span data-stu-id="36e0b-611">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="36e0b-612">La cadena `/Index` puede usarse para generar los URI para la página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-612">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="36e0b-613">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="36e0b-613">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="36e0b-614">El nombre de página es la ruta de acceso a la página de la carpeta raíz */Pages*, incluido un `/` inicial, por ejemplo `/Index`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-614">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="36e0b-615">Los ejemplos anteriores de generación de URL ofrecen opciones mejoradas y capacidades funcionales en comparación con la escritura a mano de estas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-615">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="36e0b-616">La generación de direcciones URL usa el [enrutamiento](xref:mvc/controllers/routing) y puede generar y codificar parámetros según cómo se defina la ruta en la ruta de acceso de destino.</span><span class="sxs-lookup"><span data-stu-id="36e0b-616">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="36e0b-617">La generación de direcciones URL para las páginas admite nombres relativos.</span><span class="sxs-lookup"><span data-stu-id="36e0b-617">URL generation for pages supports relative names.</span></span> <span data-ttu-id="36e0b-618">En la siguiente tabla, se muestra qué página de índice está seleccionada con diferentes parámetros `RedirectToPage` de *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36e0b-618">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="36e0b-619">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="36e0b-619">RedirectToPage(x)</span></span>| <span data-ttu-id="36e0b-620">Página</span><span class="sxs-lookup"><span data-stu-id="36e0b-620">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="36e0b-621">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="36e0b-621">RedirectToPage("/Index")</span></span> | <span data-ttu-id="36e0b-622">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="36e0b-622">*Pages/Index*</span></span> |
| <span data-ttu-id="36e0b-623">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="36e0b-623">RedirectToPage("./Index");</span></span> | <span data-ttu-id="36e0b-624">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="36e0b-624">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="36e0b-625">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="36e0b-625">RedirectToPage("../Index")</span></span> | <span data-ttu-id="36e0b-626">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="36e0b-626">*Pages/Index*</span></span> |
| <span data-ttu-id="36e0b-627">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="36e0b-627">RedirectToPage("Index")</span></span>  | <span data-ttu-id="36e0b-628">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="36e0b-628">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="36e0b-629">`RedirectToPage("Index")`, `RedirectToPage("./Index")` y `RedirectToPage("../Index")` son *nombres relativos*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-629">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="36e0b-630">El parámetro `RedirectToPage` se *combina* con la ruta de acceso de la página actual para calcular el nombre de la página de destino.</span><span class="sxs-lookup"><span data-stu-id="36e0b-630">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="36e0b-631">Vincular el nombre relativo es útil al crear sitios con una estructura compleja.</span><span class="sxs-lookup"><span data-stu-id="36e0b-631">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="36e0b-632">Si usa nombres relativos para vincular entre páginas en una carpeta, puede cambiar el nombre de esa carpeta.</span><span class="sxs-lookup"><span data-stu-id="36e0b-632">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="36e0b-633">Todos los vínculos seguirán funcionando (porque no incluyen el nombre de carpeta).</span><span class="sxs-lookup"><span data-stu-id="36e0b-633">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="36e0b-634">Para redirigir a una página en otra [área](xref:mvc/controllers/areas), especifique el área:</span><span class="sxs-lookup"><span data-stu-id="36e0b-634">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="36e0b-635">Para obtener más información, vea <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="36e0b-635">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="36e0b-636">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="36e0b-636">ViewData attribute</span></span>

<span data-ttu-id="36e0b-637">Se pueden pasar datos a una página con [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="36e0b-637">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="36e0b-638">Los valores de las propiedades de los controladores o los modelos de Razor Pages con el atributo `[ViewData]` se almacenan y cargan desde [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="36e0b-638">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="36e0b-639">En el ejemplo siguiente, el valor `AboutModel` contiene una propiedad `Title` marcada con `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-639">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="36e0b-640">La propiedad `Title` se establece en el título de la página Acerca de:</span><span class="sxs-lookup"><span data-stu-id="36e0b-640">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="36e0b-641">En la página Acerca de, acceda a la propiedad `Title` como propiedad de modelo:</span><span class="sxs-lookup"><span data-stu-id="36e0b-641">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="36e0b-642">En el diseño, el título se lee desde el diccionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="36e0b-642">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="36e0b-643">TempData</span><span class="sxs-lookup"><span data-stu-id="36e0b-643">TempData</span></span>

<span data-ttu-id="36e0b-644">ASP.NET Core expone la propiedad [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) en un [controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="36e0b-644">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="36e0b-645">Esta propiedad almacena datos hasta que se leen.</span><span class="sxs-lookup"><span data-stu-id="36e0b-645">This property stores data until it's read.</span></span> <span data-ttu-id="36e0b-646">Los métodos `Keep` y `Peek` se pueden usar para examinar los datos sin que se eliminen.</span><span class="sxs-lookup"><span data-stu-id="36e0b-646">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="36e0b-647">`TempData` es útil para el redireccionamiento cuando se necesitan los datos de más de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="36e0b-647">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="36e0b-648">El siguiente código establece el valor de `Message` mediante `TempData`:</span><span class="sxs-lookup"><span data-stu-id="36e0b-648">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="36e0b-649">El siguiente marcado en el archivo *Pages/Customers/Index.cshtml* muestra el valor de `Message` mediante `TempData`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-649">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="36e0b-650">El modelo de página *Pages/Customers/Index.cshtml.cs* aplica el atributo `[TempData]` a la propiedad `Message`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-650">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="36e0b-651">Para obtener más información, vea [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="36e0b-651">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="36e0b-652">Varios controladores por página</span><span class="sxs-lookup"><span data-stu-id="36e0b-652">Multiple handlers per page</span></span>

<span data-ttu-id="36e0b-653">En la página siguiente se usa el asistente de etiquetas `asp-page-handler` para generar marcado para dos controladores:</span><span class="sxs-lookup"><span data-stu-id="36e0b-653">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="36e0b-654">El formulario del ejemplo anterior tiene dos botones de envío, y cada uno de ellos usa `FormActionTagHelper` para enviar a una dirección URL diferente.</span><span class="sxs-lookup"><span data-stu-id="36e0b-654">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="36e0b-655">El atributo `asp-page-handler` es un complemento de `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-655">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="36e0b-656">`asp-page-handler` genera direcciones URL que envían a cada uno de los métodos de controlador definidos por una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-656">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="36e0b-657">`asp-page` no se especifica porque el ejemplo se vincula a la página actual.</span><span class="sxs-lookup"><span data-stu-id="36e0b-657">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="36e0b-658">Modelo de página:</span><span class="sxs-lookup"><span data-stu-id="36e0b-658">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="36e0b-659">El código anterior usa *métodos de controlador con nombre*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-659">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="36e0b-660">Los métodos de controlador con nombre se crean tomando el texto en el nombre después de `On<HTTP Verb>` y antes de `Async` (si existe).</span><span class="sxs-lookup"><span data-stu-id="36e0b-660">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="36e0b-661">En el ejemplo anterior, los métodos de página son OnPost **JoinList** Async y OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="36e0b-661">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="36e0b-662">Quitando *OnPost* y *Async*, los nombres de controlador son `JoinList` y `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-662">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="36e0b-663">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-663">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="36e0b-664">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-664">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="36e0b-665">Rutas personalizadas</span><span class="sxs-lookup"><span data-stu-id="36e0b-665">Custom routes</span></span>

<span data-ttu-id="36e0b-666">Use la directiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="36e0b-666">Use the `@page` directive to:</span></span>

* <span data-ttu-id="36e0b-667">Especificar una ruta personalizada a una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-667">Specify a custom route to a page.</span></span> <span data-ttu-id="36e0b-668">Por ejemplo, la ruta a la página Acerca de se puede establecer en `/Some/Other/Path` con `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-668">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="36e0b-669">Anexar segmentos a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-669">Append segments to a page's default route.</span></span> <span data-ttu-id="36e0b-670">Por ejemplo, se puede agregar un segmento "item" a la ruta predeterminada de una página con `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-670">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="36e0b-671">Anexar parámetros a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="36e0b-671">Append parameters to a page's default route.</span></span> <span data-ttu-id="36e0b-672">Por ejemplo, un parámetro de identificador, `id`, puede ser necesario para una página con `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-672">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="36e0b-673">Se admite una ruta de acceso relativa raíz designada por una tilde (`~`) al principio de la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="36e0b-673">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="36e0b-674">Por ejemplo, `@page "~/Some/Other/Path"` es lo mismo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-674">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="36e0b-675">Si no le gusta la cadena de consulta `?handler=JoinList` en la dirección URL, puede cambiar la ruta para poner el nombre del controlador en la parte de la ruta de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="36e0b-675">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="36e0b-676">Para personalizar la ruta, se puede agregar una plantilla de ruta entre comillas dobles después de la directiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-676">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="36e0b-677">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-677">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="36e0b-678">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="36e0b-678">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="36e0b-679">El signo `?` que sigue a `handler` significa que el parámetro de ruta es opcional.</span><span class="sxs-lookup"><span data-stu-id="36e0b-679">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="36e0b-680">Valores de configuración</span><span class="sxs-lookup"><span data-stu-id="36e0b-680">Configuration and settings</span></span>

<span data-ttu-id="36e0b-681">Para configurar opciones avanzadas, use el método de extensión `AddRazorPagesOptions` en el generador de MVC:</span><span class="sxs-lookup"><span data-stu-id="36e0b-681">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="36e0b-682">Actualmente, puede usar `RazorPagesOptions` para establecer el directorio raíz de páginas, o agregar convenciones de modelo de aplicación a las páginas.</span><span class="sxs-lookup"><span data-stu-id="36e0b-682">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="36e0b-683">En el futuro habilitaremos más extensibilidad de este modo.</span><span class="sxs-lookup"><span data-stu-id="36e0b-683">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="36e0b-684">Para precompilar vistas, consulte la sección sobre la [compilación de vistas de Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="36e0b-684">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="36e0b-685">[Descargue o vea el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="36e0b-685">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="36e0b-686">Consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start), que se basa en esta introducción.</span><span class="sxs-lookup"><span data-stu-id="36e0b-686">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="36e0b-687">Especificación de Razor Pages en la raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="36e0b-687">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="36e0b-688">De forma predeterminada, Razor Pages se encuentra en la raíz del directorio */Pages*.</span><span class="sxs-lookup"><span data-stu-id="36e0b-688">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="36e0b-689">Agregue [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que Razor Pages se encuentra en la [raíz del contenido](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="36e0b-689">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="36e0b-690">Especificación de Razor Pages en un directorio raíz personalizado</span><span class="sxs-lookup"><span data-stu-id="36e0b-690">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="36e0b-691">Agregue [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que Razor Pages se encuentra en un directorio raíz personalizado de la aplicación (proporcione la ruta de acceso relativa):</span><span class="sxs-lookup"><span data-stu-id="36e0b-691">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="36e0b-692">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="36e0b-692">Additional resources</span></span>

* [<span data-ttu-id="36e0b-693">Autorización de atributos y Razor Pages</span><span class="sxs-lookup"><span data-stu-id="36e0b-693">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* [<span data-ttu-id="36e0b-694">Referencia sobre la sintaxis de Razor para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36e0b-694">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
