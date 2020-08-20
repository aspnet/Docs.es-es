---
title: Introducción a Razor Pages en ASP.NET Core
author: Rick-Anderson
description: Obtenga información sobre cómo Razor Pages en ASP.NET Core facilita la programación en escenarios centrados en páginas y mejora la productividad en comparación con el uso de MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: 8058cc3c3461d72c713c8be53c3667112adb2ae5
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634129"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="e2cdf-103">Introducción a Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e2cdf-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e2cdf-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="e2cdf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="e2cdf-105">Razor Pages facilita la programación de escenarios centrados en páginas y hace que resulte más productiva que con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="e2cdf-106">Si busca un tutorial que use el enfoque Model-View-Controller, consulte [Introducción a ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="e2cdf-107">En este documento se proporciona una introducción a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="e2cdf-108">No es un tutorial paso a paso.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="e2cdf-109">Si encuentra que alguna sección es demasiado avanzada, consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="e2cdf-110">Para obtener información general de ASP.NET Core, vea [Introducción a ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e2cdf-111">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="e2cdf-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2cdf-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2cdf-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2cdf-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2cdf-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2cdf-114">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e2cdf-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="e2cdf-115">Crear un proyecto de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e2cdf-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2cdf-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2cdf-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2cdf-117">Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2cdf-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2cdf-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e2cdf-119">Ejecute `dotnet new webapp` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2cdf-120">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e2cdf-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2cdf-121">Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="e2cdf-122">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e2cdf-122">Razor Pages</span></span>

<span data-ttu-id="e2cdf-123">Razor Pages está habilitado en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="e2cdf-124">Considere la posibilidad de una página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="e2cdf-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="e2cdf-125">El código anterior se parece mucho a un [archivo de vista de Razor](xref:tutorials/first-mvc-app/adding-view) que se utiliza en una aplicación ASP.NET Core con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="e2cdf-126">La directiva [`@page`](xref:mvc/views/razor#page) lo hace diferente.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="e2cdf-127">`@page` transforma el archivo en una acción de MVC, lo que significa que administra las solicitudes directamente, sin tener que pasar a través de un controlador.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="e2cdf-128">`@page` debe ser la primera directiva de Razorde una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="e2cdf-129">`@page` afecta al comportamiento de otras construcciones de [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="e2cdf-130">Los nombres de archivo de Razor Pages tienen el sufijo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="e2cdf-131">Una página similar, con una clase `PageModel`, se muestra en los dos archivos siguientes.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="e2cdf-132">El archivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="e2cdf-133">Modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="e2cdf-134">Por convención, el archivo de clase `PageModel` tiene el mismo nombre que el archivo de Razor Pages con *.cs* anexado.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="e2cdf-135">Por ejemplo, la instancia de Razor Pages anterior es *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="e2cdf-136">El archivo que contiene la clase `PageModel` se denomina *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="e2cdf-137">Las asociaciones de rutas de dirección URL a páginas se determinan según la ubicación de la página en el sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="e2cdf-138">En la tabla siguiente, se muestra una ruta de acceso Razor Pages y la dirección URL correspondiente:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="e2cdf-139">Ruta de acceso y nombre de archivo</span><span class="sxs-lookup"><span data-stu-id="e2cdf-139">File name and path</span></span>               | <span data-ttu-id="e2cdf-140">URL correspondiente</span><span class="sxs-lookup"><span data-stu-id="e2cdf-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="e2cdf-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="e2cdf-142">`/` o `/Index`</span><span class="sxs-lookup"><span data-stu-id="e2cdf-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="e2cdf-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="e2cdf-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="e2cdf-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="e2cdf-146">`/Store` o `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="e2cdf-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="e2cdf-147">Notas:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-147">Notes:</span></span>

* <span data-ttu-id="e2cdf-148">El entorno de ejecución busca archivos de páginas de Razor en la carpeta *Pages* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="e2cdf-149">`Index` es la página predeterminada cuando una URL no incluye una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="e2cdf-150">Escritura de un formulario básico</span><span class="sxs-lookup"><span data-stu-id="e2cdf-150">Write a basic form</span></span>

<span data-ttu-id="e2cdf-151">Razor Pages está diseñado para facilitar la implementación de patrones comunes que se usan con exploradores web al compilar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="e2cdf-152">Los [enlaces de modelos](xref:mvc/models/model-binding), los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los asistentes de HTML *simplemente funcionan* con las propiedades definidas en una clase de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="e2cdf-153">Considere la posibilidad de una página que implementa un formulario básico del estilo "Póngase en contacto con nosotros" para el modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="e2cdf-154">Para los ejemplos de este documento, `DbContext` se inicializa en el archivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="e2cdf-155">La base de datos en memoria requiere el paquete NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-155">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="e2cdf-156">El modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-156">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="e2cdf-157">El contexto de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-157">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="e2cdf-158">El archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="e2cdf-159">Modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="e2cdf-160">Por convención, la clase `PageModel` se denomina `<PageName>Model` y se encuentra en el mismo espacio de nombres que la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="e2cdf-161">La clase `PageModel` permite la separación de la lógica de una página de su presentación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="e2cdf-162">Define los controladores de página para solicitudes que se envían a la página y los datos que usan para representar la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="e2cdf-163">Esta separación permite lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-163">This separation allows:</span></span>

* <span data-ttu-id="e2cdf-164">Administración de dependencias de página mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="e2cdf-165">Pruebas unitarias</span><span class="sxs-lookup"><span data-stu-id="e2cdf-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="e2cdf-166">La página tiene un *método de controlador* `OnPostAsync`, que se ejecuta en solicitudes `POST` (cuando un usuario envía el formulario).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="e2cdf-167">Se pueden agregar métodos de controlador para cualquier verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="e2cdf-168">Los controladores más comunes son:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-168">The most common handlers are:</span></span>

* <span data-ttu-id="e2cdf-169">`OnGet` para inicializar el estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="e2cdf-170">En el código anterior, el método `OnGet` muestra la instancia *CreateModel.cshtml* de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="e2cdf-171">`OnPost` para controlar los envíos del formulario.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="e2cdf-172">El sufijo de nombre `Async` es opcional, pero se usa a menudo por convención para funciones asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="e2cdf-173">El código anterior es típico de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="e2cdf-174">Si está familiarizado con las aplicaciones de ASP.NET con controladores y vistas:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="e2cdf-175">El código `OnPostAsync` del ejemplo anterior es similar al típico código de controlador.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="e2cdf-176">La mayoría de los primitivos de MVC, como el [enlace de modelos](xref:mvc/models/model-binding), la [validación](xref:mvc/models/validation) y los resultados de acciones, funcionan del mismo modo con los controladores y Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="e2cdf-177">El método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-177">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="e2cdf-178">El flujo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="e2cdf-179">Compruebe los errores de validación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-179">Check for validation errors.</span></span>

* <span data-ttu-id="e2cdf-180">Si no hay ningún error, guarde los datos y redirija.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="e2cdf-181">Si hay errores, muestre la página de nuevo con mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="e2cdf-182">En muchos casos, los errores de validación se detectan en el cliente y nunca se envían al servidor.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="e2cdf-183">El archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="e2cdf-184">El código HTML representado de *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="e2cdf-185">En el código anterior, la publicación del formulario:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="e2cdf-186">Con datos válidos:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-186">With valid data:</span></span>

  * <span data-ttu-id="e2cdf-187">El método del controlador `OnPostAsync` llama al método auxiliar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="e2cdf-188">`RedirectToPage` devuelve una instancia de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="e2cdf-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="e2cdf-190">Es el resultado de una acción.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-190">Is an action result.</span></span>
    * <span data-ttu-id="e2cdf-191">Es similar a `RedirectToAction` o `RedirectToRoute` (se usa en controladores y vistas).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="e2cdf-192">Se ha personalizado para las páginas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-192">Is customized for pages.</span></span> <span data-ttu-id="e2cdf-193">En el ejemplo anterior, redirige a la página de índice raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="e2cdf-194">`RedirectToPage` se detalla en la sección [Generación de direcciones URL para las páginas](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="e2cdf-195">Con errores de validación que se pasan al servidor:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="e2cdf-196">El método del controlador `OnPostAsync` llama al método auxiliar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="e2cdf-197">`Page` devuelve una instancia de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="e2cdf-198">Devolver `Page` es similar a cómo las acciones en los controladores devuelven `View`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="e2cdf-199">`PageResult` es el tipo de valor devuelto predeterminado para un método de controlador.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="e2cdf-200">Un método de controlador que devuelve `void` representa la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="e2cdf-201">En el ejemplo anterior, la publicación del formulario sin valores hace que se devuelva [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) como false.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="e2cdf-202">En este ejemplo, no se muestra ningún error de validación en el cliente.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="e2cdf-203">La entrega de errores de validación se trata más adelante en este documento.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-203">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="e2cdf-204">Con errores de validación detectados mediante la validación del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="e2cdf-205">Los datos **no** se publican en el servidor.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="e2cdf-206">La validación del lado cliente se explica más adelante en este documento.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="e2cdf-207">La propiedad `Customer` usa el atributo [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) para participar en el enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="e2cdf-208">`[BindProperty]`**no** debe usarse en modelos que contengan propiedades que el cliente no debe cambiar.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="e2cdf-209">Para más información, consulte [Publicación excesiva](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="e2cdf-210">De forma predeterminada, Razor Pages enlaza propiedades solo con verbos que no sean `GET`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="e2cdf-211">El enlace a propiedades elimina la necesidad de escribir código para convertir los datos HTTP en el tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="e2cdf-212">Enlazar reduce el código al usar la misma propiedad para representar los campos de formulario (`<input asp-for="Customer.Name">`) y aceptar la entrada.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="e2cdf-213">Revisión del archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="e2cdf-214">En el código anterior, el [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` enlaza el elemento `<input>` HTML con la expresión del modelo `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="e2cdf-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) hace que los asistentes de etiquetas estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="e2cdf-216">La página principal</span><span class="sxs-lookup"><span data-stu-id="e2cdf-216">The home page</span></span>

<span data-ttu-id="e2cdf-217">*Index.cshtml* es la página principal:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="e2cdf-218">La clase `PageModel` asociada (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="e2cdf-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="e2cdf-219">El archivo *Index.cshtml* contiene el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="e2cdf-220">El `<a /a>` [asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usado el atributo `asp-route-{value}` para generar un vínculo a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="e2cdf-221">El vínculo contiene datos de ruta con el identificador del contacto.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="e2cdf-222">Por ejemplo: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="e2cdf-223">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="e2cdf-224">El archivo *index.cshtml* contiene marcado para crear un botón de eliminar para cada contacto de cliente:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="e2cdf-225">El código HTML representado:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="e2cdf-226">Al representar el botón de eliminar en HTML, el elemento [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) incluye parámetros para los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="e2cdf-227">Id. de contacto de cliente especificado mediante el atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="e2cdf-228">`handler` especificado mediante el atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="e2cdf-229">Al seleccionar el botón, se envía una solicitud de formulario `POST` al servidor.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="e2cdf-230">De forma predeterminada, el nombre del método de control se selecciona de acuerdo con el valor del parámetro `handler` y según el esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="e2cdf-231">Como en este ejemplo `handler` es `delete`, el método de control `OnPostDeleteAsync` se usa para procesar la solicitud `POST`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="e2cdf-232">Si `asp-page-handler` se establece en otro valor, como `remove`, se seleccionará un método de controlador llamado `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="e2cdf-233">El método `OnPostDeleteAsync` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="e2cdf-234">Obtiene el elemento `id` de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="e2cdf-235">Realiza una consulta a la base de datos del contacto de cliente con `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="e2cdf-236">Si se encuentra el contacto del cliente, este se quita y se actualiza la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="e2cdf-237">Llama a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> para redirigir la página Index raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="e2cdf-238">El archivo Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="e2cdf-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="e2cdf-239">La primera línea contiene la directiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="e2cdf-240">La restricción de enrutamiento `"{id:int}"` indica a la página que acepte las solicitudes a la página que contienen datos de ruta `int`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="e2cdf-241">Si una solicitud a la página no contiene datos de ruta que se puedan convertir en `int`, el tiempo de ejecución devuelve un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="e2cdf-242">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="e2cdf-243">El archivo *Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-243">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="e2cdf-244">Validación</span><span class="sxs-lookup"><span data-stu-id="e2cdf-244">Validation</span></span>

<span data-ttu-id="e2cdf-245">Reglas de validación:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-245">Validation rules:</span></span>

* <span data-ttu-id="e2cdf-246">Se especifican mediante declaración en la clase de modelo.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="e2cdf-247">Se aplican en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="e2cdf-248">El espacio de nombres <xref:System.ComponentModel.DataAnnotations> proporciona un conjunto de atributos de validación integrados que se aplican mediante declaración a una clase o propiedad.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="e2cdf-249">DataAnnotations también contiene atributos de formato como [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute), que ayudan a aplicar formato y no proporcionan ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="e2cdf-250">Considere el modelo `Customer`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-250">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="e2cdf-251">Con el siguiente archivo de vista *Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="e2cdf-252">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-252">The preceding code:</span></span>

* <span data-ttu-id="e2cdf-253">Incluye scripts de validación de jQuery y jQuery.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="e2cdf-254">Usa los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) `<div />` y `<span />` para habilitar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="e2cdf-255">Validación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-255">Client-side validation.</span></span>
  * <span data-ttu-id="e2cdf-256">Representación del error de validación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-256">Validation error rendering.</span></span>

* <span data-ttu-id="e2cdf-257">Se genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="e2cdf-258">Al publicar el formulario de creación sin un valor de nombre, se muestra el mensaje de error "El campo Nombre es obligatorio".</span><span class="sxs-lookup"><span data-stu-id="e2cdf-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="e2cdf-259">en el formulario.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-259">on the form.</span></span> <span data-ttu-id="e2cdf-260">Si JavaScript está habilitado en el cliente, el explorador muestra el error sin realizar la publicación en el servidor.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="e2cdf-261">El atributo `[StringLength(10)]` genera `data-val-length-max="10"` en el código HTML representado.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="e2cdf-262">`data-val-length-max` impide que los exploradores superen la longitud máxima especificada al escribir.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="e2cdf-263">Si se usa una herramienta como [Fiddler](https://www.telerik.com/fiddler) para editar y reproducir la publicación:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="e2cdf-264">Con el nombre de más de 10 caracteres.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-264">With the name longer than 10.</span></span>
* <span data-ttu-id="e2cdf-265">Se devolverá el mensaje de error "El nombre del campo debe ser una cadena con una longitud máxima de 10 caracteres".</span><span class="sxs-lookup"><span data-stu-id="e2cdf-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="e2cdf-266">.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-266">is returned.</span></span>

<span data-ttu-id="e2cdf-267">Considere el modelo `Movie` siguiente:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="e2cdf-268">Los atributos de validación especifican el comportamiento que se exigirá a las propiedades del modelo al que se aplican:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="e2cdf-269">Los atributos `Required` y `MinimumLength` indican que una propiedad debe tener un valor, pero nada impide al usuario escribir espacios en blanco para satisfacer esta validación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="e2cdf-270">El atributo `RegularExpression` se usa para limitar los caracteres que se pueden escribir.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="e2cdf-271">En el código anterior, "Género":</span><span class="sxs-lookup"><span data-stu-id="e2cdf-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="e2cdf-272">Solo debe usar letras.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-272">Must only use letters.</span></span>
  * <span data-ttu-id="e2cdf-273">La primera letra debe estar en mayúsculas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="e2cdf-274">No se permiten espacios en blanco, números ni caracteres especiales.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="e2cdf-275">La "Clasificación" de `RegularExpression`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="e2cdf-276">Requiere que el primer carácter sea una letra mayúscula.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="e2cdf-277">Permite caracteres especiales y números en los espacios posteriores.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="e2cdf-278">"PG-13" es válido para una "Clasificación", pero se produce un error en un "Género".</span><span class="sxs-lookup"><span data-stu-id="e2cdf-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="e2cdf-279">El atributo `Range` restringe un valor a un intervalo determinado.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="e2cdf-280">El atributo `StringLength` establece la longitud máxima de una propiedad de cadena y, opcionalmente, su longitud mínima.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="e2cdf-281">Los tipos de valor (como `decimal`, `int`, `float`, `DateTime`) son intrínsecamente necesarios y no necesitan el atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="e2cdf-282">La página de creación del modelo `Movie` muestra errores de visualización con valores no válidos:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulario de vista de película con varios errores de validación de cliente de jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="e2cdf-284">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-284">For more information, see:</span></span>

* [<span data-ttu-id="e2cdf-285">Adición de validación a la aplicación Película</span><span class="sxs-lookup"><span data-stu-id="e2cdf-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="e2cdf-286">[Validación de modelos en ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="e2cdf-287">Control de solicitudes HEAD con un controlador OnGet de reserva</span><span class="sxs-lookup"><span data-stu-id="e2cdf-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="e2cdf-288">Las solicitudes `HEAD` permiten recuperar los encabezados de un recurso específico.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="e2cdf-289">A diferencia de las solicitudes `GET`, las solicitudes `HEAD` no devuelven un cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="e2cdf-290">Normalmente, se crea un controlador `OnHead` al que se llama para las solicitudes `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="e2cdf-291">Razor Pages recurre a una llamada al controlador `OnGet` si no se define ningún controlador `OnHead`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="e2cdf-292">XSRF/CSRF y Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e2cdf-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="e2cdf-293">Razor Pages está protegido mediante [validación antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="e2cdf-294">El elemento [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) inserta tokens antifalsificación en los elementos de formulario HTML.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="e2cdf-295">Usar diseños, parciales, plantillas y asistentes de etiquetas con Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e2cdf-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="e2cdf-296">Las páginas funcionan con todas las características del motor de vista de Razor.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="e2cdf-297">Los diseños, parciales, plantillas, asistentes de etiquetas, *_ViewStart.cshtml* y *_ViewImports.cshtml* funcionan de la misma manera que lo hacen con las vistas de Razor convencionales.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="e2cdf-298">Para simplificar esta página, aprovecharemos algunas de esas características.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="e2cdf-299">Agregue una [página de diseño](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="e2cdf-300">El [diseño](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="e2cdf-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="e2cdf-301">Controla el diseño de cada página (a no ser que la página no tenga diseño).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="e2cdf-302">Importa las estructuras HTML como JavaScript y hojas de estilos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="e2cdf-303">El contenido de la página de Razor se representa donde se llama a `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="e2cdf-304">Para más información, consulte la [página de diseño](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="e2cdf-305">La propiedad [Layout](xref:mvc/views/layout#specifying-a-layout) se establece en *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="e2cdf-306">El diseño está en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="e2cdf-307">Las páginas buscan otras vistas (diseños, plantillas, parciales) de forma jerárquica, a partir de la misma carpeta que la página actual.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="e2cdf-308">Un diseño en la carpeta *Pages/Shared* se puede usar desde cualquier página de Razor en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="e2cdf-309">El archivo de diseño debería ir en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="e2cdf-310">Le recomendamos que **no** coloque el archivo de diseño en la carpeta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="e2cdf-311">*Views/Shared* es un patrón de vistas de MVC.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="e2cdf-312">Razor Pages está diseñado para basarse en la jerarquía de carpetas, no en las convenciones de ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="e2cdf-313">La búsqueda de vistas de una instancia de Razor Pages incluye la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="e2cdf-314">Los diseños, plantillas y parciales que se usan con los controladores de MVC y las vistas de Razor convencionales *simplemente funcionan*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="e2cdf-315">Agregue un archivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="e2cdf-316">`@namespace` se explica más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="e2cdf-317">La directiva `@addTagHelper` pone los [asistentes de etiquetas integradas](xref:mvc/views/tag-helpers/builtin-th/Index) en todas las páginas de la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="e2cdf-318">La directiva `@namespace` establecida en una página:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="e2cdf-319">La directiva `@namespace` establece el espacio de nombres de la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="e2cdf-320">La directiva `@model` no necesita incluir el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="e2cdf-321">Cuando la directiva `@namespace` se encuentra en *_ViewImports.cshtml*, el espacio de nombres especificado proporciona el prefijo del espacio de nombres generado en la página que importa la directiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="e2cdf-322">El resto del espacio de nombres generado (la parte del sufijo) es la ruta de acceso relativa separada por puntos entre la carpeta que contiene *_ViewImports.cshtml* y la carpeta que contiene la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="e2cdf-323">Por ejemplo, la clase `PageModel`*Pages/Customers/Edit.cshtml.cs* establece explícitamente el espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="e2cdf-324">El archivo *Pages/_ViewImports.cshtml* establece el espacio de nombres siguiente:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="e2cdf-325">El espacio de nombres generado para la página de Razor *Pages/Customers/Edit.cshtml* es el mismo que la clase `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="e2cdf-326">`@namespace` *también funciona con vistas de Razor convencionales.*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="e2cdf-327">Considere el archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="e2cdf-328">Archivo de vista actualizado *Pages/Create.cshtml* con *_ViewImports.cshtml* y el archivo de distribución anterior:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="e2cdf-329">En el código anterior, el elemento *_ViewImports. cshtml* importó el espacio de nombres y los asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="e2cdf-330">El archivo de distribución importó los archivos JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="e2cdf-331">El [proyecto de inicio de Razor Pages](#rpvs17) contiene *Pages/_ValidationScriptsPartial.cshtml*, que enlaza la validación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="e2cdf-332">Para más información sobre las vistas parciales, vea <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="e2cdf-333">Generación de direcciones URL para las páginas</span><span class="sxs-lookup"><span data-stu-id="e2cdf-333">URL generation for Pages</span></span>

<span data-ttu-id="e2cdf-334">La página `Create`, mostrada anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="e2cdf-335">La aplicación tiene la siguiente estructura de archivos o carpetas:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="e2cdf-336">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-336">*/Pages*</span></span>

  * <span data-ttu-id="e2cdf-337">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="e2cdf-338">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="e2cdf-339">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-339">*/Customers*</span></span>

    * <span data-ttu-id="e2cdf-340">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="e2cdf-341">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="e2cdf-342">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-342">*Index.cshtml*</span></span>

<span data-ttu-id="e2cdf-343">Las páginas *Pages/Customers/Create.cshtml* y *Pages/Customers/Edit.cshtml* redirigen a *Pages/Customers/Index.cshtml* si la operación se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="e2cdf-344">La cadena `./Index` es un nombre de página relativo que se usa para acceder a la página anterior.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="e2cdf-345">Se usa para generar direcciones URL a la página *Pages/Customers/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="e2cdf-346">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="e2cdf-347">El nombre de página absoluto `/Index` se usa para generar direcciones URL a la página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="e2cdf-348">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="e2cdf-349">El nombre de página es la ruta de acceso a la página de la carpeta raíz */Pages*, incluido un `/` inicial, por ejemplo `/Index`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="e2cdf-350">Los ejemplos anteriores de generación de URL ofrecen opciones mejoradas y capacidades funcionales en comparación con la escritura a mano de estas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="e2cdf-351">La generación de direcciones URL usa el [enrutamiento](xref:mvc/controllers/routing) y puede generar y codificar parámetros según cómo se defina la ruta en la ruta de acceso de destino.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="e2cdf-352">La generación de direcciones URL para las páginas admite nombres relativos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="e2cdf-353">En la siguiente tabla, se muestra qué página de índice está seleccionada con diferentes parámetros `RedirectToPage` en *Pages/Customers/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="e2cdf-354">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="e2cdf-354">RedirectToPage(x)</span></span>| <span data-ttu-id="e2cdf-355">Página</span><span class="sxs-lookup"><span data-stu-id="e2cdf-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="e2cdf-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="e2cdf-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="e2cdf-357">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-357">*Pages/Index*</span></span> |
| <span data-ttu-id="e2cdf-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="e2cdf-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="e2cdf-359">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="e2cdf-360">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="e2cdf-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="e2cdf-361">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-361">*Pages/Index*</span></span> |
| <span data-ttu-id="e2cdf-362">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="e2cdf-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="e2cdf-363">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="e2cdf-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")` y `RedirectToPage("../Index")` son *nombres relativos*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="e2cdf-365">El parámetro `RedirectToPage` se *combina* con la ruta de acceso de la página actual para calcular el nombre de la página de destino.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="e2cdf-366">Vincular el nombre relativo es útil al crear sitios con una estructura compleja.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="e2cdf-367">Cuando se usan nombres relativos para el vínculo entre las páginas de una carpeta:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="e2cdf-368">Al cambiar el nombre de una carpeta, no se rompen los vínculos relativos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="e2cdf-369">Los vínculos no se rompen porque no incluyen el nombre de la carpeta.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="e2cdf-370">Para redirigir a una página en otra [área](xref:mvc/controllers/areas), especifique el área:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="e2cdf-371">Para obtener más información, vea <xref:mvc/controllers/areas> y <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="e2cdf-372">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="e2cdf-372">ViewData attribute</span></span>

<span data-ttu-id="e2cdf-373">Se pueden pasar datos a una página con <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="e2cdf-374">Las propiedades con el atributo `[ViewData]` tienen sus valores almacenados y cargados desde el elemento <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="e2cdf-375">En el ejemplo siguiente, el elemento `AboutModel` aplica el atributo `[ViewData]` a la propiedad `Title`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="e2cdf-376">En la página Acerca de, acceda a la propiedad `Title` como propiedad de modelo:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="e2cdf-377">En el diseño, el título se lee desde el diccionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="e2cdf-378">TempData</span><span class="sxs-lookup"><span data-stu-id="e2cdf-378">TempData</span></span>

<span data-ttu-id="e2cdf-379">ASP.NET Core expone el elemento <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="e2cdf-380">Esta propiedad almacena datos hasta que se leen.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-380">This property stores data until it's read.</span></span> <span data-ttu-id="e2cdf-381">Los métodos <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> y <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> se pueden usar para examinar los datos sin que se eliminen.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="e2cdf-382">`TempData` es útil para el redireccionamiento cuando se necesitan los datos de más de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="e2cdf-383">El siguiente código establece el valor de `Message` mediante `TempData`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="e2cdf-384">El siguiente marcado en el archivo *Pages/Customers/Index.cshtml* muestra el valor de `Message` mediante `TempData`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="e2cdf-385">El modelo de página *Pages/Customers/Index.cshtml.cs* aplica el atributo `[TempData]` a la propiedad `Message`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="e2cdf-386">Para más información, consulte [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="e2cdf-387">Varios controladores por página</span><span class="sxs-lookup"><span data-stu-id="e2cdf-387">Multiple handlers per page</span></span>

<span data-ttu-id="e2cdf-388">En la página siguiente se usa el asistente de etiquetas `asp-page-handler` para generar marcado para dos controladores:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="e2cdf-389">El formulario del ejemplo anterior tiene dos botones de envío, y cada uno de ellos usa `FormActionTagHelper` para enviar a una dirección URL diferente.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="e2cdf-390">El atributo `asp-page-handler` es un complemento de `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="e2cdf-391">`asp-page-handler` genera direcciones URL que envían a cada uno de los métodos de controlador definidos por una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="e2cdf-392">`asp-page` no se especifica porque el ejemplo se vincula a la página actual.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="e2cdf-393">Modelo de página:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-393">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="e2cdf-394">El código anterior usa *métodos de controlador con nombre*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="e2cdf-395">Los métodos de controlador con nombre se crean tomando el texto en el nombre después de `On<HTTP Verb>` y antes de `Async` (si existe).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="e2cdf-396">En el ejemplo anterior, los métodos de página son OnPost**JoinList**Async y OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="e2cdf-397">Quitando *OnPost* y *Async*, los nombres de controlador son `JoinList` y `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="e2cdf-398">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="e2cdf-399">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="e2cdf-400">Rutas personalizadas</span><span class="sxs-lookup"><span data-stu-id="e2cdf-400">Custom routes</span></span>

<span data-ttu-id="e2cdf-401">Use la directiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="e2cdf-402">Especificar una ruta personalizada a una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-402">Specify a custom route to a page.</span></span> <span data-ttu-id="e2cdf-403">Por ejemplo, la ruta a la página Acerca de se puede establecer en `/Some/Other/Path` con `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="e2cdf-404">Anexar segmentos a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-404">Append segments to a page's default route.</span></span> <span data-ttu-id="e2cdf-405">Por ejemplo, se puede agregar un segmento "item" a la ruta predeterminada de una página con `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="e2cdf-406">Anexar parámetros a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="e2cdf-407">Por ejemplo, un parámetro de identificador, `id`, puede ser necesario para una página con `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="e2cdf-408">Se admite una ruta de acceso relativa raíz designada por una tilde (`~`) al principio de la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="e2cdf-409">Por ejemplo, `@page "~/Some/Other/Path"` es lo mismo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="e2cdf-410">Si no le gusta la cadena de consulta `?handler=JoinList` en la dirección URL, puede cambiar la ruta para poner el nombre del controlador en la parte de la ruta de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="e2cdf-411">Para personalizar la ruta, se puede agregar una plantilla de ruta entre comillas dobles después de la directiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="e2cdf-412">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="e2cdf-413">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="e2cdf-414">El signo `?` que sigue a `handler` significa que el parámetro de ruta es opcional.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="e2cdf-415">Valores de configuración avanzados</span><span class="sxs-lookup"><span data-stu-id="e2cdf-415">Advanced configuration and settings</span></span>

<span data-ttu-id="e2cdf-416">La mayoría de las aplicaciones no requieren la configuración de las siguientes secciones.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="e2cdf-417">Para configurar las opciones avanzadas, use la sobrecarga <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> que configura <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-417">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="e2cdf-418">Use el elemento <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> para establecer el directorio raíz de páginas o agregar convenciones de modelo de aplicación para las páginas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="e2cdf-419">Para obtener más información sobre las convenciones, vea [Convenciones de autorización de Razor Pages](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="e2cdf-420">Para precompilar vistas, consulte la sección sobre la [compilación de vistas de Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="e2cdf-421">Especificación de Razor Pages en la raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="e2cdf-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="e2cdf-422">De forma predeterminada, Razor Pages se encuentra en la raíz del directorio */Pages*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="e2cdf-423">Agregue <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> para especificar que sus instancias de Razor Pages se encuentran en la [raíz de contenido](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="e2cdf-424">Especificación de Razor Pages en un directorio raíz personalizado</span><span class="sxs-lookup"><span data-stu-id="e2cdf-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="e2cdf-425">Agregue <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> para especificar que Razor Pages se encuentra en un directorio raíz personalizado en la aplicación (proporcione una ruta de acceso relativa):</span><span class="sxs-lookup"><span data-stu-id="e2cdf-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="e2cdf-426">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e2cdf-426">Additional resources</span></span>

* <span data-ttu-id="e2cdf-427">Consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start), que se basa en esta introducción.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="e2cdf-428">Autorización de atributos y Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e2cdf-428">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <span data-ttu-id="e2cdf-429">[Descargue o vea el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-429">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e2cdf-430">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="e2cdf-430">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="e2cdf-431">RazorPages es una nueva característica de ASP.NET Core MVC que facilita la codificación de escenarios centrados en páginas y hace que sea más productiva.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-431">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="e2cdf-432">Si busca un tutorial que use el enfoque Model-View-Controller, consulte [Introducción a ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-432">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="e2cdf-433">En este documento se proporciona una introducción a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-433">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="e2cdf-434">No es un tutorial paso a paso.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-434">It's not a step by step tutorial.</span></span> <span data-ttu-id="e2cdf-435">Si encuentra que alguna sección es demasiado avanzada, consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-435">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="e2cdf-436">Para obtener información general de ASP.NET Core, vea [Introducción a ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-436">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e2cdf-437">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="e2cdf-437">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2cdf-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2cdf-438">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2cdf-439">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2cdf-439">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2cdf-440">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e2cdf-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="e2cdf-441">Crear un proyecto de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e2cdf-441">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2cdf-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2cdf-442">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2cdf-443">Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-443">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2cdf-444">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e2cdf-444">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2cdf-445">Ejecute `dotnet new webapp` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-445">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="e2cdf-446">Abra el archivo *.csproj* generado desde Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-446">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2cdf-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2cdf-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e2cdf-448">Ejecute `dotnet new webapp` desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-448">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="e2cdf-449">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e2cdf-449">Razor Pages</span></span>

<span data-ttu-id="e2cdf-450">Razor Pages está habilitado en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-450">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="e2cdf-451">Considere la posibilidad de una página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="e2cdf-451">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="e2cdf-452">El código anterior se parece mucho a un [archivo de vista de Razor](xref:tutorials/first-mvc-app/adding-view) que se utiliza en una aplicación ASP.NET Core con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-452">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="e2cdf-453">La directiva `@page` lo hace diferente.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-453">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="e2cdf-454">`@page` transforma el archivo en una acción de MVC, lo que significa que administra las solicitudes directamente, sin tener que pasar a través de un controlador.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-454">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="e2cdf-455">`@page` debe ser la primera directiva de Razor de una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-455">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="e2cdf-456">`@page` afecta al comportamiento de otras construcciones de Razor.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-456">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="e2cdf-457">Una página similar, con una clase `PageModel`, se muestra en los dos archivos siguientes.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-457">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="e2cdf-458">El archivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-458">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="e2cdf-459">Modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-459">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="e2cdf-460">Por convención, el archivo de clase `PageModel` tiene el mismo nombre que el archivo de Razor Pages con *.cs* anexado.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-460">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="e2cdf-461">Por ejemplo, la instancia de Razor Pages anterior es *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-461">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="e2cdf-462">El archivo que contiene la clase `PageModel` se denomina *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-462">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="e2cdf-463">Las asociaciones de rutas de dirección URL a páginas se determinan según la ubicación de la página en el sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-463">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="e2cdf-464">En la tabla siguiente, se muestra una ruta de acceso Razor Pages y la dirección URL correspondiente:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-464">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="e2cdf-465">Ruta de acceso y nombre de archivo</span><span class="sxs-lookup"><span data-stu-id="e2cdf-465">File name and path</span></span>               | <span data-ttu-id="e2cdf-466">URL correspondiente</span><span class="sxs-lookup"><span data-stu-id="e2cdf-466">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="e2cdf-467">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-467">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="e2cdf-468">`/` o `/Index`</span><span class="sxs-lookup"><span data-stu-id="e2cdf-468">`/` or `/Index`</span></span> |
| <span data-ttu-id="e2cdf-469">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-469">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="e2cdf-470">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-470">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="e2cdf-471">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-471">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="e2cdf-472">`/Store` o `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="e2cdf-472">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="e2cdf-473">Notas:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-473">Notes:</span></span>

* <span data-ttu-id="e2cdf-474">El entorno de ejecución busca archivos de páginas de Razor en la carpeta *Pages* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-474">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="e2cdf-475">`Index` es la página predeterminada cuando una URL no incluye una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-475">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="e2cdf-476">Escritura de un formulario básico</span><span class="sxs-lookup"><span data-stu-id="e2cdf-476">Write a basic form</span></span>

<span data-ttu-id="e2cdf-477">Razor Pages está diseñado para facilitar la implementación de patrones comunes que se usan con exploradores web al compilar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-477">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="e2cdf-478">Los [enlaces de modelos](xref:mvc/models/model-binding), los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los asistentes de HTML *simplemente funcionan* con las propiedades definidas en una clase de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-478">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="e2cdf-479">Considere la posibilidad de una página que implementa un formulario básico del estilo "Póngase en contacto con nosotros" para el modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-479">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="e2cdf-480">Para los ejemplos de este documento, `DbContext` se inicializa en el archivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-480">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="e2cdf-481">El modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-481">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="e2cdf-482">El contexto de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-482">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="e2cdf-483">El archivo de vista *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-483">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="e2cdf-484">Modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-484">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="e2cdf-485">Por convención, la clase `PageModel` se denomina `<PageName>Model` y se encuentra en el mismo espacio de nombres que la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-485">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="e2cdf-486">La clase `PageModel` permite la separación de la lógica de una página de su presentación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-486">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="e2cdf-487">Define los controladores de página para solicitudes que se envían a la página y los datos que usan para representar la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-487">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="e2cdf-488">Esta separación permite lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-488">This separation allows:</span></span>

* <span data-ttu-id="e2cdf-489">Administración de dependencias de página mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-489">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="e2cdf-490">[Pruebas](xref:test/razor-pages-tests) unitarias de las páginas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-490">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="e2cdf-491">La página tiene un *método de controlador* `OnPostAsync`, que se ejecuta en solicitudes `POST` (cuando un usuario envía el formulario).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-491">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="e2cdf-492">Puede agregar métodos de controlador para cualquier verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-492">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="e2cdf-493">Los controladores más comunes son:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-493">The most common handlers are:</span></span>

* <span data-ttu-id="e2cdf-494">`OnGet` para inicializar el estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-494">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="e2cdf-495">Ejemplo [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-495">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="e2cdf-496">`OnPost` para controlar los envíos del formulario.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-496">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="e2cdf-497">El sufijo de nombre `Async` es opcional, pero se usa a menudo por convención para funciones asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-497">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="e2cdf-498">El código anterior es típico de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-498">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="e2cdf-499">Si está familiarizado con las aplicaciones de ASP.NET con controladores y vistas:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-499">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="e2cdf-500">El código `OnPostAsync` del ejemplo anterior es similar al típico código de controlador.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-500">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="e2cdf-501">La mayoría de los primitivos MVC como el [enlace de modelos](xref:mvc/models/model-binding), la [validación](xref:mvc/models/validation), la [validación](xref:mvc/models/validation) y los resultados de acciones se comparten.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-501">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="e2cdf-502">El método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-502">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="e2cdf-503">El flujo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-503">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="e2cdf-504">Compruebe los errores de validación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-504">Check for validation errors.</span></span>

* <span data-ttu-id="e2cdf-505">Si no hay ningún error, guarde los datos y redirija.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-505">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="e2cdf-506">Si hay errores, muestre la página de nuevo con mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-506">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="e2cdf-507">La validación del lado cliente es idéntica a las aplicaciones de ASP.NET Core MVC tradicionales.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-507">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="e2cdf-508">En muchos casos, los errores de validación se detectan en el cliente y nunca se envían al servidor.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-508">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="e2cdf-509">Cuando los datos se escriben correctamente, el método del controlador `OnPostAsync` llama al método del asistente `RedirectToPage` para devolver una instancia de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-509">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="e2cdf-510">`RedirectToPage` es un resultado de acción nueva, similar a `RedirectToAction` o `RedirectToRoute`, pero personalizada para las páginas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-510">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="e2cdf-511">En el ejemplo anterior, redirige a la página de índice raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-511">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="e2cdf-512">`RedirectToPage` se detalla en la sección [Generación de direcciones URL para las páginas](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-512">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="e2cdf-513">Cuando el formulario enviado tiene errores de validación (que se pasan al servidor), el método del controlador `OnPostAsync` llama al método del asistente `Page`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-513">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="e2cdf-514">`Page` devuelve una instancia de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-514">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="e2cdf-515">Devolver `Page` es similar a cómo las acciones en los controladores devuelven `View`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-515">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="e2cdf-516">`PageResult` es el tipo de valor devuelto predeterminado para un método de controlador.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-516">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="e2cdf-517">Un método de controlador que devuelve `void` representa la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-517">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="e2cdf-518">La propiedad `Customer` usa el atributo `[BindProperty]` para participar en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-518">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="e2cdf-519">De forma predeterminada, Razor Pages enlaza propiedades solo con verbos que no sean `GET`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-519">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="e2cdf-520">Enlazar a propiedades puede reducir la cantidad de código que se debe escribir.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-520">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="e2cdf-521">Enlazar reduce el código al usar la misma propiedad para representar los campos de formulario (`<input asp-for="Customer.Name">`) y aceptar la entrada.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-521">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="e2cdf-522">La página principal (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="e2cdf-522">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="e2cdf-523">La clase `PageModel` asociada (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="e2cdf-523">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="e2cdf-524">El archivo *Index.cshtml* contiene el siguiente marcado para crear un vínculo de edición para cada contacto:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-524">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="e2cdf-525">El `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usado el atributo `asp-route-{value}` para generar un vínculo a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-525">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="e2cdf-526">El vínculo contiene datos de ruta con el identificador del contacto.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-526">The link contains route data with the contact ID.</span></span> <span data-ttu-id="e2cdf-527">Por ejemplo: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-527">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="e2cdf-528">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-528">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="e2cdf-529">Los asistentes de etiquetas se habilitan mediante `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="e2cdf-529">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="e2cdf-530">El archivo *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-530">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="e2cdf-531">La primera línea contiene la directiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-531">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="e2cdf-532">La restricción de enrutamiento `"{id:int}"` indica a la página que acepte las solicitudes a la página que contienen datos de ruta `int`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-532">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="e2cdf-533">Si una solicitud a la página no contiene datos de ruta que se puedan convertir en `int`, el tiempo de ejecución devuelve un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-533">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="e2cdf-534">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-534">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="e2cdf-535">El archivo *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-535">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="e2cdf-536">El archivo *index.cshtml* también contiene una marca para crear un botón de eliminar para cada contacto de cliente:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-536">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="e2cdf-537">Al representar dicho botón de eliminar en HTML, `formaction` incluye parámetros para:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-537">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="e2cdf-538">Id. de contacto de cliente especificado mediante el atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-538">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="e2cdf-539">`handler` especificado mediante el atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-539">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="e2cdf-540">Aquí tiene un ejemplo de un botón de eliminar representado con un id. de contacto de cliente de `1`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-540">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="e2cdf-541">Al seleccionar el botón, se envía una solicitud de formulario `POST` al servidor.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-541">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="e2cdf-542">De forma predeterminada, el nombre del método de control se selecciona de acuerdo con el valor del parámetro `handler` y según el esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-542">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="e2cdf-543">Como en este ejemplo `handler` es `delete`, el método de control `OnPostDeleteAsync` se usa para procesar la solicitud `POST`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-543">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="e2cdf-544">Si `asp-page-handler` se establece en otro valor, como `remove`, se seleccionará un método de controlador llamado `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-544">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="e2cdf-545">En el código siguiente se muestra el controlador `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-545">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="e2cdf-546">El método `OnPostDeleteAsync` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-546">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="e2cdf-547">Acepta el elemento `id` de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-547">Accepts the `id` from the query string.</span></span> <span data-ttu-id="e2cdf-548">Si la directiva de página *Index.cshtml* contuviera la restricción de enrutamiento `"{id:int?}"`, `id` provendría de los datos de la ruta.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-548">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="e2cdf-549">Los datos de la ruta de `id` se especifican en el URI. Por ejemplo, `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-549">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="e2cdf-550">Realiza una consulta a la base de datos del contacto de cliente con `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-550">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="e2cdf-551">Si encuentra dicho contacto de cliente, se quita de la lista correspondiente.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-551">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="e2cdf-552">Luego, se actualiza la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-552">The database is updated.</span></span>
* <span data-ttu-id="e2cdf-553">Llama a `RedirectToPage` para redirigir la página Index raíz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-553">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="e2cdf-554">Marcado de las propiedades de página según sea necesario</span><span class="sxs-lookup"><span data-stu-id="e2cdf-554">Mark page properties as required</span></span>

<span data-ttu-id="e2cdf-555">Las propiedades de un valor `PageModel` se pueden marcar con el atributo [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):</span><span class="sxs-lookup"><span data-stu-id="e2cdf-555">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="e2cdf-556">Para obtener más información, vea [Validación de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-556">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="e2cdf-557">Control de solicitudes HEAD con un controlador OnGet de reserva</span><span class="sxs-lookup"><span data-stu-id="e2cdf-557">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="e2cdf-558">Las solicitudes `HEAD` permiten recuperar los encabezados de un recurso específico.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-558">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="e2cdf-559">A diferencia de las solicitudes `GET`, las solicitudes `HEAD` no devuelven un cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-559">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="e2cdf-560">Normalmente, se crea un controlador `OnHead` al que se llama para las solicitudes `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-560">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="e2cdf-561">En ASP.NET Core 2.1 o versiones posteriores, Razor Pages recurre a una llamada al controlador `OnGet` si no se define ningún controlador `OnHead`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-561">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="e2cdf-562">Este comportamiento se habilita mediante la llamada a [SetCompatibilityVersion](xref:mvc/compatibility-version) en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-562">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="e2cdf-563">Las plantillas predeterminadas generan la llamada `SetCompatibilityVersion` en ASP.NET Core 2.1 y 2.2.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-563">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="e2cdf-564">`SetCompatibilityVersion` define con eficacia la opción de Razor Pages `true` como `AllowMappingHeadRequestsToGetHandler`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-564">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="e2cdf-565">En lugar de aceptar todos los comportamientos con `SetCompatibilityVersion`, puede aceptar explícitamente comportamientos *específicos*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-565">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="e2cdf-566">El código siguiente permite que las solicitudes `HEAD` se asignen al controlador `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-566">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="e2cdf-567">XSRF/CSRF y Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e2cdf-567">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="e2cdf-568">No tiene que escribir ningún código para la [validación antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-568">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="e2cdf-569">La validación y generación de tokens antifalsificación se incluyen automáticamente en Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-569">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="e2cdf-570">Usar diseños, parciales, plantillas y asistentes de etiquetas con Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e2cdf-570">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="e2cdf-571">Las páginas funcionan con todas las características del motor de vista de Razor.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-571">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="e2cdf-572">Los diseños, parciales, plantillas, asistentes de etiquetas, *_ViewStart.cshtml*, *_ViewImports.cshtml* funcionan de la misma manera que lo hacen con las vistas de Razor convencionales.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-572">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="e2cdf-573">Para simplificar esta página, aprovecharemos algunas de esas características.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-573">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="e2cdf-574">Agregue una [página de diseño](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-574">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="e2cdf-575">El [diseño](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="e2cdf-575">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="e2cdf-576">Controla el diseño de cada página (a no ser que la página no tenga diseño).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-576">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="e2cdf-577">Importa las estructuras HTML como JavaScript y hojas de estilos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-577">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="e2cdf-578">Vea [Layout page](xref:mvc/views/layout) (Página de diseño) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-578">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="e2cdf-579">La propiedad [Layout](xref:mvc/views/layout#specifying-a-layout) se establece en *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-579">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="e2cdf-580">El diseño está en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-580">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="e2cdf-581">Las páginas buscan otras vistas (diseños, plantillas, parciales) de forma jerárquica, a partir de la misma carpeta que la página actual.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-581">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="e2cdf-582">Un diseño en la carpeta *Pages/Shared* se puede usar desde cualquier página de Razor en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-582">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="e2cdf-583">El archivo de diseño debería ir en la carpeta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-583">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="e2cdf-584">Le recomendamos que **no** coloque el archivo de diseño en la carpeta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-584">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="e2cdf-585">*Views/Shared* es un patrón de vistas de MVC.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-585">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="e2cdf-586">Razor Pages está diseñado para basarse en la jerarquía de carpetas, no en las convenciones de ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-586">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="e2cdf-587">La búsqueda de vistas de instancia de Razor Pages incluye la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-587">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="e2cdf-588">Los diseños, plantillas y parciales que usa con los controladores de MVC y las vistas de Razor convencionales *simplemente funcionan*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-588">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="e2cdf-589">Agregue un archivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-589">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="e2cdf-590">`@namespace` se explica más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-590">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="e2cdf-591">La directiva `@addTagHelper` pone los [asistentes de etiquetas integradas](xref:mvc/views/tag-helpers/builtin-th/Index) en todas las páginas de la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-591">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="e2cdf-592">Cuando la directiva `@namespace` se usa explícitamente en una página:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-592">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="e2cdf-593">La directiva establece el espacio de nombres de la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-593">The directive sets the namespace for the page.</span></span> <span data-ttu-id="e2cdf-594">La directiva `@model` no necesita incluir el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-594">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="e2cdf-595">Cuando la directiva `@namespace` se encuentra en *_ViewImports.cshtml*, el espacio de nombres especificado proporciona el prefijo del espacio de nombres generado en la página que importa la directiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-595">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="e2cdf-596">El resto del espacio de nombres generado (la parte del sufijo) es la ruta de acceso relativa separada por puntos entre la carpeta que contiene *_ViewImports.cshtml* y la carpeta que contiene la página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-596">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="e2cdf-597">Por ejemplo, la clase `PageModel`*Pages/Customers/Edit.cshtml.cs* establece explícitamente el espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-597">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="e2cdf-598">El archivo *Pages/_ViewImports.cshtml* establece el espacio de nombres siguiente:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-598">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="e2cdf-599">El espacio de nombres generado para la página de Razor *Pages/Customers/Edit.cshtml* es el mismo que la clase `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-599">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="e2cdf-600">`@namespace` *también funciona con vistas de Razor convencionales.*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-600">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="e2cdf-601">El archivo de vista *Pages/Create.cshtml* original:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-601">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="e2cdf-602">El archivo de vista *Pages/Create.cshtml* actualizado:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-602">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="e2cdf-603">El [proyecto de inicio de Razor Pages](#rpvs17) contiene *Pages/_ValidationScriptsPartial.cshtml*, que enlaza la validación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-603">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="e2cdf-604">Para más información sobre las vistas parciales, vea <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-604">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="e2cdf-605">Generación de direcciones URL para las páginas</span><span class="sxs-lookup"><span data-stu-id="e2cdf-605">URL generation for Pages</span></span>

<span data-ttu-id="e2cdf-606">La página `Create`, mostrada anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-606">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="e2cdf-607">La aplicación tiene la siguiente estructura de archivos o carpetas:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-607">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="e2cdf-608">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-608">*/Pages*</span></span>

  * <span data-ttu-id="e2cdf-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-609">*Index.cshtml*</span></span>
  * <span data-ttu-id="e2cdf-610">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-610">*/Customers*</span></span>

    * <span data-ttu-id="e2cdf-611">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-611">*Create.cshtml*</span></span>
    * <span data-ttu-id="e2cdf-612">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-612">*Edit.cshtml*</span></span>
    * <span data-ttu-id="e2cdf-613">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-613">*Index.cshtml*</span></span>

<span data-ttu-id="e2cdf-614">Las páginas *Pages/Customers/Create.cshtml* y *Pages/Customers/Edit.cshtml* redirigen a *Pages/Index.cshtml* si se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-614">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="e2cdf-615">La cadena `/Index` forma parte del URI para tener acceso a la página anterior.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-615">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="e2cdf-616">La cadena `/Index` puede usarse para generar los URI para la página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-616">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="e2cdf-617">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-617">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="e2cdf-618">El nombre de página es la ruta de acceso a la página de la carpeta raíz */Pages*, incluido un `/` inicial, por ejemplo `/Index`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-618">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="e2cdf-619">Los ejemplos anteriores de generación de URL ofrecen opciones mejoradas y capacidades funcionales en comparación con la escritura a mano de estas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-619">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="e2cdf-620">La generación de direcciones URL usa el [enrutamiento](xref:mvc/controllers/routing) y puede generar y codificar parámetros según cómo se defina la ruta en la ruta de acceso de destino.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-620">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="e2cdf-621">La generación de direcciones URL para las páginas admite nombres relativos.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-621">URL generation for pages supports relative names.</span></span> <span data-ttu-id="e2cdf-622">En la siguiente tabla, se muestra qué página de índice está seleccionada con diferentes parámetros `RedirectToPage` de *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-622">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="e2cdf-623">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="e2cdf-623">RedirectToPage(x)</span></span>| <span data-ttu-id="e2cdf-624">Página</span><span class="sxs-lookup"><span data-stu-id="e2cdf-624">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="e2cdf-625">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="e2cdf-625">RedirectToPage("/Index")</span></span> | <span data-ttu-id="e2cdf-626">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-626">*Pages/Index*</span></span> |
| <span data-ttu-id="e2cdf-627">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="e2cdf-627">RedirectToPage("./Index");</span></span> | <span data-ttu-id="e2cdf-628">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-628">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="e2cdf-629">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="e2cdf-629">RedirectToPage("../Index")</span></span> | <span data-ttu-id="e2cdf-630">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-630">*Pages/Index*</span></span> |
| <span data-ttu-id="e2cdf-631">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="e2cdf-631">RedirectToPage("Index")</span></span>  | <span data-ttu-id="e2cdf-632">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="e2cdf-632">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="e2cdf-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")` y `RedirectToPage("../Index")` son *nombres relativos*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="e2cdf-634">El parámetro `RedirectToPage` se *combina* con la ruta de acceso de la página actual para calcular el nombre de la página de destino.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-634">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="e2cdf-635">Vincular el nombre relativo es útil al crear sitios con una estructura compleja.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-635">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="e2cdf-636">Si usa nombres relativos para vincular entre páginas en una carpeta, puede cambiar el nombre de esa carpeta.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-636">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="e2cdf-637">Todos los vínculos seguirán funcionando (porque no incluyen el nombre de carpeta).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-637">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="e2cdf-638">Para redirigir a una página en otra [área](xref:mvc/controllers/areas), especifique el área:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-638">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="e2cdf-639">Para obtener más información, vea <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-639">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="e2cdf-640">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="e2cdf-640">ViewData attribute</span></span>

<span data-ttu-id="e2cdf-641">Se pueden pasar datos a una página con [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-641">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="e2cdf-642">Los valores de las propiedades de los controladores o los modelos de Razor Pages con el atributo `[ViewData]` se almacenan y cargan desde [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-642">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="e2cdf-643">En el ejemplo siguiente, el valor `AboutModel` contiene una propiedad `Title` marcada con `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-643">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="e2cdf-644">La propiedad `Title` se establece en el título de la página Acerca de:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-644">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="e2cdf-645">En la página Acerca de, acceda a la propiedad `Title` como propiedad de modelo:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-645">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="e2cdf-646">En el diseño, el título se lee desde el diccionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-646">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="e2cdf-647">TempData</span><span class="sxs-lookup"><span data-stu-id="e2cdf-647">TempData</span></span>

<span data-ttu-id="e2cdf-648">ASP.NET Core expone la propiedad [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) en un [controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-648">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="e2cdf-649">Esta propiedad almacena datos hasta que se leen.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-649">This property stores data until it's read.</span></span> <span data-ttu-id="e2cdf-650">Los métodos `Keep` y `Peek` se pueden usar para examinar los datos sin que se eliminen.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-650">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="e2cdf-651">`TempData` es útil para el redireccionamiento cuando se necesitan los datos de más de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-651">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="e2cdf-652">El siguiente código establece el valor de `Message` mediante `TempData`:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-652">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="e2cdf-653">El siguiente marcado en el archivo *Pages/Customers/Index.cshtml* muestra el valor de `Message` mediante `TempData`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-653">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="e2cdf-654">El modelo de página *Pages/Customers/Index.cshtml.cs* aplica el atributo `[TempData]` a la propiedad `Message`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-654">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="e2cdf-655">Para obtener más información, vea [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-655">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="e2cdf-656">Varios controladores por página</span><span class="sxs-lookup"><span data-stu-id="e2cdf-656">Multiple handlers per page</span></span>

<span data-ttu-id="e2cdf-657">En la página siguiente se usa el asistente de etiquetas `asp-page-handler` para generar marcado para dos controladores:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-657">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="e2cdf-658">El formulario del ejemplo anterior tiene dos botones de envío, y cada uno de ellos usa `FormActionTagHelper` para enviar a una dirección URL diferente.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-658">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="e2cdf-659">El atributo `asp-page-handler` es un complemento de `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-659">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="e2cdf-660">`asp-page-handler` genera direcciones URL que envían a cada uno de los métodos de controlador definidos por una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-660">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="e2cdf-661">`asp-page` no se especifica porque el ejemplo se vincula a la página actual.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-661">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="e2cdf-662">Modelo de página:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-662">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="e2cdf-663">El código anterior usa *métodos de controlador con nombre*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-663">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="e2cdf-664">Los métodos de controlador con nombre se crean tomando el texto en el nombre después de `On<HTTP Verb>` y antes de `Async` (si existe).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-664">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="e2cdf-665">En el ejemplo anterior, los métodos de página son OnPost**JoinList**Async y OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-665">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="e2cdf-666">Quitando *OnPost* y *Async*, los nombres de controlador son `JoinList` y `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-666">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="e2cdf-667">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-667">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="e2cdf-668">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-668">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="e2cdf-669">Rutas personalizadas</span><span class="sxs-lookup"><span data-stu-id="e2cdf-669">Custom routes</span></span>

<span data-ttu-id="e2cdf-670">Use la directiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-670">Use the `@page` directive to:</span></span>

* <span data-ttu-id="e2cdf-671">Especificar una ruta personalizada a una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-671">Specify a custom route to a page.</span></span> <span data-ttu-id="e2cdf-672">Por ejemplo, la ruta a la página Acerca de se puede establecer en `/Some/Other/Path` con `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-672">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="e2cdf-673">Anexar segmentos a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-673">Append segments to a page's default route.</span></span> <span data-ttu-id="e2cdf-674">Por ejemplo, se puede agregar un segmento "item" a la ruta predeterminada de una página con `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-674">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="e2cdf-675">Anexar parámetros a la ruta predeterminada de una página.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-675">Append parameters to a page's default route.</span></span> <span data-ttu-id="e2cdf-676">Por ejemplo, un parámetro de identificador, `id`, puede ser necesario para una página con `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-676">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="e2cdf-677">Se admite una ruta de acceso relativa raíz designada por una tilde (`~`) al principio de la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-677">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="e2cdf-678">Por ejemplo, `@page "~/Some/Other/Path"` es lo mismo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-678">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="e2cdf-679">Si no le gusta la cadena de consulta `?handler=JoinList` en la dirección URL, puede cambiar la ruta para poner el nombre del controlador en la parte de la ruta de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-679">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="e2cdf-680">Para personalizar la ruta, se puede agregar una plantilla de ruta entre comillas dobles después de la directiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-680">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="e2cdf-681">Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-681">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="e2cdf-682">La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-682">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="e2cdf-683">El signo `?` que sigue a `handler` significa que el parámetro de ruta es opcional.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-683">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="e2cdf-684">Valores de configuración</span><span class="sxs-lookup"><span data-stu-id="e2cdf-684">Configuration and settings</span></span>

<span data-ttu-id="e2cdf-685">Para configurar opciones avanzadas, use el método de extensión `AddRazorPagesOptions` en el generador de MVC:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-685">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="e2cdf-686">Actualmente, puede usar `RazorPagesOptions` para establecer el directorio raíz de páginas, o agregar convenciones de modelo de aplicación a las páginas.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-686">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="e2cdf-687">En el futuro habilitaremos más extensibilidad de este modo.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-687">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="e2cdf-688">Para precompilar vistas, consulte la sección sobre la [compilación de vistas de Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-688">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="e2cdf-689">[Descargue o vea el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="e2cdf-689">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="e2cdf-690">Consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start), que se basa en esta introducción.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-690">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="e2cdf-691">Especificación de Razor Pages en la raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="e2cdf-691">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="e2cdf-692">De forma predeterminada, Razor Pages se encuentra en la raíz del directorio */Pages*.</span><span class="sxs-lookup"><span data-stu-id="e2cdf-692">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="e2cdf-693">Agregue [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que Razor Pages se encuentra en la [raíz del contenido](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="e2cdf-693">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="e2cdf-694">Especificación de Razor Pages en un directorio raíz personalizado</span><span class="sxs-lookup"><span data-stu-id="e2cdf-694">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="e2cdf-695">Agregue [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que Razor Pages se encuentra en un directorio raíz personalizado de la aplicación (proporcione la ruta de acceso relativa):</span><span class="sxs-lookup"><span data-stu-id="e2cdf-695">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="e2cdf-696">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e2cdf-696">Additional resources</span></span>

* [<span data-ttu-id="e2cdf-697">Autorización de atributos y Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e2cdf-697">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
