---
title: Control de errores en aplicaciones Blazor de ASP.NET Core
author: guardrex
description: Descubra cómo Blazor de ASP.NET Core administra las excepciones no controladas y cómo desarrollar aplicaciones que detecten y controlen los errores.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
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
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: 5f7112d9a072f28d387e07bdf69ec0b7595ff6b4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014443"
---
# <a name="handle-errors-in-aspnet-core-no-locblazor-apps"></a><span data-ttu-id="35c47-103">Control de errores en aplicaciones Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35c47-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="35c47-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="35c47-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="35c47-105">En este artículo se describe cómo Blazor administra las excepciones no controladas y cómo desarrollar aplicaciones que detecten y controlen los errores.</span><span class="sxs-lookup"><span data-stu-id="35c47-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="35c47-106">Errores detallados durante el desarrollo</span><span class="sxs-lookup"><span data-stu-id="35c47-106">Detailed errors during development</span></span>

<span data-ttu-id="35c47-107">Cuando una aplicación Blazor no funciona correctamente durante el desarrollo, recibir información detallada del error de la aplicación ayuda a solucionar el problema.</span><span class="sxs-lookup"><span data-stu-id="35c47-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="35c47-108">Cuando se produce un error, en las aplicaciones Blazor se muestra una barra dorada en la parte inferior de la pantalla:</span><span class="sxs-lookup"><span data-stu-id="35c47-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="35c47-109">Durante el desarrollo, la barra dorada le dirige a la consola del explorador, donde puede ver la excepción.</span><span class="sxs-lookup"><span data-stu-id="35c47-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="35c47-110">En producción, la barra dorada informa al usuario de que se ha producido un error y recomienda actualizar el explorador.</span><span class="sxs-lookup"><span data-stu-id="35c47-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="35c47-111">La interfaz de usuario para esta experiencia de control de errores forma parte de las plantillas de proyecto de Blazor.</span><span class="sxs-lookup"><span data-stu-id="35c47-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="35c47-112">En una aplicación Blazor WebAssembly, personalice la experiencia en el archivo `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="35c47-112">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="35c47-113">En una aplicación Blazor Server, personalice la experiencia en el archivo `Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="35c47-113">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="35c47-114">Los estilos incluidos en las plantillas de Blazor (`wwwroot/css/app.css` o `wwwroot/css/site.css`) ocultan el elemento `blazor-error-ui`, que se muestra posteriormente cuando se produce un error:</span><span class="sxs-lookup"><span data-stu-id="35c47-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-no-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="35c47-115">Cómo reacciona una aplicación Blazor Server a las excepciones no controladas</span><span class="sxs-lookup"><span data-stu-id="35c47-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="35c47-116">Blazor Server es un marco con estado.</span><span class="sxs-lookup"><span data-stu-id="35c47-116">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="35c47-117">Mientras los usuarios interactúan con una aplicación, mantienen una conexión con el servidor, lo que se denomina *circuito*.</span><span class="sxs-lookup"><span data-stu-id="35c47-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="35c47-118">El circuito contiene instancias de componentes activas, además de muchos otros aspectos del estado, como:</span><span class="sxs-lookup"><span data-stu-id="35c47-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="35c47-119">La salida representada más reciente de los componentes.</span><span class="sxs-lookup"><span data-stu-id="35c47-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="35c47-120">El conjunto actual de delegados de control de eventos que se pueden desencadenar por eventos del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="35c47-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="35c47-121">Si un usuario abre la aplicación en varias pestañas del explorador, tiene varios circuitos independientes.</span><span class="sxs-lookup"><span data-stu-id="35c47-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="35c47-122">Blazor trata la mayoría de las excepciones no controladas como graves para el circuito en el que se producen.</span><span class="sxs-lookup"><span data-stu-id="35c47-122">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="35c47-123">Si se finaliza un circuito debido a una excepción no controlada, el usuario solo puede seguir interactuando con la aplicación si recarga la página para crear otro circuito.</span><span class="sxs-lookup"><span data-stu-id="35c47-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="35c47-124">Los circuitos externos al que se ha finalizado, que son circuitos para otros usuarios u otras pestañas del explorador, no se ven afectados.</span><span class="sxs-lookup"><span data-stu-id="35c47-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="35c47-125">Este escenario es parecido a cuando una aplicación de escritorio se bloquea.</span><span class="sxs-lookup"><span data-stu-id="35c47-125">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="35c47-126">La aplicación bloqueada debe reiniciarse, pero otras no resultan afectadas.</span><span class="sxs-lookup"><span data-stu-id="35c47-126">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="35c47-127">Un circuito finaliza cuando se produce una excepción no controlada por los siguientes motivos:</span><span class="sxs-lookup"><span data-stu-id="35c47-127">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="35c47-128">Una excepción no controlada a menudo deja el circuito en un estado indefinido.</span><span class="sxs-lookup"><span data-stu-id="35c47-128">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="35c47-129">Después de una excepción no controlada no se puede garantizar el funcionamiento normal de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="35c47-129">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="35c47-130">Es posible que aparezcan vulnerabilidades de seguridad en la aplicación si el circuito continúa.</span><span class="sxs-lookup"><span data-stu-id="35c47-130">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="35c47-131">Administración de excepciones no controladas en el código de desarrollador</span><span class="sxs-lookup"><span data-stu-id="35c47-131">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="35c47-132">Para que una aplicación continúe después de un error, debe tener lógica de control de errores.</span><span class="sxs-lookup"><span data-stu-id="35c47-132">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="35c47-133">En secciones posteriores de este artículo se describen posibles orígenes de excepciones no controladas.</span><span class="sxs-lookup"><span data-stu-id="35c47-133">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="35c47-134">En producción, en la interfaz de usuario no se representan mensajes de excepción del marco ni seguimientos de la pila.</span><span class="sxs-lookup"><span data-stu-id="35c47-134">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="35c47-135">La representación de mensajes de excepción o seguimientos de la pila podría:</span><span class="sxs-lookup"><span data-stu-id="35c47-135">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="35c47-136">Divulgar información confidencial a los usuarios finales.</span><span class="sxs-lookup"><span data-stu-id="35c47-136">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="35c47-137">Ayudar a un usuario malintencionado a detectar debilidades en una aplicación que pueden poner en peligro la seguridad de la aplicación, el servidor o la red.</span><span class="sxs-lookup"><span data-stu-id="35c47-137">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="35c47-138">Registro de errores con un proveedor persistente</span><span class="sxs-lookup"><span data-stu-id="35c47-138">Log errors with a persistent provider</span></span>

<span data-ttu-id="35c47-139">Si se produce una excepción no controlada, la excepción se registra en las instancias de <xref:Microsoft.Extensions.Logging.ILogger> configuradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="35c47-139">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="35c47-140">De forma predeterminada, las aplicaciones Blazor registran en la salida de la consola con el proveedor de registro de la consola.</span><span class="sxs-lookup"><span data-stu-id="35c47-140">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="35c47-141">Considere la posibilidad de realizar el registro en una ubicación más permanente con un proveedor que administre el tamaño y la rotación del registro.</span><span class="sxs-lookup"><span data-stu-id="35c47-141">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="35c47-142">Para obtener más información, vea <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="35c47-142">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="35c47-143">Durante el desarrollo, Blazor normalmente envía los detalles completos de las excepciones a la consola del explorador para ayudar en la depuración.</span><span class="sxs-lookup"><span data-stu-id="35c47-143">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="35c47-144">En producción, los errores detallados en la consola del explorador están deshabilitados de forma predeterminada, lo que significa que los errores no se envían a los clientes, pero los detalles completos de la excepción se siguen registrando en el lado del servidor.</span><span class="sxs-lookup"><span data-stu-id="35c47-144">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="35c47-145">Para obtener más información, vea <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="35c47-145">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="35c47-146">Debe decidir qué incidentes registrar y el nivel de gravedad de los incidentes registrados.</span><span class="sxs-lookup"><span data-stu-id="35c47-146">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="35c47-147">Es posible que los usuarios hostiles puedan desencadenar errores deliberadamente.</span><span class="sxs-lookup"><span data-stu-id="35c47-147">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="35c47-148">Por ejemplo, no registre un incidente de un error en el que se proporcione un valor `ProductId` desconocido en la dirección URL de un componente que muestra los detalles del producto.</span><span class="sxs-lookup"><span data-stu-id="35c47-148">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="35c47-149">No todos los errores se deben tratar como incidentes de alta gravedad para el registro.</span><span class="sxs-lookup"><span data-stu-id="35c47-149">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="35c47-150">Para obtener más información, vea <xref:blazor/fundamentals/logging>.</span><span class="sxs-lookup"><span data-stu-id="35c47-150">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="35c47-151">Lugares donde se pueden producir errores</span><span class="sxs-lookup"><span data-stu-id="35c47-151">Places where errors may occur</span></span>

<span data-ttu-id="35c47-152">El código del marco de trabajo y la aplicación puede desencadenar excepciones no controladas en cualquiera de las ubicaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="35c47-152">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="35c47-153">Creación de instancias de componentes</span><span class="sxs-lookup"><span data-stu-id="35c47-153">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="35c47-154">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="35c47-154">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="35c47-155">Lógica de representación</span><span class="sxs-lookup"><span data-stu-id="35c47-155">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="35c47-156">Controladores de eventos</span><span class="sxs-lookup"><span data-stu-id="35c47-156">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="35c47-157">Eliminación de componentes</span><span class="sxs-lookup"><span data-stu-id="35c47-157">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="35c47-158">Interoperabilidad de JavaScript</span><span class="sxs-lookup"><span data-stu-id="35c47-158">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="35c47-159">Nueva representación de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="35c47-159">Blazor Server rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="35c47-160">Las excepciones no controladas anteriores se describen en las siguientes secciones de este artículo.</span><span class="sxs-lookup"><span data-stu-id="35c47-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="35c47-161">Creación de instancias de componentes</span><span class="sxs-lookup"><span data-stu-id="35c47-161">Component instantiation</span></span>

<span data-ttu-id="35c47-162">Cuando Blazor crea una instancia de un componente:</span><span class="sxs-lookup"><span data-stu-id="35c47-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="35c47-163">Se invoca el constructor del componente.</span><span class="sxs-lookup"><span data-stu-id="35c47-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="35c47-164">Se invocan los constructores de cualquier servicio de inserción de dependencias que no sea singleton proporcionado al constructor del componente a través de la directiva [`@inject`](xref:mvc/views/razor#inject) o el atributo [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component).</span><span class="sxs-lookup"><span data-stu-id="35c47-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="35c47-165">Se produce un error en un circuito de Blazor Server cuando cualquier constructor ejecutado o un establecedor de cualquier propiedad `[Inject]` inicia una excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="35c47-165">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="35c47-166">La excepción es grave porque el marco no puede crear una instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="35c47-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="35c47-167">Si la lógica del constructor puede iniciar excepciones, la aplicación debe interceptarlas mediante una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro.</span><span class="sxs-lookup"><span data-stu-id="35c47-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="35c47-168">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="35c47-168">Lifecycle methods</span></span>

<span data-ttu-id="35c47-169">Durante la vigencia de un componente, Blazor invoca los [métodos de ciclo de vida](xref:blazor/components/lifecycle) siguientes:</span><span class="sxs-lookup"><span data-stu-id="35c47-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="35c47-170">Si cualquier método de ciclo de vida inicia una excepción, de forma sincrónica o asincrónica, la excepción es grave para un circuito de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="35c47-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="35c47-171">Para que los componentes traten los errores de los métodos de ciclo de vida, agregue lógica de control de errores.</span><span class="sxs-lookup"><span data-stu-id="35c47-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="35c47-172">En el ejemplo siguiente, donde <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> llama a un método para obtener un producto:</span><span class="sxs-lookup"><span data-stu-id="35c47-172">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="35c47-173">Una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) controla una excepción que se inicia en el método `ProductRepository.GetProductByIdAsync`.</span><span class="sxs-lookup"><span data-stu-id="35c47-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="35c47-174">Cuando se ejecuta el bloque `catch`:</span><span class="sxs-lookup"><span data-stu-id="35c47-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="35c47-175">`loadFailed` se establece en `true`, que se usa para mostrar un mensaje de error al usuario.</span><span class="sxs-lookup"><span data-stu-id="35c47-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="35c47-176">El error no se registra.</span><span class="sxs-lookup"><span data-stu-id="35c47-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="35c47-177">Lógica de representación</span><span class="sxs-lookup"><span data-stu-id="35c47-177">Rendering logic</span></span>

<span data-ttu-id="35c47-178">El marcado declarativo de un archivo de componente `.razor` se compila en un método de C# denominado <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span><span class="sxs-lookup"><span data-stu-id="35c47-178">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="35c47-179">Cuando se representa un componente, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> ejecuta y genera una estructura de datos que describe los elementos, el texto y los componentes secundarios del componente representado.</span><span class="sxs-lookup"><span data-stu-id="35c47-179">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="35c47-180">La lógica de representación puede iniciar una excepción.</span><span class="sxs-lookup"><span data-stu-id="35c47-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="35c47-181">Un ejemplo de este escenario se produce cuando se evalúa `@someObject.PropertyName` pero `@someObject` es `null`.</span><span class="sxs-lookup"><span data-stu-id="35c47-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="35c47-182">Una excepción no controlada iniciada por la lógica de representación es grave para un circuito de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="35c47-182">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="35c47-183">Para evitar una excepción de referencia nula en la lógica de representación, busque un objeto `null` antes de acceder a sus miembros.</span><span class="sxs-lookup"><span data-stu-id="35c47-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="35c47-184">En el ejemplo siguiente, no se accede a las propiedades `person.Address` si `person.Address` es `null`:</span><span class="sxs-lookup"><span data-stu-id="35c47-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="35c47-185">En el código anterior se supone que `person` no es `null`.</span><span class="sxs-lookup"><span data-stu-id="35c47-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="35c47-186">A menudo, la estructura del código garantiza que un objeto existe en el momento en que se representa el componente.</span><span class="sxs-lookup"><span data-stu-id="35c47-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="35c47-187">En esos casos, no es necesario comprobar `null` en la lógica de representación.</span><span class="sxs-lookup"><span data-stu-id="35c47-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="35c47-188">En el ejemplo anterior, es posible que se garantice la existencia de `person` porque se crea `person` cuando se crea una instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="35c47-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="35c47-189">Controladores de eventos</span><span class="sxs-lookup"><span data-stu-id="35c47-189">Event handlers</span></span>

<span data-ttu-id="35c47-190">El código del lado cliente desencadena invocaciones de código de C# cuando se crean controladores de eventos mediante:</span><span class="sxs-lookup"><span data-stu-id="35c47-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="35c47-191">Otros atributos `@on...`</span><span class="sxs-lookup"><span data-stu-id="35c47-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="35c47-192">Es posible que el código del controlador de eventos inicie una excepción no controlada en estos escenarios.</span><span class="sxs-lookup"><span data-stu-id="35c47-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="35c47-193">Si un controlador de eventos inicia una excepción no controlada (por ejemplo, se produce un error en una consulta de base de datos), la excepción es grave para un circuito de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="35c47-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="35c47-194">Si la aplicación llama a código que podría generar un error por motivos externos, capture las excepciones mediante una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro.</span><span class="sxs-lookup"><span data-stu-id="35c47-194">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="35c47-195">Si el código de usuario no captura y controla la excepción, el marco de trabajo registra la excepción y finaliza el circuito.</span><span class="sxs-lookup"><span data-stu-id="35c47-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="35c47-196">Eliminación de componentes</span><span class="sxs-lookup"><span data-stu-id="35c47-196">Component disposal</span></span>

<span data-ttu-id="35c47-197">Un componente se puede quitar de la interfaz de usuario, por ejemplo, porque el usuario ha navegado a otra página.</span><span class="sxs-lookup"><span data-stu-id="35c47-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="35c47-198">Cuando un componente que implementa <xref:System.IDisposable?displayProperty=fullName> se quita de la interfaz de usuario, el marco de trabajo llama al método <xref:System.IDisposable.Dispose%2A> del componente.</span><span class="sxs-lookup"><span data-stu-id="35c47-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="35c47-199">Si el método `Dispose` del componente inicia una excepción no controlada, la excepción es grave para un circuito de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="35c47-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="35c47-200">Si la lógica de eliminación puede iniciar excepciones, la aplicación debe interceptarlas mediante una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro.</span><span class="sxs-lookup"><span data-stu-id="35c47-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="35c47-201">Para obtener más información sobre la eliminación de componentes, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="35c47-201">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="35c47-202">Interoperabilidad de JavaScript</span><span class="sxs-lookup"><span data-stu-id="35c47-202">JavaScript interop</span></span>

<span data-ttu-id="35c47-203"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> permite que el código de .NET realice llamadas asincrónicas al entorno de ejecución de JavaScript en el explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="35c47-203"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="35c47-204">Se aplican las condiciones siguientes al control de errores con <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="35c47-204">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="35c47-205">Si una llamada a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> produce un error de forma sincrónica, se produce una excepción de .NET.</span><span class="sxs-lookup"><span data-stu-id="35c47-205">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="35c47-206">Se puede producir un error en una llamada a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>, por ejemplo, porque no se puedan serializar los argumentos proporcionados.</span><span class="sxs-lookup"><span data-stu-id="35c47-206">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="35c47-207">El código del desarrollador debe detectar la excepción.</span><span class="sxs-lookup"><span data-stu-id="35c47-207">Developer code must catch the exception.</span></span> <span data-ttu-id="35c47-208">Si el código de la aplicación en un controlador de eventos o en un método de ciclo de vida de componente no controla una excepción, la excepción resultante es grave para un circuito de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="35c47-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="35c47-209">Si se produce un error en una llamada a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> de forma asincrónica, se produce un error en el objeto <xref:System.Threading.Tasks.Task> de .NET.</span><span class="sxs-lookup"><span data-stu-id="35c47-209">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="35c47-210">Se puede producir un error en una llamada a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>, por ejemplo, porque el código de JavaScript inicia una excepción o devuelve un objeto `Promise` que se ha completado como `rejected`.</span><span class="sxs-lookup"><span data-stu-id="35c47-210">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="35c47-211">El código del desarrollador debe detectar la excepción.</span><span class="sxs-lookup"><span data-stu-id="35c47-211">Developer code must catch the exception.</span></span> <span data-ttu-id="35c47-212">Si usa el operador [`await`](/dotnet/csharp/language-reference/keywords/await), considere la posibilidad de encapsular la llamada de método en una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro.</span><span class="sxs-lookup"><span data-stu-id="35c47-212">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="35c47-213">De lo contrario, el código con error provoca una excepción no controlada que es grave para un circuito de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="35c47-213">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="35c47-214">De forma predeterminada, las llamadas a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> se deben completar en un período determinado o, de lo contrario, se agota el tiempo de espera de la llamada. El período de tiempo de expiración predeterminado es de un minuto.</span><span class="sxs-lookup"><span data-stu-id="35c47-214">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="35c47-215">El tiempo de expiración protege al código de una pérdida en la conectividad de red o de código JavaScript que nunca devuelve un mensaje de finalización.</span><span class="sxs-lookup"><span data-stu-id="35c47-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="35c47-216">Si se agota el tiempo de espera de la llamada, se produce un error en el objeto <xref:System.Threading.Tasks> resultante con una excepción <xref:System.OperationCanceledException>.</span><span class="sxs-lookup"><span data-stu-id="35c47-216">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="35c47-217">Capture y procese la excepción con el registro.</span><span class="sxs-lookup"><span data-stu-id="35c47-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="35c47-218">Del mismo modo, el código de JavaScript puede iniciar llamadas a métodos de .NET indicados por el atributo [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="35c47-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="35c47-219">Si estos métodos de .NET inician una excepción no controlada:</span><span class="sxs-lookup"><span data-stu-id="35c47-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="35c47-220">La excepción no se trata como grave para un circuito de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="35c47-220">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="35c47-221">Se rechaza el objeto `Promise` del lado de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="35c47-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="35c47-222">Tiene la opción de usar el código de control de errores en el lado de .NET o en el de JavaScript de la llamada de método.</span><span class="sxs-lookup"><span data-stu-id="35c47-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="35c47-223">Para obtener más información, vea los artículos siguientes:</span><span class="sxs-lookup"><span data-stu-id="35c47-223">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="no-locblazor-server-prerendering"></a><span data-ttu-id="35c47-224">Representación previa de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="35c47-224">Blazor Server prerendering</span></span>

<span data-ttu-id="35c47-225">Se puede realizar la representación previa de los componentes de Blazor mediante el [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para que su marcado HTML representado se devuelva como parte de la solicitud HTTP inicial del usuario.</span><span class="sxs-lookup"><span data-stu-id="35c47-225">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="35c47-226">El funcionamiento es el siguiente:</span><span class="sxs-lookup"><span data-stu-id="35c47-226">This works by:</span></span>

* <span data-ttu-id="35c47-227">Se crea un circuito para todos los componentes con representación previa que forman parte de la misma página.</span><span class="sxs-lookup"><span data-stu-id="35c47-227">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="35c47-228">Se genera el código HTML inicial.</span><span class="sxs-lookup"><span data-stu-id="35c47-228">Generating the initial HTML.</span></span>
* <span data-ttu-id="35c47-229">El circuito se trata como `disconnected` hasta que el explorador del usuario establece una conexión de SignalR al mismo servidor.</span><span class="sxs-lookup"><span data-stu-id="35c47-229">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="35c47-230">Cuando se establece la conexión, se reanuda la interactividad en el circuito y se actualiza el marcado HTML de los componentes.</span><span class="sxs-lookup"><span data-stu-id="35c47-230">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="35c47-231">Si algún componente inicia una excepción no controlada durante la representación previa, por ejemplo, durante un método de ciclo de vida o en la lógica de representación:</span><span class="sxs-lookup"><span data-stu-id="35c47-231">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="35c47-232">La excepción es grave para el circuito.</span><span class="sxs-lookup"><span data-stu-id="35c47-232">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="35c47-233">La excepción se inicia en la pila de llamadas desde el asistente de etiquetas <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="35c47-233">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="35c47-234">Por tanto, se produce un error en toda la solicitud HTTP a menos que el código del desarrollador detecte la excepción de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="35c47-234">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="35c47-235">En circunstancias normales, cuando se produce un error en la representación previa, continuar con la generación y representación del componente no tiene sentido, ya que un componente en funcionamiento no se puede representar.</span><span class="sxs-lookup"><span data-stu-id="35c47-235">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="35c47-236">Para tolerar los errores que se puedan producir durante la representación previa, se debe colocar la lógica de control de errores dentro de un componente que pueda iniciar excepciones.</span><span class="sxs-lookup"><span data-stu-id="35c47-236">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="35c47-237">Use instrucciones [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro.</span><span class="sxs-lookup"><span data-stu-id="35c47-237">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="35c47-238">En lugar de encapsular el asistente de etiquetas <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> en una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch), coloque la lógica de control de errores en el componente representado por el asistente de etiquetas <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="35c47-238">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="35c47-239">Escenarios avanzados</span><span class="sxs-lookup"><span data-stu-id="35c47-239">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="35c47-240">Representación recursiva</span><span class="sxs-lookup"><span data-stu-id="35c47-240">Recursive rendering</span></span>

<span data-ttu-id="35c47-241">Los componentes se pueden anidar de forma recursiva.</span><span class="sxs-lookup"><span data-stu-id="35c47-241">Components can be nested recursively.</span></span> <span data-ttu-id="35c47-242">Esto resulta útil para representar estructuras de datos recursivas.</span><span class="sxs-lookup"><span data-stu-id="35c47-242">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="35c47-243">Por ejemplo, un componente `TreeNode` puede representar más componentes `TreeNode` para cada uno de los elementos secundarios del nodo.</span><span class="sxs-lookup"><span data-stu-id="35c47-243">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="35c47-244">Al realizar la representación de forma recursiva, evite patrones de codificación que produzcan una recursión infinita:</span><span class="sxs-lookup"><span data-stu-id="35c47-244">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="35c47-245">No represente de forma recursiva una estructura de datos que contenga un ciclo.</span><span class="sxs-lookup"><span data-stu-id="35c47-245">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="35c47-246">Por ejemplo, no represente un nodo de árbol cuyos elementos secundarios se incluyan a sí mismos.</span><span class="sxs-lookup"><span data-stu-id="35c47-246">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="35c47-247">No cree una cadena de diseños que contengan un ciclo.</span><span class="sxs-lookup"><span data-stu-id="35c47-247">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="35c47-248">Por ejemplo, no debe crear un diseño cuyo diseño sea él mismo.</span><span class="sxs-lookup"><span data-stu-id="35c47-248">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="35c47-249">No permita que un usuario final incumpla las invariables de recursión (reglas) a través de entradas de datos malintencionadas o llamadas de interoperabilidad de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="35c47-249">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="35c47-250">Bucles infinitos durante la representación:</span><span class="sxs-lookup"><span data-stu-id="35c47-250">Infinite loops during rendering:</span></span>

* <span data-ttu-id="35c47-251">Hace que el proceso de representación continúe de manera indefinida.</span><span class="sxs-lookup"><span data-stu-id="35c47-251">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="35c47-252">Equivale a crear un bucle sin terminar.</span><span class="sxs-lookup"><span data-stu-id="35c47-252">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="35c47-253">En estos escenarios, se produce un error en un circuito de Blazor Server afectado y el subproceso normalmente intenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="35c47-253">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="35c47-254">Consumir el máximo tiempo de CPU que permita el sistema operativo, de manera indefinida.</span><span class="sxs-lookup"><span data-stu-id="35c47-254">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="35c47-255">Consumir una cantidad ilimitada de memoria del servidor.</span><span class="sxs-lookup"><span data-stu-id="35c47-255">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="35c47-256">El consumo de memoria ilimitada es equivalente al escenario en el que un bucle sin terminar agrega entradas a una colección en cada iteración.</span><span class="sxs-lookup"><span data-stu-id="35c47-256">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="35c47-257">Para evitar patrones de recursión, asegúrese de que el código de representación recursiva contenga condiciones de detención adecuadas.</span><span class="sxs-lookup"><span data-stu-id="35c47-257">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="35c47-258">Lógica de árbol de representación personalizada</span><span class="sxs-lookup"><span data-stu-id="35c47-258">Custom render tree logic</span></span>

<span data-ttu-id="35c47-259">La mayoría de los componentes Blazor se implementan como archivos `.razor` y se compilan para generar lógica que opere en un objeto <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> y representar su salida.</span><span class="sxs-lookup"><span data-stu-id="35c47-259">Most Blazor components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="35c47-260">Un desarrollador puede implementar de forma manual lógica de <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> mediante código de C# por procedimientos.</span><span class="sxs-lookup"><span data-stu-id="35c47-260">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="35c47-261">Para obtener más información, vea <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="35c47-261">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="35c47-262">El uso de la lógica del generador de árboles de representación manual se considera un escenario avanzado y no seguro, no recomendado para el desarrollo de componentes generales.</span><span class="sxs-lookup"><span data-stu-id="35c47-262">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="35c47-263">Si se escribe código de <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, el desarrollador debe garantizar la corrección del código.</span><span class="sxs-lookup"><span data-stu-id="35c47-263">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="35c47-264">Por ejemplo, el desarrollador debe asegurarse de que:</span><span class="sxs-lookup"><span data-stu-id="35c47-264">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="35c47-265">Las llamadas a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> y <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> se equilibran correctamente.</span><span class="sxs-lookup"><span data-stu-id="35c47-265">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="35c47-266">Los atributos solo se agregan en los lugares correctos.</span><span class="sxs-lookup"><span data-stu-id="35c47-266">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="35c47-267">Una lógica incorrecta del generador de árboles de representación manual puede producir un comportamiento indefinido arbitrario, incluidos bloqueos, bloqueos del servidor y vulnerabilidades de seguridad.</span><span class="sxs-lookup"><span data-stu-id="35c47-267">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="35c47-268">Considere la posibilidad de usar lógica de generador de árbol de representación manual en el mismo nivel de complejidad y con el mismo nivel de *peligro* como escribir código de ensamblado o instrucciones MSIL a mano.</span><span class="sxs-lookup"><span data-stu-id="35c47-268">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
