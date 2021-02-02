---
title: Inicio de la aplicación en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo la clase Startup de ASP.NET Core configura los servicios y la canalización de solicitudes de la aplicación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/startup
ms.openlocfilehash: e2825476c5fe36571b365ac5ee3c57ff4db61b87
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658682"
---
# <a name="app-startup-in-aspnet-core"></a>Inicio de la aplicación en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) y [Steve Smith](https://ardalis.com)

La clase `Startup` configura los servicios y la canalización de solicitudes de la aplicación.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>Clase Startup

Las aplicaciones de ASP.NET Core utilizan una clase `Startup`, que se denomina `Startup` por convención. La clase `Startup`:

* Incluye opcionalmente un método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> para configurar los *servicios* de la aplicación. Un servicio es un componente reutilizable que proporciona funcionalidades de la aplicación. Los servicios se *registran* en `ConfigureServices` y se usan en la aplicación a través de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) o <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.
* Incluye un método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> para crear la canalización de procesamiento de solicitudes de la aplicación.

El tiempo de ejecución ASP.NET Core llama a `ConfigureServices` y `Configure` cuando la aplicación se inicia:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

El ejemplo anterior corresponde a [Razor Pages](xref:razor-pages/index); la versión para MVC es similar.


La clase `Startup` se especifica cuando se crea el [host](xref:fundamentals/index#host) de la aplicación. Habitualmente, la clase `Startup` se especifica mediante una llamada al método[WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) en el generador de host:

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

El host proporciona servicios que están disponibles para el constructor de clase `Startup`. La aplicación agrega servicios adicionales a través de `ConfigureServices`. Los servicios de la aplicación y el host están disponibles en `Configure` y en toda la aplicación.

Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el [host genérico](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

La mayoría de los servicios no están disponibles hasta que se llama al método `Configure`.

### <a name="multiple-startup"></a>Inicio múltiple

Cuando la aplicación define clases `Startup` independientes para otros entornos (por ejemplo, `StartupDevelopment`), la clase `Startup` correspondiente se selecciona en tiempo de ejecución. La clase cuyo sufijo de nombre coincide con el entorno actual se establece como prioritaria. Si la aplicación se ejecuta en el entorno de desarrollo e incluye tanto la clase `Startup` como la clase `StartupDevelopment`, se utiliza la clase `StartupDevelopment`. Para obtener más información, consulte [Uso de varios entornos](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Para obtener más información sobre el host, vea [El host](xref:fundamentals/index#host). Para obtener información sobre cómo controlar los errores que se producen durante el inicio, consulte [Control de excepciones de inicio](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Método ConfigureServices

El método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> es:

* Opcional.
* Lo llama el host antes del método `Configure` para configurar los servicios de la aplicación.
* Es donde se establecen por convención las [opciones de configuración](xref:fundamentals/configuration/index).

El host puede configurar algunos servicios antes de que se llame a los métodos `Startup`. Para obtener más información, vea [El host](xref:fundamentals/index#host).

Para las características que requieren una configuración sustancial, hay métodos de extensión `Add{Service}` en <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Por ejemplo, **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores y **Add**RazorPages:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

La adición de servicios al contenedor de servicios hace que estén disponibles en la aplicación y en el método `Configure`. Los servicios se resuelven a través de la [inserción de dependencias](xref:fundamentals/dependency-injection) o desde <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

## <a name="the-configure-method"></a>El método Configure

El método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> se usa para especificar la forma en que la aplicación responde a las solicitudes HTTP. La canalización de solicitudes se configura mediante la adición de componentes de [middleware](xref:fundamentals/middleware/index) a una instancia de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` está disponible para el método `Configure`, pero no está registrado en el contenedor de servicios. El hospedaje crea un elemento `IApplicationBuilder` y lo pasa directamente a `Configure`.

Las [plantillas de ASP.NET Core](/dotnet/core/tools/dotnet-new) configuran la canalización con compatibilidad para lo siguiente:

* [Página de excepciones para el desarrollador](xref:fundamentals/error-handling#developer-exception-page)
* [Controlador de excepciones](xref:fundamentals/error-handling#exception-handler-page)
* [Seguridad de transporte estricta de HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Redireccionamiento de HTTPS](xref:security/enforcing-ssl)
* [Archivos estáticos](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) y [Razor Pages](xref:razor-pages/index)


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

El ejemplo anterior corresponde a [Razor Pages](xref:razor-pages/index); la versión para MVC es similar.

Cada método de extensión `Use` agrega uno o más componentes de middleware a la canalización de solicitudes. Por ejemplo, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configura [middleware](xref:fundamentals/middleware/index) para proporcionar [archivos estáticos](xref:fundamentals/static-files).

Cada componente de middleware de la canalización de solicitudes es responsable de invocar al siguiente componente de la canalización o de cortocircuitar la cadena en caso de ser necesario.

En la firma del método `Configure` se pueden especificar servicios adicionales, como `IWebHostEnvironment`, `ILoggerFactory`, o bien todo lo definido en `ConfigureServices`. Estos servicios adicionales se insertan si están disponibles.

Para obtener más información sobre cómo usar `IApplicationBuilder` y el orden de procesamiento de middleware, consulte <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Configuración de servicios sin inicio

Para configurar los servicios y la canalización de procesamiento de solicitudes sin usar una clase `Startup`, llame a los métodos de conveniencia `ConfigureServices` y `Configure` en el generador de host. Varias llamadas a `ConfigureServices` se anexan entre sí. Si hay varias llamadas al método `Configure`, se usa la última llamada a `Configure`.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>Extensión del inicio con filtros de inicio

Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:

* Para configurar el middleware al principio o al final de la canalización de middleware [Configure](#the-configure-method) de una aplicación sin una llamada explícita a `Use{Middleware}`. ASP.NET Core usa `IStartupFilter` para agregar los valores predeterminados al principio de la canalización sin que el creador de la aplicación tenga que registrar explícitamente el middleware predeterminado. `IStartupFilter` permite que otro componente llame a `Use{Middleware}` en nombre del creador de la aplicación.
* Para crear una canalización de métodos `Configure`. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) puede configurar middleware para que se ejecute antes o después del middleware agregado por las bibliotecas.

`IStartupFilter` implementa <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, que recibe y devuelve un elemento `Action<IApplicationBuilder>`. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> define una clase para configurar la canalización de solicitudes de una aplicación. Para más información, vea [Creación de una canalización de middleware con IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Cada instancia de `IStartupFilter` puede agregar uno o más middleware en la canalización de solicitudes. Los filtros se invocan en el orden en que se agregaron al contenedor de servicios. Los filtros pueden agregar middleware antes o después de pasar el control al siguiente filtro, por lo que se anexan al principio o al final de la canalización de la aplicación.

En el ejemplo siguiente, se muestra cómo registrar un componente de middleware con `IStartupFilter`. El componente de middleware `RequestSetOptionsMiddleware` establece un valor de opciones de un parámetro de cadena de consulta:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

`RequestSetOptionsMiddleware` está configurado en las clase `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter` se registra en el contenedor de servicios en <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

Cuando se proporciona un parámetro de cadena de consulta para `option`, el middleware procesa la asignación del valor antes de que el middleware de ASP.NET Core represente la respuesta.

El orden de ejecución de middleware se establece según el orden de registros de `IStartupFilter`:

* Varias implementaciones de `IStartupFilter` pueden interactuar con los mismos objetos. Si el orden es importante, ordene los registros de servicio de `IStartupFilter` para que coincidan con el orden en que se deben ejecutar los middleware.
* Las bibliotecas pueden agregar middleware con una o varias implementaciones de `IStartupFilter` que se ejecuten antes o después de otro middleware de aplicación registrado con `IStartupFilter`. Para invocar un middleware `IStartupFilter` antes de un middleware agregado por el elemento `IStartupFilter` de una biblioteca:

  * Coloque el registro del servicio antes de que la biblioteca se agregue al contenedor de servicios.
  * Para la invocación posterior, coloque el registro del servicio después de que se agregue la biblioteca.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Agregar opciones de configuración en el inicio desde un ensamblado externo

Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo fuera de la clase `Startup` de esta. Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Recursos adicionales

* [El host](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>Clase Startup

Las aplicaciones de ASP.NET Core utilizan una clase `Startup`, que se denomina `Startup` por convención. La clase `Startup`:

* Incluye opcionalmente un método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> para configurar los *servicios* de la aplicación. Un servicio es un componente reutilizable que proporciona funcionalidades de la aplicación. Los servicios se *registran* en `ConfigureServices` y se usan en la aplicación a través de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) o <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.
* Incluye un método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> para crear la canalización de procesamiento de solicitudes de la aplicación.

El tiempo de ejecución ASP.NET Core llama a `ConfigureServices` y `Configure` cuando la aplicación se inicia:

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

La clase `Startup` se especifica cuando se crea el [host](xref:fundamentals/index#host) de la aplicación. Habitualmente, la clase `Startup` se especifica mediante una llamada al método[WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) en el generador de host:

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

El host proporciona servicios que están disponibles para el constructor de clase `Startup`. La aplicación agrega servicios adicionales a través de `ConfigureServices`. Después, los servicios de la aplicación y el host están disponibles en `Configure` y en toda la aplicación.

Un uso común de la [inserción de dependencias](xref:fundamentals/dependency-injection) en la clase `Startup` consiste en insertar:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> para configurar servicios según el entorno.
* <xref:Microsoft.Extensions.Configuration.IConfiguration> para leer la configuración.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory> para crear un registrador en `Startup.ConfigureServices`.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

La mayoría de los servicios no están disponibles hasta que se llama al método `Configure`.

### <a name="multiple-startup"></a>Inicio múltiple

Cuando la aplicación define clases `Startup` independientes para otros entornos (por ejemplo, `StartupDevelopment`), la clase `Startup` correspondiente se selecciona en tiempo de ejecución. La clase cuyo sufijo de nombre coincide con el entorno actual se establece como prioritaria. Si la aplicación se ejecuta en el entorno de desarrollo e incluye tanto la clase `Startup` como la clase `StartupDevelopment`, se utiliza la clase `StartupDevelopment`. Para obtener más información, consulte [Uso de varios entornos](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Para obtener más información sobre el host, vea [El host](xref:fundamentals/index#host). Para obtener información sobre cómo controlar los errores que se producen durante el inicio, consulte [Control de excepciones de inicio](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Método ConfigureServices

El método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> es:

* Opcional.
* Lo llama el host antes del método `Configure` para configurar los servicios de la aplicación.
* Es donde se establecen por convención las [opciones de configuración](xref:fundamentals/configuration/index).

El host puede configurar algunos servicios antes de que se llame a los métodos `Startup`. Para obtener más información, vea [El host](xref:fundamentals/index#host).

Para las características que requieren una configuración sustancial, hay métodos de extensión `Add{Service}` en <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Por ejemplo, **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores y **Add**RazorPages:

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

La adición de servicios al contenedor de servicios hace que estén disponibles en la aplicación y en el método `Configure`. Los servicios se resuelven a través de la [inserción de dependencias](xref:fundamentals/dependency-injection) o desde <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

Vea [SetCompatibilityVersion](xref:mvc/compatibility-version) para obtener más información sobre `SetCompatibilityVersion`.

## <a name="the-configure-method"></a>El método Configure

El método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> se usa para especificar la forma en que la aplicación responde a las solicitudes HTTP. La canalización de solicitudes se configura mediante la adición de componentes de [middleware](xref:fundamentals/middleware/index) a una instancia de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` está disponible para el método `Configure`, pero no está registrado en el contenedor de servicios. El hospedaje crea un elemento `IApplicationBuilder` y lo pasa directamente a `Configure`.

Las [plantillas de ASP.NET Core](/dotnet/core/tools/dotnet-new) configuran la canalización con compatibilidad para lo siguiente:

* [Página de excepciones para el desarrollador](xref:fundamentals/error-handling#developer-exception-page)
* [Controlador de excepciones](xref:fundamentals/error-handling#exception-handler-page)
* [Seguridad de transporte estricta de HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Redireccionamiento de HTTPS](xref:security/enforcing-ssl)
* [Archivos estáticos](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) y [Razor Pages](xref:razor-pages/index)
* [Reglamento General de Protección de Datos (GDPR)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Cada método de extensión `Use` agrega uno o más componentes de middleware a la canalización de solicitudes. Por ejemplo, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configura [middleware](xref:fundamentals/middleware/index) para proporcionar [archivos estáticos](xref:fundamentals/static-files).

Cada componente de middleware de la canalización de solicitudes es responsable de invocar al siguiente componente de la canalización o de cortocircuitar la cadena en caso de ser necesario.

En la firma del método `Configure` se pueden especificar servicios adicionales, como `IHostingEnvironment` e `ILoggerFactory`, o bien todo lo definido en `ConfigureServices`. Estos servicios adicionales se insertan si están disponibles.

Para obtener más información sobre cómo usar `IApplicationBuilder` y el orden de procesamiento de middleware, consulte <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Configuración de servicios sin inicio

Para configurar los servicios y la canalización de procesamiento de solicitudes sin usar una clase `Startup`, llame a los métodos de conveniencia `ConfigureServices` y `Configure` en el generador de host. Varias llamadas a `ConfigureServices` se anexan entre sí. Si hay varias llamadas al método `Configure`, se usa la última llamada a `Configure`.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Extensión del inicio con filtros de inicio

Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:

* Para configurar el middleware al principio o al final de la canalización de middleware [Configure](#the-configure-method) de una aplicación sin una llamada explícita a `Use{Middleware}`. ASP.NET Core usa `IStartupFilter` para agregar los valores predeterminados al principio de la canalización sin que el creador de la aplicación tenga que registrar explícitamente el middleware predeterminado. `IStartupFilter` permite que otro componente llame a `Use{Middleware}` en nombre del creador de la aplicación.
* Para crear una canalización de métodos `Configure`. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) puede configurar middleware para que se ejecute antes o después del middleware agregado por las bibliotecas.

`IStartupFilter` implementa <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, que recibe y devuelve un elemento `Action<IApplicationBuilder>`. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> define una clase para configurar la canalización de solicitudes de una aplicación. Para más información, vea [Creación de una canalización de middleware con IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Cada instancia de `IStartupFilter` puede agregar uno o más middleware en la canalización de solicitudes. Los filtros se invocan en el orden en que se agregaron al contenedor de servicios. Los filtros pueden agregar middleware antes o después de pasar el control al siguiente filtro, por lo que se anexan al principio o al final de la canalización de la aplicación.

En el ejemplo siguiente, se muestra cómo registrar un componente de middleware con `IStartupFilter`. El componente de middleware `RequestSetOptionsMiddleware` establece un valor de opciones de un parámetro de cadena de consulta:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

`RequestSetOptionsMiddleware` está configurado en las clase `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter` se registra en el contenedor de servicios en <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Cuando se proporciona un parámetro de cadena de consulta para `option`, el middleware procesa la asignación del valor antes de que el middleware de ASP.NET Core represente la respuesta.

El orden de ejecución de middleware se establece según el orden de registros de `IStartupFilter`:

* Varias implementaciones de `IStartupFilter` pueden interactuar con los mismos objetos. Si el orden es importante, ordene los registros de servicio de `IStartupFilter` para que coincidan con el orden en que se deben ejecutar los middleware.
* Las bibliotecas pueden agregar middleware con una o varias implementaciones de `IStartupFilter` que se ejecuten antes o después de otro middleware de aplicación registrado con `IStartupFilter`. Para invocar un middleware `IStartupFilter` antes de un middleware agregado por el elemento `IStartupFilter` de una biblioteca:

  * Coloque el registro del servicio antes de que la biblioteca se agregue al contenedor de servicios.
  * Para la invocación posterior, coloque el registro del servicio después de que se agregue la biblioteca.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Agregar opciones de configuración en el inicio desde un ensamblado externo

Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo fuera de la clase `Startup` de esta. Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Recursos adicionales

* [El host](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end
