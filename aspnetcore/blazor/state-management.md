---
title: Administración de estado de Blazor en ASP.NET Core
author: guardrex
description: Aprenda a conservar el estado en las aplicaciones Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 6e6f3047da30490caff4f820003a3018e8c26aaa
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506621"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="736ee-103">Administración de estado de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="736ee-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="736ee-104">De [Steve Sanderson](https://github.com/SteveSandersonMS) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="736ee-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="736ee-105">El estado de usuario creado en una aplicación Blazor WebAssembly se guarda en la memoria del explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="736ee-106">Entre los ejemplos de estado de usuario almacenados en la memoria del explorador se incluyen:</span><span class="sxs-lookup"><span data-stu-id="736ee-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="736ee-107">La jerarquía de instancias del componente y su salida de representación más reciente en la interfaz de usuario representada.</span><span class="sxs-lookup"><span data-stu-id="736ee-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="736ee-108">Los valores de los campos y las propiedades de las instancias del componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="736ee-109">Los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="736ee-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="736ee-110">Los valores establecidos mediante llamadas de [interoperabilidad de JavaScript](xref:blazor/call-javascript-from-dotnet).</span><span class="sxs-lookup"><span data-stu-id="736ee-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="736ee-111">Cuando un usuario cierra y vuelve a abrir el explorador, o vuelve a cargar la página, se pierde el estado de usuario guardado en la memoria del explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

> [!NOTE]
> <span data-ttu-id="736ee-112">El [almacenamiento del explorador protegido](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) (espacio de nombres <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName>) se basa en la protección de datos de ASP.NET Core y solo se admite para aplicaciones de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="736ee-112">[Protected Browser Storage](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) (<xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> namespace) relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="736ee-113">Conservación del estado en todas las sesiones del explorador</span><span class="sxs-lookup"><span data-stu-id="736ee-113">Persist state across browser sessions</span></span>

<span data-ttu-id="736ee-114">En general, se mantiene el estado en las sesiones del explorador en las que los usuarios crean datos de forma activa, no simplemente leyendo los datos que ya existen.</span><span class="sxs-lookup"><span data-stu-id="736ee-114">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="736ee-115">Para conservar el estado en todas las sesiones del explorador, la aplicación debe conservar los datos en otra ubicación de almacenamiento distinta a la memoria del explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-115">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="736ee-116">La persistencia del estado no es automática.</span><span class="sxs-lookup"><span data-stu-id="736ee-116">State persistence isn't automatic.</span></span> <span data-ttu-id="736ee-117">Debe seguir los pasos al desarrollar la aplicación para implementar la persistencia de datos con estado.</span><span class="sxs-lookup"><span data-stu-id="736ee-117">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="736ee-118">La persistencia de los datos normalmente solo es necesaria para el estado de alto valor que los usuarios crearon.</span><span class="sxs-lookup"><span data-stu-id="736ee-118">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="736ee-119">En los ejemplos siguientes, el estado persistente ahorra tiempo o ayuda en las actividades comerciales:</span><span class="sxs-lookup"><span data-stu-id="736ee-119">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="736ee-120">Formularios web de varios pasos: en un formulario web que consta de varios pasos, en caso de que el estado se pierda, los usuarios tienen que dedicar mucho tiempo a volver a escribir los datos que ya han proporcionado.</span><span class="sxs-lookup"><span data-stu-id="736ee-120">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="736ee-121">Un usuario pierde el estado en este escenario si se sale del formulario y vuelve más adelante.</span><span class="sxs-lookup"><span data-stu-id="736ee-121">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="736ee-122">Carros de la compra: se puede mantener cualquier componente comercial importante de una aplicación que represente ingresos potenciales.</span><span class="sxs-lookup"><span data-stu-id="736ee-122">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="736ee-123">Un usuario que pierde su estado y, por tanto, su carro de la compra, puede comprar menos productos o servicios cuando vuelva al sitio más adelante.</span><span class="sxs-lookup"><span data-stu-id="736ee-123">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="736ee-124">Una aplicación solo puede conservar el *estado de la aplicación*.</span><span class="sxs-lookup"><span data-stu-id="736ee-124">An app can only persist *app state*.</span></span> <span data-ttu-id="736ee-125">Las interfaces de usuario no se pueden conservar, como instancias de componentes y sus árboles de representación.</span><span class="sxs-lookup"><span data-stu-id="736ee-125">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="736ee-126">Los componentes y los árboles de representación no suelen ser serializables.</span><span class="sxs-lookup"><span data-stu-id="736ee-126">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="736ee-127">Para conservar el estado de la interfaz de usuario, como los nodos expandidos de un control de vista de árbol, la aplicación debe tener código personalizado para modelar el comportamiento del estado de la interfaz de usuario como estado de la aplicación serializable.</span><span class="sxs-lookup"><span data-stu-id="736ee-127">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="736ee-128">Dónde conservar el estado</span><span class="sxs-lookup"><span data-stu-id="736ee-128">Where to persist state</span></span>

<span data-ttu-id="736ee-129">Existen ubicaciones comunes para el estado persistente:</span><span class="sxs-lookup"><span data-stu-id="736ee-129">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="736ee-130">Almacenamiento del lado servidor</span><span class="sxs-lookup"><span data-stu-id="736ee-130">Server-side storage</span></span>](#server-side-storage-wasm)
* [<span data-ttu-id="736ee-131">URL</span><span class="sxs-lookup"><span data-stu-id="736ee-131">URL</span></span>](#url-wasm)
* [<span data-ttu-id="736ee-132">Almacenamiento del explorador</span><span class="sxs-lookup"><span data-stu-id="736ee-132">Browser storage</span></span>](#browser-storage-wasm)
* [<span data-ttu-id="736ee-133">Servicio de contenedor de estado en memoria</span><span class="sxs-lookup"><span data-stu-id="736ee-133">In-memory state container service</span></span>](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm"><span data-ttu-id="736ee-134">Almacenamiento del lado servidor</span><span class="sxs-lookup"><span data-stu-id="736ee-134">Server-side storage</span></span></h2>

<span data-ttu-id="736ee-135">Para una persistencia de datos permanente que abarque varios usuarios y dispositivos, la aplicación puede usar el almacenamiento del lado servidor independiente al que se accede a través de una API web.</span><span class="sxs-lookup"><span data-stu-id="736ee-135">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="736ee-136">Las opciones incluyen:</span><span class="sxs-lookup"><span data-stu-id="736ee-136">Options include:</span></span>

* <span data-ttu-id="736ee-137">Blob Storage</span><span class="sxs-lookup"><span data-stu-id="736ee-137">Blob storage</span></span>
* <span data-ttu-id="736ee-138">Almacenamiento de pares clave-valor</span><span class="sxs-lookup"><span data-stu-id="736ee-138">Key-value storage</span></span>
* <span data-ttu-id="736ee-139">Base de datos relacional</span><span class="sxs-lookup"><span data-stu-id="736ee-139">Relational database</span></span>
* <span data-ttu-id="736ee-140">Almacenamiento de tablas</span><span class="sxs-lookup"><span data-stu-id="736ee-140">Table storage</span></span>

<span data-ttu-id="736ee-141">Una vez guardados los datos, el estado del usuario se conserva y está disponible en cualquier nueva sesión del explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-141">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="736ee-142">Dado que las aplicaciones Blazor WebAssembly se ejecutan completamente en el explorador del usuario, requieren medidas adicionales para acceder a sistemas externos seguros, como bases de datos y servicios de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="736ee-142">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="736ee-143">Las aplicaciones de Blazor WebAssembly se protegen de la misma manera que las aplicaciones de página única (SPA).</span><span class="sxs-lookup"><span data-stu-id="736ee-143">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="736ee-144">Normalmente, una aplicación autentica a un usuario a través de [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) y, a continuación, interactúa con los servicios de almacenamiento y las bases de datos a través de llamadas de API web a una aplicación del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="736ee-144">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="736ee-145">La aplicación del lado servidor media en la transferencia de datos entre la aplicación Blazor WebAssembly y la base de datos o el servicio de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="736ee-145">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="736ee-146">La aplicación Blazor WebAssembly mantiene una conexión efímera con la aplicación del lado servidor, mientras que la aplicación del lado servidor tiene una conexión persistente con el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="736ee-146">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="736ee-147">Para obtener más información, consulte los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="736ee-147">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="736ee-148">Artículos sobre *seguridad e Identity* de Blazor</span><span class="sxs-lookup"><span data-stu-id="736ee-148">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="736ee-149">Para obtener más información sobre las opciones de almacenamiento de datos de Azure, consulte lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="736ee-149">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="736ee-150">Bases de datos de Azure</span><span class="sxs-lookup"><span data-stu-id="736ee-150">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="736ee-151">Documentación de Azure Storage</span><span class="sxs-lookup"><span data-stu-id="736ee-151">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-wasm"><span data-ttu-id="736ee-152">Resolución</span><span class="sxs-lookup"><span data-stu-id="736ee-152">URL</span></span></h2>

<span data-ttu-id="736ee-153">Para los datos transitorios que representan el estado de navegación, modele los datos como parte de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="736ee-153">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="736ee-154">Entre los ejemplos de estado de usuario modelado en la dirección URL se incluyen:</span><span class="sxs-lookup"><span data-stu-id="736ee-154">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="736ee-155">El identificador de una entidad visualizada.</span><span class="sxs-lookup"><span data-stu-id="736ee-155">The ID of a viewed entity.</span></span>
* <span data-ttu-id="736ee-156">El número de la página actual en una cuadrícula paginada.</span><span class="sxs-lookup"><span data-stu-id="736ee-156">The current page number in a paged grid.</span></span>

<span data-ttu-id="736ee-157">El contenido de la barra de direcciones del explorador se conserva si el usuario vuelve a cargar la página manualmente.</span><span class="sxs-lookup"><span data-stu-id="736ee-157">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="736ee-158">Para obtener información sobre cómo definir patrones de direcciones URL con la directiva [`@page`](xref:mvc/views/razor#page), vea <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="736ee-158">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-wasm"><span data-ttu-id="736ee-159">Almacenamiento del explorador</span><span class="sxs-lookup"><span data-stu-id="736ee-159">Browser storage</span></span></h2>

<span data-ttu-id="736ee-160">En el caso de los datos transitorios que el usuario crea activamente, una ubicación de almacenamiento que se suele usar son las colecciones [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) del explorador:</span><span class="sxs-lookup"><span data-stu-id="736ee-160">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="736ee-161">`localStorage` está en el ámbito de la ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-161">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="736ee-162">Si el usuario recarga la página o la cierra y vuelve a abrir el explorador, el estado se conserva.</span><span class="sxs-lookup"><span data-stu-id="736ee-162">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="736ee-163">Si el usuario abre varias pestañas del explorador, el estado se comparte entre las pestañas.</span><span class="sxs-lookup"><span data-stu-id="736ee-163">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="736ee-164">Los datos se conservan en `localStorage` hasta que se borran explícitamente.</span><span class="sxs-lookup"><span data-stu-id="736ee-164">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="736ee-165">`sessionStorage` está en el ámbito de la pestaña del explorador. Si el usuario recarga la pestaña, el estado se conserva.</span><span class="sxs-lookup"><span data-stu-id="736ee-165">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="736ee-166">Si el usuario cierra la pestaña o el explorador, se pierde el estado.</span><span class="sxs-lookup"><span data-stu-id="736ee-166">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="736ee-167">Si el usuario abre varias pestañas del explorador, cada pestaña tendrá su propia versión independiente de los datos.</span><span class="sxs-lookup"><span data-stu-id="736ee-167">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="736ee-168">`localStorage` y `sessionStorage` se pueden usar en aplicaciones Blazor WebAssembly, pero solo escribiendo código personalizado o usando un paquete de terceros.</span><span class="sxs-lookup"><span data-stu-id="736ee-168">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="736ee-169">Por lo general, es más seguro usar `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="736ee-169">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="736ee-170">`sessionStorage` evita el riesgo de que un usuario abra varias pestañas y encuentre lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="736ee-170">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="736ee-171">Errores en el almacenamiento de estado en todas las pestañas.</span><span class="sxs-lookup"><span data-stu-id="736ee-171">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="736ee-172">Comportamiento confuso cuando una pestaña sobrescribe el estado de otras.</span><span class="sxs-lookup"><span data-stu-id="736ee-172">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="736ee-173">`localStorage` es la mejor opción si la aplicación debe conservar el estado en el cierre y volver a abrir el explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-173">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="736ee-174">Los usuarios pueden ver o alterar los datos almacenados en `localStorage` y `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="736ee-174">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

<h2 id="in-memory-state-container-service-wasm"><span data-ttu-id="736ee-175">Servicio de contenedor de estado en memoria</span><span class="sxs-lookup"><span data-stu-id="736ee-175">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="736ee-176">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="736ee-176">Additional resources</span></span>

* [<span data-ttu-id="736ee-177">Guardar el estado de la aplicación antes de una operación de autenticación</span><span class="sxs-lookup"><span data-stu-id="736ee-177">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="736ee-178">El servidor Blazor Server es un marco para aplicaciones con estado.</span><span class="sxs-lookup"><span data-stu-id="736ee-178">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="736ee-179">La mayoría de las veces, la aplicación mantiene una conexión con el servidor.</span><span class="sxs-lookup"><span data-stu-id="736ee-179">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="736ee-180">El estado del usuario se mantiene en la memoria del servidor en un *circuito*.</span><span class="sxs-lookup"><span data-stu-id="736ee-180">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="736ee-181">Entre los ejemplos de estado de usuario que se mantiene en un circuito se incluyen:</span><span class="sxs-lookup"><span data-stu-id="736ee-181">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="736ee-182">La jerarquía de instancias del componente y su salida de representación más reciente en la interfaz de usuario representada.</span><span class="sxs-lookup"><span data-stu-id="736ee-182">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="736ee-183">Los valores de los campos y las propiedades de las instancias del componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-183">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="736ee-184">Los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) que se encuentran en el ámbito del circuito.</span><span class="sxs-lookup"><span data-stu-id="736ee-184">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="736ee-185">También se puede encontrar el estado de usuario en las variables de JavaScript en el conjunto de la memoria del explorador mediante llamadas de [interoperabilidad de JavaScript](xref:blazor/call-javascript-from-dotnet).</span><span class="sxs-lookup"><span data-stu-id="736ee-185">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="736ee-186">Si un usuario experimenta una pérdida de conexión de red temporal, Blazor intenta volver a conectar al usuario a su circuito original con su estado original.</span><span class="sxs-lookup"><span data-stu-id="736ee-186">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="736ee-187">Sin embargo, no siempre es posible volver a conectar a un usuario con su circuito original en la memoria del servidor:</span><span class="sxs-lookup"><span data-stu-id="736ee-187">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="736ee-188">El servidor no puede mantener un circuito desconectado para siempre.</span><span class="sxs-lookup"><span data-stu-id="736ee-188">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="736ee-189">El servidor debe liberar un circuito desconectado después de un tiempo de espera o cuando el servidor esté bajo presión de memoria.</span><span class="sxs-lookup"><span data-stu-id="736ee-189">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="736ee-190">En entornos de implementación de carga equilibrada y multiservidor, se puede producir un error en los servidores individuales o estos se pueden quitar automáticamente cuando ya no sean necesarios para controlar el volumen total de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="736ee-190">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="736ee-191">Las solicitudes de procesamiento de servidor originales de un usuario pueden dejar de estar disponibles cuando el usuario intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="736ee-191">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="736ee-192">El usuario podría cerrar y volver a abrir el explorador o recargar la página, lo que elimina cualquier estado que se mantenga en la memoria del explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-192">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="736ee-193">Por ejemplo, los valores de las variables de JavaScript establecidos mediante llamadas de interoperabilidad de JavaScript se pierden.</span><span class="sxs-lookup"><span data-stu-id="736ee-193">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="736ee-194">Cuando un usuario no se puede volver a conectar a su circuito original, el usuario recibe un circuito nuevo con un estado vacío.</span><span class="sxs-lookup"><span data-stu-id="736ee-194">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="736ee-195">Esto es equivalente a cerrar y volver a abrir una aplicación de escritorio.</span><span class="sxs-lookup"><span data-stu-id="736ee-195">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="736ee-196">Conservación del estado entre circuitos</span><span class="sxs-lookup"><span data-stu-id="736ee-196">Persist state across circuits</span></span>

<span data-ttu-id="736ee-197">En general, se mantiene el estado en los circuitos en los que los usuarios crean datos de forma activa, no simplemente leyendo los datos que ya existen.</span><span class="sxs-lookup"><span data-stu-id="736ee-197">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="736ee-198">Para conservar el estado en los circuitos, la aplicación debe conservar los datos en otra ubicación de almacenamiento distinta a la memoria del servidor.</span><span class="sxs-lookup"><span data-stu-id="736ee-198">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="736ee-199">La persistencia del estado no es automática.</span><span class="sxs-lookup"><span data-stu-id="736ee-199">State persistence isn't automatic.</span></span> <span data-ttu-id="736ee-200">Debe seguir los pasos al desarrollar la aplicación para implementar la persistencia de datos con estado.</span><span class="sxs-lookup"><span data-stu-id="736ee-200">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="736ee-201">La persistencia de los datos normalmente solo es necesaria para el estado de alto valor que los usuarios crearon.</span><span class="sxs-lookup"><span data-stu-id="736ee-201">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="736ee-202">En los ejemplos siguientes, el estado persistente ahorra tiempo o ayuda en las actividades comerciales:</span><span class="sxs-lookup"><span data-stu-id="736ee-202">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="736ee-203">Formularios web de varios pasos: en un formulario web que consta de varios pasos, en caso de que el estado se pierda, los usuarios tienen que dedicar mucho tiempo a volver a escribir los datos que ya han proporcionado.</span><span class="sxs-lookup"><span data-stu-id="736ee-203">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="736ee-204">Un usuario pierde el estado en este escenario si se sale del formulario y vuelve más adelante.</span><span class="sxs-lookup"><span data-stu-id="736ee-204">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="736ee-205">Carros de la compra: se puede mantener cualquier componente comercial importante de una aplicación que represente ingresos potenciales.</span><span class="sxs-lookup"><span data-stu-id="736ee-205">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="736ee-206">Un usuario que pierde su estado y, por tanto, su carro de la compra, puede comprar menos productos o servicios cuando vuelva al sitio más adelante.</span><span class="sxs-lookup"><span data-stu-id="736ee-206">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="736ee-207">Una aplicación solo puede conservar el *estado de la aplicación*.</span><span class="sxs-lookup"><span data-stu-id="736ee-207">An app can only persist *app state*.</span></span> <span data-ttu-id="736ee-208">Las interfaces de usuario no se pueden conservar, como instancias de componentes y sus árboles de representación.</span><span class="sxs-lookup"><span data-stu-id="736ee-208">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="736ee-209">Los componentes y los árboles de representación no suelen ser serializables.</span><span class="sxs-lookup"><span data-stu-id="736ee-209">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="736ee-210">Para conservar el estado de la interfaz de usuario, como los nodos expandidos de un control de vista de árbol, la aplicación debe tener código personalizado para modelar el comportamiento del estado de la interfaz de usuario como estado de la aplicación serializable.</span><span class="sxs-lookup"><span data-stu-id="736ee-210">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="736ee-211">Dónde conservar el estado</span><span class="sxs-lookup"><span data-stu-id="736ee-211">Where to persist state</span></span>

<span data-ttu-id="736ee-212">Existen ubicaciones comunes para el estado persistente:</span><span class="sxs-lookup"><span data-stu-id="736ee-212">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="736ee-213">Almacenamiento del lado servidor</span><span class="sxs-lookup"><span data-stu-id="736ee-213">Server-side storage</span></span>](#server-side-storage-server)
* [<span data-ttu-id="736ee-214">URL</span><span class="sxs-lookup"><span data-stu-id="736ee-214">URL</span></span>](#url-server)
* [<span data-ttu-id="736ee-215">Almacenamiento del explorador</span><span class="sxs-lookup"><span data-stu-id="736ee-215">Browser storage</span></span>](#browser-storage-server)
* [<span data-ttu-id="736ee-216">Servicio de contenedor de estado en memoria</span><span class="sxs-lookup"><span data-stu-id="736ee-216">In-memory state container service</span></span>](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server"><span data-ttu-id="736ee-217">Almacenamiento del lado servidor</span><span class="sxs-lookup"><span data-stu-id="736ee-217">Server-side storage</span></span></h2>

<span data-ttu-id="736ee-218">Para una persistencia de datos permanente que abarque varios usuarios y dispositivos, la aplicación puede usar el almacenamiento del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="736ee-218">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="736ee-219">Las opciones incluyen:</span><span class="sxs-lookup"><span data-stu-id="736ee-219">Options include:</span></span>

* <span data-ttu-id="736ee-220">Blob Storage</span><span class="sxs-lookup"><span data-stu-id="736ee-220">Blob storage</span></span>
* <span data-ttu-id="736ee-221">Almacenamiento de pares clave-valor</span><span class="sxs-lookup"><span data-stu-id="736ee-221">Key-value storage</span></span>
* <span data-ttu-id="736ee-222">Base de datos relacional</span><span class="sxs-lookup"><span data-stu-id="736ee-222">Relational database</span></span>
* <span data-ttu-id="736ee-223">Almacenamiento de tablas</span><span class="sxs-lookup"><span data-stu-id="736ee-223">Table storage</span></span>

<span data-ttu-id="736ee-224">Una vez guardados los datos, el estado del usuario se conserva y está disponible en cualquier circuito nuevo.</span><span class="sxs-lookup"><span data-stu-id="736ee-224">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="736ee-225">Para obtener más información sobre las opciones de almacenamiento de datos de Azure, consulte lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="736ee-225">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="736ee-226">Bases de datos de Azure</span><span class="sxs-lookup"><span data-stu-id="736ee-226">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="736ee-227">Documentación de Azure Storage</span><span class="sxs-lookup"><span data-stu-id="736ee-227">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-server"><span data-ttu-id="736ee-228">Resolución</span><span class="sxs-lookup"><span data-stu-id="736ee-228">URL</span></span></h2>

<span data-ttu-id="736ee-229">Para los datos transitorios que representan el estado de navegación, modele los datos como parte de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="736ee-229">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="736ee-230">Entre los ejemplos de estado de usuario modelado en la dirección URL se incluyen:</span><span class="sxs-lookup"><span data-stu-id="736ee-230">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="736ee-231">El identificador de una entidad visualizada.</span><span class="sxs-lookup"><span data-stu-id="736ee-231">The ID of a viewed entity.</span></span>
* <span data-ttu-id="736ee-232">El número de la página actual en una cuadrícula paginada.</span><span class="sxs-lookup"><span data-stu-id="736ee-232">The current page number in a paged grid.</span></span>

<span data-ttu-id="736ee-233">Se conserva el contenido de la barra de direcciones del explorador:</span><span class="sxs-lookup"><span data-stu-id="736ee-233">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="736ee-234">Si el usuario recarga manualmente la página.</span><span class="sxs-lookup"><span data-stu-id="736ee-234">If the user manually reloads the page.</span></span>
* <span data-ttu-id="736ee-235">Si el servidor web deja de estar disponible y el usuario se ve obligado a volver a cargar la página para conectarse a un servidor diferente.</span><span class="sxs-lookup"><span data-stu-id="736ee-235">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="736ee-236">Para obtener información sobre cómo definir patrones de direcciones URL con la directiva [`@page`](xref:mvc/views/razor#page), vea <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="736ee-236">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-server"><span data-ttu-id="736ee-237">Almacenamiento del explorador</span><span class="sxs-lookup"><span data-stu-id="736ee-237">Browser storage</span></span></h2>

<span data-ttu-id="736ee-238">En el caso de los datos transitorios que el usuario crea activamente, una ubicación de almacenamiento que se suele usar son las colecciones [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) del explorador:</span><span class="sxs-lookup"><span data-stu-id="736ee-238">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="736ee-239">`localStorage` está en el ámbito de la ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-239">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="736ee-240">Si el usuario recarga la página o la cierra y vuelve a abrir el explorador, el estado se conserva.</span><span class="sxs-lookup"><span data-stu-id="736ee-240">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="736ee-241">Si el usuario abre varias pestañas del explorador, el estado se comparte entre las pestañas.</span><span class="sxs-lookup"><span data-stu-id="736ee-241">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="736ee-242">Los datos se conservan en `localStorage` hasta que se borran explícitamente.</span><span class="sxs-lookup"><span data-stu-id="736ee-242">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="736ee-243">`sessionStorage` está en el ámbito de la pestaña del explorador. Si el usuario recarga la pestaña, el estado se conserva.</span><span class="sxs-lookup"><span data-stu-id="736ee-243">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="736ee-244">Si el usuario cierra la pestaña o el explorador, se pierde el estado.</span><span class="sxs-lookup"><span data-stu-id="736ee-244">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="736ee-245">Si el usuario abre varias pestañas del explorador, cada pestaña tendrá su propia versión independiente de los datos.</span><span class="sxs-lookup"><span data-stu-id="736ee-245">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="736ee-246">Por lo general, es más seguro usar `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="736ee-246">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="736ee-247">`sessionStorage` evita el riesgo de que un usuario abra varias pestañas y encuentre lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="736ee-247">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="736ee-248">Errores en el almacenamiento de estado en todas las pestañas.</span><span class="sxs-lookup"><span data-stu-id="736ee-248">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="736ee-249">Comportamiento confuso cuando una pestaña sobrescribe el estado de otras.</span><span class="sxs-lookup"><span data-stu-id="736ee-249">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="736ee-250">`localStorage` es la mejor opción si la aplicación debe conservar el estado en el cierre y volver a abrir el explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-250">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="736ee-251">Advertencias sobre el uso del almacenamiento del explorador:</span><span class="sxs-lookup"><span data-stu-id="736ee-251">Caveats for using browser storage:</span></span>

* <span data-ttu-id="736ee-252">De forma similar al uso de una base de datos del lado servidor, la carga y el almacenamiento de datos son asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="736ee-252">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="736ee-253">A diferencia de las bases de datos del lado servidor, el almacenamiento no está disponible durante la representación previa porque la página solicitada no existe en el explorador durante la fase de representación previa.</span><span class="sxs-lookup"><span data-stu-id="736ee-253">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="736ee-254">Es razonable almacenar unos pocos kilobytes de datos para las aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="736ee-254">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="736ee-255">Más allá de unos pocos kilobytes, debe tener en cuenta las implicaciones de rendimiento porque los datos se cargan y se guardan en la red.</span><span class="sxs-lookup"><span data-stu-id="736ee-255">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="736ee-256">Los usuarios pueden ver o alterar los datos.</span><span class="sxs-lookup"><span data-stu-id="736ee-256">Users may view or tamper with the data.</span></span> <span data-ttu-id="736ee-257">La [protección de datos de ASP.NET Core](xref:security/data-protection/introduction) puede mitigar el riesgo.</span><span class="sxs-lookup"><span data-stu-id="736ee-257">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="736ee-258">Por ejemplo, el [almacenamiento del explorador protegido de ASP.NET Core](#aspnet-core-protected-browser-storage) usa la protección de datos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="736ee-258">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="736ee-259">Los paquetes NuGet de terceros proporcionan API para trabajar con `localStorage` y `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="736ee-259">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="736ee-260">Merece la pena tener en cuenta la elección de un paquete que utiliza de forma transparente la [protección de datos de ASP.NET Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="736ee-260">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="736ee-261">La protección de datos cifra los datos almacenados y reduce el riesgo potencial de alteración de los datos almacenados.</span><span class="sxs-lookup"><span data-stu-id="736ee-261">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="736ee-262">Si los datos serializados con JSON se almacenan como texto sin formato, los usuarios pueden ver los datos mediante las herramientas de desarrollo del explorador y también modificar los datos almacenados.</span><span class="sxs-lookup"><span data-stu-id="736ee-262">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="736ee-263">La protección de datos no siempre es un problema, ya que los datos pueden ser bastante triviales.</span><span class="sxs-lookup"><span data-stu-id="736ee-263">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="736ee-264">Por ejemplo, la lectura o modificación del color almacenado de un elemento de la interfaz de usuario no supone un riesgo de seguridad significativo para el usuario o la organización.</span><span class="sxs-lookup"><span data-stu-id="736ee-264">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="736ee-265">Evite que los usuarios puedan inspeccionar o manipular *datos confidenciales*.</span><span class="sxs-lookup"><span data-stu-id="736ee-265">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="736ee-266">Almacenamiento del explorador protegido de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="736ee-266">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="736ee-267">Por ejemplo, el almacenamiento del explorador protegido de ASP.NET Core usa la [protección de datos de ASP.NET Core](xref:security/data-protection/introduction) para [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span><span class="sxs-lookup"><span data-stu-id="736ee-267">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="736ee-268">El almacenamiento del explorador protegido se basa en la protección de datos de ASP.NET Core y solo se admite para aplicaciones de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="736ee-268">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="736ee-269">Guardar y cargar datos dentro de un componente</span><span class="sxs-lookup"><span data-stu-id="736ee-269">Save and load data within a component</span></span>

<span data-ttu-id="736ee-270">En cualquier componente que requiera cargar o guardar datos en el almacenamiento del explorador, use la directiva [`@inject`](xref:mvc/views/razor#inject) para insertar una instancia de uno de los elementos siguientes:</span><span class="sxs-lookup"><span data-stu-id="736ee-270">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="736ee-271">La elección depende de la ubicación del almacenamiento del explorador que desee usar.</span><span class="sxs-lookup"><span data-stu-id="736ee-271">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="736ee-272">En el ejemplo siguiente, se usa `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="736ee-272">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="736ee-273">La directiva `@using` se puede colocar en el archivo `_Imports.razor` de la aplicación, en lugar de en el componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-273">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="736ee-274">El uso del archivo `_Imports.razor` hace que el espacio de nombres esté disponible para segmentos más grandes de la aplicación o para toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="736ee-274">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="736ee-275">Para conservar el valor de `currentCount` en el componente `Counter` de una aplicación basada en la plantilla del proyecto Blazor Server, modifique el método `IncrementCount` para usar `ProtectedSessionStore.SetAsync`:</span><span class="sxs-lookup"><span data-stu-id="736ee-275">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="736ee-276">En aplicaciones más grandes y realistas, el almacenamiento de campos individuales es un escenario improbable.</span><span class="sxs-lookup"><span data-stu-id="736ee-276">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="736ee-277">Es más probable que las aplicaciones almacenen objetos de modelo completos que incluyen un estado complejo.</span><span class="sxs-lookup"><span data-stu-id="736ee-277">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="736ee-278">`ProtectedSessionStore` serializa y deserializa automáticamente los datos de JSON para almacenar objetos de estado complejos.</span><span class="sxs-lookup"><span data-stu-id="736ee-278">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="736ee-279">En el ejemplo de código anterior, los datos de `currentCount` se almacenan como `sessionStorage['count']` en el explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="736ee-279">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="736ee-280">Los datos no se almacenan como texto sin formato, sino que se protegen mediante la protección de datos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="736ee-280">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="736ee-281">Los datos cifrados pueden inspeccionarse si se evalúa `sessionStorage['count']` en la consola del desarrollador del explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-281">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="736ee-282">Para recuperar los datos de `currentCount` si el usuario vuelve al componente `Counter` más adelante, incluso si está en un circuito nuevo, use `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="736ee-282">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="736ee-283">Si los parámetros del componente incluyen el estado de navegación, llame a `ProtectedSessionStore.GetAsync` y asigne el resultado no `null` en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="736ee-283">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="736ee-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> solo se llama una vez la primera vez que se crea una instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="736ee-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> no se llama de nuevo más tarde si el usuario navega a una dirección URL diferente mientras permanece en la misma página.</span><span class="sxs-lookup"><span data-stu-id="736ee-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="736ee-286">Para obtener más información, vea <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="736ee-286">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="736ee-287">Los ejemplos de esta sección solo funcionan si el servidor no tiene habilitada la representación previa.</span><span class="sxs-lookup"><span data-stu-id="736ee-287">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="736ee-288">Con la representación previa habilitada, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-288">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="736ee-289">Deshabilite la representación previa o agregue código adicional para trabajar con la representación previa.</span><span class="sxs-lookup"><span data-stu-id="736ee-289">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="736ee-290">Para obtener más información sobre cómo escribir código que funcione con la representación previa, consulte la sección [Controlar la representación previa](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="736ee-290">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="736ee-291">Controlar el estado de carga</span><span class="sxs-lookup"><span data-stu-id="736ee-291">Handle the loading state</span></span>

<span data-ttu-id="736ee-292">Dado que se accede al almacenamiento del explorador de forma asincrónica a través de una conexión de red, siempre hay un período de tiempo antes de que los datos se carguen y estén disponibles para un componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-292">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="736ee-293">Para obtener los mejores resultados, represente un mensaje de estado de carga mientras la carga está en curso, en lugar de mostrar datos en blanco o predeterminados.</span><span class="sxs-lookup"><span data-stu-id="736ee-293">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="736ee-294">Un enfoque consiste en realizar un seguimiento de si los datos son `null`, lo que significa que los datos aún se están cargando.</span><span class="sxs-lookup"><span data-stu-id="736ee-294">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="736ee-295">En el componente `Counter` predeterminado, el recuento se mantiene en `int`.</span><span class="sxs-lookup"><span data-stu-id="736ee-295">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="736ee-296">[Haga que `currentCount` admita valores NULL](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) agregando un signo de interrogación (`?`) al tipo (`int`):</span><span class="sxs-lookup"><span data-stu-id="736ee-296">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="736ee-297">En lugar de mostrar de manera incondicional el recuento y el botón **`Increment`** , muestre estos elementos solo si se cargan los datos comprobando <xref:System.Nullable%601.HasValue%2A>:</span><span class="sxs-lookup"><span data-stu-id="736ee-297">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="736ee-298">Controlar la representación previa</span><span class="sxs-lookup"><span data-stu-id="736ee-298">Handle prerendering</span></span>

<span data-ttu-id="736ee-299">Durante la representación previa:</span><span class="sxs-lookup"><span data-stu-id="736ee-299">During prerendering:</span></span>

* <span data-ttu-id="736ee-300">No existe ninguna conexión interactiva con el explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="736ee-300">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="736ee-301">El explorador todavía no tiene ninguna página en la que se pueda ejecutar código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="736ee-301">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="736ee-302">`localStorage` o `sessionStorage` no están disponibles durante la representación previa.</span><span class="sxs-lookup"><span data-stu-id="736ee-302">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="736ee-303">Si el componente intenta interactuar con el almacenamiento, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-303">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="736ee-304">Una manera de resolver el error es deshabilitar la representación previa.</span><span class="sxs-lookup"><span data-stu-id="736ee-304">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="736ee-305">Esta suele ser la mejor opción si la aplicación hace un uso intensivo del almacenamiento basado en explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-305">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="736ee-306">La representación previa agrega complejidad y no beneficia a la aplicación porque esta no puede representar previamente ningún contenido útil hasta que `localStorage` o `sessionStorage` estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="736ee-306">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="736ee-307">Para deshabilitar la representación previa, abra el archivo `Pages/_Host.cshtml` y cambie el atributo `render-mode` del [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) a <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span><span class="sxs-lookup"><span data-stu-id="736ee-307">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="736ee-308">La representación previa puede resultar útil para otras páginas que no utilizan `localStorage` o `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="736ee-308">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="736ee-309">Para mantener la representación previa, postergue la operación de carga hasta que el explorador esté conectado al circuito.</span><span class="sxs-lookup"><span data-stu-id="736ee-309">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="736ee-310">El siguiente es un ejemplo para almacenar un valor de contador:</span><span class="sxs-lookup"><span data-stu-id="736ee-310">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="736ee-311">Factorizar la preservación de estado en una ubicación común</span><span class="sxs-lookup"><span data-stu-id="736ee-311">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="736ee-312">Si muchos componentes se basan en el almacenamiento basado en explorador, al volver a implementar el código del proveedor de estado muchas veces se crea la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="736ee-312">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="736ee-313">Una opción para evitar la duplicación de código es crear un *componente primario del proveedor de estado* que encapsula la lógica del proveedor de estado.</span><span class="sxs-lookup"><span data-stu-id="736ee-313">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="736ee-314">Los componentes secundarios pueden trabajar con datos persistentes sin tener en cuenta el mecanismo de persistencia del estado.</span><span class="sxs-lookup"><span data-stu-id="736ee-314">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="736ee-315">En el siguiente ejemplo de un componente `CounterStateProvider`, se conservan los datos del contador en `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="736ee-315">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="736ee-316">El componente `CounterStateProvider` controla la fase de carga, ya que no representa su contenido secundario hasta que se completa la carga.</span><span class="sxs-lookup"><span data-stu-id="736ee-316">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="736ee-317">Para usar el componente `CounterStateProvider`, encapsule una instancia del componente en torno a cualquier otro componente que requiera acceso al estado del contador.</span><span class="sxs-lookup"><span data-stu-id="736ee-317">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="736ee-318">Para que el estado sea accesible a todos los componentes de una aplicación, encapsule el componente `CounterStateProvider` alrededor de <xref:Microsoft.AspNetCore.Components.Routing.Router> en el componente `App` (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="736ee-318">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="736ee-319">Los componentes encapsulados reciben y pueden modificar el estado persistente del contador.</span><span class="sxs-lookup"><span data-stu-id="736ee-319">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="736ee-320">El siguiente componente `Counter` implementa el patrón:</span><span class="sxs-lookup"><span data-stu-id="736ee-320">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="736ee-321">No es necesario que el componente anterior interactúe con `ProtectedBrowserStorage`, ni se trata de una fase de carga.</span><span class="sxs-lookup"><span data-stu-id="736ee-321">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="736ee-322">Para tratar la representación previa tal y como se ha descrito anteriormente, `CounterStateProvider` se puede modificar para que todos los componentes que consumen los datos del contador funcionen automáticamente con la representación previa.</span><span class="sxs-lookup"><span data-stu-id="736ee-322">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="736ee-323">Para obtener más información, vea la sección [Controlar la representación previa](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="736ee-323">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="736ee-324">En general, se recomienda el patrón *componente primario del proveedor de estado*:</span><span class="sxs-lookup"><span data-stu-id="736ee-324">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="736ee-325">Para consumir el estado en muchos componentes.</span><span class="sxs-lookup"><span data-stu-id="736ee-325">To consume state across many components.</span></span>
* <span data-ttu-id="736ee-326">Si solo hay un objeto de estado de nivel superior para conservar.</span><span class="sxs-lookup"><span data-stu-id="736ee-326">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="736ee-327">Para conservar muchos objetos de estado diferentes y consumir distintos subconjuntos de objetos en distintos lugares, es mejor evitar la conservación global del estado.</span><span class="sxs-lookup"><span data-stu-id="736ee-327">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="736ee-328">Paquete de NuGet experimental del almacenamiento de explorador protegido</span><span class="sxs-lookup"><span data-stu-id="736ee-328">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="736ee-329">Por ejemplo, el almacenamiento del explorador protegido de ASP.NET Core usa la [protección de datos de ASP.NET Core](xref:security/data-protection/introduction) para [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span><span class="sxs-lookup"><span data-stu-id="736ee-329">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="736ee-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` es un paquete experimental no compatible que no es adecuado para su uso en producción.</span><span class="sxs-lookup"><span data-stu-id="736ee-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="736ee-331">El paquete solo está disponible para su uso en aplicaciones Blazor Server de ASP.NET Core 3.1.</span><span class="sxs-lookup"><span data-stu-id="736ee-331">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="736ee-332">Configuración</span><span class="sxs-lookup"><span data-stu-id="736ee-332">Configuration</span></span>

1. <span data-ttu-id="736ee-333">Agregue una referencia de paquete a [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="736ee-333">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="736ee-334">En el archivo `Pages/_Host.cshtml`, agregue el siguiente script dentro de la etiqueta de cierre `</body>`:</span><span class="sxs-lookup"><span data-stu-id="736ee-334">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="736ee-335">En `Startup.ConfigureServices`, llame a `AddProtectedBrowserStorage` para agregar los servicios `localStorage` y `sessionStorage` a la colección de servicios:</span><span class="sxs-lookup"><span data-stu-id="736ee-335">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="736ee-336">Guardar y cargar datos dentro de un componente</span><span class="sxs-lookup"><span data-stu-id="736ee-336">Save and load data within a component</span></span>

<span data-ttu-id="736ee-337">En cualquier componente que requiera cargar o guardar datos en el almacenamiento del explorador, use la directiva [`@inject`](xref:mvc/views/razor#inject) para insertar una instancia de uno de los elementos siguientes:</span><span class="sxs-lookup"><span data-stu-id="736ee-337">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="736ee-338">La elección depende de la ubicación del almacenamiento del explorador que desee usar.</span><span class="sxs-lookup"><span data-stu-id="736ee-338">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="736ee-339">En el ejemplo siguiente, se usa `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="736ee-339">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="736ee-340">La instrucción `@using` se puede colocar en un archivo `_Imports.razor`, en lugar de en el componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-340">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="736ee-341">El uso del archivo `_Imports.razor` hace que el espacio de nombres esté disponible para segmentos más grandes de la aplicación o para toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="736ee-341">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="736ee-342">Para conservar el valor de `currentCount` en el componente `Counter` de una aplicación basada en la plantilla del proyecto Blazor Server, modifique el método `IncrementCount` para usar `ProtectedSessionStore.SetAsync`:</span><span class="sxs-lookup"><span data-stu-id="736ee-342">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="736ee-343">En aplicaciones más grandes y realistas, el almacenamiento de campos individuales es un escenario improbable.</span><span class="sxs-lookup"><span data-stu-id="736ee-343">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="736ee-344">Es más probable que las aplicaciones almacenen objetos de modelo completos que incluyen un estado complejo.</span><span class="sxs-lookup"><span data-stu-id="736ee-344">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="736ee-345">`ProtectedSessionStore` serializa y deserializa automáticamente los datos de JSON.</span><span class="sxs-lookup"><span data-stu-id="736ee-345">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="736ee-346">En el ejemplo de código anterior, los datos de `currentCount` se almacenan como `sessionStorage['count']` en el explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="736ee-346">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="736ee-347">Los datos no se almacenan como texto sin formato, sino que se protegen mediante la protección de datos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="736ee-347">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="736ee-348">Los datos cifrados pueden inspeccionarse si se evalúa `sessionStorage['count']` en la consola del desarrollador del explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-348">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="736ee-349">Para recuperar los datos de `currentCount` si el usuario vuelve al componente `Counter` más adelante, incluso si está en un circuito totalmente nuevo, use `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="736ee-349">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="736ee-350">Si los parámetros del componente incluyen el estado de navegación, llame a `ProtectedSessionStore.GetAsync` y asigne el resultado en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="736ee-350">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="736ee-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> solo se llama una vez la primera vez que se crea una instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="736ee-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> no se llama de nuevo más tarde si el usuario navega a una dirección URL diferente mientras permanece en la misma página.</span><span class="sxs-lookup"><span data-stu-id="736ee-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="736ee-353">Para obtener más información, vea <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="736ee-353">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="736ee-354">Los ejemplos de esta sección solo funcionan si el servidor no tiene habilitada la representación previa.</span><span class="sxs-lookup"><span data-stu-id="736ee-354">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="736ee-355">Con la representación previa habilitada, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-355">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="736ee-356">Deshabilite la representación previa o agregue código adicional para trabajar con la representación previa.</span><span class="sxs-lookup"><span data-stu-id="736ee-356">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="736ee-357">Para obtener más información sobre cómo escribir código que funcione con la representación previa, consulte la sección [Controlar la representación previa](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="736ee-357">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="736ee-358">Controlar el estado de carga</span><span class="sxs-lookup"><span data-stu-id="736ee-358">Handle the loading state</span></span>

<span data-ttu-id="736ee-359">Dado que se accede al almacenamiento del explorador de forma asincrónica a través de una conexión de red, siempre hay un período de tiempo antes de que los datos se carguen y estén disponibles para un componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-359">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="736ee-360">Para obtener los mejores resultados, represente un mensaje de estado de carga mientras la carga está en curso, en lugar de mostrar datos en blanco o predeterminados.</span><span class="sxs-lookup"><span data-stu-id="736ee-360">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="736ee-361">Un enfoque consiste en realizar un seguimiento de si los datos son `null`, lo que significa que los datos aún se están cargando.</span><span class="sxs-lookup"><span data-stu-id="736ee-361">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="736ee-362">En el componente `Counter` predeterminado, el recuento se mantiene en `int`.</span><span class="sxs-lookup"><span data-stu-id="736ee-362">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="736ee-363">[Haga que `currentCount` admita valores NULL](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) agregando un signo de interrogación (`?`) al tipo (`int`):</span><span class="sxs-lookup"><span data-stu-id="736ee-363">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="736ee-364">En lugar de mostrar de manera incondicional el recuento y el botón **`Increment`** , elija que estos elementos se muestren solo si se cargan los datos:</span><span class="sxs-lookup"><span data-stu-id="736ee-364">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="736ee-365">Controlar la representación previa</span><span class="sxs-lookup"><span data-stu-id="736ee-365">Handle prerendering</span></span>

<span data-ttu-id="736ee-366">Durante la representación previa:</span><span class="sxs-lookup"><span data-stu-id="736ee-366">During prerendering:</span></span>

* <span data-ttu-id="736ee-367">No existe ninguna conexión interactiva con el explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="736ee-367">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="736ee-368">El explorador todavía no tiene ninguna página en la que se pueda ejecutar código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="736ee-368">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="736ee-369">`localStorage` o `sessionStorage` no están disponibles durante la representación previa.</span><span class="sxs-lookup"><span data-stu-id="736ee-369">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="736ee-370">Si el componente intenta interactuar con el almacenamiento, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.</span><span class="sxs-lookup"><span data-stu-id="736ee-370">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="736ee-371">Una manera de resolver el error es deshabilitar la representación previa.</span><span class="sxs-lookup"><span data-stu-id="736ee-371">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="736ee-372">Esta suele ser la mejor opción si la aplicación hace un uso intensivo del almacenamiento basado en explorador.</span><span class="sxs-lookup"><span data-stu-id="736ee-372">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="736ee-373">La representación previa agrega complejidad y no beneficia a la aplicación porque esta no puede representar previamente ningún contenido útil hasta que `localStorage` o `sessionStorage` estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="736ee-373">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="736ee-374">Para deshabilitar la representación previa, abra el archivo `Pages/_Host.cshtml` y cambie el atributo `render-mode` del [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) a <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span><span class="sxs-lookup"><span data-stu-id="736ee-374">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="736ee-375">La representación previa puede resultar útil para otras páginas que no utilizan `localStorage` o `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="736ee-375">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="736ee-376">Para mantener la representación previa, postergue la operación de carga hasta que el explorador esté conectado al circuito.</span><span class="sxs-lookup"><span data-stu-id="736ee-376">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="736ee-377">El siguiente es un ejemplo para almacenar un valor de contador:</span><span class="sxs-lookup"><span data-stu-id="736ee-377">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="736ee-378">Factorizar la preservación de estado en una ubicación común</span><span class="sxs-lookup"><span data-stu-id="736ee-378">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="736ee-379">Si muchos componentes se basan en el almacenamiento basado en explorador, al volver a implementar el código del proveedor de estado muchas veces se crea la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="736ee-379">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="736ee-380">Una opción para evitar la duplicación de código es crear un *componente primario del proveedor de estado* que encapsula la lógica del proveedor de estado.</span><span class="sxs-lookup"><span data-stu-id="736ee-380">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="736ee-381">Los componentes secundarios pueden trabajar con datos persistentes sin tener en cuenta el mecanismo de persistencia del estado.</span><span class="sxs-lookup"><span data-stu-id="736ee-381">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="736ee-382">En el siguiente ejemplo de un componente `CounterStateProvider`, se conservan los datos del contador en `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="736ee-382">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="736ee-383">El componente `CounterStateProvider` controla la fase de carga, ya que no representa su contenido secundario hasta que se completa la carga.</span><span class="sxs-lookup"><span data-stu-id="736ee-383">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="736ee-384">Para usar el componente `CounterStateProvider`, encapsule una instancia del componente en torno a cualquier otro componente que requiera acceso al estado del contador.</span><span class="sxs-lookup"><span data-stu-id="736ee-384">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="736ee-385">Para que el estado sea accesible a todos los componentes de una aplicación, encapsule el componente `CounterStateProvider` alrededor de <xref:Microsoft.AspNetCore.Components.Routing.Router> en el componente `App` (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="736ee-385">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="736ee-386">Los componentes encapsulados reciben y pueden modificar el estado persistente del contador.</span><span class="sxs-lookup"><span data-stu-id="736ee-386">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="736ee-387">El siguiente componente `Counter` implementa el patrón:</span><span class="sxs-lookup"><span data-stu-id="736ee-387">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="736ee-388">No es necesario que el componente anterior interactúe con `ProtectedBrowserStorage`, ni se trata de una fase de carga.</span><span class="sxs-lookup"><span data-stu-id="736ee-388">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="736ee-389">Para tratar la representación previa tal y como se ha descrito anteriormente, `CounterStateProvider` se puede modificar para que todos los componentes que consumen los datos del contador funcionen automáticamente con la representación previa.</span><span class="sxs-lookup"><span data-stu-id="736ee-389">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="736ee-390">Para obtener más información, vea la sección [Controlar la representación previa](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="736ee-390">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="736ee-391">En general, se recomienda el patrón *componente primario del proveedor de estado*:</span><span class="sxs-lookup"><span data-stu-id="736ee-391">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="736ee-392">Para consumir el estado en muchos componentes.</span><span class="sxs-lookup"><span data-stu-id="736ee-392">To consume state across many components.</span></span>
* <span data-ttu-id="736ee-393">Si solo hay un objeto de estado de nivel superior para conservar.</span><span class="sxs-lookup"><span data-stu-id="736ee-393">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="736ee-394">Para conservar muchos objetos de estado diferentes y consumir distintos subconjuntos de objetos en distintos lugares, es mejor evitar la conservación global del estado.</span><span class="sxs-lookup"><span data-stu-id="736ee-394">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

<h2 id="in-memory-state-container-service-server"><span data-ttu-id="736ee-395">Servicio de contenedor de estado en memoria</span><span class="sxs-lookup"><span data-stu-id="736ee-395">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

::: zone-end
