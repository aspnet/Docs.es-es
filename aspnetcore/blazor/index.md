---
title: Introducción a ASP.NET Core Blazor
author: guardrex
description: Explore ASP.NET Core Blazor, una forma de compilar la interfaz de usuario web interactiva del lado cliente con .NET en una aplicación de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: 79c225a0714562a01afe67bf8e59f3b3f98a6265
ms.sourcegitcommit: e9b8835a02f75b6378b766edb8bab23b14a4192b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2020
ms.locfileid: "97666864"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a>Introducción a ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

*Le damos la bienvenida a Blazor.*

Blazor es una plataforma de trabajo para la creación de interfaces de usuario web interactivas del lado cliente con [.NET](/dotnet/standard/tour):

* Cree interfaces de usuario completamente interactivas con [C#](/dotnet/csharp/) en lugar de [JavaScript](https://www.javascript.com).
* Comparta la lógica de aplicación del lado cliente y servidor escrita con .NET.
* Represente la interfaz de usuario como HTML y CSS para la compatibilidad con todos los exploradores, incluidos los móviles.
* Realice la integración con plataformas de hospedaje modernas, como [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

El uso de .NET para el desarrollo web en el lado cliente ofrece las siguientes ventajas:

* Escribe el código en C# en lugar de JavaScript.
* Aprovechamiento del ecosistema .NET existente de [bibliotecas .NET](/dotnet/standard/class-libraries).
* Uso compartido de la lógica de aplicación en el servidor y el cliente.
* Beneficios de rendimiento, confiabilidad y seguridad de .NET.
* Mantenga la productividad con [Visual Studio](https://visualstudio.microsoft.com) en Windows, Linux y macOS.
* Compile sobre un conjunto común de lenguajes, marcos y herramientas que son estables, completos y fáciles de usar.

## <a name="components"></a>Componentes

Las aplicaciones de Blazor se basan en *componentes*. En Blazor, un componente es un elemento de la interfaz de usuario, como una página, un cuadro de diálogo o un formulario de entrada de datos.

Los componentes son clases C# de .NET integradas en [ensamblados de .NET](/dotnet/standard/assembly/) que:

* Definen la lógica de representación de la interfaz de usuario flexible.
* Controlan acciones del usuario.
* Se pueden anidar y reutilizar.
* Se pueden compartir y distribuir como [bibliotecas de clases de Razor](xref:razor-pages/ui-class) o [paquetes NuGet](/nuget/what-is-nuget).

La clase del componente normalmente se escribe en forma de página de marcado de [Razor](xref:mvc/views/razor) con la extensión de nombre de archivo `.razor`. Formalmente se hace referencia a los componentes de Blazor como *componentes de Razor* . Razor es una sintaxis para combinar marcado HTML con código de C# diseñada para aumentar la productividad del desarrollador. Razor le permite cambiar entre marcado HTML y C# en el mismo archivo gracias a la compatibilidad de programación de [IntelliSense](/visualstudio/ide/using-intellisense) en Visual Studio. Razor Pages y MVC también usan Razor. A diferencia de Razor Pages y MVC, que se compilan en torno a un modelo de solicitud y respuesta, los componentes se usan específicamente en la composición y la lógica de la interfaz de usuario del lado cliente.

Blazor usa etiquetas HTML naturales para la composición de la interfaz de usuario. En el siguiente marcado de Razor se muestra un componente (`Dialog.razor`) que presenta un cuadro de diálogo y procesa un evento cuando el usuario selecciona un botón:

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

En el ejemplo anterior, `OnYes` es un método de C# desencadenado por el evento `onclick` del botón. El texto (`ChildContent`) y el título (`Title`) del cuadro de diálogo se proporciona mediante el siguiente componente que usa este componente en su interfaz de usuario.

El componente `Dialog` está anidado en otro componente mediante una etiqueta HTML. En el ejemplo siguiente, el componente `Index` (`Pages/Index.razor`) utiliza el componente `Dialog` anterior. El atributo `Title` de la etiqueta pasa un valor para el título en la propiedad `Title` del componente `Dialog`.  El contenido del elemento `<Dialog>` establece el texto del componente `Dialog` (`ChildContent`). Cuando el componente `Dialog` se agrega al componente `Index`, [IntelliSense en Visual Studio](/visualstudio/ide/using-intellisense) agiliza el desarrollo con la finalización de la sintaxis y los parámetros.

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

El cuadro de diálogo se representa cuando se accede al componente `Index` en un explorador. Cuando el usuario selecciona el botón, la consola de herramientas de desarrollo del explorador muestra el mensaje escrito por el método `OnYes`:

![Componente Dialog representado en el explorador anidado en el interior del componente Index. La consola de herramientas de desarrollo del explorador muestra el mensaje escrito por el código de C# cuando el usuario selecciona el botón Sí. en la interfaz de usuario.](index/_static/dialog.png)

Los componentes se representan en una representación en memoria del elemento [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) del explorador denominado *árbol de representación*, que se usa para actualizar la interfaz de usuario de manera eficaz y flexible.

## Blazor WebAssembly

Blazor WebAssembly es un [marco de aplicaciones de página única (SPA)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) para compilar aplicaciones web interactivas del lado cliente con. NET. Blazor WebAssembly usa estándares web abiertos sin complementos o recompilación de código en otros lenguajes. Blazor WebAssembly funciona en todos los exploradores web modernos, incluidos los exploradores para dispositivos móviles.

La ejecución de código .NET dentro de exploradores web se consigue mediante [WebAssembly](https://webassembly.org) (abreviado como `wasm`). WebAssembly es un formato de código de bytes compacto optimizado para descargas rápidas y una velocidad de ejecución máxima. WebAssembly es un estándar web abierto y se admite en exploradores web sin complementos.

El código de WebAssembly puede acceder a toda la funcionalidad del explorador mediante JavaScript, denominada *interoperabilidad de JavaScript*. El código de .NET que se ejecuta a través de WebAssembly en el explorador se ejecuta a su vez en el espacio aislado de JavaScript del explorador con las protecciones que proporciona dicho espacio aislado contra acciones malintencionadas en la máquina cliente.

![Blazor WebAssembly ejecuta código de .NET en el explorador con WebAssembly.](index/_static/blazor-webassembly.png)

Cuando se compila y ejecuta una aplicación de Blazor WebAssembly en un explorador:

* Los archivos de código C# y los archivos de Razor se compilan en ensamblados de .NET.
* Los ensamblados y el [runtime de .NET](/dotnet/framework/get-started/overview) se descargan en el explorador.
* Blazor WebAssembly arranca el entorno de ejecución .NET y lo configura para cargar los ensamblados de la aplicación. El entorno de ejecución de Blazor WebAssembly emplea la interoperabilidad de JavaScript para gestionar la manipulación de DOM y las llamadas API del explorador.

El tamaño de la aplicación publicada, su *tamaño de carga*, es un factor de rendimiento crítico para la facilidad de uso de una aplicación. Una aplicación grande tarda un tiempo relativamente largo en descargarse en un explorador, lo que repercute en la experiencia del usuario. Blazor WebAssembly optimiza el tamaño de carga para reducir los tiempos de descarga:

::: moniker range=">= aspnetcore-5.0"

* Se ha quitado el código sin usar de la aplicación cuando se publica mediante el [recortador del lenguaje intermedio (IL)](xref:blazor/host-and-deploy/configure-trimmer).
* Las respuestas HTTP se comprimen.
* El entorno de ejecución .NET y los ensamblados se almacenan en caché en el explorador.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Se ha quitado el código sin usar de la aplicación cuando se publica mediante el [enlazador del lenguaje intermedio (IL)](xref:blazor/host-and-deploy/configure-linker).
* Las respuestas HTTP se comprimen.
* El entorno de ejecución .NET y los ensamblados se almacenan en caché en el explorador.

::: moniker-end

## Blazor Server

Blazor separa la lógica de representación de componentes del modo en el que se aplican las actualizaciones de la interfaz de usuario. *Blazor Server* ofrece compatibilidad con el hospedaje de componentes de Razor en el servidor de una aplicación ASP.NET Core. Las actualizaciones de la interfaz de usuario se controlan mediante una conexión de [SignalR](xref:signalr/introduction).

El runtime controla:

* El envío de eventos de interfaz de usuario desde el explorador al servidor.
* La aplicación de actualizaciones de la interfaz de usuario al componente representado que devuelve el servidor.

La conexión que usa el servidor de Blazor Server para comunicarse con el explorador también se emplea para administrar las llamadas de interoperabilidad de JavaScript.

![Blazor Server ejecuta código de .NET en el servidor e interactúa con Document Object Model en el cliente mediante una conexión de SignalR](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Interoperabilidad de JavaScript

En el caso de aplicaciones que necesitan bibliotecas de JavaScript de terceros y acceso a las API de explorador, los componentes interoperan con JavaScript. Los componentes pueden usar cualquier biblioteca o API que pueda utilizar JavaScript. El código de C# puede [llamar a código de JavaScript](xref:blazor/call-javascript-from-dotnet) y, a su vez, el código de JavaScript puede [llamar al código de C#](xref:blazor/call-dotnet-from-javascript).

## <a name="code-sharing-and-net-standard"></a>Uso compartido de código y .NET Standard

Blazor implementa [.NET Standard](/dotnet/standard/net-standard), que permite a los proyectos de Blazor hacer referencia a las bibliotecas que cumplen con las especificaciones de ese estándar. .NET Standard es una especificación formal de las API de .NET comunes entre las implementaciones de .NET. Las bibliotecas de clase .NET Standard pueden compartirse a través de diferentes plataformas .NET, como Blazor, .NET Framework, .NET Core, Xamarin, Mono y Unity.

Las API que no pueden aplicarse dentro de un explorador web (por ejemplo, para acceder al sistema de archivos, abrir un socket y ejecutar subprocesos) generan una excepción <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Recursos adicionales

* [WebAssembly](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [Guía de C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Vínculos interesantes de la comunidad de Blazor](https://github.com/AdrienTorris/awesome-blazor)
