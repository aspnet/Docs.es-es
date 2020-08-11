---
title: Introducción a ASP.NET Core Blazor
author: guardrex
description: Explore ASP.NET Core Blazor, una forma de compilar la interfaz de usuario web interactiva del lado cliente con .NET en una aplicación de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 06/19/2020
no-loc:
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
ms.openlocfilehash: ad543087243658f09a23e4f6d957d0c6aa77b361
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014183"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a>Introducción a ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

*Le damos la bienvenida a Blazor.*

Blazor es una plataforma de trabajo para la creación de interfaces de usuario web interactivas del lado cliente con .NET:

* Cree interfaces de usuario completamente interactivas con C# en lugar de JavaScript.
* Comparta la lógica de aplicación del lado cliente y servidor escrita con .NET.
* Represente la interfaz de usuario como HTML y CSS para la compatibilidad con todos los exploradores, incluidos los móviles.
* Realice la integración con plataformas de hospedaje modernas, como [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

El uso de .NET para el desarrollo web en el lado cliente ofrece las siguientes ventajas:

* Escribe el código en C# en lugar de JavaScript.
* Aprovechamiento del ecosistema y las bibliotecas .NET existentes.
* Uso compartido de la lógica de aplicación en el servidor y el cliente.
* Beneficios de rendimiento, confiabilidad y seguridad de .NET.
* Mantenga la productividad con Visual Studio en Windows, Linux y macOS.
* Compile sobre un conjunto común de lenguajes, marcos y herramientas que son estables, completos y fáciles de usar.

## <a name="components"></a>Componentes

Las aplicaciones de Blazor se basan en *componentes*. En Blazor, un componente es un elemento de la interfaz de usuario, como una página, un cuadro de diálogo o un formulario de entrada de datos.

Los componentes son clases de .NET compiladas en ensamblados de .NET que:

* Definen la lógica de representación de la interfaz de usuario flexible.
* Controlan acciones del usuario.
* Se pueden anidar y reutilizar.
* Se pueden compartir y distribuir como [bibliotecas de clases de Razor](xref:razor-pages/ui-class) o [paquetes NuGet](/nuget/what-is-nuget).

La clase del componente normalmente se escribe en forma de página de marcado de [Razor](xref:mvc/views/razor) con la extensión de nombre de archivo `.razor`. Formalmente se hace referencia a los componentes de Blazor como *componentes de Razor* . Razor es una sintaxis para combinar marcado HTML con código de C# diseñada para aumentar la productividad del desarrollador. Razor le permite cambiar entre marcado HTML y C# en el mismo archivo gracias a la compatibilidad con [IntelliSense](/visualstudio/ide/using-intellisense). Razor Pages y MVC también usan Razor. A diferencia de Razor Pages y MVC, que se compilan en torno a un modelo de solicitud y respuesta, los componentes se usan específicamente en la composición y la lógica de la interfaz de usuario del lado cliente.

El siguiente marcado de Razor muestra un componente (`Dialog.razor`), que se puede anidar dentro de otro:

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

El contenido del cuerpo (`ChildContent`) y el título (`Title`) del cuadro de diálogo los proporciona el componente que usa este componente en su interfaz de usuario. `OnYes` es un método de C# desencadenado por el evento `onclick` del botón.

Blazor usa etiquetas HTML naturales para la composición de la interfaz de usuario. Los elementos HTML especifican componentes y los atributos de la etiqueta pasan valores a las propiedades del componente.

En el ejemplo siguiente, el componente `Index` utiliza el componente `Dialog`. `ChildContent` y `Title` se establecen mediante los atributos y el contenido del elemento `<Dialog>`.

`Pages/Index.razor`:

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

El cuadro de diálogo se representa cuando se accede al elemento principal (`Pages/Index.razor`) en un explorador:

![Componente Dialog representado en el explorador](index/_static/dialog.png)

Cuando este componente se usa en la aplicación, IntelliSense en [Visual Studio](/visualstudio/ide/using-intellisense) y [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) acelera el desarrollo con la finalización de parámetros y sintaxis.

Los componentes se representan en una representación en memoria de la especificación Document Object Model (DOM) del explorador llamada *árbol de representación*, que se usa para actualizar la interfaz de usuario de una manera eficaz y flexible.

## Blazor WebAssembly

Blazor WebAssembly es un marco de aplicaciones de una sola página para compilar aplicaciones web interactivas del lado cliente con. NET. Blazor WebAssembly usa estándares web abiertos sin complementos ni transpilación de código, y funciona en todos los exploradores web modernos, incluidos los exploradores para dispositivos móviles.

La ejecución de código .NET dentro de exploradores web se consigue mediante [WebAssembly](https://webassembly.org) (abreviado como `wasm`). WebAssembly es un formato de código de bytes compacto optimizado para descargas rápidas y una velocidad de ejecución máxima. WebAssembly es un estándar web abierto y se admite en exploradores web sin complementos.

El código de WebAssembly puede acceder a toda la funcionalidad del explorador mediante la *interoperabilidad de JavaScript* *.* El código de .NET que se ejecuta a través de WebAssembly en el explorador se ejecuta a su vez en el espacio aislado de JavaScript del explorador con las protecciones que proporciona dicho espacio aislado contra acciones malintencionadas en la máquina cliente.

![Blazor WebAssembly ejecuta código de .NET en el explorador con WebAssembly.](index/_static/blazor-webassembly.png)

Cuando se compila y ejecuta una aplicación de Blazor WebAssembly en un explorador:

* Los archivos de código C# y los archivos de Razor se compilan en ensamblados de .NET.
* Los ensamblados y el entorno de ejecución de .NET se descargan en el explorador.
* Blazor WebAssembly arranca el entorno de ejecución .NET y lo configura para cargar los ensamblados de la aplicación. El entorno de ejecución de Blazor WebAssembly emplea la interoperabilidad de JavaScript para gestionar la manipulación de DOM y las llamadas API del explorador.

El tamaño de la aplicación publicada, su *tamaño de carga*, es un factor de rendimiento crítico para la facilidad de uso de una aplicación. Una aplicación grande tarda un tiempo relativamente largo en descargarse en un explorador, lo que repercute en la experiencia del usuario. Blazor WebAssembly optimiza el tamaño de carga para reducir los tiempos de descarga:

* Se ha quitado el código sin usar de la aplicación cuando se publica mediante el [enlazador del lenguaje intermedio (IL)](xref:blazor/host-and-deploy/configure-linker).
* Las respuestas HTTP se comprimen.
* El entorno de ejecución .NET y los ensamblados se almacenan en caché en el explorador.

## Blazor Server

Blazor separa la lógica de representación de componentes del modo en el que se aplican las actualizaciones de la interfaz de usuario. Blazor Server admite el hospedaje de componentes de Razor en el servidor de una aplicación de ASP.NET Core. Las actualizaciones de la interfaz de usuario se controlan mediante una conexión de [SignalR](xref:signalr/introduction).

El entorno de ejecución controla el envío de eventos de la interfaz de usuario del explorador al servidor y, después de ejecutar los componentes, vuelve a aplicar al explorador las actualizaciones de la interfaz de usuario enviadas por el servidor.

La conexión que usa el servidor de Blazor Server para comunicarse con el explorador también se emplea para administrar las llamadas de interoperabilidad de JavaScript.

![Blazor Server ejecuta código de .NET en el servidor e interactúa con Document Object Model en el cliente mediante una conexión de SignalR](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Interoperabilidad de JavaScript

En el caso de aplicaciones que necesitan bibliotecas de JavaScript de terceros y acceso a las API de explorador, los componentes interoperan con JavaScript. Los componentes pueden usar cualquier biblioteca o API que pueda utilizar JavaScript. El código de C# puede llamar a código JavaScript y, a su vez, el código JavaScript puede llamar a código de C#. Para obtener más información, vea los artículos siguientes:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a>Uso compartido de código y .NET Standard

Blazor implementa [.NET Standard 2.1](/dotnet/standard/net-standard), que permite a los proyectos de Blazor hacer referencia a las bibliotecas que cumplen con ese estándar o con especificaciones anteriores. .NET Standard es una especificación formal de las API de .NET comunes entre las implementaciones de .NET. Las bibliotecas de clase .NET Standard pueden compartirse a través de diferentes plataformas .NET, como Blazor, .NET Framework, .NET Core, Xamarin, Mono y Unity.

Las API que no pueden aplicarse dentro de un explorador web (por ejemplo, para acceder al sistema de archivos, abrir un socket y ejecutar subprocesos) generan una excepción <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Recursos adicionales

* [WebAssembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [Guía de C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Vínculos interesantes de la comunidad de Blazor](https://github.com/AdrienTorris/awesome-blazor)
