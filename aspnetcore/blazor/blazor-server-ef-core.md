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
ms.openlocfilehash: 10fc0afe84065f2c226d1e9c2f4314142369613a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97011889"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a>Blazor Server de ASP.NET Core con Entity Framework Core (EF Core)

Por: [Jeremy Likness](https://github.com/JeremyLikness)

:::moniker range=">= aspnetcore-5.0"

El servidor Blazor Server es un marco para aplicaciones con estado. La aplicación mantiene una conexión continua con el servidor, y el estado del usuario se mantiene en la memoria del servidor en un *circuito*. Un ejemplo de estado del usuario son los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) que se encuentran en el ámbito del circuito. El modelo de aplicación único que proporciona Blazor Server requiere un enfoque especial para usar Entity Framework Core.

> [!NOTE]
> En este artículo se describe EF Core en aplicaciones Blazor Server. Las aplicaciones Blazor WebAssembly se ejecutan en un espacio aislado de WebAssembly que evita la mayoría de conexiones de base de datos directas. La ejecución de EF Core en Blazor WebAssembly supera el ámbito de este artículo.

<h2 id="sample-app-5x">Aplicación de ejemplo</h2>

La aplicación de ejemplo se ha compilado como una referencia para las aplicaciones Blazor Server en las que se usa EF Core. La aplicación de ejemplo incluye una cuadrícula con operaciones de ordenación y filtrado, eliminación, adición y actualización. En el ejemplo se muestra el uso de EF Core para controlar la simultaneidad optimista.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

En el ejemplo se usa una base de datos [SQLite](https://www.sqlite.org/index.html) local para que se pueda utilizar en cualquier plataforma. En el ejemplo también se configura el registro de base de datos para mostrar las consultas SQL que se generan. Esto se configura en `appsettings.Development.json`:

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Los componentes de cuadrícula, adición y vista usan el patrón de "contexto por operación", en el que se crea un contexto para cada operación. El componente de edición usa el patrón de "contexto por componente", en el que se crea un contexto para cada componente.

> [!NOTE]
> En algunos de los ejemplos de código de este tema se necesitan espacios de nombres y servicios que no se muestran. Para inspeccionar el código totalmente operativo, incluidas las directivas obligatorias [`@using`](xref:mvc/views/razor#using) e [`@inject`](xref:mvc/views/razor#inject) para ejemplos de Razor, vea la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).

<h2 id="database-access-5x">Acceso a la base de datos</h2>

EF Core se basa en un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> como medio para [configurar el acceso a la base de datos](/ef/core/miscellaneous/configuring-dbcontext) y actuar como una [*unidad de trabajo*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core proporciona la extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> para las aplicaciones de ASP.NET Core, que registra el contexto como un servicio *con ámbito* de forma predeterminada. En las aplicaciones Blazor Server, los registros con ámbito de servicio pueden ser problemáticos, ya que la instancia se comparte entre los componentes del circuito del usuario. <xref:Microsoft.EntityFrameworkCore.DbContext> no es seguro para subprocesos y no está diseñado para su uso simultáneo. Las duraciones existentes no son adecuadas por estos motivos:

* **Singleton** comparte el estado entre todos los usuarios de la aplicación y lleva a un uso simultáneo inadecuado.
* **Con ámbito** (el valor predeterminado) supone un problema similar entre los componentes para el mismo usuario.
* **Transitorio** genera una nueva instancia por solicitud, pero como los componentes pueden ser de larga duración, esto da lugar a un contexto que dura más de lo previsto.

Las recomendaciones siguientes están diseñadas para proporcionar un enfoque coherente al uso de EF Core en aplicaciones Blazor Server.

* De forma predeterminada, considere la posibilidad de usar un contexto por operación. El contexto está diseñado para la creación de instancias rápidas de baja sobrecarga:

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Use una marca para evitar varias operaciones simultáneas:

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

  Coloque las operaciones después de la línea `Loading = true;` en el bloque `try`.

* En el caso de las operaciones de larga duración que aprovechan el [seguimiento de cambios](/ef/core/querying/tracking) o el [control de simultaneidad](/ef/core/saving/concurrency) de EF Core, [el ámbito del contexto debe ser la duración del componente](#scope-to-the-component-lifetime-5x).

<h3 id="new-dbcontext-instances-5x">Nuevas instancias de DbContext</h3>

La manera más rápida de crear una instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> consiste en usar `new`. Pero hay varios escenarios es los que puede ser necesario resolver dependencias adicionales. Por ejemplo, es posible que quiera usar [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar el contexto.

La solución recomendada para crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> con dependencias es usar un generador. En EF Core 5.0 o posterior se proporciona un generador integrado para crear contextos.

En el ejemplo siguiente se configura [SQLite](https://www.sqlite.org/index.html) y se habilita el registro de datos. El código usa un [método de extensión (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) para configurar el generador de bases de datos para la inserción de dependencias y proporcionar opciones predeterminadas:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

El generador se inserta en los componentes y se usa para crear instancias. Por ejemplo, en `Pages/Index.razor`:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` es una [referencia de componente](xref:blazor/components/index#capture-references-to-components) del componente de `GridWrapper`. Vea el componente de `Index` (`Pages/Index.razor`) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).

Se pueden crear instancias de <xref:Microsoft.EntityFrameworkCore.DbContext> con un generador que permite configurar la cadena de conexión por `DbContext`, como cuando se usa [el modelo Identity de ASP.NET Core](xref:security/authentication/customize_identity_model):

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x">Ámbito de la duración del componente</h3>

Es posible que quiera crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> que exista mientras dure un componente. Esto le permite usarlo como una [unidad de trabajo](https://martinfowler.com/eaaCatalog/unitOfWork.html) y aprovechar características integradas como el seguimiento de cambios y la resolución de simultaneidad.
Puede usar el generador para crear un contexto y realizar su seguimiento mientras dure el componente. En primer lugar, implemente <xref:System.IDisposable> e inserte el generador como se muestra en `Pages/EditContact.razor`:

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

La aplicación de ejemplo garantiza que el contexto se desecha cuando se desecha el componente:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Por último, se invalida [`OnInitializedAsync`](xref:blazor/components/lifecycle) para crear un contexto. En la aplicación de ejemplo, [`OnInitializedAsync`](xref:blazor/components/lifecycle) carga el contacto en el mismo método:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging">Habilitar el registro de datos confidenciales</h3>

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> incluye datos de la aplicación en los mensajes de excepción y en la plataforma de registro. Los datos registrados pueden incluir los valores asignados a las propiedades de las instancias de entidad y los valores de parámetro para los comandos enviados a la base de datos. El registro de datos con <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> es un **riesgo de seguridad**, ya que puede exponer contraseñas y otra información de identificación personal (PII) cuando registra instrucciones SQL ejecutadas en la base de datos.

Se recomienda habilitar <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> solo para desarrollo y pruebas:

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

El servidor Blazor Server es un marco para aplicaciones con estado. La aplicación mantiene una conexión continua con el servidor, y el estado del usuario se mantiene en la memoria del servidor en un *circuito*. Un ejemplo de estado del usuario son los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) que se encuentran en el ámbito del circuito. El modelo de aplicación único que proporciona Blazor Server requiere un enfoque especial para usar Entity Framework Core.

> [!NOTE]
> En este artículo se describe EF Core en aplicaciones Blazor Server. Las aplicaciones Blazor WebAssembly se ejecutan en un espacio aislado de WebAssembly que evita la mayoría de conexiones de base de datos directas. La ejecución de EF Core en Blazor WebAssembly supera el ámbito de este artículo.

<h2 id="sample-app-3x">Aplicación de ejemplo</h2>

La aplicación de ejemplo se ha compilado como una referencia para las aplicaciones Blazor Server en las que se usa EF Core. La aplicación de ejemplo incluye una cuadrícula con operaciones de ordenación y filtrado, eliminación, adición y actualización. En el ejemplo se muestra el uso de EF Core para controlar la simultaneidad optimista.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

En el ejemplo se usa una base de datos [SQLite](https://www.sqlite.org/index.html) local para que se pueda utilizar en cualquier plataforma. En el ejemplo también se configura el registro de base de datos para mostrar las consultas SQL que se generan. Esto se configura en `appsettings.Development.json`:

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Los componentes de cuadrícula, adición y vista usan el patrón de "contexto por operación", en el que se crea un contexto para cada operación. El componente de edición usa el patrón de "contexto por componente", en el que se crea un contexto para cada componente.

> [!NOTE]
> En algunos de los ejemplos de código de este tema se necesitan espacios de nombres y servicios que no se muestran. Para inspeccionar el código totalmente operativo, incluidas las directivas obligatorias [`@using`](xref:mvc/views/razor#using) e [`@inject`](xref:mvc/views/razor#inject) para ejemplos de Razor, vea la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).

<h2 id="database-access-3x">Acceso a la base de datos</h2>

EF Core se basa en un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> como medio para [configurar el acceso a la base de datos](/ef/core/miscellaneous/configuring-dbcontext) y actuar como una [*unidad de trabajo*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core proporciona la extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> para las aplicaciones de ASP.NET Core, que registra el contexto como un servicio *con ámbito* de forma predeterminada. En las aplicaciones Blazor Server, esto puede suponer un problema, ya que la instancia se comparte entre los componentes del circuito del usuario. <xref:Microsoft.EntityFrameworkCore.DbContext> no es seguro para subprocesos y no está diseñado para su uso simultáneo. Las duraciones existentes no son adecuadas por estos motivos:

* **Singleton** comparte el estado entre todos los usuarios de la aplicación y lleva a un uso simultáneo inadecuado.
* **Con ámbito** (el valor predeterminado) supone un problema similar entre los componentes para el mismo usuario.
* **Transitorio** genera una nueva instancia por solicitud, pero como los componentes pueden ser de larga duración, esto da lugar a un contexto que dura más de lo previsto.

Las recomendaciones siguientes están diseñadas para proporcionar un enfoque coherente al uso de EF Core en aplicaciones Blazor Server.

* De forma predeterminada, considere la posibilidad de usar un contexto por operación. El contexto está diseñado para la creación de instancias rápidas de baja sobrecarga:

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Use una marca para evitar varias operaciones simultáneas:

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

  Coloque las operaciones después de la línea `Loading = true;` en el bloque `try`.

* En el caso de las operaciones de larga duración que aprovechan el [seguimiento de cambios](/ef/core/querying/tracking) o el [control de simultaneidad](/ef/core/saving/concurrency) de EF Core, [el ámbito del contexto debe ser la duración del componente](#scope-to-the-component-lifetime-3x).

<h3 id="new-dbcontext-instances-3x">Nuevas instancias de DbContext</h3>

La manera más rápida de crear una instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> consiste en usar `new`. Pero hay varios escenarios es los que puede ser necesario resolver dependencias adicionales. Por ejemplo, es posible que quiera usar [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) para configurar el contexto.

La solución recomendada para crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> con dependencias es usar un generador. La aplicación de ejemplo implementa su propio generador en `Data/DbContextFactory.cs`.

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

En el generador anterior:

* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisface todas las dependencias a través del proveedor de servicios.
* `IDbContextFactory` está disponible en EF Core ASP.NET Core 5.0 o posterior, por lo que la interfaz se [implementa en la aplicación de ejemplo para ASP.NET Core 3 y siguientes](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).

En el ejemplo siguiente se configura [SQLite](https://www.sqlite.org/index.html) y se habilita el registro de datos. El código usa un método de extensión para configurar el generador de bases de datos para la inserción de dependencias y proporcionar opciones predeterminadas:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

El generador se inserta en los componentes y se usa para crear instancias. Por ejemplo, en `Pages/Index.razor`:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` es una [referencia de componente](xref:blazor/components/index#capture-references-to-components) del componente de `GridWrapper`. Vea el componente de `Index` (`Pages/Index.razor`) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).

Se pueden crear instancias de <xref:Microsoft.EntityFrameworkCore.DbContext> con un generador que permite configurar la cadena de conexión por `DbContext`, como cuando se usa [modelo de Identity de ASP.NET Core])(xref:security/authentication/customize_identity_model):

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x">Ámbito de la duración del componente</h3>

Es posible que quiera crear un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> que exista mientras dure un componente. Esto le permite usarlo como una [unidad de trabajo](https://martinfowler.com/eaaCatalog/unitOfWork.html) y aprovechar características integradas como el seguimiento de cambios y la resolución de simultaneidad.
Puede usar el generador para crear un contexto y realizar su seguimiento mientras dure el componente. En primer lugar, implemente <xref:System.IDisposable> e inserte el generador como se muestra en `Pages/EditContact.razor`:

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

La aplicación de ejemplo garantiza que el contexto se desecha cuando se desecha el componente:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Por último, se invalida [`OnInitializedAsync`](xref:blazor/components/lifecycle) para crear un contexto. En la aplicación de ejemplo, [`OnInitializedAsync`](xref:blazor/components/lifecycle) carga el contacto en el mismo método:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

En el ejemplo anterior:

* Cuando `Busy` se establece en `true`, pueden comenzar las operaciones asincrónicas. Cuando `Busy` se vuelve a establecer en `false`, las operaciones asincrónicas deben finalizar.
* Coloque la lógica de control de errores adicional en un bloque `catch`.

<h3 id="enable-sensitive-data-logging">Habilitar el registro de datos confidenciales</h3>

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> incluye datos de la aplicación en los mensajes de excepción y en la plataforma de registro. Los datos registrados pueden incluir los valores asignados a las propiedades de las instancias de entidad y los valores de parámetro para los comandos enviados a la base de datos. El registro de datos con <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> es un **riesgo de seguridad**, ya que puede exponer contraseñas y otra información de identificación personal (PII) cuando registra instrucciones SQL ejecutadas en la base de datos.

Se recomienda habilitar <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> solo para desarrollo y pruebas:

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

## <a name="additional-resources"></a>Recursos adicionales

* [Documentación de EF Core](/ef/)
