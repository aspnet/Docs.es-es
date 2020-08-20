---
title: Uso de las API de ASP.NET Core en una biblioteca de clases
author: scottaddie
description: Aprenda a usar las API de ASP.NET Core en una biblioteca de clases.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 571e6c66f60bbc09b902ff9064d2fb1c18c433dc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630060"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Uso de las API de ASP.NET Core en una biblioteca de clases

Por [Scott Addie](https://github.com/scottaddie)

En este documento se proporcionan instrucciones para usar las API de ASP.NET Core en una biblioteca de clases. Para obtener todas las demás instrucciones de bibliotecas, consulte [Guía de la biblioteca de código abierto](/dotnet/standard/library-guidance/).

## <a name="determine-which-aspnet-core-versions-to-support"></a>Determinación de las versiones de ASP.NET Core compatibles

ASP.NET Core sigue la [directiva de compatibilidad de .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Consulte la directiva de compatibilidad al determinar qué versiones de ASP.NET Core se admiten en una biblioteca. Una biblioteca debe cumplir estas condiciones:

* Hacer todo lo posible para admitir todas las versiones de ASP.NET Core clasificadas como versiones *compatibles a largo plazo* (LTS).
* No sentirse obligada a admitir versiones de ASP.NET Core clasificadas como *final del ciclo de vida* (EOL).

A medida que se lanzan versiones preliminares de ASP.NET Core, se publican los cambios importantes en el repositorio [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) de GitHub. Las pruebas de compatibilidad de las bibliotecas se pueden llevar a cabo a medida que se desarrollan las características del marco.

## <a name="use-the-aspnet-core-shared-framework"></a>Uso del marco compartido de .NET Core

Con el lanzamiento de .NET Core 3,0, muchos ensamblados de ASP.NET Core ya no se publican en NuGet como paquetes. Ahora, los ensamblados se incluyen en el marco compartido de `Microsoft.AspNetCore.App`, que se instala con los instaladores del SDK de .NET Core y el entorno de ejecución. Para obtener una lista de los paquetes que ya no se publican, vea [Quitar referencias de paquetes obsoletas](xref:migration/22-to-30#remove-obsolete-package-references).

A partir de .NET Core 3.0, los proyectos que usan el SDK de MSBuild `Microsoft.NET.Sdk.Web` hacen referencia implícitamente al marco compartido. Los proyectos que usan el SDK de `Microsoft.NET.Sdk` o `Microsoft.NET.Sdk.Razor` deben hacer referencia a ASP.NET Core para usar las API de ASP.NET Core en el marco compartido.

Para hacer referencia a ASP.NET Core, agregue el siguiente elemento `<FrameworkReference>` al archivo del proyecto:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

La referencia a ASP.NET Core de esta manera solo se admite para los proyectos que tienen como destino .NET Core 3.x.

## <a name="include-no-locblazor-extensibility"></a>Inclusión de la extensibilidad Blazor

Blazor admite los [modelos de hospedaje](xref:blazor/hosting-models) de WebAssembly (WASM) y Server. A menos que haya un motivo específico para no hacerlo, una biblioteca de [componentes Razor](xref:blazor/components/index) debe admitir ambos modelos de hospedaje. Una biblioteca de componentes Razor debe usar el [SDK Microsoft.NET.Sdk.Razor](xref:razor-pages/sdk)

### <a name="support-both-hosting-models"></a>Compatibilidad con ambos modelos de hospedaje

Para permitir el uso de componentes Razor desde proyectos tanto de [Blazor Server](xref:blazor/hosting-models#blazor-server) como de [BlazorWebAssembly de ](xref:blazor/hosting-models#blazor-webassembly), use las instrucciones siguientes de su editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Use la plantilla de proyecto de **Biblioteca de clases de Razor** . Se debe desactivar la casilla **Support pages and views** (Admitir páginas y vistas) de la plantilla.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ejecute el comando siguiente en el terminal integrado:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Use la plantilla de proyecto de **Biblioteca de clases de Razor** .

---

El proyecto generado a partir de la plantilla hace lo siguiente:

* Se dirige a .NET Standard 2.0.
* Establece la propiedad `RazorLangVersion` como `3.0`. `3.0` es el valor predeterminado para .NET Core 3.x.
* Agrega las siguientes referencias de paquete:
  * [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Por ejemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Compatibilidad con un modelo de hospedaje específico

Es mucho menos común admitir un único modelo de hospedaje de Blazor. Por ejemplo, para admitir el uso de componentes Razor únicamente desde proyectos de [Blazor Server](xref:blazor/hosting-models#blazor-server):

* Diríjase a .NET Core 3.x.
* Agregue un elemento `<FrameworkReference>` para el marco compartido.

Por ejemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Para más información sobre las bibliotecas que contienen componentes Razor, vea [Bibliotecas de clases de componentes Razor de ASP.NET Core](xref:blazor/components/class-libraries).

## <a name="include-mvc-extensibility"></a>Inclusión de la extensibilidad MVC

En esta sección se describen las recomendaciones para las bibliotecas que incluyen:

* [Vistas de Razor o Razor Pages](#razor-views-or-razor-pages)
* [Asistentes de etiquetas](#tag-helpers)
* [Visualización de componentes](#view-components)

En esta sección no se explica la compatibilidad con múltiples versiones de MVC. Para obtener instrucciones sobre cómo admitir varias versiones de ASP.NET Core, consulte [Compatibilidad con varias versiones de ASP.NET Core](#support-multiple-aspnet-core-versions).

### <a name="no-locrazor-views-or-no-locrazor-pages"></a>Vistas de Razor o Razor Pages

Un proyecto que incluya [vistas de Razor](xref:mvc/views/overview) o [Razor Pages](xref:razor-pages/index) debe usar el [SDK Microsoft.NET.Sdk.Razor](xref:razor-pages/sdk)

Si el proyecto tiene como destino .NET Core 3.x, requiere lo siguiente:

* Una propiedad de MSBuild `AddRazorSupportForMvc` establecida en `true`.
* Un elemento `<FrameworkReference>` para el marco compartido.

La plantilla de proyecto **Biblioteca de clases de Razor** cumple los requisitos anteriores de los proyectos que tienen como destino .NET Core 3.x. Utilice las instrucciones siguientes para su editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Use la plantilla de proyecto de **Biblioteca de clases de Razor** . Se activa la casilla **Support pages and views** (Admitir páginas y vistas) de la plantilla.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ejecute el comando siguiente en el terminal integrado:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

No hay compatibilidad con plantillas de proyecto en este momento.

---

Por ejemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Si el proyecto tiene como destino .NET Standard, se requiere una referencia de paquete de [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc). El paquete `Microsoft.AspNetCore.Mvc` se ha migrado al marco compartido en ASP.NET Core 3.0 y, por tanto, ya no se publica. Por ejemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Asistentes de etiquetas

Un proyecto que incluye [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) debe usar el SDK `Microsoft.NET.Sdk`. Si el destino es .NET Core 3.x, agregue un elemento `<FrameworkReference>` para el marco compartido. Por ejemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Si el destino es .NET Standard (para admitir versiones anteriores a ASP.NET Core 3.x), agregue una referencia de paquete a [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor). El paquete de `Microsoft.AspNetCore.Mvc.Razor` se ha migrado al marco compartido y, por tanto, ya no se publica. Por ejemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Componentes de vista

Un proyecto que incluya [Componentes de vista](xref:mvc/views/view-components) debe utilizar el SDK de `Microsoft.NET.Sdk`. Si el destino es .NET Core 3.x, agregue un elemento `<FrameworkReference>` para el marco compartido. Por ejemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Si el destino es .NET Standard (para admitir versiones anteriores a ASP.NET Core 3.x), agregue una referencia de paquete a [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). El paquete de `Microsoft.AspNetCore.Mvc.ViewFeatures` se ha migrado al marco compartido y, por tanto, ya no se publica. Por ejemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Compatibilidad con varias versiones de ASP.NET Core

Se requiere la compatibilidad con múltiples versiones para crear una biblioteca que admita diferentes variantes de ASP.NET Core. Piense en un escenario en el que una biblioteca de asistentes de etiquetas deba admitir las siguientes variantes de ASP.NET Core:

* ASP.NET Core 2.1 con .NET Framework 4.6.1 como destino
* ASP.NET Core 2.x con .NET Core 2.x como destino
* ASP.NET Core 3.x con .NET Core 3.x como destino

El siguiente archivo de proyecto admite estas variantes a través de la propiedad `TargetFrameworks`:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

Con el archivo de proyecto anterior:

* El paquete de `Markdig` se agrega a todos los consumidores.
* Se ha agregado una referencia a [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) para los consumidores cuyo destino es .NET Framework 4.6.1 o posterior o .NET Core 2.x. La versión 2.1.0 del paquete funciona con ASP.NET Core 2.2 gracias a la compatibilidad con versiones anteriores.
* Se hace referencia al marco compartido para los consumidores que tienen como destino .NET Core 3.x. El paquete de `Microsoft.AspNetCore.Mvc.Razor` se incluye en el marco compartido.

Como alternativa, se podría tomar como destino .NET Standard 2.0 en lugar de .NET Core 2.1 y .NET Framework 4.6.1:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

Con el archivo de proyecto anterior, hay que tomar en cuenta los siguientes aspectos:

* Dado que la biblioteca solo contiene asistentes de etiquetas, es más sencillo dirigirse a las plataformas específicas en las que se ejecuta ASP.NET Core: .NET Core y .NET Framework. Los asistentes de etiquetas no se pueden usar en otros marcos de destino compatibles con .NET Standard 2.0 como Unity, UWP y Xamarin.
* El uso de .NET Standard 2.0 de .NET Framework tiene algunos problemas que se han solucionado en .NET Framework 4.7.2. Puede mejorar la experiencia de los consumidores que usan .NET Framework de 4.6.1 a 4.7.1 tomando como destino .NET Framework 4.6.1.

Si la biblioteca necesita llamar a API específicas de la plataforma, diríjase a implementaciones de .NET específicas en lugar de .NET Standard. Para obtener más información, consulte [Compatibilidad con múltiples versiones](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Uso de una API que no ha cambiado

Imagine un escenario en el que está actualizando una biblioteca de middleware de .NET Core 2.2 a 3.0. Las API de middleware ASP.NET Core que se usan en la biblioteca no han cambiado entre ASP.NET Core 2.2 y 3.0. Siga estos pasos para seguir admitiendo la biblioteca de middleware en .NET Core 3.0:

* Siga las [instrucciones de la biblioteca estándar](/dotnet/standard/library-guidance/).
* Agregue una referencia de paquete para cada paquete de NuGet de la API si el ensamblado correspondiente no existe en el marco compartido.

## <a name="use-an-api-that-changed"></a>Uso de una API que ha cambiado

Imagine un escenario en el que está actualizando una biblioteca de .NET Core 2.2 a .NET Core 3.0. Una API de ASP.NET Core que se usa en la biblioteca tiene un [cambio importante](/dotnet/core/compatibility/breaking-changes) en ASP.NET Core 3.0. Considere si la biblioteca se puede volver a escribir para que no use la API dañada en todas las versiones.

Si puede volver a escribir la biblioteca, hágalo y continúe con el marco anterior como destino (por ejemplo, .NET Standard 2.0 o .NET Framework 4.6.1) con referencias de paquete.

Si no puede volver a escribir la biblioteca, siga estos pasos:

* Agregue un destino para .NET Core 3.0.
* Agregue un elemento `<FrameworkReference>` para el marco compartido.
* Use la [directiva de preprocesador de #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) con el símbolo del marco de destino adecuado para compilar el código de forma condicional.

Por ejemplo, las lecturas y escrituras sincrónicas en secuencias de respuesta y solicitud HTTP están deshabilitadas de forma predeterminada a partir de ASP.NET Core 3.0. ASP.NET Core 2.2 admite el comportamiento sincrónico de forma predeterminada. Considere una biblioteca de middleware en la que se deben habilitar las lecturas y escrituras sincrónicas donde se está produciendo la E/S. La biblioteca debe incluir el código para habilitar las características sincrónicas en la directiva de preprocesador adecuada. Por ejemplo:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Uso de una API presentada en 3.0

Imagine que quiere usar una API de ASP.NET Core que se presentó en ASP.NET Core 3.0. Pregúntese lo siguiente:

1. ¿La biblioteca requiere funcionalmente la nueva API?
1. ¿La biblioteca puede implementar esta característica de manera diferente?

Si la biblioteca requiere funcionalmente la API y no hay manera de implementarla en el nivel inferior:

* Diríjase solo a .NET Core 3.x.
* Agregue un elemento `<FrameworkReference>` para el marco compartido.

Si la biblioteca puede implementar esta característica de manera diferente:

* Agregue .NET Core 3.x como plataforma de destino.
* Agregue un elemento `<FrameworkReference>` para el marco compartido.
* Use la [directiva de preprocesador de #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) con el símbolo del marco de destino adecuado para compilar el código de forma condicional.

Por ejemplo, el siguiente asistente de etiquetas usa la interfaz <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> presentada en ASP.NET Core 3.0. Los consumidores que tienen como destino .NET Core 3.0 ejecutan la ruta de acceso al código definida por el símbolo del marco de destino `NETCOREAPP3_0`. El tipo de parámetro de constructor del asistente de etiquetas cambia a <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> para los consumidores de .NET Core 2.1 y .NET Framework 4.6.1. Este cambio era necesario porque ASP.NET Core 3.0 marcó `IHostingEnvironment` como obsoleto y recomendó su sustitución por `IWebHostEnvironment`.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

El siguiente archivo de proyecto de destino múltiple admite este escenario del asistente de etiquetas:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Uso de una API que se ha quitado del marco compartido

Para usar un ensamblado de ASP.NET Core que se haya quitado del marco de trabajo compartido, agregue la referencia de paquete adecuada. Para obtener una lista de los paquetes que se han quitado del marco compartido en ASP.NET Core 3.0, consulte [Quitar referencias de paquetes obsoletas](xref:migration/22-to-30#remove-obsolete-package-references).

Por ejemplo, para agregar el cliente de la API web:

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [Compatibilidad con implementaciones de .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Directivas de compatibilidad de .NET](https://dotnet.microsoft.com/platform/support/policy)
