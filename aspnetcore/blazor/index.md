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
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="2a7f9-103">Introducción a ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="2a7f9-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="2a7f9-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2a7f9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2a7f9-105">*Le damos la bienvenida a Blazor.*</span><span class="sxs-lookup"><span data-stu-id="2a7f9-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="2a7f9-106">Blazor es una plataforma de trabajo para la creación de interfaces de usuario web interactivas del lado cliente con [.NET](/dotnet/standard/tour):</span><span class="sxs-lookup"><span data-stu-id="2a7f9-106">Blazor is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="2a7f9-107">Cree interfaces de usuario completamente interactivas con [C#](/dotnet/csharp/) en lugar de [JavaScript](https://www.javascript.com).</span><span class="sxs-lookup"><span data-stu-id="2a7f9-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="2a7f9-108">Comparta la lógica de aplicación del lado cliente y servidor escrita con .NET.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="2a7f9-109">Represente la interfaz de usuario como HTML y CSS para la compatibilidad con todos los exploradores, incluidos los móviles.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="2a7f9-110">Realice la integración con plataformas de hospedaje modernas, como [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="2a7f9-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="2a7f9-111">El uso de .NET para el desarrollo web en el lado cliente ofrece las siguientes ventajas:</span><span class="sxs-lookup"><span data-stu-id="2a7f9-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="2a7f9-112">Escribe el código en C# en lugar de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="2a7f9-113">Aprovechamiento del ecosistema .NET existente de [bibliotecas .NET](/dotnet/standard/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="2a7f9-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="2a7f9-114">Uso compartido de la lógica de aplicación en el servidor y el cliente.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="2a7f9-115">Beneficios de rendimiento, confiabilidad y seguridad de .NET.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="2a7f9-116">Mantenga la productividad con [Visual Studio](https://visualstudio.microsoft.com) en Windows, Linux y macOS.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="2a7f9-117">Compile sobre un conjunto común de lenguajes, marcos y herramientas que son estables, completos y fáciles de usar.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="2a7f9-118">Componentes</span><span class="sxs-lookup"><span data-stu-id="2a7f9-118">Components</span></span>

<span data-ttu-id="2a7f9-119">Las aplicaciones de Blazor se basan en *componentes*.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="2a7f9-120">En Blazor, un componente es un elemento de la interfaz de usuario, como una página, un cuadro de diálogo o un formulario de entrada de datos.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="2a7f9-121">Los componentes son clases C# de .NET integradas en [ensamblados de .NET](/dotnet/standard/assembly/) que:</span><span class="sxs-lookup"><span data-stu-id="2a7f9-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="2a7f9-122">Definen la lógica de representación de la interfaz de usuario flexible.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="2a7f9-123">Controlan acciones del usuario.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-123">Handle user events.</span></span>
* <span data-ttu-id="2a7f9-124">Se pueden anidar y reutilizar.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-124">Can be nested and reused.</span></span>
* <span data-ttu-id="2a7f9-125">Se pueden compartir y distribuir como [bibliotecas de clases de Razor](xref:razor-pages/ui-class) o [paquetes NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="2a7f9-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="2a7f9-126">La clase del componente normalmente se escribe en forma de página de marcado de [Razor](xref:mvc/views/razor) con la extensión de nombre de archivo `.razor`.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="2a7f9-127">Formalmente se hace referencia a los componentes de Blazor como *componentes de Razor* .</span><span class="sxs-lookup"><span data-stu-id="2a7f9-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="2a7f9-128">Razor es una sintaxis para combinar marcado HTML con código de C# diseñada para aumentar la productividad del desarrollador.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="2a7f9-129">Razor le permite cambiar entre marcado HTML y C# en el mismo archivo gracias a la compatibilidad de programación de [IntelliSense](/visualstudio/ide/using-intellisense) en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="2a7f9-130">Razor Pages y MVC también usan Razor.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="2a7f9-131">A diferencia de Razor Pages y MVC, que se compilan en torno a un modelo de solicitud y respuesta, los componentes se usan específicamente en la composición y la lógica de la interfaz de usuario del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="2a7f9-132">Blazor usa etiquetas HTML naturales para la composición de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-132">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="2a7f9-133">En el siguiente marcado de Razor se muestra un componente (`Dialog.razor`) que presenta un cuadro de diálogo y procesa un evento cuando el usuario selecciona un botón:</span><span class="sxs-lookup"><span data-stu-id="2a7f9-133">The following Razor markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

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

<span data-ttu-id="2a7f9-134">En el ejemplo anterior, `OnYes` es un método de C# desencadenado por el evento `onclick` del botón.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="2a7f9-135">El texto (`ChildContent`) y el título (`Title`) del cuadro de diálogo se proporciona mediante el siguiente componente que usa este componente en su interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="2a7f9-136">El componente `Dialog` está anidado en otro componente mediante una etiqueta HTML.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="2a7f9-137">En el ejemplo siguiente, el componente `Index` (`Pages/Index.razor`) utiliza el componente `Dialog` anterior.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="2a7f9-138">El atributo `Title` de la etiqueta pasa un valor para el título en la propiedad `Title` del componente `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="2a7f9-139">El contenido del elemento `<Dialog>` establece el texto del componente `Dialog` (`ChildContent`).</span><span class="sxs-lookup"><span data-stu-id="2a7f9-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="2a7f9-140">Cuando el componente `Dialog` se agrega al componente `Index`, [IntelliSense en Visual Studio](/visualstudio/ide/using-intellisense) agiliza el desarrollo con la finalización de la sintaxis y los parámetros.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

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

<span data-ttu-id="2a7f9-141">El cuadro de diálogo se representa cuando se accede al componente `Index` en un explorador.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="2a7f9-142">Cuando el usuario selecciona el botón, la consola de herramientas de desarrollo del explorador muestra el mensaje escrito por el método `OnYes`:</span><span class="sxs-lookup"><span data-stu-id="2a7f9-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![Componente Dialog representado en el explorador anidado en el interior del componente Index.](index/_static/dialog.png)

<span data-ttu-id="2a7f9-146">Los componentes se representan en una representación en memoria del elemento [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) del explorador denominado *árbol de representación*, que se usa para actualizar la interfaz de usuario de manera eficaz y flexible.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="2a7f9-147">Blazor WebAssembly es un [marco de aplicaciones de página única (SPA)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) para compilar aplicaciones web interactivas del lado cliente con. NET.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-147">Blazor WebAssembly is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="2a7f9-148">Blazor WebAssembly usa estándares web abiertos sin complementos o recompilación de código en otros lenguajes.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-148">Blazor WebAssembly uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="2a7f9-149">Blazor WebAssembly funciona en todos los exploradores web modernos, incluidos los exploradores para dispositivos móviles.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-149">Blazor WebAssembly works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="2a7f9-150">La ejecución de código .NET dentro de exploradores web se consigue mediante [WebAssembly](https://webassembly.org) (abreviado como `wasm`).</span><span class="sxs-lookup"><span data-stu-id="2a7f9-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="2a7f9-151">WebAssembly es un formato de código de bytes compacto optimizado para descargas rápidas y una velocidad de ejecución máxima.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="2a7f9-152">WebAssembly es un estándar web abierto y se admite en exploradores web sin complementos.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="2a7f9-153">El código de WebAssembly puede acceder a toda la funcionalidad del explorador mediante JavaScript, denominada *interoperabilidad de JavaScript*.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability*, often shortened to *JavaScript interop* or *JS interop*.</span></span> <span data-ttu-id="2a7f9-154">El código de .NET que se ejecuta a través de WebAssembly en el explorador se ejecuta a su vez en el espacio aislado de JavaScript del explorador con las protecciones que proporciona dicho espacio aislado contra acciones malintencionadas en la máquina cliente.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![Blazor WebAssembly ejecuta código de .NET en el explorador con WebAssembly.](index/_static/blazor-webassembly.png)

<span data-ttu-id="2a7f9-156">Cuando se compila y ejecuta una aplicación de Blazor WebAssembly en un explorador:</span><span class="sxs-lookup"><span data-stu-id="2a7f9-156">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="2a7f9-157">Los archivos de código C# y los archivos de Razor se compilan en ensamblados de .NET.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-157">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="2a7f9-158">Los ensamblados y el [runtime de .NET](/dotnet/framework/get-started/overview) se descargan en el explorador.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="2a7f9-159">Blazor WebAssembly arranca el entorno de ejecución .NET y lo configura para cargar los ensamblados de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-159">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="2a7f9-160">El entorno de ejecución de Blazor WebAssembly emplea la interoperabilidad de JavaScript para gestionar la manipulación de DOM y las llamadas API del explorador.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-160">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="2a7f9-161">El tamaño de la aplicación publicada, su *tamaño de carga*, es un factor de rendimiento crítico para la facilidad de uso de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-161">The size of the published app, its *payload size*, is a critical performance factor for an app's usability.</span></span> <span data-ttu-id="2a7f9-162">Una aplicación grande tarda un tiempo relativamente largo en descargarse en un explorador, lo que repercute en la experiencia del usuario.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="2a7f9-163">Blazor WebAssembly optimiza el tamaño de carga para reducir los tiempos de descarga:</span><span class="sxs-lookup"><span data-stu-id="2a7f9-163">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="2a7f9-164">Se ha quitado el código sin usar de la aplicación cuando se publica mediante el [recortador del lenguaje intermedio (IL)](xref:blazor/host-and-deploy/configure-trimmer).</span><span class="sxs-lookup"><span data-stu-id="2a7f9-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="2a7f9-165">Las respuestas HTTP se comprimen.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="2a7f9-166">El entorno de ejecución .NET y los ensamblados se almacenan en caché en el explorador.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="2a7f9-167">Se ha quitado el código sin usar de la aplicación cuando se publica mediante el [enlazador del lenguaje intermedio (IL)](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="2a7f9-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="2a7f9-168">Las respuestas HTTP se comprimen.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="2a7f9-169">El entorno de ejecución .NET y los ensamblados se almacenan en caché en el explorador.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="2a7f9-170">Blazor separa la lógica de representación de componentes del modo en el que se aplican las actualizaciones de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-170">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="2a7f9-171">*Blazor Server* ofrece compatibilidad con el hospedaje de componentes de Razor en el servidor de una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-171">*Blazor Server* provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="2a7f9-172">Las actualizaciones de la interfaz de usuario se controlan mediante una conexión de [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="2a7f9-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="2a7f9-173">El runtime controla:</span><span class="sxs-lookup"><span data-stu-id="2a7f9-173">The runtime handles:</span></span>

* <span data-ttu-id="2a7f9-174">El envío de eventos de interfaz de usuario desde el explorador al servidor.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="2a7f9-175">La aplicación de actualizaciones de la interfaz de usuario al componente representado que devuelve el servidor.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="2a7f9-176">La conexión que usa el servidor de Blazor Server para comunicarse con el explorador también se emplea para administrar las llamadas de interoperabilidad de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-176">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Blazor Server ejecuta código de .NET en el servidor e interactúa con Document Object Model en el cliente mediante una conexión de SignalR](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="2a7f9-178">Interoperabilidad de JavaScript</span><span class="sxs-lookup"><span data-stu-id="2a7f9-178">JavaScript interop</span></span>

<span data-ttu-id="2a7f9-179">En el caso de aplicaciones que necesitan bibliotecas de JavaScript de terceros y acceso a las API de explorador, los componentes interoperan con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="2a7f9-180">Los componentes pueden usar cualquier biblioteca o API que pueda utilizar JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="2a7f9-181">El código de C# puede [llamar a código de JavaScript](xref:blazor/call-javascript-from-dotnet) y, a su vez, el código de JavaScript puede [llamar al código de C#](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="2a7f9-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="2a7f9-182">Uso compartido de código y .NET Standard</span><span class="sxs-lookup"><span data-stu-id="2a7f9-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="2a7f9-183">Blazor implementa [.NET Standard](/dotnet/standard/net-standard), que permite a los proyectos de Blazor hacer referencia a las bibliotecas que cumplen con las especificaciones de ese estándar.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-183">Blazor implements the [.NET Standard](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="2a7f9-184">.NET Standard es una especificación formal de las API de .NET comunes entre las implementaciones de .NET.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="2a7f9-185">Las bibliotecas de clase .NET Standard pueden compartirse a través de diferentes plataformas .NET, como Blazor, .NET Framework, .NET Core, Xamarin, Mono y Unity.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-185">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="2a7f9-186">Las API que no pueden aplicarse dentro de un explorador web (por ejemplo, para acceder al sistema de archivos, abrir un socket y ejecutar subprocesos) generan una excepción <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="2a7f9-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a7f9-187">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="2a7f9-187">Additional resources</span></span>

* [<span data-ttu-id="2a7f9-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2a7f9-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="2a7f9-189">Guía de C#</span><span class="sxs-lookup"><span data-stu-id="2a7f9-189">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="2a7f9-190">HTML</span><span class="sxs-lookup"><span data-stu-id="2a7f9-190">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="2a7f9-191">[Vínculos interesantes de la comunidad de Blazor](https://github.com/AdrienTorris/awesome-blazor)</span><span class="sxs-lookup"><span data-stu-id="2a7f9-191">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
