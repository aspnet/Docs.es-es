---
title: RazorConvenciones de autorización de páginas en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo controlar el acceso a las páginas con convenciones que autorizan a los usuarios y permiten a los usuarios anónimos acceder a páginas o carpetas de páginas.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 0f4022e46993c6a957d3d0c133b3db56fa650edc
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/21/2020
ms.locfileid: "86568826"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Razor<span data-ttu-id="bf420-103">Convenciones de autorización de páginas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf420-103"> Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bf420-104">Una manera de controlar el acceso en la Razor aplicación páginas es usar las convenciones de autorización en el inicio.</span><span class="sxs-lookup"><span data-stu-id="bf420-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="bf420-105">Estas convenciones permiten autorizar a los usuarios y permitir que los usuarios anónimos tengan acceso a páginas individuales o carpetas de páginas.</span><span class="sxs-lookup"><span data-stu-id="bf420-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="bf420-106">Las convenciones descritas en este tema aplican automáticamente los [filtros de autorización](xref:mvc/controllers/filters#authorization-filters) para controlar el acceso.</span><span class="sxs-lookup"><span data-stu-id="bf420-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="bf420-107">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bf420-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bf420-108">La aplicación de ejemplo usa la [autenticación de Identity cookies sin ASP.net Core ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="bf420-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="bf420-109">Los conceptos y los ejemplos que se muestran en este tema se aplican igualmente a las aplicaciones que usan ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="bf420-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="bf420-110">Para usar ASP.NET Core Identity , siga las instrucciones de <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="bf420-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="bf420-111">Requerir autorización para tener acceso a una página</span><span class="sxs-lookup"><span data-stu-id="bf420-111">Require authorization to access a page</span></span>

<span data-ttu-id="bf420-112">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Convención para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a la página en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="bf420-113">La ruta de acceso especificada es la ruta de acceso del motor de vista, que es la Razor ruta de acceso relativa raíz de las páginas sin una extensión y que solo contiene barras diagonales.</span><span class="sxs-lookup"><span data-stu-id="bf420-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="bf420-114">Para especificar una [Directiva de autorización](xref:security/authorization/policies), use una [sobrecarga AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="bf420-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="bf420-115">Un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> se puede aplicar a una clase de modelo de página con el `[Authorize]` atributo de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf420-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="bf420-116">Para obtener más información, consulte [Authorize Filter Attribute](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="bf420-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="bf420-117">Requerir autorización para tener acceso a una carpeta de páginas</span><span class="sxs-lookup"><span data-stu-id="bf420-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="bf420-118">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Convención para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas las páginas de una carpeta en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="bf420-119">La ruta de acceso especificada es la ruta del motor de vista, que es la Razor ruta de acceso relativa de la raíz de las páginas.</span><span class="sxs-lookup"><span data-stu-id="bf420-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="bf420-120">Para especificar una [Directiva de autorización](xref:security/authorization/policies), use una [sobrecarga AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="bf420-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="bf420-121">Requerir autorización para tener acceso a una página de área</span><span class="sxs-lookup"><span data-stu-id="bf420-121">Require authorization to access an area page</span></span>

<span data-ttu-id="bf420-122">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Convención para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a la página de área en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="bf420-123">El nombre de página es la ruta de acceso del archivo sin una extensión relativa al directorio raíz de páginas del área especificada.</span><span class="sxs-lookup"><span data-stu-id="bf420-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="bf420-124">Por ejemplo, el nombre de página de las *áreas de archivo/ Identity /pages/Manage/accounts.cshtml* es */Manage/accounts*.</span><span class="sxs-lookup"><span data-stu-id="bf420-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="bf420-125">Para especificar una [Directiva de autorización](xref:security/authorization/policies), use una [sobrecarga AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="bf420-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="bf420-126">Requerir autorización para tener acceso a una carpeta de áreas</span><span class="sxs-lookup"><span data-stu-id="bf420-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="bf420-127">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Convención para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas las áreas de una carpeta en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="bf420-128">La ruta de acceso de la carpeta es la ruta de acceso de la carpeta relativa al directorio raíz de páginas del área especificada.</span><span class="sxs-lookup"><span data-stu-id="bf420-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="bf420-129">Por ejemplo, la ruta de acceso de la carpeta para los archivos en *areas/ Identity /pages/Manage/* es */Manage*.</span><span class="sxs-lookup"><span data-stu-id="bf420-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="bf420-130">Para especificar una [Directiva de autorización](xref:security/authorization/policies), use una [sobrecarga AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="bf420-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="bf420-131">Permitir acceso anónimo a una página</span><span class="sxs-lookup"><span data-stu-id="bf420-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="bf420-132">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Convención para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a una página en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="bf420-133">La ruta de acceso especificada es la ruta de acceso del motor de vista, que es la Razor ruta de acceso relativa raíz de las páginas sin una extensión y que solo contiene barras diagonales.</span><span class="sxs-lookup"><span data-stu-id="bf420-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="bf420-134">Permitir acceso anónimo a una carpeta de páginas</span><span class="sxs-lookup"><span data-stu-id="bf420-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="bf420-135">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Convención para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a todas las páginas de una carpeta en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="bf420-136">La ruta de acceso especificada es la ruta del motor de vista, que es la Razor ruta de acceso relativa de la raíz de las páginas.</span><span class="sxs-lookup"><span data-stu-id="bf420-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="bf420-137">Nota sobre cómo combinar el acceso autorizado y anónimo</span><span class="sxs-lookup"><span data-stu-id="bf420-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="bf420-138">Es válido especificar que una carpeta de páginas requiere autorización y, a continuación, especificar que una página dentro de esa carpeta permita el acceso anónimo:</span><span class="sxs-lookup"><span data-stu-id="bf420-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="bf420-139">Sin embargo, el inverso no es válido.</span><span class="sxs-lookup"><span data-stu-id="bf420-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="bf420-140">No se puede declarar una carpeta de páginas para el acceso anónimo y, a continuación, especificar una página dentro de esa carpeta que requiera autorización:</span><span class="sxs-lookup"><span data-stu-id="bf420-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="bf420-141">Se produce un error en la solicitud de autorización en la página privada.</span><span class="sxs-lookup"><span data-stu-id="bf420-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="bf420-142">Cuando <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> y <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> se aplican a la página, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> tiene prioridad y controla el acceso.</span><span class="sxs-lookup"><span data-stu-id="bf420-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bf420-143">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bf420-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bf420-144">Una manera de controlar el acceso en la Razor aplicación páginas es usar las convenciones de autorización en el inicio.</span><span class="sxs-lookup"><span data-stu-id="bf420-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="bf420-145">Estas convenciones permiten autorizar a los usuarios y permitir que los usuarios anónimos tengan acceso a páginas individuales o carpetas de páginas.</span><span class="sxs-lookup"><span data-stu-id="bf420-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="bf420-146">Las convenciones descritas en este tema aplican automáticamente los [filtros de autorización](xref:mvc/controllers/filters#authorization-filters) para controlar el acceso.</span><span class="sxs-lookup"><span data-stu-id="bf420-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="bf420-147">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bf420-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bf420-148">La aplicación de ejemplo usa la [autenticación de Identity cookies sin ASP.net Core ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="bf420-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="bf420-149">Los conceptos y los ejemplos que se muestran en este tema se aplican igualmente a las aplicaciones que usan ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="bf420-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="bf420-150">Para usar ASP.NET Core Identity , siga las instrucciones de <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="bf420-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="bf420-151">Requerir autorización para tener acceso a una página</span><span class="sxs-lookup"><span data-stu-id="bf420-151">Require authorization to access a page</span></span>

<span data-ttu-id="bf420-152">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Convención a través <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> de para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a la página en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="bf420-153">La ruta de acceso especificada es la ruta de acceso del motor de vista, que es la Razor ruta de acceso relativa raíz de las páginas sin una extensión y que solo contiene barras diagonales.</span><span class="sxs-lookup"><span data-stu-id="bf420-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="bf420-154">Para especificar una [Directiva de autorización](xref:security/authorization/policies), use una [sobrecarga AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="bf420-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="bf420-155">Un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> se puede aplicar a una clase de modelo de página con el `[Authorize]` atributo de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf420-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="bf420-156">Para obtener más información, consulte [Authorize Filter Attribute](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="bf420-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="bf420-157">Requerir autorización para tener acceso a una carpeta de páginas</span><span class="sxs-lookup"><span data-stu-id="bf420-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="bf420-158">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Convención a través <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> de para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas las páginas de una carpeta en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="bf420-159">La ruta de acceso especificada es la ruta del motor de vista, que es la Razor ruta de acceso relativa de la raíz de las páginas.</span><span class="sxs-lookup"><span data-stu-id="bf420-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="bf420-160">Para especificar una [Directiva de autorización](xref:security/authorization/policies), use una [sobrecarga AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="bf420-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="bf420-161">Requerir autorización para tener acceso a una página de área</span><span class="sxs-lookup"><span data-stu-id="bf420-161">Require authorization to access an area page</span></span>

<span data-ttu-id="bf420-162">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Convención a través <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> de para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a la página de área en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="bf420-163">El nombre de página es la ruta de acceso del archivo sin una extensión relativa al directorio raíz de páginas del área especificada.</span><span class="sxs-lookup"><span data-stu-id="bf420-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="bf420-164">Por ejemplo, el nombre de página de las *áreas de archivo/ Identity /pages/Manage/accounts.cshtml* es */Manage/accounts*.</span><span class="sxs-lookup"><span data-stu-id="bf420-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="bf420-165">Para especificar una [Directiva de autorización](xref:security/authorization/policies), use una [sobrecarga AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="bf420-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="bf420-166">Requerir autorización para tener acceso a una carpeta de áreas</span><span class="sxs-lookup"><span data-stu-id="bf420-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="bf420-167">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Convención a través <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> de para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas las áreas de una carpeta en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="bf420-168">La ruta de acceso de la carpeta es la ruta de acceso de la carpeta relativa al directorio raíz de páginas del área especificada.</span><span class="sxs-lookup"><span data-stu-id="bf420-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="bf420-169">Por ejemplo, la ruta de acceso de la carpeta para los archivos en *areas/ Identity /pages/Manage/* es */Manage*.</span><span class="sxs-lookup"><span data-stu-id="bf420-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="bf420-170">Para especificar una [Directiva de autorización](xref:security/authorization/policies), use una [sobrecarga AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="bf420-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="bf420-171">Permitir acceso anónimo a una página</span><span class="sxs-lookup"><span data-stu-id="bf420-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="bf420-172">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Convención a través <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> de para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a una página en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="bf420-173">La ruta de acceso especificada es la ruta de acceso del motor de vista, que es la Razor ruta de acceso relativa raíz de las páginas sin una extensión y que solo contiene barras diagonales.</span><span class="sxs-lookup"><span data-stu-id="bf420-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="bf420-174">Permitir acceso anónimo a una carpeta de páginas</span><span class="sxs-lookup"><span data-stu-id="bf420-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="bf420-175">Use la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Convención a través <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> de para agregar un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a todas las páginas de una carpeta en la ruta de acceso especificada:</span><span class="sxs-lookup"><span data-stu-id="bf420-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="bf420-176">La ruta de acceso especificada es la ruta del motor de vista, que es la Razor ruta de acceso relativa de la raíz de las páginas.</span><span class="sxs-lookup"><span data-stu-id="bf420-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="bf420-177">Nota sobre cómo combinar el acceso autorizado y anónimo</span><span class="sxs-lookup"><span data-stu-id="bf420-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="bf420-178">Es válido especificar que una carpeta de páginas que requieran autorización y que especifique que una página de esa carpeta permita el acceso anónimo:</span><span class="sxs-lookup"><span data-stu-id="bf420-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="bf420-179">Sin embargo, el inverso no es válido.</span><span class="sxs-lookup"><span data-stu-id="bf420-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="bf420-180">No se puede declarar una carpeta de páginas para el acceso anónimo y, a continuación, especificar una página dentro de esa carpeta que requiera autorización:</span><span class="sxs-lookup"><span data-stu-id="bf420-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="bf420-181">Se produce un error en la solicitud de autorización en la página privada.</span><span class="sxs-lookup"><span data-stu-id="bf420-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="bf420-182">Cuando <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> y <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> se aplican a la página, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> tiene prioridad y controla el acceso.</span><span class="sxs-lookup"><span data-stu-id="bf420-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bf420-183">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bf420-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
