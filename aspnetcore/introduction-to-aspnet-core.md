---
title: Introducción a ASP.NET Core
author: rick-anderson
description: Obtenga una introducción a ASP.NET Core, un marco multiplataforma de código abierto y de alto rendimiento que tiene como finalidad compilar aplicaciones modernas conectadas a Internet y habilitadas para la nube.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: index
ms.openlocfilehash: 4301e0d59364573767ab4cae25a4818ff84b9abc
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93052232"
---
# <a name="introduction-to-aspnet-core"></a>Introducción a ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) y [Shaun Luttin](https://twitter.com/dicshaunary)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core es un marco multiplataforma de [código abierto](https://github.com/dotnet/aspnetcore) y de alto rendimiento que tiene como finalidad compilar aplicaciones modernas conectadas a Internet y habilitadas para la nube. Con ASP.NET Core puede hacer lo siguiente:

* Compilar servicios y aplicaciones web, aplicaciones de [Internet de las cosas (IoT)](https://www.microsoft.com/internet-of-things/) y back-ends móviles.
* Usar sus herramientas de desarrollo favoritas en Windows, macOS y Linux.
* Efectuar implementaciones locales y en la nube.
* Ejecutar en [.NET Core](/dotnet/core/introduction).

## <a name="why-choose-aspnet-core"></a>¿Por qué elegir ASP.NET Core?

Millones de desarrolladores usan o han usado [ASP.NET 4.x](/aspnet/overview) para crear aplicaciones web. ASP.NET Core es un nuevo diseño de ASP.NET 4.x que incluye cambios en la arquitectura que dan como resultado un marco más sencillo y modular.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Creación de API web e interfaces de usuario web mediante ASP.NET Core MVC

ASP.NET Core MVC proporciona características para crear [API web](xref:tutorials/first-web-api) y [aplicaciones web](xref:tutorials/razor-pages/index):

* El [patrón Modelo-Vista-Controlador (MVC)](xref:mvc/overview) permite que se puedan hacer pruebas en las API web y en las aplicaciones web.
* [Razor Pages](xref:razor-pages/index) es un modelo de programación basado en páginas que facilita la compilación de interfaces de usuario web y hace que sea más productiva.
* El [marcado de RazorRazor](xref:mvc/views/razor) proporciona una sintaxis productiva para [Razor Pages](xref:razor-pages/index) y las [vistas de MVC](xref:mvc/views/overview).
* Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.
* La compatibilidad integrada para [varios formatos de datos y la negociación de contenidos](xref:web-api/advanced/formatting) permite que las API web lleguen a una amplia gama de clientes, como los exploradores y los dispositivos móviles.
* El [enlace de modelo](xref:mvc/models/model-binding) asigna automáticamente datos de solicitudes HTTP a parámetros de método de acción.
* La [validación de modelos](xref:mvc/models/validation) efectúa una validación del lado cliente y del lado servidor de forma automática.

## <a name="client-side-development"></a>Desarrollo del lado del cliente

ASP.NET Core se integra perfectamente con bibliotecas y plataformas de trabajo populares del lado cliente, que incluyen [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) y [Bootstrap](https://getbootstrap.com/). Para más información, consulte <xref:blazor/index> y los temas relacionados en *Client-side development* (Desarrollo del lado cliente).

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>Plataformas de destino de ASP.NET Core

ASP.NET Core 3.x y las versiones posteriores solo pueden tener como destino .NET Core. Por lo general, ASP.NET Core se compone de bibliotecas de [.NET Standard](/dotnet/standard/net-standard). Las bibliotecas escritas con .NET Standard 2.0 se ejecutan en [cualquier plataforma .NET que implementa .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

El uso de .NET Core como destino cuenta con varias ventajas que van en aumento con cada versión. Entre las ventajas del uso de .NET Core en vez de .NET Framework se incluyen las siguientes:

* Multiplataforma. Es compatible con Windows, macOS y Linux.
* Rendimiento mejorado
* [Control de versiones en paralelo](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Nuevas API.
* Código Abierto

## <a name="recommended-learning-path"></a>Ruta de aprendizaje recomendada

Se recomienda realizar la siguiente secuencia de tutoriales a modo de introducción para desarrollar aplicaciones de ASP.NET Core:

1. Realice el tutorial correspondiente al tipo de aplicación que quiera desarrollar o mantener.

   |Tipo de aplicación  |Escenario  |Tutorial  |
   |----------|----------|----------|
   |Aplicación web                   | Nuevo desarrollo de interfaz de usuario web del lado servidor |[Primeros pasos con Razor Pages](xref:tutorials/razor-pages/razor-pages-start) |
   |Aplicación web                   | Mantener una aplicación de MVC |[Introducción a MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Aplicación web                   | Desarrollo de interfaz de usuario web del lado cliente |[Introducción a Blazor](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |Web API                   | Servicios HTTP RESTful |[Creación de una API web](xref:tutorials/first-web-api)&dagger; |
   |Aplicación de llamada a procedimiento remoto | Servicios de contrato primero con búferes de protocolo |[Introducción a un servicio gRPC](xref:tutorials/grpc/grpc-start) |
   |Aplicación en tiempo real             | Comunicación bidireccional entre servidores y clientes conectados |[Introducción a SignalR](xref:tutorials/signalr) |

1. Realice un tutorial que muestra cómo realizar el acceso a datos básicos.

   |Escenario  |Tutorial  |
   |----------|----------|
   |Nuevo desarrollo        |[Razor Pages con Entity Framework Core](xref:data/ef-rp/intro) |
   |Mantener una aplicación de MVC |[MVC con Entity Framework Core](xref:data/ef-mvc/intro) |

1. Lea una introducción a los [conceptos básicos](xref:fundamentals/index) de ASP.NET Core aplicables a todos los tipos de aplicaciones.

1. Examine la tabla de contenido para ver otros temas de interés.

&dagger;También hay un [tutorial de API web interactivo](/learn/modules/build-web-api-net-core). No es necesario instalar ninguna herramienta de desarrollo localmente. El código se ejecuta en un [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) en el explorador, y se usa [curl](https://curl.haxx.se/) para realizar las pruebas.

## <a name="migrate-from-net-framework"></a>Migración desde .NET Framework

Para obtener una guía de referencia para migrar aplicaciones de ASP.NET 4.x a ASP.NET Core, vea <xref:migration/proper-to-2x/index>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core es un marco multiplataforma de [código abierto](https://github.com/dotnet/aspnetcore) y de alto rendimiento que tiene como finalidad compilar aplicaciones modernas conectadas a Internet y habilitadas para la nube. Con ASP.NET Core puede hacer lo siguiente:

* Compilar servicios y aplicaciones web, aplicaciones de [Internet de las cosas (IoT)](https://www.microsoft.com/internet-of-things/) y back-ends móviles.
* Usar sus herramientas de desarrollo favoritas en Windows, macOS y Linux.
* Efectuar implementaciones locales y en la nube.
* Ejecutarlo en [.NET Core o en .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-choose-aspnet-core"></a>¿Por qué elegir ASP.NET Core?

Millones de desarrolladores usan o han usado [ASP.NET 4.x](/aspnet/overview) para crear aplicaciones web. ASP.NET Core es un nuevo diseño de ASP.NET 4.x que cuenta con cambios en la arquitectura que dan como resultado un marco más sencillo y modular.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Creación de API web e interfaces de usuario web mediante ASP.NET Core MVC

ASP.NET Core MVC proporciona características para crear [API web](xref:tutorials/first-web-api) y [aplicaciones web](xref:tutorials/razor-pages/index):

* El [patrón Modelo-Vista-Controlador (MVC)](xref:mvc/overview) permite que se puedan hacer pruebas en las API web y en las aplicaciones web.
* [Razor Pages](xref:razor-pages/index) es un modelo de programación basado en páginas que facilita la compilación de interfaces de usuario web y hace que sea más productiva.
* El [marcado de RazorRazor](xref:mvc/views/razor) proporciona una sintaxis productiva para [Razor Pages](xref:razor-pages/index) y las [vistas de MVC](xref:mvc/views/overview).
* Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.
* La compatibilidad integrada para [varios formatos de datos y la negociación de contenidos](xref:web-api/advanced/formatting) permite que las API web lleguen a una amplia gama de clientes, como los exploradores y los dispositivos móviles.
* El [enlace de modelo](xref:mvc/models/model-binding) asigna automáticamente datos de solicitudes HTTP a parámetros de método de acción.
* La [validación de modelos](xref:mvc/models/validation) efectúa una validación del lado cliente y del lado servidor de forma automática.

## <a name="client-side-development"></a>Desarrollo del lado del cliente

ASP.NET Core se integra perfectamente con bibliotecas y plataformas de trabajo populares del lado cliente, que incluyen [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) y [Bootstrap](https://getbootstrap.com/). Para más información, consulte <xref:blazor/index> y los temas relacionados en *Client-side development* (Desarrollo del lado cliente).

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>ASP.NET Core con .NET Framework como destino

ASP.NET Core 2.x puede tener como destino .NET Core o .NET Framework. Las aplicaciones de ASP.NET Core que tienen como destino .NET Framework no son multiplataforma, sino que solo se ejecutan en Windows. Por lo general, ASP.NET Core 2.x está formado por bibliotecas de [.NET Standard](/dotnet/standard/net-standard). Las bibliotecas escritas con .NET Standard 2.0 se ejecutan en [cualquier plataforma .NET que implementa .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

ASP.NET Core 2.x se admite en las versiones de .NET Framework que implementan .NET Standard 2.0:

* Se recomienda encarecidamente usar la versión más reciente de .NET Framework.
* .NET Framework 4.6.1 y posterior.

ASP.NET Core 3.0 y versiones posteriores solo se ejecutan en .NET Core. Para obtener más información sobre este cambio, vea [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Descripción general de los cambios que se aplicarán a ASP.NET Core 3.0).

El uso de .NET Core como destino cuenta con varias ventajas que van en aumento con cada versión. Entre las ventajas del uso de .NET Core en vez de .NET Framework se incluyen las siguientes:

* Multiplataforma. Ejecución en macOS, Linux y Windows.
* Rendimiento mejorado
* [Control de versiones en paralelo](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Nuevas API.
* Código Abierto

Para ayudar a cerrar la brecha de API existente entre .NET Framework y .NET Core, el [paquete de compatibilidad de Windows](/dotnet/core/porting/windows-compat-pack) ha permitido que miles de API solo de Windows estén disponibles en .NET Core. Estas API no estaban disponibles en .NET Core 1.x.

## <a name="recommended-learning-path"></a>Ruta de aprendizaje recomendada

Se recomienda la siguiente secuencia de tutoriales y artículos para obtener una introducción para desarrollar aplicaciones de ASP.NET Core:

1. Realice el tutorial correspondiente al tipo de aplicación que quiera desarrollar o mantener.

   |Tipo de aplicación  |Escenario  |Tutorial  |
   |----------|----------|----------|
   |Aplicación web                   | Para un nuevo desarrollo        |[Primeros pasos con Razor Pages](xref:tutorials/razor-pages/razor-pages-start) |
   |Aplicación web                   | Para mantener una aplicación MVC |[Introducción a MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Web API                   |                            |[Creación de una API web](xref:tutorials/first-web-api)&dagger; |
   |Aplicación en tiempo real             |                            |[Introducción a SignalR](xref:tutorials/signalr) |

1. Realice un tutorial que muestra cómo realizar el acceso a datos básicos.

   |Escenario  |Tutorial  |
   |----------|----------|
   | Para un nuevo desarrollo        |[Razor Pages con Entity Framework Core](xref:data/ef-rp/intro) |
   | Para mantener una aplicación MVC |[MVC con Entity Framework Core](xref:data/ef-mvc/intro) |

1. Lea una introducción a los [conceptos básicos](xref:fundamentals/index) de ASP.NET Core aplicables a todos los tipos de aplicaciones.

1. Examine la tabla de contenido para ver otros temas de interés.

&dagger;Hay también un [tutorial de API web que se realiza completamente en el explorador](/learn/modules/build-web-api-net-core); no es necesario instalar ningún IDE local. El código se ejecuta en un [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) y se usa [curl](https://curl.haxx.se/) para realizar pruebas.

## <a name="migrate-from-net-framework"></a>Migración desde .NET Framework

Para obtener una guía de referencia para migrar aplicaciones de ASP.NET a ASP.NET Core, consulte <xref:migration/proper-to-2x/index>.

::: moniker-end

## <a name="how-to-download-a-sample"></a>Cómo descargar un ejemplo

En muchos de los artículos y tutoriales se incluyen vínculos a código de ejemplo.

1. [Descargue el archivo ZIP del repositorio de ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).
1. Descomprima el archivo *Docs-master.zip*.
1. Use la dirección URL del vínculo de ejemplo para ir al directorio de ejemplo.

### <a name="preprocessor-directives-in-sample-code"></a>Directivas de preprocesador en código de ejemplo

Para mostrar varios escenarios, las aplicaciones de ejemplo usan las directivas de preprocesador `#define` y `#if-#else/#elif-#endif` para compilar de forma selectiva y ejecutar secciones distintas de código de ejemplo. Para los ejemplos que usan este enfoque, establezca la directiva `#define` en la parte superior de los archivos C# para definir el símbolo asociado con el escenario que quiera ejecutar. Algunos ejemplos requieren la definición del símbolo en la parte superior de varios archivos para ejecutar un escenario.

Por ejemplo, la siguiente lista de símbolos de `#define` indica que hay cuatro escenarios disponibles (un escenario por símbolo). La configuración de ejemplo actual ejecuta el escenario `TemplateCode`:

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

Para cambiar el ejemplo el escenario `ExpandDefault`, defina el símbolo `ExpandDefault` y deje los símbolos restantes comentados:

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

Para obtener información sobre cómo usar [directivas de preprocesador de C#](/dotnet/csharp/language-reference/preprocessor-directives/) para compilar selectivamente secciones de código, vea [#define (Referencia de C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) e [#if (Referencia de C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).

### <a name="regions-in-sample-code"></a>Regiones en código de ejemplo

Algunas aplicaciones de ejemplo contienen secciones de código rodeadas de las directivas [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) y [#end-region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) de C#. El sistema de creación de documentación inserta estas regiones en los temas de documentación representados.  

Normalmente, los nombres de región contienen la palabra "snippet". En el ejemplo siguiente se muestra una región denominada `snippet_WebHostDefaults`:

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

En el archivo Markdown del tema se hace referencia al fragmento de código de C# anterior con la siguiente línea:

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

Puede ignorar sin problemas (o incluso quitar) las directivas `#region` y `#endregion` que rodean el código. No altere el código de estas directivas si tiene planeado ejecutar los escenarios de ejemplo descritos en el tema. Puede alterarlo si quiere experimentar con otros escenarios.

Para obtener más información, consulte [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets) (Contribución a la documentación de ASP.NET: fragmentos de código).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, vea los siguientes recursos:

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Conceptos básicos de ASP.NET Core](xref:fundamentals/index)
* [La reunión semanal de la comunidad de ASP.NET](https://live.asp.net/) trata el progreso y los planes del equipo. Incluye nuevos blogs y nuevo software de terceros.
