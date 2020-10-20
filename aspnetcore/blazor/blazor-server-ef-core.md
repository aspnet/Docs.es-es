---
title: Blazor Server de ASP.NET Core con Entity Framework Core (EF Core)
author: JeremyLikness
description: Instrucciones para usar EF Core en aplicaciones Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: fc902cb5a82fda9fdbed09c40d66a846d9360f6a
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900744"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="f9389-103">Blazor Server de ASP.NET Core con Entity Framework Core (EF Core)</span><span class="sxs-lookup"><span data-stu-id="f9389-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="f9389-104">Por: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="f9389-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f9389-105">El servidor Blazor Server es un marco para aplicaciones con estado.</span><span class="sxs-lookup"><span data-stu-id="f9389-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="f9389-106">La aplicación mantiene una conexión continua con el servidor, y el estado del usuario se mantiene en la memoria del servidor en un *circuito*.</span><span class="sxs-lookup"><span data-stu-id="f9389-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="f9389-107">Un ejemplo de estado del usuario son los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) que se encuentran en el ámbito del circuito.</span><span class="sxs-lookup"><span data-stu-id="f9389-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="f9389-108">El modelo de aplicación único que proporciona Blazor Server requiere un enfoque especial para usar Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f9389-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="f9389-109">En este artículo se describe EF Core en aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f9389-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="f9389-110">Las aplicaciones Blazor WebAssembly se ejecutan en un espacio aislado de WebAssembly que evita la mayoría de conexiones de base de datos directas.</span><span class="sxs-lookup"><span data-stu-id="f9389-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="f9389-111">La ejecución de EF Core en Blazor WebAssembly supera el ámbito de este artículo.</span><span class="sxs-lookup"><span data-stu-id="f9389-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="f9389-112">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="f9389-112">Sample app</span></span></h2>

<span data-ttu-id="f9389-113">La aplicación de ejemplo se ha compilado como una referencia para las aplicaciones Blazor Server en las que se usa EF Core.</span><span class="sxs-lookup"><span data-stu-id="f9389-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="f9389-114">La aplicación de ejemplo incluye una cuadrícula con operaciones de ordenación y filtrado, eliminación, adición y actualización.</span><span class="sxs-lookup"><span data-stu-id="f9389-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="f9389-115">En el ejemplo se muestra el uso de EF Core para controlar la simultaneidad optimista.</span><span class="sxs-lookup"><span data-stu-id="f9389-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="f9389-116">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9389-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f9389-117">En el ejemplo se usa una base de datos [SQLite](https://www.sqlite.org/index.html) local para que se pueda utilizar en cualquier plataforma.</span><span class="sxs-lookup"><span data-stu-id="f9389-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="f9389-118">En el ejemplo también se configura el registro de base de datos para mostrar las consultas SQL que se generan.</span><span class="sxs-lookup"><span data-stu-id="f9389-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="f9389-119">Esto se configura en `appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="f9389-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="f9389-120">Los componentes de cuadrícula, adición y vista usan el patrón de "contexto por operación", en el que se crea un contexto para cada operación.</span><span class="sxs-lookup"><span data-stu-id="f9389-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="f9389-121">El componente de edición usa el patrón de "contexto por componente", en el que se crea un contexto para cada componente.</span><span class="sxs-lookup"><span data-stu-id="f9389-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="f9389-122">En algunos de los ejemplos de código de este tema se necesitan espacios de nombres y servicios que no se muestran.</span><span class="sxs-lookup"><span data-stu-id="f9389-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="f9389-123">Para inspeccionar el código totalmente operativo, incluidas las directivas obligatorias [`@using`](xref:mvc/views/razor#using) e [`@inject`](xref:mvc/views/razor#inject) para ejemplos de Razor, vea la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="f9389-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="f9389-124">Acceso a la base de datos</span><span class="sxs-lookup"><span data-stu-id="f9389-124">Database access</span></span></h2>

<span data-ttu-id="f9389-125">EF Core se basa en un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> como medio para [configurar el acceso a la base de datos](/ef/core/miscellaneous/configuring-dbcontext) y actuar como una [*unidad de trabajo*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="f9389-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="f9389-126">EF Core proporciona la extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> para las aplicaciones de ASP.NET Core, que registra el contexto como un servicio *con ámbito* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f9389-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="f9389-127">En las aplicaciones Blazor Server, los registros con ámbito de servicio pueden ser problemáticos, ya que la instancia se comparte entre los componentes del circuito del usuario.</span><span class="sxs-lookup"><span data-stu-id="f9389-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="f9389-128"><xref:Microsoft.EntityFrameworkCore.DbContext> no es seguro para subprocesos y no está diseñado para su uso simultáneo.</span><span class="sxs-lookup"><span data-stu-id="f9389-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="f9389-129">Las duraciones existentes no son adecuadas por estos motivos:</span><span class="sxs-lookup"><span data-stu-id="f9389-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="f9389-130">**Singleton** comparte el estado entre todos los usuarios de la aplicación y lleva a un uso simultáneo inadecuado.</span><span class="sxs-lookup"><span data-stu-id="f9389-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="f9389-131">**Con ámbito** (el valor predeterminado) supone un problema similar entre los componentes para el mismo usuario.</span><span class="sxs-lookup"><span data-stu-id="f9389-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="f9389-132">**Transitorio** genera una nueva instancia por solicitud, pero como los componentes pueden ser de larga duración, esto da lugar a un contexto que dura más de lo previsto.</span><span class="sxs-lookup"><span data-stu-id="f9389-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="f9389-133">Las recomendaciones siguientes están diseñadas para proporcionar un enfoque coherente al uso de EF Core en aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f9389-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="f9389-134">De forma predeterminada, considere la posibilidad de usar un contexto por operación.</span><span class="sxs-lookup"><span data-stu-id="f9389-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="f9389-135">El contexto está diseñado para la creación de instancias rápidas de baja sobrecarga:</span><span class="sxs-lookup"><span data-stu-id="f9389-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="f9389-136">Use una marca para evitar varias operaciones simultáneas:</span><span class="sxs-lookup"><span data-stu-id="f9389-136">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="f9389-137">Coloque las operaciones después de la línea `Loading = true;` en el bloque `try`.</span><span class="sxs-lookup"><span data-stu-id="f9389-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="f9389-138">En el caso de las operaciones de larga duración que aprovechan el [seguimiento de cambios](/ef/core/querying/tracking) o el [control de simultaneidad](/ef/core/saving/concurrency) de EF Core, [el ámbito del contexto debe ser la duración del componente](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="f9389-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="f9389-139">Nuevas instancias de DbContext</span><span class="sxs-lookup"><span data-stu-id="f9389-139">New DbContext instances</span></span></h3>

<span data-ttu-id="f9389-140">La manera más rápida de crear una instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> consiste en usar `new`.</span><span class="sxs-lookup"><span data-stu-id="f9389-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="f9389-141">Pero hay varios escenarios es los que puede ser necesario resolver dependencias adicionales.</span><span class="sxs-lookup"><span data-stu-id="f9389-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="f9389-142">Por ejemplo, es posible que quiera usar [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar el contexto.</span><span class="sxs-lookup"><span data-stu-id="f9389-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="f9389-143">La solución recomendada para crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> con dependencias es usar un generador.</span><span class="sxs-lookup"><span data-stu-id="f9389-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="f9389-144">En EF Core 5.0 o posterior se proporciona un generador integrado para crear contextos.</span><span class="sxs-lookup"><span data-stu-id="f9389-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="f9389-145">En el ejemplo siguiente se configura [SQLite](https://www.sqlite.org/index.html) y se habilita el registro de datos.</span><span class="sxs-lookup"><span data-stu-id="f9389-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="f9389-146">El código usa un [método de extensión (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) para configurar el generador de bases de datos para la inserción de dependencias y proporcionar opciones predeterminadas:</span><span class="sxs-lookup"><span data-stu-id="f9389-146">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="f9389-147">El generador se inserta en los componentes y se usa para crear instancias.</span><span class="sxs-lookup"><span data-stu-id="f9389-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="f9389-148">Por ejemplo, en `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9389-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="f9389-149">`Wrapper` es una [referencia de componente](xref:blazor/components/index#capture-references-to-components) del componente de `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="f9389-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="f9389-150">Vea el componente de `Index` (`Pages/Index.razor`) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="f9389-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="f9389-151">Ámbito de la duración del componente</span><span class="sxs-lookup"><span data-stu-id="f9389-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="f9389-152">Es posible que quiera crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> que exista mientras dure un componente.</span><span class="sxs-lookup"><span data-stu-id="f9389-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="f9389-153">Esto le permite usarlo como una [unidad de trabajo](https://martinfowler.com/eaaCatalog/unitOfWork.html) y aprovechar características integradas como el seguimiento de cambios y la resolución de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="f9389-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="f9389-154">Puede usar el generador para crear un contexto y realizar su seguimiento mientras dure el componente.</span><span class="sxs-lookup"><span data-stu-id="f9389-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="f9389-155">En primer lugar, implemente <xref:System.IDisposable> e inserte el generador como se muestra en `Pages/EditContact.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9389-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="f9389-156">La aplicación de ejemplo garantiza que el contexto se desecha cuando se desecha el componente:</span><span class="sxs-lookup"><span data-stu-id="f9389-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="f9389-157">Por último, se invalida [`OnInitializedAsync`](xref:blazor/components/lifecycle) para crear un contexto.</span><span class="sxs-lookup"><span data-stu-id="f9389-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="f9389-158">En la aplicación de ejemplo, [`OnInitializedAsync`](xref:blazor/components/lifecycle) carga el contacto en el mismo método:</span><span class="sxs-lookup"><span data-stu-id="f9389-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="f9389-159">El servidor Blazor Server es un marco para aplicaciones con estado.</span><span class="sxs-lookup"><span data-stu-id="f9389-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="f9389-160">La aplicación mantiene una conexión continua con el servidor, y el estado del usuario se mantiene en la memoria del servidor en un *circuito*.</span><span class="sxs-lookup"><span data-stu-id="f9389-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="f9389-161">Un ejemplo de estado del usuario son los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) que se encuentran en el ámbito del circuito.</span><span class="sxs-lookup"><span data-stu-id="f9389-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="f9389-162">El modelo de aplicación único que proporciona Blazor Server requiere un enfoque especial para usar Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f9389-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="f9389-163">En este artículo se describe EF Core en aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f9389-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="f9389-164">Las aplicaciones Blazor WebAssembly se ejecutan en un espacio aislado de WebAssembly que evita la mayoría de conexiones de base de datos directas.</span><span class="sxs-lookup"><span data-stu-id="f9389-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="f9389-165">La ejecución de EF Core en Blazor WebAssembly supera el ámbito de este artículo.</span><span class="sxs-lookup"><span data-stu-id="f9389-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="f9389-166">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="f9389-166">Sample app</span></span></h2>

<span data-ttu-id="f9389-167">La aplicación de ejemplo se ha compilado como una referencia para las aplicaciones Blazor Server en las que se usa EF Core.</span><span class="sxs-lookup"><span data-stu-id="f9389-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="f9389-168">La aplicación de ejemplo incluye una cuadrícula con operaciones de ordenación y filtrado, eliminación, adición y actualización.</span><span class="sxs-lookup"><span data-stu-id="f9389-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="f9389-169">En el ejemplo se muestra el uso de EF Core para controlar la simultaneidad optimista.</span><span class="sxs-lookup"><span data-stu-id="f9389-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="f9389-170">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9389-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f9389-171">En el ejemplo se usa una base de datos [SQLite](https://www.sqlite.org/index.html) local para que se pueda utilizar en cualquier plataforma.</span><span class="sxs-lookup"><span data-stu-id="f9389-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="f9389-172">En el ejemplo también se configura el registro de base de datos para mostrar las consultas SQL que se generan.</span><span class="sxs-lookup"><span data-stu-id="f9389-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="f9389-173">Esto se configura en `appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="f9389-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="f9389-174">Los componentes de cuadrícula, adición y vista usan el patrón de "contexto por operación", en el que se crea un contexto para cada operación.</span><span class="sxs-lookup"><span data-stu-id="f9389-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="f9389-175">El componente de edición usa el patrón de "contexto por componente", en el que se crea un contexto para cada componente.</span><span class="sxs-lookup"><span data-stu-id="f9389-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="f9389-176">En algunos de los ejemplos de código de este tema se necesitan espacios de nombres y servicios que no se muestran.</span><span class="sxs-lookup"><span data-stu-id="f9389-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="f9389-177">Para inspeccionar el código totalmente operativo, incluidas las directivas obligatorias [`@using`](xref:mvc/views/razor#using) e [`@inject`](xref:mvc/views/razor#inject) para ejemplos de Razor, vea la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="f9389-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="f9389-178">Acceso a la base de datos</span><span class="sxs-lookup"><span data-stu-id="f9389-178">Database access</span></span></h2>

<span data-ttu-id="f9389-179">EF Core se basa en un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> como medio para [configurar el acceso a la base de datos](/ef/core/miscellaneous/configuring-dbcontext) y actuar como una [*unidad de trabajo*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="f9389-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="f9389-180">EF Core proporciona la extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> para las aplicaciones de ASP.NET Core, que registra el contexto como un servicio *con ámbito* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f9389-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="f9389-181">En las aplicaciones Blazor Server, esto puede suponer un problema, ya que la instancia se comparte entre los componentes del circuito del usuario.</span><span class="sxs-lookup"><span data-stu-id="f9389-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="f9389-182"><xref:Microsoft.EntityFrameworkCore.DbContext> no es seguro para subprocesos y no está diseñado para su uso simultáneo.</span><span class="sxs-lookup"><span data-stu-id="f9389-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="f9389-183">Las duraciones existentes no son adecuadas por estos motivos:</span><span class="sxs-lookup"><span data-stu-id="f9389-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="f9389-184">**Singleton** comparte el estado entre todos los usuarios de la aplicación y lleva a un uso simultáneo inadecuado.</span><span class="sxs-lookup"><span data-stu-id="f9389-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="f9389-185">**Con ámbito** (el valor predeterminado) supone un problema similar entre los componentes para el mismo usuario.</span><span class="sxs-lookup"><span data-stu-id="f9389-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="f9389-186">**Transitorio** genera una nueva instancia por solicitud, pero como los componentes pueden ser de larga duración, esto da lugar a un contexto que dura más de lo previsto.</span><span class="sxs-lookup"><span data-stu-id="f9389-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="f9389-187">Las recomendaciones siguientes están diseñadas para proporcionar un enfoque coherente al uso de EF Core en aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f9389-187">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="f9389-188">De forma predeterminada, considere la posibilidad de usar un contexto por operación.</span><span class="sxs-lookup"><span data-stu-id="f9389-188">By default, consider using one context per operation.</span></span> <span data-ttu-id="f9389-189">El contexto está diseñado para la creación de instancias rápidas de baja sobrecarga:</span><span class="sxs-lookup"><span data-stu-id="f9389-189">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="f9389-190">Use una marca para evitar varias operaciones simultáneas:</span><span class="sxs-lookup"><span data-stu-id="f9389-190">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="f9389-191">Coloque las operaciones después de la línea `Loading = true;` en el bloque `try`.</span><span class="sxs-lookup"><span data-stu-id="f9389-191">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="f9389-192">En el caso de las operaciones de larga duración que aprovechan el [seguimiento de cambios](/ef/core/querying/tracking) o el [control de simultaneidad](/ef/core/saving/concurrency) de EF Core, [el ámbito del contexto debe ser la duración del componente](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="f9389-192">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="f9389-193">Nuevas instancias de DbContext</span><span class="sxs-lookup"><span data-stu-id="f9389-193">New DbContext instances</span></span></h3>

<span data-ttu-id="f9389-194">La manera más rápida de crear una instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> consiste en usar `new`.</span><span class="sxs-lookup"><span data-stu-id="f9389-194">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="f9389-195">Pero hay varios escenarios es los que puede ser necesario resolver dependencias adicionales.</span><span class="sxs-lookup"><span data-stu-id="f9389-195">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="f9389-196">Por ejemplo, es posible que quiera usar [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar el contexto.</span><span class="sxs-lookup"><span data-stu-id="f9389-196">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="f9389-197">La solución recomendada para crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> con dependencias es usar un generador.</span><span class="sxs-lookup"><span data-stu-id="f9389-197">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="f9389-198">La aplicación de ejemplo implementa su propio generador en `Data/DbContextFactory.cs`.</span><span class="sxs-lookup"><span data-stu-id="f9389-198">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="f9389-199">En el generador anterior:</span><span class="sxs-lookup"><span data-stu-id="f9389-199">In the preceding factory:</span></span>

* <span data-ttu-id="f9389-200"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisface todas las dependencias a través del proveedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="f9389-200"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="f9389-201">`IDbContextFactory` está disponible en EF Core ASP.NET Core 5.0 o posterior, por lo que la interfaz se [implementa en la aplicación de ejemplo para ASP.NET Core 3 y siguientes](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="f9389-201">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="f9389-202">En el ejemplo siguiente se configura [SQLite](https://www.sqlite.org/index.html) y se habilita el registro de datos.</span><span class="sxs-lookup"><span data-stu-id="f9389-202">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="f9389-203">El código usa un método de extensión para configurar el generador de bases de datos para la inserción de dependencias y proporcionar opciones predeterminadas:</span><span class="sxs-lookup"><span data-stu-id="f9389-203">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="f9389-204">El generador se inserta en los componentes y se usa para crear instancias.</span><span class="sxs-lookup"><span data-stu-id="f9389-204">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="f9389-205">Por ejemplo, en `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9389-205">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="f9389-206">`Wrapper` es una [referencia de componente](xref:blazor/components/index#capture-references-to-components) del componente de `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="f9389-206">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="f9389-207">Vea el componente de `Index` (`Pages/Index.razor`) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="f9389-207">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="f9389-208">Ámbito de la duración del componente</span><span class="sxs-lookup"><span data-stu-id="f9389-208">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="f9389-209">Es posible que quiera crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> que exista mientras dure un componente.</span><span class="sxs-lookup"><span data-stu-id="f9389-209">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="f9389-210">Esto le permite usarlo como una [unidad de trabajo](https://martinfowler.com/eaaCatalog/unitOfWork.html) y aprovechar características integradas como el seguimiento de cambios y la resolución de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="f9389-210">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="f9389-211">Puede usar el generador para crear un contexto y realizar su seguimiento mientras dure el componente.</span><span class="sxs-lookup"><span data-stu-id="f9389-211">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="f9389-212">En primer lugar, implemente <xref:System.IDisposable> e inserte el generador como se muestra en `Pages/EditContact.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9389-212">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="f9389-213">La aplicación de ejemplo garantiza que el contexto se desecha cuando se desecha el componente:</span><span class="sxs-lookup"><span data-stu-id="f9389-213">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="f9389-214">Por último, se invalida [`OnInitializedAsync`](xref:blazor/components/lifecycle) para crear un contexto.</span><span class="sxs-lookup"><span data-stu-id="f9389-214">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="f9389-215">En la aplicación de ejemplo, [`OnInitializedAsync`](xref:blazor/components/lifecycle) carga el contacto en el mismo método:</span><span class="sxs-lookup"><span data-stu-id="f9389-215">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="f9389-216">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="f9389-216">In the preceding example:</span></span>

* <span data-ttu-id="f9389-217">Cuando `Busy` se establece en `true`, pueden comenzar las operaciones asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="f9389-217">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="f9389-218">Cuando `Busy` se vuelve a establecer en `false`, las operaciones asincrónicas deben finalizar.</span><span class="sxs-lookup"><span data-stu-id="f9389-218">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="f9389-219">Coloque la lógica de control de errores adicional en un bloque `catch`.</span><span class="sxs-lookup"><span data-stu-id="f9389-219">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f9389-220">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f9389-220">Additional resources</span></span>

* [<span data-ttu-id="f9389-221">Documentación de EF Core</span><span class="sxs-lookup"><span data-stu-id="f9389-221">EF Core documentation</span></span>](/ef/)
