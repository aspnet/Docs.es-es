---
title: Blazor Server de ASP.NET Core con Entity Framework Core (EF Core)
author: JeremyLikness
description: Instrucciones para usar EF Core en aplicaciones Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 53d276db996304852d69566584e43d47aa73f921
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586558"
---
# <a name="aspnet-core-blazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="d32c4-103">Blazor Server de ASP.NET Core con Entity Framework Core (EF Core)</span><span class="sxs-lookup"><span data-stu-id="d32c4-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d32c4-104">El servidor Blazor Server es un marco para aplicaciones con estado.</span><span class="sxs-lookup"><span data-stu-id="d32c4-104">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="d32c4-105">La aplicación mantiene una conexión continua con el servidor, y el estado del usuario se mantiene en la memoria del servidor en un *circuito*.</span><span class="sxs-lookup"><span data-stu-id="d32c4-105">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="d32c4-106">Un ejemplo de estado del usuario son los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) que se encuentran en el ámbito del circuito.</span><span class="sxs-lookup"><span data-stu-id="d32c4-106">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="d32c4-107">El modelo de aplicación único que proporciona Blazor Server requiere un enfoque especial para usar Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d32c4-107">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="d32c4-108">En este artículo se describe EF Core en aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d32c4-108">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="d32c4-109">Las aplicaciones Blazor WebAssembly se ejecutan en un espacio aislado de WebAssembly que evita la mayoría de conexiones de base de datos directas.</span><span class="sxs-lookup"><span data-stu-id="d32c4-109">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="d32c4-110">La ejecución de EF Core en Blazor WebAssembly supera el ámbito de este artículo.</span><span class="sxs-lookup"><span data-stu-id="d32c4-110">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="d32c4-111">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="d32c4-111">Sample app</span></span></h2>

<span data-ttu-id="d32c4-112">La aplicación de ejemplo se ha compilado como una referencia para las aplicaciones Blazor Server en las que se usa EF Core.</span><span class="sxs-lookup"><span data-stu-id="d32c4-112">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="d32c4-113">La aplicación de ejemplo incluye una cuadrícula con operaciones de ordenación y filtrado, eliminación, adición y actualización.</span><span class="sxs-lookup"><span data-stu-id="d32c4-113">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="d32c4-114">En el ejemplo se muestra el uso de EF Core para controlar la simultaneidad optimista.</span><span class="sxs-lookup"><span data-stu-id="d32c4-114">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="d32c4-115">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d32c4-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d32c4-116">En el ejemplo se usa una base de datos [SQLite](https://www.sqlite.org/index.html) local para que se pueda utilizar en cualquier plataforma.</span><span class="sxs-lookup"><span data-stu-id="d32c4-116">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="d32c4-117">En el ejemplo también se configura el registro de base de datos para mostrar las consultas SQL que se generan.</span><span class="sxs-lookup"><span data-stu-id="d32c4-117">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="d32c4-118">Esto se configura en `appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="d32c4-118">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="d32c4-119">Los componentes de cuadrícula, adición y vista usan el patrón de "contexto por operación", en el que se crea un contexto para cada operación.</span><span class="sxs-lookup"><span data-stu-id="d32c4-119">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="d32c4-120">El componente de edición usa el patrón de "contexto por componente", en el que se crea un contexto para cada componente.</span><span class="sxs-lookup"><span data-stu-id="d32c4-120">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="d32c4-121">En algunos de los ejemplos de código de este tema se necesitan espacios de nombres y servicios que no se muestran.</span><span class="sxs-lookup"><span data-stu-id="d32c4-121">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="d32c4-122">Para inspeccionar el código totalmente operativo, incluidas las directivas obligatorias [`@using`](xref:mvc/views/razor#using) e [`@inject`](xref:mvc/views/razor#inject) para ejemplos de Razor, vea la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="d32c4-122">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="d32c4-123">Acceso a la base de datos</span><span class="sxs-lookup"><span data-stu-id="d32c4-123">Database access</span></span></h2>

<span data-ttu-id="d32c4-124">EF Core se basa en un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> como medio para [configurar el acceso a la base de datos](/ef/core/miscellaneous/configuring-dbcontext) y actuar como una [*unidad de trabajo*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="d32c4-124">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="d32c4-125">EF Core proporciona la extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> para las aplicaciones de ASP.NET Core, que registra el contexto como un servicio *con ámbito* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d32c4-125">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="d32c4-126">En las aplicaciones Blazor Server, los registros con ámbito de servicio pueden ser problemáticos, ya que la instancia se comparte entre los componentes del circuito del usuario.</span><span class="sxs-lookup"><span data-stu-id="d32c4-126">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="d32c4-127"><xref:Microsoft.EntityFrameworkCore.DbContext> no es seguro para subprocesos y no está diseñado para su uso simultáneo.</span><span class="sxs-lookup"><span data-stu-id="d32c4-127"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="d32c4-128">Las duraciones existentes no son adecuadas por estos motivos:</span><span class="sxs-lookup"><span data-stu-id="d32c4-128">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="d32c4-129">**Singleton** comparte el estado entre todos los usuarios de la aplicación y lleva a un uso simultáneo inadecuado.</span><span class="sxs-lookup"><span data-stu-id="d32c4-129">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="d32c4-130">**Con ámbito** (el valor predeterminado) supone un problema similar entre los componentes para el mismo usuario.</span><span class="sxs-lookup"><span data-stu-id="d32c4-130">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="d32c4-131">**Transitorio** genera una nueva instancia por solicitud, pero como los componentes pueden ser de larga duración, esto da lugar a un contexto que dura más de lo previsto.</span><span class="sxs-lookup"><span data-stu-id="d32c4-131">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="d32c4-132">Las recomendaciones siguientes están diseñadas para proporcionar un enfoque coherente al uso de EF Core en aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d32c4-132">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="d32c4-133">De forma predeterminada, considere la posibilidad de usar un contexto por operación.</span><span class="sxs-lookup"><span data-stu-id="d32c4-133">By default, consider using one context per operation.</span></span> <span data-ttu-id="d32c4-134">El contexto está diseñado para la creación de instancias rápidas de baja sobrecarga:</span><span class="sxs-lookup"><span data-stu-id="d32c4-134">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="d32c4-135">Use una marca para evitar varias operaciones simultáneas:</span><span class="sxs-lookup"><span data-stu-id="d32c4-135">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="d32c4-136">Coloque las operaciones después de la línea `Loading = true;` en el bloque `try`.</span><span class="sxs-lookup"><span data-stu-id="d32c4-136">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="d32c4-137">En el caso de las operaciones de larga duración que aprovechan el [seguimiento de cambios](/ef/core/querying/tracking) o el [control de simultaneidad](/ef/core/saving/concurrency) de EF Core, [el ámbito del contexto debe ser la duración del componente](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="d32c4-137">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="d32c4-138">Nuevas instancias de DbContext</span><span class="sxs-lookup"><span data-stu-id="d32c4-138">New DbContext instances</span></span></h3>

<span data-ttu-id="d32c4-139">La manera más rápida de crear una instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> consiste en usar `new`.</span><span class="sxs-lookup"><span data-stu-id="d32c4-139">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="d32c4-140">Pero hay varios escenarios es los que puede ser necesario resolver dependencias adicionales.</span><span class="sxs-lookup"><span data-stu-id="d32c4-140">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="d32c4-141">Por ejemplo, es posible que quiera usar [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar el contexto.</span><span class="sxs-lookup"><span data-stu-id="d32c4-141">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="d32c4-142">La solución recomendada para crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> con dependencias es usar un generador.</span><span class="sxs-lookup"><span data-stu-id="d32c4-142">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="d32c4-143">En EF Core 5.0 o posterior se proporciona un generador integrado para crear contextos.</span><span class="sxs-lookup"><span data-stu-id="d32c4-143">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="d32c4-144">En el ejemplo siguiente se configura [SQLite](https://www.sqlite.org/index.html) y se habilita el registro de datos.</span><span class="sxs-lookup"><span data-stu-id="d32c4-144">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="d32c4-145">El código usa un [método de extensión (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) para configurar el generador de bases de datos para la inserción de dependencias y proporcionar opciones predeterminadas:</span><span class="sxs-lookup"><span data-stu-id="d32c4-145">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="d32c4-146">El generador se inserta en los componentes y se usa para crear instancias.</span><span class="sxs-lookup"><span data-stu-id="d32c4-146">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="d32c4-147">Por ejemplo, en `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="d32c4-147">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="d32c4-148">`Wrapper` es una [referencia de componente](xref:blazor/components/index#capture-references-to-components) del componente de `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="d32c4-148">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="d32c4-149">Vea el componente de `Index` (`Pages/Index.razor`) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="d32c4-149">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="d32c4-150">Se pueden crear instancias de <xref:Microsoft.EntityFrameworkCore.DbContext> con un generador que permite configurar la cadena de conexión por `DbContext`, como cuando se usa [el modelo Identity de ASP.NET Core](xref:security/authentication/customize_identity_model):</span><span class="sxs-lookup"><span data-stu-id="d32c4-150">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model](xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="d32c4-151">Ámbito de la duración del componente</span><span class="sxs-lookup"><span data-stu-id="d32c4-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="d32c4-152">Es posible que quiera crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> que exista mientras dure un componente.</span><span class="sxs-lookup"><span data-stu-id="d32c4-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="d32c4-153">Esto le permite usarlo como una [unidad de trabajo](https://martinfowler.com/eaaCatalog/unitOfWork.html) y aprovechar características integradas como el seguimiento de cambios y la resolución de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="d32c4-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="d32c4-154">Puede usar el generador para crear un contexto y realizar su seguimiento mientras dure el componente.</span><span class="sxs-lookup"><span data-stu-id="d32c4-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="d32c4-155">En primer lugar, implemente <xref:System.IDisposable> e inserte el generador como se muestra en `Pages/EditContact.razor`:</span><span class="sxs-lookup"><span data-stu-id="d32c4-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="d32c4-156">La aplicación de ejemplo garantiza que el contexto se desecha cuando se desecha el componente:</span><span class="sxs-lookup"><span data-stu-id="d32c4-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="d32c4-157">Por último, se invalida [`OnInitializedAsync`](xref:blazor/components/lifecycle) para crear un contexto.</span><span class="sxs-lookup"><span data-stu-id="d32c4-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="d32c4-158">En la aplicación de ejemplo, [`OnInitializedAsync`](xref:blazor/components/lifecycle) carga el contacto en el mismo método:</span><span class="sxs-lookup"><span data-stu-id="d32c4-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="d32c4-159">Habilitar el registro de datos confidenciales</span><span class="sxs-lookup"><span data-stu-id="d32c4-159">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="d32c4-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> incluye datos de la aplicación en los mensajes de excepción y en la plataforma de registro.</span><span class="sxs-lookup"><span data-stu-id="d32c4-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="d32c4-161">Los datos registrados pueden incluir los valores asignados a las propiedades de las instancias de entidad y los valores de parámetro para los comandos enviados a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d32c4-161">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="d32c4-162">El registro de datos con <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> es un **riesgo de seguridad**, ya que puede exponer contraseñas y otra información de identificación personal (PII) cuando registra instrucciones SQL ejecutadas en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d32c4-162">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="d32c4-163">Se recomienda habilitar <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> solo para desarrollo y pruebas:</span><span class="sxs-lookup"><span data-stu-id="d32c4-163">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="d32c4-164">El servidor Blazor Server es un marco para aplicaciones con estado.</span><span class="sxs-lookup"><span data-stu-id="d32c4-164">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="d32c4-165">La aplicación mantiene una conexión continua con el servidor, y el estado del usuario se mantiene en la memoria del servidor en un *circuito*.</span><span class="sxs-lookup"><span data-stu-id="d32c4-165">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="d32c4-166">Un ejemplo de estado del usuario son los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) que se encuentran en el ámbito del circuito.</span><span class="sxs-lookup"><span data-stu-id="d32c4-166">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="d32c4-167">El modelo de aplicación único que proporciona Blazor Server requiere un enfoque especial para usar Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d32c4-167">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="d32c4-168">En este artículo se describe EF Core en aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d32c4-168">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="d32c4-169">Las aplicaciones Blazor WebAssembly se ejecutan en un espacio aislado de WebAssembly que evita la mayoría de conexiones de base de datos directas.</span><span class="sxs-lookup"><span data-stu-id="d32c4-169">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="d32c4-170">La ejecución de EF Core en Blazor WebAssembly supera el ámbito de este artículo.</span><span class="sxs-lookup"><span data-stu-id="d32c4-170">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="d32c4-171">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="d32c4-171">Sample app</span></span></h2>

<span data-ttu-id="d32c4-172">La aplicación de ejemplo se ha compilado como una referencia para las aplicaciones Blazor Server en las que se usa EF Core.</span><span class="sxs-lookup"><span data-stu-id="d32c4-172">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="d32c4-173">La aplicación de ejemplo incluye una cuadrícula con operaciones de ordenación y filtrado, eliminación, adición y actualización.</span><span class="sxs-lookup"><span data-stu-id="d32c4-173">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="d32c4-174">En el ejemplo se muestra el uso de EF Core para controlar la simultaneidad optimista.</span><span class="sxs-lookup"><span data-stu-id="d32c4-174">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="d32c4-175">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d32c4-175">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d32c4-176">En el ejemplo se usa una base de datos [SQLite](https://www.sqlite.org/index.html) local para que se pueda utilizar en cualquier plataforma.</span><span class="sxs-lookup"><span data-stu-id="d32c4-176">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="d32c4-177">En el ejemplo también se configura el registro de base de datos para mostrar las consultas SQL que se generan.</span><span class="sxs-lookup"><span data-stu-id="d32c4-177">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="d32c4-178">Esto se configura en `appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="d32c4-178">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="d32c4-179">Los componentes de cuadrícula, adición y vista usan el patrón de "contexto por operación", en el que se crea un contexto para cada operación.</span><span class="sxs-lookup"><span data-stu-id="d32c4-179">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="d32c4-180">El componente de edición usa el patrón de "contexto por componente", en el que se crea un contexto para cada componente.</span><span class="sxs-lookup"><span data-stu-id="d32c4-180">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="d32c4-181">En algunos de los ejemplos de código de este tema se necesitan espacios de nombres y servicios que no se muestran.</span><span class="sxs-lookup"><span data-stu-id="d32c4-181">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="d32c4-182">Para inspeccionar el código totalmente operativo, incluidas las directivas obligatorias [`@using`](xref:mvc/views/razor#using) e [`@inject`](xref:mvc/views/razor#inject) para ejemplos de Razor, vea la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="d32c4-182">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="d32c4-183">Acceso a la base de datos</span><span class="sxs-lookup"><span data-stu-id="d32c4-183">Database access</span></span></h2>

<span data-ttu-id="d32c4-184">EF Core se basa en un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> como medio para [configurar el acceso a la base de datos](/ef/core/miscellaneous/configuring-dbcontext) y actuar como una [*unidad de trabajo*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="d32c4-184">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="d32c4-185">EF Core proporciona la extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> para las aplicaciones de ASP.NET Core, que registra el contexto como un servicio *con ámbito* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d32c4-185">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="d32c4-186">En las aplicaciones Blazor Server, esto puede suponer un problema, ya que la instancia se comparte entre los componentes del circuito del usuario.</span><span class="sxs-lookup"><span data-stu-id="d32c4-186">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="d32c4-187"><xref:Microsoft.EntityFrameworkCore.DbContext> no es seguro para subprocesos y no está diseñado para su uso simultáneo.</span><span class="sxs-lookup"><span data-stu-id="d32c4-187"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="d32c4-188">Las duraciones existentes no son adecuadas por estos motivos:</span><span class="sxs-lookup"><span data-stu-id="d32c4-188">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="d32c4-189">**Singleton** comparte el estado entre todos los usuarios de la aplicación y lleva a un uso simultáneo inadecuado.</span><span class="sxs-lookup"><span data-stu-id="d32c4-189">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="d32c4-190">**Con ámbito** (el valor predeterminado) supone un problema similar entre los componentes para el mismo usuario.</span><span class="sxs-lookup"><span data-stu-id="d32c4-190">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="d32c4-191">**Transitorio** genera una nueva instancia por solicitud, pero como los componentes pueden ser de larga duración, esto da lugar a un contexto que dura más de lo previsto.</span><span class="sxs-lookup"><span data-stu-id="d32c4-191">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="d32c4-192">Las recomendaciones siguientes están diseñadas para proporcionar un enfoque coherente al uso de EF Core en aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d32c4-192">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="d32c4-193">De forma predeterminada, considere la posibilidad de usar un contexto por operación.</span><span class="sxs-lookup"><span data-stu-id="d32c4-193">By default, consider using one context per operation.</span></span> <span data-ttu-id="d32c4-194">El contexto está diseñado para la creación de instancias rápidas de baja sobrecarga:</span><span class="sxs-lookup"><span data-stu-id="d32c4-194">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="d32c4-195">Use una marca para evitar varias operaciones simultáneas:</span><span class="sxs-lookup"><span data-stu-id="d32c4-195">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="d32c4-196">Coloque las operaciones después de la línea `Loading = true;` en el bloque `try`.</span><span class="sxs-lookup"><span data-stu-id="d32c4-196">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="d32c4-197">En el caso de las operaciones de larga duración que aprovechan el [seguimiento de cambios](/ef/core/querying/tracking) o el [control de simultaneidad](/ef/core/saving/concurrency) de EF Core, [el ámbito del contexto debe ser la duración del componente](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="d32c4-197">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="d32c4-198">Nuevas instancias de DbContext</span><span class="sxs-lookup"><span data-stu-id="d32c4-198">New DbContext instances</span></span></h3>

<span data-ttu-id="d32c4-199">La manera más rápida de crear una instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> consiste en usar `new`.</span><span class="sxs-lookup"><span data-stu-id="d32c4-199">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="d32c4-200">Pero hay varios escenarios es los que puede ser necesario resolver dependencias adicionales.</span><span class="sxs-lookup"><span data-stu-id="d32c4-200">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="d32c4-201">Por ejemplo, es posible que quiera usar [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar el contexto.</span><span class="sxs-lookup"><span data-stu-id="d32c4-201">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="d32c4-202">La solución recomendada para crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> con dependencias es usar un generador.</span><span class="sxs-lookup"><span data-stu-id="d32c4-202">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="d32c4-203">La aplicación de ejemplo implementa su propio generador en `Data/DbContextFactory.cs`.</span><span class="sxs-lookup"><span data-stu-id="d32c4-203">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="d32c4-204">En el generador anterior:</span><span class="sxs-lookup"><span data-stu-id="d32c4-204">In the preceding factory:</span></span>

* <span data-ttu-id="d32c4-205"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisface todas las dependencias a través del proveedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="d32c4-205"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="d32c4-206">`IDbContextFactory` está disponible en EF Core ASP.NET Core 5.0 o posterior, por lo que la interfaz se [implementa en la aplicación de ejemplo para ASP.NET Core 3 y siguientes](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="d32c4-206">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="d32c4-207">En el ejemplo siguiente se configura [SQLite](https://www.sqlite.org/index.html) y se habilita el registro de datos.</span><span class="sxs-lookup"><span data-stu-id="d32c4-207">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="d32c4-208">El código usa un método de extensión para configurar el generador de bases de datos para la inserción de dependencias y proporcionar opciones predeterminadas:</span><span class="sxs-lookup"><span data-stu-id="d32c4-208">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="d32c4-209">El generador se inserta en los componentes y se usa para crear instancias.</span><span class="sxs-lookup"><span data-stu-id="d32c4-209">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="d32c4-210">Por ejemplo, en `Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="d32c4-210">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="d32c4-211">`Wrapper` es una [referencia de componente](xref:blazor/components/index#capture-references-to-components) del componente de `GridWrapper`.</span><span class="sxs-lookup"><span data-stu-id="d32c4-211">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="d32c4-212">Vea el componente de `Index` (`Pages/Index.razor`) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="d32c4-212">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="d32c4-213">Se pueden crear instancias de <xref:Microsoft.EntityFrameworkCore.DbContext> con un generador que permite configurar la cadena de conexión por `DbContext`, como cuando se usa [modelo de Identity de ASP.NET Core])(xref:security/authentication/customize_identity_model):</span><span class="sxs-lookup"><span data-stu-id="d32c4-213">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="d32c4-214">Ámbito de la duración del componente</span><span class="sxs-lookup"><span data-stu-id="d32c4-214">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="d32c4-215">Es posible que quiera crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> que exista mientras dure un componente.</span><span class="sxs-lookup"><span data-stu-id="d32c4-215">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="d32c4-216">Esto le permite usarlo como una [unidad de trabajo](https://martinfowler.com/eaaCatalog/unitOfWork.html) y aprovechar características integradas como el seguimiento de cambios y la resolución de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="d32c4-216">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="d32c4-217">Puede usar el generador para crear un contexto y realizar su seguimiento mientras dure el componente.</span><span class="sxs-lookup"><span data-stu-id="d32c4-217">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="d32c4-218">En primer lugar, implemente <xref:System.IDisposable> e inserte el generador como se muestra en `Pages/EditContact.razor`:</span><span class="sxs-lookup"><span data-stu-id="d32c4-218">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="d32c4-219">La aplicación de ejemplo garantiza que el contexto se desecha cuando se desecha el componente:</span><span class="sxs-lookup"><span data-stu-id="d32c4-219">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="d32c4-220">Por último, se invalida [`OnInitializedAsync`](xref:blazor/components/lifecycle) para crear un contexto.</span><span class="sxs-lookup"><span data-stu-id="d32c4-220">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="d32c4-221">En la aplicación de ejemplo, [`OnInitializedAsync`](xref:blazor/components/lifecycle) carga el contacto en el mismo método:</span><span class="sxs-lookup"><span data-stu-id="d32c4-221">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="d32c4-222">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="d32c4-222">In the preceding example:</span></span>

* <span data-ttu-id="d32c4-223">Cuando `Busy` se establece en `true`, pueden comenzar las operaciones asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="d32c4-223">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="d32c4-224">Cuando `Busy` se vuelve a establecer en `false`, las operaciones asincrónicas deben finalizar.</span><span class="sxs-lookup"><span data-stu-id="d32c4-224">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="d32c4-225">Coloque la lógica de control de errores adicional en un bloque `catch`.</span><span class="sxs-lookup"><span data-stu-id="d32c4-225">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="d32c4-226">Habilitar el registro de datos confidenciales</span><span class="sxs-lookup"><span data-stu-id="d32c4-226">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="d32c4-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> incluye datos de la aplicación en los mensajes de excepción y en la plataforma de registro.</span><span class="sxs-lookup"><span data-stu-id="d32c4-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="d32c4-228">Los datos registrados pueden incluir los valores asignados a las propiedades de las instancias de entidad y los valores de parámetro para los comandos enviados a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d32c4-228">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="d32c4-229">El registro de datos con <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> es un **riesgo de seguridad**, ya que puede exponer contraseñas y otra información de identificación personal (PII) cuando registra instrucciones SQL ejecutadas en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d32c4-229">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="d32c4-230">Se recomienda habilitar <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> solo para desarrollo y pruebas:</span><span class="sxs-lookup"><span data-stu-id="d32c4-230">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d32c4-231">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d32c4-231">Additional resources</span></span>

* [<span data-ttu-id="d32c4-232">Documentación de EF Core</span><span class="sxs-lookup"><span data-stu-id="d32c4-232">EF Core documentation</span></span>](/ef/)
