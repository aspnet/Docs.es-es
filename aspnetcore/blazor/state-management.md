---
title: Administración de estado de Blazor en ASP.NET Core
author: guardrex
description: Aprenda a conservar el estado en las aplicaciones Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 28ca3b5c4472dc21e709d01705dc64168107ca61
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013559"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="a2dab-103">Administración de estado de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2dab-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="a2dab-104">De [Steve Sanderson](https://github.com/SteveSandersonMS) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a2dab-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="a2dab-105">El estado de usuario creado en una aplicación Blazor WebAssembly se guarda en la memoria del explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="a2dab-106">Entre los ejemplos de estado de usuario almacenados en la memoria del explorador se incluyen:</span><span class="sxs-lookup"><span data-stu-id="a2dab-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="a2dab-107">La jerarquía de instancias del componente y su salida de representación más reciente en la interfaz de usuario representada.</span><span class="sxs-lookup"><span data-stu-id="a2dab-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="a2dab-108">Los valores de los campos y las propiedades de las instancias del componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="a2dab-109">Los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a2dab-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="a2dab-110">Los valores establecidos mediante llamadas de [interoperabilidad de JavaScript](xref:blazor/call-javascript-from-dotnet).</span><span class="sxs-lookup"><span data-stu-id="a2dab-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="a2dab-111">Cuando un usuario cierra y vuelve a abrir el explorador, o vuelve a cargar la página, se pierde el estado de usuario guardado en la memoria del explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="a2dab-112">Conservación del estado en todas las sesiones del explorador</span><span class="sxs-lookup"><span data-stu-id="a2dab-112">Persist state across browser sessions</span></span>

<span data-ttu-id="a2dab-113">En general, se mantiene el estado en las sesiones del explorador en las que los usuarios crean datos de forma activa, no simplemente leyendo los datos que ya existen.</span><span class="sxs-lookup"><span data-stu-id="a2dab-113">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="a2dab-114">Para conservar el estado en todas las sesiones del explorador, la aplicación debe conservar los datos en otra ubicación de almacenamiento distinta a la memoria del explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-114">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="a2dab-115">La persistencia del estado no es automática.</span><span class="sxs-lookup"><span data-stu-id="a2dab-115">State persistence isn't automatic.</span></span> <span data-ttu-id="a2dab-116">Debe seguir los pasos al desarrollar la aplicación para implementar la persistencia de datos con estado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-116">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="a2dab-117">La persistencia de los datos normalmente solo es necesaria para el estado de alto valor que los usuarios crearon.</span><span class="sxs-lookup"><span data-stu-id="a2dab-117">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="a2dab-118">En los ejemplos siguientes, el estado persistente ahorra tiempo o ayuda en las actividades comerciales:</span><span class="sxs-lookup"><span data-stu-id="a2dab-118">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="a2dab-119">Formularios web de varios pasos: en un formulario web que consta de varios pasos, en caso de que el estado se pierda, los usuarios tienen que dedicar mucho tiempo a volver a escribir los datos que ya han proporcionado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-119">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="a2dab-120">Un usuario pierde el estado en este escenario si se sale del formulario y vuelve más adelante.</span><span class="sxs-lookup"><span data-stu-id="a2dab-120">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="a2dab-121">Carros de la compra: se puede mantener cualquier componente comercial importante de una aplicación que represente ingresos potenciales.</span><span class="sxs-lookup"><span data-stu-id="a2dab-121">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="a2dab-122">Un usuario que pierde su estado y, por tanto, su carro de la compra, puede comprar menos productos o servicios cuando vuelva al sitio más adelante.</span><span class="sxs-lookup"><span data-stu-id="a2dab-122">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="a2dab-123">Una aplicación solo puede conservar el *estado de la aplicación*.</span><span class="sxs-lookup"><span data-stu-id="a2dab-123">An app can only persist *app state*.</span></span> <span data-ttu-id="a2dab-124">Las interfaces de usuario no se pueden conservar, como instancias de componentes y sus árboles de representación.</span><span class="sxs-lookup"><span data-stu-id="a2dab-124">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="a2dab-125">Los componentes y los árboles de representación no suelen ser serializables.</span><span class="sxs-lookup"><span data-stu-id="a2dab-125">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="a2dab-126">Para conservar el estado de la interfaz de usuario, como los nodos expandidos de un control de vista de árbol, la aplicación debe tener código personalizado para modelar el comportamiento del estado de la interfaz de usuario como estado de la aplicación serializable.</span><span class="sxs-lookup"><span data-stu-id="a2dab-126">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="a2dab-127">Dónde conservar el estado</span><span class="sxs-lookup"><span data-stu-id="a2dab-127">Where to persist state</span></span>

<span data-ttu-id="a2dab-128">Existen tres ubicaciones comunes para el estado persistente:</span><span class="sxs-lookup"><span data-stu-id="a2dab-128">Three common locations exist for persisting state:</span></span>

* [<span data-ttu-id="a2dab-129">Almacenamiento del lado servidor</span><span class="sxs-lookup"><span data-stu-id="a2dab-129">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="a2dab-130">URL</span><span class="sxs-lookup"><span data-stu-id="a2dab-130">URL</span></span>](#url)
* [<span data-ttu-id="a2dab-131">Almacenamiento del explorador</span><span class="sxs-lookup"><span data-stu-id="a2dab-131">Browser storage</span></span>](#browser-storage)

### <a name="server-side-storage"></a><span data-ttu-id="a2dab-132">Almacenamiento del lado servidor</span><span class="sxs-lookup"><span data-stu-id="a2dab-132">Server-side storage</span></span>

<span data-ttu-id="a2dab-133">Para una persistencia de datos permanente que abarque varios usuarios y dispositivos, la aplicación puede usar el almacenamiento del lado servidor independiente al que se accede a través de una API web.</span><span class="sxs-lookup"><span data-stu-id="a2dab-133">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="a2dab-134">Las opciones incluyen:</span><span class="sxs-lookup"><span data-stu-id="a2dab-134">Options include:</span></span>

* <span data-ttu-id="a2dab-135">Blob Storage</span><span class="sxs-lookup"><span data-stu-id="a2dab-135">Blob storage</span></span>
* <span data-ttu-id="a2dab-136">Almacenamiento de pares clave-valor</span><span class="sxs-lookup"><span data-stu-id="a2dab-136">Key-value storage</span></span>
* <span data-ttu-id="a2dab-137">Base de datos relacional</span><span class="sxs-lookup"><span data-stu-id="a2dab-137">Relational database</span></span>
* <span data-ttu-id="a2dab-138">Almacenamiento de tablas</span><span class="sxs-lookup"><span data-stu-id="a2dab-138">Table storage</span></span>

<span data-ttu-id="a2dab-139">Una vez guardados los datos, el estado del usuario se conserva y está disponible en cualquier nueva sesión del explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-139">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="a2dab-140">Dado que las aplicaciones Blazor WebAssembly se ejecutan completamente en el explorador del usuario, requieren medidas adicionales para acceder a sistemas externos seguros, como bases de datos y servicios de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="a2dab-140">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="a2dab-141">Las aplicaciones de Blazor WebAssembly se protegen de la misma manera que las aplicaciones de página única (SPA).</span><span class="sxs-lookup"><span data-stu-id="a2dab-141">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="a2dab-142">Normalmente, una aplicación autentica a un usuario a través de [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) y, a continuación, interactúa con los servicios de almacenamiento y las bases de datos a través de llamadas de API web a una aplicación del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="a2dab-142">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="a2dab-143">La aplicación del lado servidor media en la transferencia de datos entre la aplicación Blazor WebAssembly y la base de datos o el servicio de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="a2dab-143">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="a2dab-144">La aplicación Blazor WebAssembly mantiene una conexión efímera con la aplicación del lado servidor, mientras que la aplicación del lado servidor tiene una conexión persistente con el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="a2dab-144">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="a2dab-145">Para obtener más información, consulte los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="a2dab-145">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="a2dab-146">Artículos sobre *seguridad e Identity* de Blazor</span><span class="sxs-lookup"><span data-stu-id="a2dab-146">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="a2dab-147">Para obtener más información sobre las opciones de almacenamiento de datos de Azure, consulte lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a2dab-147">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="a2dab-148">Bases de datos de Azure</span><span class="sxs-lookup"><span data-stu-id="a2dab-148">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="a2dab-149">Documentación de Azure Storage</span><span class="sxs-lookup"><span data-stu-id="a2dab-149">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="a2dab-150">Resolución</span><span class="sxs-lookup"><span data-stu-id="a2dab-150">URL</span></span>

<span data-ttu-id="a2dab-151">Para los datos transitorios que representan el estado de navegación, modele los datos como parte de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="a2dab-151">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="a2dab-152">Entre los ejemplos de estado de usuario modelado en la dirección URL se incluyen:</span><span class="sxs-lookup"><span data-stu-id="a2dab-152">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="a2dab-153">El identificador de una entidad visualizada.</span><span class="sxs-lookup"><span data-stu-id="a2dab-153">The ID of a viewed entity.</span></span>
* <span data-ttu-id="a2dab-154">El número de la página actual en una cuadrícula paginada.</span><span class="sxs-lookup"><span data-stu-id="a2dab-154">The current page number in a paged grid.</span></span>

<span data-ttu-id="a2dab-155">El contenido de la barra de direcciones del explorador se conserva si el usuario vuelve a cargar la página manualmente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-155">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="a2dab-156">Para obtener información sobre cómo definir patrones de direcciones URL con la directiva [`@page`](xref:mvc/views/razor#page), vea <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="a2dab-156">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="a2dab-157">Almacenamiento del explorador</span><span class="sxs-lookup"><span data-stu-id="a2dab-157">Browser storage</span></span>

<span data-ttu-id="a2dab-158">En el caso de los datos transitorios que el usuario crea activamente, una ubicación de almacenamiento que se suele usar son las colecciones [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) del explorador:</span><span class="sxs-lookup"><span data-stu-id="a2dab-158">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="a2dab-159">`localStorage` está en el ámbito de la ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-159">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="a2dab-160">Si el usuario recarga la página o la cierra y vuelve a abrir el explorador, el estado se conserva.</span><span class="sxs-lookup"><span data-stu-id="a2dab-160">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="a2dab-161">Si el usuario abre varias pestañas del explorador, el estado se comparte entre las pestañas.</span><span class="sxs-lookup"><span data-stu-id="a2dab-161">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="a2dab-162">Los datos se conservan en `localStorage` hasta que se borran explícitamente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-162">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="a2dab-163">`sessionStorage` está en el ámbito de la pestaña del explorador. Si el usuario recarga la pestaña, el estado se conserva.</span><span class="sxs-lookup"><span data-stu-id="a2dab-163">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="a2dab-164">Si el usuario cierra la pestaña o el explorador, se pierde el estado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-164">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="a2dab-165">Si el usuario abre varias pestañas del explorador, cada pestaña tendrá su propia versión independiente de los datos.</span><span class="sxs-lookup"><span data-stu-id="a2dab-165">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="a2dab-166">`localStorage` y `sessionStorage` se pueden usar en aplicaciones Blazor WebAssembly, pero solo escribiendo código personalizado o usando un paquete de terceros.</span><span class="sxs-lookup"><span data-stu-id="a2dab-166">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="a2dab-167">Por lo general, es más seguro usar `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="a2dab-167">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="a2dab-168">`sessionStorage` evita el riesgo de que un usuario abra varias pestañas y encuentre lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a2dab-168">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="a2dab-169">Errores en el almacenamiento de estado en todas las pestañas.</span><span class="sxs-lookup"><span data-stu-id="a2dab-169">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="a2dab-170">Comportamiento confuso cuando una pestaña sobrescribe el estado de otras.</span><span class="sxs-lookup"><span data-stu-id="a2dab-170">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="a2dab-171">`localStorage` es la mejor opción si la aplicación debe conservar el estado en el cierre y volver a abrir el explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-171">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="a2dab-172">Los usuarios pueden ver o alterar los datos almacenados en `localStorage` y `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="a2dab-172">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a2dab-173">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a2dab-173">Additional resources</span></span>

* [<span data-ttu-id="a2dab-174">Guardar el estado de la aplicación antes de una operación de autenticación</span><span class="sxs-lookup"><span data-stu-id="a2dab-174">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="a2dab-175">El servidor Blazor Server es un marco para aplicaciones con estado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-175">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="a2dab-176">La mayoría de las veces, la aplicación mantiene una conexión con el servidor.</span><span class="sxs-lookup"><span data-stu-id="a2dab-176">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="a2dab-177">El estado del usuario se mantiene en la memoria del servidor en un *circuito*.</span><span class="sxs-lookup"><span data-stu-id="a2dab-177">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="a2dab-178">Entre los ejemplos de estado de usuario que se mantiene en un circuito se incluyen:</span><span class="sxs-lookup"><span data-stu-id="a2dab-178">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="a2dab-179">La jerarquía de instancias del componente y su salida de representación más reciente en la interfaz de usuario representada.</span><span class="sxs-lookup"><span data-stu-id="a2dab-179">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="a2dab-180">Los valores de los campos y las propiedades de las instancias del componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-180">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="a2dab-181">Los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) que se encuentran en el ámbito del circuito.</span><span class="sxs-lookup"><span data-stu-id="a2dab-181">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="a2dab-182">También se puede encontrar el estado de usuario en las variables de JavaScript en el conjunto de la memoria del explorador mediante llamadas de [interoperabilidad de JavaScript](xref:blazor/call-javascript-from-dotnet).</span><span class="sxs-lookup"><span data-stu-id="a2dab-182">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="a2dab-183">Si un usuario experimenta una pérdida de conexión de red temporal, Blazor intenta volver a conectar al usuario a su circuito original con su estado original.</span><span class="sxs-lookup"><span data-stu-id="a2dab-183">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="a2dab-184">Sin embargo, no siempre es posible volver a conectar a un usuario con su circuito original en la memoria del servidor:</span><span class="sxs-lookup"><span data-stu-id="a2dab-184">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="a2dab-185">El servidor no puede mantener un circuito desconectado para siempre.</span><span class="sxs-lookup"><span data-stu-id="a2dab-185">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="a2dab-186">El servidor debe liberar un circuito desconectado después de un tiempo de espera o cuando el servidor esté bajo presión de memoria.</span><span class="sxs-lookup"><span data-stu-id="a2dab-186">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="a2dab-187">En entornos de implementación de carga equilibrada y multiservidor, se puede producir un error en los servidores individuales o estos se pueden quitar automáticamente cuando ya no sean necesarios para controlar el volumen total de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="a2dab-187">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="a2dab-188">Las solicitudes de procesamiento de servidor originales de un usuario pueden dejar de estar disponibles cuando el usuario intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="a2dab-188">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="a2dab-189">El usuario podría cerrar y volver a abrir el explorador o recargar la página, lo que elimina cualquier estado que se mantenga en la memoria del explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-189">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="a2dab-190">Por ejemplo, los valores de las variables de JavaScript establecidos mediante llamadas de interoperabilidad de JavaScript se pierden.</span><span class="sxs-lookup"><span data-stu-id="a2dab-190">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="a2dab-191">Cuando un usuario no se puede volver a conectar a su circuito original, el usuario recibe un circuito nuevo con un estado vacío.</span><span class="sxs-lookup"><span data-stu-id="a2dab-191">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="a2dab-192">Esto es equivalente a cerrar y volver a abrir una aplicación de escritorio.</span><span class="sxs-lookup"><span data-stu-id="a2dab-192">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="a2dab-193">Conservación del estado entre circuitos</span><span class="sxs-lookup"><span data-stu-id="a2dab-193">Persist state across circuits</span></span>

<span data-ttu-id="a2dab-194">En general, se mantiene el estado en los circuitos en los que los usuarios crean datos de forma activa, no simplemente leyendo los datos que ya existen.</span><span class="sxs-lookup"><span data-stu-id="a2dab-194">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="a2dab-195">Para conservar el estado en los circuitos, la aplicación debe conservar los datos en otra ubicación de almacenamiento distinta a la memoria del servidor.</span><span class="sxs-lookup"><span data-stu-id="a2dab-195">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="a2dab-196">La persistencia del estado no es automática.</span><span class="sxs-lookup"><span data-stu-id="a2dab-196">State persistence isn't automatic.</span></span> <span data-ttu-id="a2dab-197">Debe seguir los pasos al desarrollar la aplicación para implementar la persistencia de datos con estado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-197">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="a2dab-198">La persistencia de los datos normalmente solo es necesaria para el estado de alto valor que los usuarios crearon.</span><span class="sxs-lookup"><span data-stu-id="a2dab-198">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="a2dab-199">En los ejemplos siguientes, el estado persistente ahorra tiempo o ayuda en las actividades comerciales:</span><span class="sxs-lookup"><span data-stu-id="a2dab-199">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="a2dab-200">Formularios web de varios pasos: en un formulario web que consta de varios pasos, en caso de que el estado se pierda, los usuarios tienen que dedicar mucho tiempo a volver a escribir los datos que ya han proporcionado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-200">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="a2dab-201">Un usuario pierde el estado en este escenario si se sale del formulario y vuelve más adelante.</span><span class="sxs-lookup"><span data-stu-id="a2dab-201">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="a2dab-202">Carros de la compra: se puede mantener cualquier componente comercial importante de una aplicación que represente ingresos potenciales.</span><span class="sxs-lookup"><span data-stu-id="a2dab-202">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="a2dab-203">Un usuario que pierde su estado y, por tanto, su carro de la compra, puede comprar menos productos o servicios cuando vuelva al sitio más adelante.</span><span class="sxs-lookup"><span data-stu-id="a2dab-203">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="a2dab-204">Una aplicación solo puede conservar el *estado de la aplicación*.</span><span class="sxs-lookup"><span data-stu-id="a2dab-204">An app can only persist *app state*.</span></span> <span data-ttu-id="a2dab-205">Las interfaces de usuario no se pueden conservar, como instancias de componentes y sus árboles de representación.</span><span class="sxs-lookup"><span data-stu-id="a2dab-205">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="a2dab-206">Los componentes y los árboles de representación no suelen ser serializables.</span><span class="sxs-lookup"><span data-stu-id="a2dab-206">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="a2dab-207">Para conservar el estado de la interfaz de usuario, como los nodos expandidos de un control de vista de árbol, la aplicación debe tener código personalizado para modelar el comportamiento del estado de la interfaz de usuario como estado de la aplicación serializable.</span><span class="sxs-lookup"><span data-stu-id="a2dab-207">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="a2dab-208">Dónde conservar el estado</span><span class="sxs-lookup"><span data-stu-id="a2dab-208">Where to persist state</span></span>

<span data-ttu-id="a2dab-209">Existen tres ubicaciones comunes para el estado persistente:</span><span class="sxs-lookup"><span data-stu-id="a2dab-209">Three common locations exist for persisting state:</span></span>

* [<span data-ttu-id="a2dab-210">Almacenamiento del lado servidor</span><span class="sxs-lookup"><span data-stu-id="a2dab-210">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="a2dab-211">URL</span><span class="sxs-lookup"><span data-stu-id="a2dab-211">URL</span></span>](#url)
* [<span data-ttu-id="a2dab-212">Almacenamiento del explorador</span><span class="sxs-lookup"><span data-stu-id="a2dab-212">Browser storage</span></span>](#browser-storage)

### <a name="server-side-storage"></a><span data-ttu-id="a2dab-213">Almacenamiento del lado servidor</span><span class="sxs-lookup"><span data-stu-id="a2dab-213">Server-side storage</span></span>

<span data-ttu-id="a2dab-214">Para una persistencia de datos permanente que abarque varios usuarios y dispositivos, la aplicación puede usar el almacenamiento del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="a2dab-214">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="a2dab-215">Las opciones incluyen:</span><span class="sxs-lookup"><span data-stu-id="a2dab-215">Options include:</span></span>

* <span data-ttu-id="a2dab-216">Blob Storage</span><span class="sxs-lookup"><span data-stu-id="a2dab-216">Blob storage</span></span>
* <span data-ttu-id="a2dab-217">Almacenamiento de pares clave-valor</span><span class="sxs-lookup"><span data-stu-id="a2dab-217">Key-value storage</span></span>
* <span data-ttu-id="a2dab-218">Base de datos relacional</span><span class="sxs-lookup"><span data-stu-id="a2dab-218">Relational database</span></span>
* <span data-ttu-id="a2dab-219">Almacenamiento de tablas</span><span class="sxs-lookup"><span data-stu-id="a2dab-219">Table storage</span></span>

<span data-ttu-id="a2dab-220">Una vez guardados los datos, el estado del usuario se conserva y está disponible en cualquier circuito nuevo.</span><span class="sxs-lookup"><span data-stu-id="a2dab-220">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="a2dab-221">Para obtener más información sobre las opciones de almacenamiento de datos de Azure, consulte lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a2dab-221">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="a2dab-222">Bases de datos de Azure</span><span class="sxs-lookup"><span data-stu-id="a2dab-222">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="a2dab-223">Documentación de Azure Storage</span><span class="sxs-lookup"><span data-stu-id="a2dab-223">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="a2dab-224">Resolución</span><span class="sxs-lookup"><span data-stu-id="a2dab-224">URL</span></span>

<span data-ttu-id="a2dab-225">Para los datos transitorios que representan el estado de navegación, modele los datos como parte de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="a2dab-225">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="a2dab-226">Entre los ejemplos de estado de usuario modelado en la dirección URL se incluyen:</span><span class="sxs-lookup"><span data-stu-id="a2dab-226">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="a2dab-227">El identificador de una entidad visualizada.</span><span class="sxs-lookup"><span data-stu-id="a2dab-227">The ID of a viewed entity.</span></span>
* <span data-ttu-id="a2dab-228">El número de la página actual en una cuadrícula paginada.</span><span class="sxs-lookup"><span data-stu-id="a2dab-228">The current page number in a paged grid.</span></span>

<span data-ttu-id="a2dab-229">Se conserva el contenido de la barra de direcciones del explorador:</span><span class="sxs-lookup"><span data-stu-id="a2dab-229">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="a2dab-230">Si el usuario recarga manualmente la página.</span><span class="sxs-lookup"><span data-stu-id="a2dab-230">If the user manually reloads the page.</span></span>
* <span data-ttu-id="a2dab-231">Si el servidor web deja de estar disponible y el usuario se ve obligado a volver a cargar la página para conectarse a un servidor diferente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-231">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="a2dab-232">Para obtener información sobre cómo definir patrones de direcciones URL con la directiva [`@page`](xref:mvc/views/razor#page), vea <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="a2dab-232">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="a2dab-233">Almacenamiento del explorador</span><span class="sxs-lookup"><span data-stu-id="a2dab-233">Browser storage</span></span>

<span data-ttu-id="a2dab-234">En el caso de los datos transitorios que el usuario crea activamente, una ubicación de almacenamiento que se suele usar son las colecciones [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) del explorador:</span><span class="sxs-lookup"><span data-stu-id="a2dab-234">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="a2dab-235">`localStorage` está en el ámbito de la ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-235">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="a2dab-236">Si el usuario recarga la página o la cierra y vuelve a abrir el explorador, el estado se conserva.</span><span class="sxs-lookup"><span data-stu-id="a2dab-236">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="a2dab-237">Si el usuario abre varias pestañas del explorador, el estado se comparte entre las pestañas.</span><span class="sxs-lookup"><span data-stu-id="a2dab-237">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="a2dab-238">Los datos se conservan en `localStorage` hasta que se borran explícitamente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-238">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="a2dab-239">`sessionStorage` está en el ámbito de la pestaña del explorador. Si el usuario recarga la pestaña, el estado se conserva.</span><span class="sxs-lookup"><span data-stu-id="a2dab-239">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="a2dab-240">Si el usuario cierra la pestaña o el explorador, se pierde el estado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-240">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="a2dab-241">Si el usuario abre varias pestañas del explorador, cada pestaña tendrá su propia versión independiente de los datos.</span><span class="sxs-lookup"><span data-stu-id="a2dab-241">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="a2dab-242">Por lo general, es más seguro usar `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="a2dab-242">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="a2dab-243">`sessionStorage` evita el riesgo de que un usuario abra varias pestañas y encuentre lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a2dab-243">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="a2dab-244">Errores en el almacenamiento de estado en todas las pestañas.</span><span class="sxs-lookup"><span data-stu-id="a2dab-244">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="a2dab-245">Comportamiento confuso cuando una pestaña sobrescribe el estado de otras.</span><span class="sxs-lookup"><span data-stu-id="a2dab-245">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="a2dab-246">`localStorage` es la mejor opción si la aplicación debe conservar el estado en el cierre y volver a abrir el explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-246">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="a2dab-247">Advertencias sobre el uso del almacenamiento del explorador:</span><span class="sxs-lookup"><span data-stu-id="a2dab-247">Caveats for using browser storage:</span></span>

* <span data-ttu-id="a2dab-248">De forma similar al uso de una base de datos del lado servidor, la carga y el almacenamiento de datos son asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="a2dab-248">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="a2dab-249">A diferencia de las bases de datos del lado servidor, el almacenamiento no está disponible durante la representación previa porque la página solicitada no existe en el explorador durante la fase de representación previa.</span><span class="sxs-lookup"><span data-stu-id="a2dab-249">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="a2dab-250">Es razonable almacenar unos pocos kilobytes de datos para las aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a2dab-250">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="a2dab-251">Más allá de unos pocos kilobytes, debe tener en cuenta las implicaciones de rendimiento porque los datos se cargan y se guardan en la red.</span><span class="sxs-lookup"><span data-stu-id="a2dab-251">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="a2dab-252">Los usuarios pueden ver o alterar los datos.</span><span class="sxs-lookup"><span data-stu-id="a2dab-252">Users may view or tamper with the data.</span></span> <span data-ttu-id="a2dab-253">La [protección de datos de ASP.NET Core](xref:security/data-protection/introduction) puede mitigar el riesgo.</span><span class="sxs-lookup"><span data-stu-id="a2dab-253">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="a2dab-254">Por ejemplo, el [almacenamiento del explorador protegido de ASP.NET Core](#aspnet-core-protected-browser-storage) usa la protección de datos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2dab-254">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="a2dab-255">Los paquetes NuGet de terceros proporcionan API para trabajar con `localStorage` y `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="a2dab-255">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="a2dab-256">Merece la pena tener en cuenta la elección de un paquete que utiliza de forma transparente la [protección de datos de ASP.NET Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="a2dab-256">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="a2dab-257">La protección de datos cifra los datos almacenados y reduce el riesgo potencial de alteración de los datos almacenados.</span><span class="sxs-lookup"><span data-stu-id="a2dab-257">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="a2dab-258">Si los datos serializados con JSON se almacenan como texto sin formato, los usuarios pueden ver los datos mediante las herramientas de desarrollo del explorador y también modificar los datos almacenados.</span><span class="sxs-lookup"><span data-stu-id="a2dab-258">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="a2dab-259">La protección de datos no siempre es un problema, ya que los datos pueden ser bastante triviales.</span><span class="sxs-lookup"><span data-stu-id="a2dab-259">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="a2dab-260">Por ejemplo, la lectura o modificación del color almacenado de un elemento de la interfaz de usuario no supone un riesgo de seguridad significativo para el usuario o la organización.</span><span class="sxs-lookup"><span data-stu-id="a2dab-260">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="a2dab-261">Evite que los usuarios puedan inspeccionar o manipular *datos confidenciales*.</span><span class="sxs-lookup"><span data-stu-id="a2dab-261">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="a2dab-262">Almacenamiento del explorador protegido de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2dab-262">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="a2dab-263">Por ejemplo, el almacenamiento del explorador protegido de ASP.NET Core usa la [protección de datos de ASP.NET Core](xref:security/data-protection/introduction) para [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span><span class="sxs-lookup"><span data-stu-id="a2dab-263">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="a2dab-264">El almacenamiento del explorador protegido se basa en la protección de datos de ASP.NET Core y solo se admite para aplicaciones de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a2dab-264">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="a2dab-265">Configuración</span><span class="sxs-lookup"><span data-stu-id="a2dab-265">Configuration</span></span>

1. <span data-ttu-id="a2dab-266">Agregue una referencia de paquete a [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions).</span><span class="sxs-lookup"><span data-stu-id="a2dab-266">Add a package reference to [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions).</span></span>
1. <span data-ttu-id="a2dab-267">En `Startup.ConfigureServices`, llame a `AddProtectedBrowserStorage` para agregar los servicios `localStorage` y `sessionStorage` a la colección de servicios:</span><span class="sxs-lookup"><span data-stu-id="a2dab-267">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="a2dab-268">Guardar y cargar datos dentro de un componente</span><span class="sxs-lookup"><span data-stu-id="a2dab-268">Save and load data within a component</span></span>

<span data-ttu-id="a2dab-269">En cualquier componente que requiera cargar o guardar datos en el almacenamiento del explorador, use la directiva [`@inject`](xref:mvc/views/razor#inject) para insertar una instancia de uno de los elementos siguientes:</span><span class="sxs-lookup"><span data-stu-id="a2dab-269">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="a2dab-270">La elección depende de la ubicación del almacenamiento del explorador que desee usar.</span><span class="sxs-lookup"><span data-stu-id="a2dab-270">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="a2dab-271">En el ejemplo siguiente, se usa `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="a2dab-271">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="a2dab-272">La directiva `@using` se puede colocar en el archivo `_Imports.razor` de la aplicación, en lugar de en el componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-272">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="a2dab-273">El uso del archivo `_Imports.razor` hace que el espacio de nombres esté disponible para segmentos más grandes de la aplicación o para toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2dab-273">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="a2dab-274">Para conservar el valor de `currentCount` en el componente `Counter` de una aplicación basada en la plantilla del proyecto Blazor Server, modifique el método `IncrementCount` para usar `ProtectedSessionStore.SetAsync`:</span><span class="sxs-lookup"><span data-stu-id="a2dab-274">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="a2dab-275">En aplicaciones más grandes y realistas, el almacenamiento de campos individuales es un escenario improbable.</span><span class="sxs-lookup"><span data-stu-id="a2dab-275">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="a2dab-276">Es más probable que las aplicaciones almacenen objetos de modelo completos que incluyen un estado complejo.</span><span class="sxs-lookup"><span data-stu-id="a2dab-276">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="a2dab-277">`ProtectedSessionStore` serializa y deserializa automáticamente los datos de JSON para almacenar objetos de estado complejos.</span><span class="sxs-lookup"><span data-stu-id="a2dab-277">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="a2dab-278">En el ejemplo de código anterior, los datos de `currentCount` se almacenan como `sessionStorage['count']` en el explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="a2dab-278">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="a2dab-279">Los datos no se almacenan como texto sin formato, sino que se protegen mediante la protección de datos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2dab-279">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="a2dab-280">Los datos cifrados pueden inspeccionarse si se evalúa `sessionStorage['count']` en la consola del desarrollador del explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-280">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="a2dab-281">Para recuperar los datos de `currentCount` si el usuario vuelve al componente `Counter` más adelante, incluso si está en un circuito nuevo, use `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="a2dab-281">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="a2dab-282">Si los parámetros del componente incluyen el estado de navegación, llame a `ProtectedSessionStore.GetAsync` y asigne el resultado no `null` en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="a2dab-282">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="a2dab-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> solo se llama una vez la primera vez que se crea una instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="a2dab-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> no se llama de nuevo más tarde si el usuario navega a una dirección URL diferente mientras permanece en la misma página.</span><span class="sxs-lookup"><span data-stu-id="a2dab-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="a2dab-285">Para obtener más información, vea <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="a2dab-285">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="a2dab-286">Los ejemplos de esta sección solo funcionan si el servidor no tiene habilitada la representación previa.</span><span class="sxs-lookup"><span data-stu-id="a2dab-286">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="a2dab-287">Con la representación previa habilitada, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-287">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="a2dab-288">Deshabilite la representación previa o agregue código adicional para trabajar con la representación previa.</span><span class="sxs-lookup"><span data-stu-id="a2dab-288">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="a2dab-289">Para obtener más información sobre cómo escribir código que funcione con la representación previa, consulte la sección [Controlar la representación previa](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="a2dab-289">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="a2dab-290">Controlar el estado de carga</span><span class="sxs-lookup"><span data-stu-id="a2dab-290">Handle the loading state</span></span>

<span data-ttu-id="a2dab-291">Dado que se accede al almacenamiento del explorador de forma asincrónica a través de una conexión de red, siempre hay un período de tiempo antes de que los datos se carguen y estén disponibles para un componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-291">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="a2dab-292">Para obtener los mejores resultados, represente un mensaje de estado de carga mientras la carga está en curso, en lugar de mostrar datos en blanco o predeterminados.</span><span class="sxs-lookup"><span data-stu-id="a2dab-292">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="a2dab-293">Un enfoque consiste en realizar un seguimiento de si los datos son `null`, lo que significa que los datos aún se están cargando.</span><span class="sxs-lookup"><span data-stu-id="a2dab-293">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="a2dab-294">En el componente `Counter` predeterminado, el recuento se mantiene en `int`.</span><span class="sxs-lookup"><span data-stu-id="a2dab-294">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="a2dab-295">[Haga que `currentCount` admita valores NULL](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) agregando un signo de interrogación (`?`) al tipo (`int`):</span><span class="sxs-lookup"><span data-stu-id="a2dab-295">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="a2dab-296">En lugar de mostrar de manera incondicional el recuento y el botón **`Increment`** , muestre estos elementos solo si se cargan los datos comprobando <xref:System.Nullable%601.HasValue%2A>:</span><span class="sxs-lookup"><span data-stu-id="a2dab-296">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="a2dab-297">Controlar la representación previa</span><span class="sxs-lookup"><span data-stu-id="a2dab-297">Handle prerendering</span></span>

<span data-ttu-id="a2dab-298">Durante la representación previa:</span><span class="sxs-lookup"><span data-stu-id="a2dab-298">During prerendering:</span></span>

* <span data-ttu-id="a2dab-299">No existe ninguna conexión interactiva con el explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="a2dab-299">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="a2dab-300">El explorador todavía no tiene ninguna página en la que se pueda ejecutar código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a2dab-300">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="a2dab-301">`localStorage` o `sessionStorage` no están disponibles durante la representación previa.</span><span class="sxs-lookup"><span data-stu-id="a2dab-301">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="a2dab-302">Si el componente intenta interactuar con el almacenamiento, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-302">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="a2dab-303">Una manera de resolver el error es deshabilitar la representación previa.</span><span class="sxs-lookup"><span data-stu-id="a2dab-303">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="a2dab-304">Esta suele ser la mejor opción si la aplicación hace un uso intensivo del almacenamiento basado en explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-304">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="a2dab-305">La representación previa agrega complejidad y no beneficia a la aplicación porque esta no puede representar previamente ningún contenido útil hasta que `localStorage` o `sessionStorage` estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="a2dab-305">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="a2dab-306">Para deshabilitar la representación previa, abra el archivo `Pages/_Host.cshtml` y cambie el atributo `render-mode` del [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) a <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span><span class="sxs-lookup"><span data-stu-id="a2dab-306">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="a2dab-307">La representación previa puede resultar útil para otras páginas que no utilizan `localStorage` o `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="a2dab-307">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="a2dab-308">Para mantener la representación previa, postergue la operación de carga hasta que el explorador esté conectado al circuito.</span><span class="sxs-lookup"><span data-stu-id="a2dab-308">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="a2dab-309">El siguiente es un ejemplo para almacenar un valor de contador:</span><span class="sxs-lookup"><span data-stu-id="a2dab-309">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="a2dab-310">Factorizar la preservación de estado en una ubicación común</span><span class="sxs-lookup"><span data-stu-id="a2dab-310">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="a2dab-311">Si muchos componentes se basan en el almacenamiento basado en explorador, al volver a implementar el código del proveedor de estado muchas veces se crea la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="a2dab-311">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="a2dab-312">Una opción para evitar la duplicación de código es crear un *componente primario del proveedor de estado* que encapsula la lógica del proveedor de estado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-312">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="a2dab-313">Los componentes secundarios pueden trabajar con datos persistentes sin tener en cuenta el mecanismo de persistencia del estado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-313">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="a2dab-314">En el siguiente ejemplo de un componente `CounterStateProvider`, se conservan los datos del contador en `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="a2dab-314">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
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

<span data-ttu-id="a2dab-315">El componente `CounterStateProvider` controla la fase de carga, ya que no representa su contenido secundario hasta que se completa la carga.</span><span class="sxs-lookup"><span data-stu-id="a2dab-315">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="a2dab-316">Para usar el componente `CounterStateProvider`, encapsule una instancia del componente en torno a cualquier otro componente que requiera acceso al estado del contador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-316">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="a2dab-317">Para que el estado sea accesible a todos los componentes de una aplicación, encapsule el componente `CounterStateProvider` alrededor de <xref:Microsoft.AspNetCore.Components.Routing.Router> en el componente `App` (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="a2dab-317">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="a2dab-318">Los componentes encapsulados reciben y pueden modificar el estado persistente del contador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-318">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="a2dab-319">El siguiente componente `Counter` implementa el patrón:</span><span class="sxs-lookup"><span data-stu-id="a2dab-319">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="a2dab-320">No es necesario que el componente anterior interactúe con `ProtectedBrowserStorage`, ni se trata de una fase de carga.</span><span class="sxs-lookup"><span data-stu-id="a2dab-320">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="a2dab-321">Para tratar la representación previa tal y como se ha descrito anteriormente, `CounterStateProvider` se puede modificar para que todos los componentes que consumen los datos del contador funcionen automáticamente con la representación previa.</span><span class="sxs-lookup"><span data-stu-id="a2dab-321">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="a2dab-322">Para obtener más información, vea la sección [Controlar la representación previa](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="a2dab-322">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="a2dab-323">En general, se recomienda el patrón *componente primario del proveedor de estado*:</span><span class="sxs-lookup"><span data-stu-id="a2dab-323">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="a2dab-324">Para consumir el estado en muchos componentes.</span><span class="sxs-lookup"><span data-stu-id="a2dab-324">To consume state across many components.</span></span>
* <span data-ttu-id="a2dab-325">Si solo hay un objeto de estado de nivel superior para conservar.</span><span class="sxs-lookup"><span data-stu-id="a2dab-325">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="a2dab-326">Para conservar muchos objetos de estado diferentes y consumir distintos subconjuntos de objetos en distintos lugares, es mejor evitar la conservación global del estado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-326">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="a2dab-327">Paquete de NuGet experimental del almacenamiento de explorador protegido</span><span class="sxs-lookup"><span data-stu-id="a2dab-327">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="a2dab-328">Por ejemplo, el almacenamiento del explorador protegido de ASP.NET Core usa la [protección de datos de ASP.NET Core](xref:security/data-protection/introduction) para [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span><span class="sxs-lookup"><span data-stu-id="a2dab-328">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="a2dab-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` es un paquete experimental no compatible que no es adecuado para su uso en producción.</span><span class="sxs-lookup"><span data-stu-id="a2dab-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="a2dab-330">El paquete solo está disponible para su uso en aplicaciones Blazor Server de ASP.NET Core 3.1.</span><span class="sxs-lookup"><span data-stu-id="a2dab-330">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="a2dab-331">Configuración</span><span class="sxs-lookup"><span data-stu-id="a2dab-331">Configuration</span></span>

1. <span data-ttu-id="a2dab-332">Agregue una referencia de paquete a [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="a2dab-332">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="a2dab-333">En el archivo `Pages/_Host.cshtml`, agregue el siguiente script dentro de la etiqueta de cierre `</body>`:</span><span class="sxs-lookup"><span data-stu-id="a2dab-333">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="a2dab-334">En `Startup.ConfigureServices`, llame a `AddProtectedBrowserStorage` para agregar los servicios `localStorage` y `sessionStorage` a la colección de servicios:</span><span class="sxs-lookup"><span data-stu-id="a2dab-334">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="a2dab-335">Guardar y cargar datos dentro de un componente</span><span class="sxs-lookup"><span data-stu-id="a2dab-335">Save and load data within a component</span></span>

<span data-ttu-id="a2dab-336">En cualquier componente que requiera cargar o guardar datos en el almacenamiento del explorador, use la directiva [`@inject`](xref:mvc/views/razor#inject) para insertar una instancia de uno de los elementos siguientes:</span><span class="sxs-lookup"><span data-stu-id="a2dab-336">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="a2dab-337">La elección depende de la ubicación del almacenamiento del explorador que desee usar.</span><span class="sxs-lookup"><span data-stu-id="a2dab-337">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="a2dab-338">En el ejemplo siguiente, se usa `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="a2dab-338">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="a2dab-339">La instrucción `@using` se puede colocar en un archivo `_Imports.razor`, en lugar de en el componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-339">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="a2dab-340">El uso del archivo `_Imports.razor` hace que el espacio de nombres esté disponible para segmentos más grandes de la aplicación o para toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2dab-340">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="a2dab-341">Para conservar el valor de `currentCount` en el componente `Counter` de una aplicación basada en la plantilla del proyecto Blazor Server, modifique el método `IncrementCount` para usar `ProtectedSessionStore.SetAsync`:</span><span class="sxs-lookup"><span data-stu-id="a2dab-341">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="a2dab-342">En aplicaciones más grandes y realistas, el almacenamiento de campos individuales es un escenario improbable.</span><span class="sxs-lookup"><span data-stu-id="a2dab-342">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="a2dab-343">Es más probable que las aplicaciones almacenen objetos de modelo completos que incluyen un estado complejo.</span><span class="sxs-lookup"><span data-stu-id="a2dab-343">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="a2dab-344">`ProtectedSessionStore` serializa y deserializa automáticamente los datos de JSON.</span><span class="sxs-lookup"><span data-stu-id="a2dab-344">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="a2dab-345">En el ejemplo de código anterior, los datos de `currentCount` se almacenan como `sessionStorage['count']` en el explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="a2dab-345">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="a2dab-346">Los datos no se almacenan como texto sin formato, sino que se protegen mediante la protección de datos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2dab-346">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="a2dab-347">Los datos cifrados pueden inspeccionarse si se evalúa `sessionStorage['count']` en la consola del desarrollador del explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-347">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="a2dab-348">Para recuperar los datos de `currentCount` si el usuario vuelve al componente `Counter` más adelante, incluso si está en un circuito totalmente nuevo, use `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="a2dab-348">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="a2dab-349">Si los parámetros del componente incluyen el estado de navegación, llame a `ProtectedSessionStore.GetAsync` y asigne el resultado en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="a2dab-349">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="a2dab-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> solo se llama una vez la primera vez que se crea una instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="a2dab-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> no se llama de nuevo más tarde si el usuario navega a una dirección URL diferente mientras permanece en la misma página.</span><span class="sxs-lookup"><span data-stu-id="a2dab-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="a2dab-352">Para obtener más información, vea <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="a2dab-352">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="a2dab-353">Los ejemplos de esta sección solo funcionan si el servidor no tiene habilitada la representación previa.</span><span class="sxs-lookup"><span data-stu-id="a2dab-353">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="a2dab-354">Con la representación previa habilitada, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-354">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="a2dab-355">Deshabilite la representación previa o agregue código adicional para trabajar con la representación previa.</span><span class="sxs-lookup"><span data-stu-id="a2dab-355">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="a2dab-356">Para obtener más información sobre cómo escribir código que funcione con la representación previa, consulte la sección [Controlar la representación previa](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="a2dab-356">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="a2dab-357">Controlar el estado de carga</span><span class="sxs-lookup"><span data-stu-id="a2dab-357">Handle the loading state</span></span>

<span data-ttu-id="a2dab-358">Dado que se accede al almacenamiento del explorador de forma asincrónica a través de una conexión de red, siempre hay un período de tiempo antes de que los datos se carguen y estén disponibles para un componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-358">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="a2dab-359">Para obtener los mejores resultados, represente un mensaje de estado de carga mientras la carga está en curso, en lugar de mostrar datos en blanco o predeterminados.</span><span class="sxs-lookup"><span data-stu-id="a2dab-359">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="a2dab-360">Un enfoque consiste en realizar un seguimiento de si los datos son `null`, lo que significa que los datos aún se están cargando.</span><span class="sxs-lookup"><span data-stu-id="a2dab-360">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="a2dab-361">En el componente `Counter` predeterminado, el recuento se mantiene en `int`.</span><span class="sxs-lookup"><span data-stu-id="a2dab-361">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="a2dab-362">[Haga que `currentCount` admita valores NULL](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) agregando un signo de interrogación (`?`) al tipo (`int`):</span><span class="sxs-lookup"><span data-stu-id="a2dab-362">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="a2dab-363">En lugar de mostrar de manera incondicional el recuento y el botón **`Increment`** , elija que estos elementos se muestren solo si se cargan los datos:</span><span class="sxs-lookup"><span data-stu-id="a2dab-363">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="a2dab-364">Controlar la representación previa</span><span class="sxs-lookup"><span data-stu-id="a2dab-364">Handle prerendering</span></span>

<span data-ttu-id="a2dab-365">Durante la representación previa:</span><span class="sxs-lookup"><span data-stu-id="a2dab-365">During prerendering:</span></span>

* <span data-ttu-id="a2dab-366">No existe ninguna conexión interactiva con el explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="a2dab-366">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="a2dab-367">El explorador todavía no tiene ninguna página en la que se pueda ejecutar código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a2dab-367">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="a2dab-368">`localStorage` o `sessionStorage` no están disponibles durante la representación previa.</span><span class="sxs-lookup"><span data-stu-id="a2dab-368">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="a2dab-369">Si el componente intenta interactuar con el almacenamiento, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.</span><span class="sxs-lookup"><span data-stu-id="a2dab-369">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="a2dab-370">Una manera de resolver el error es deshabilitar la representación previa.</span><span class="sxs-lookup"><span data-stu-id="a2dab-370">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="a2dab-371">Esta suele ser la mejor opción si la aplicación hace un uso intensivo del almacenamiento basado en explorador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-371">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="a2dab-372">La representación previa agrega complejidad y no beneficia a la aplicación porque esta no puede representar previamente ningún contenido útil hasta que `localStorage` o `sessionStorage` estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="a2dab-372">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="a2dab-373">Para deshabilitar la representación previa, abra el archivo `Pages/_Host.cshtml` y cambie el atributo `render-mode` del [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) a <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span><span class="sxs-lookup"><span data-stu-id="a2dab-373">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="a2dab-374">La representación previa puede resultar útil para otras páginas que no utilizan `localStorage` o `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="a2dab-374">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="a2dab-375">Para mantener la representación previa, postergue la operación de carga hasta que el explorador esté conectado al circuito.</span><span class="sxs-lookup"><span data-stu-id="a2dab-375">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="a2dab-376">El siguiente es un ejemplo para almacenar un valor de contador:</span><span class="sxs-lookup"><span data-stu-id="a2dab-376">The following is an example for storing a counter value:</span></span>

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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="a2dab-377">Factorizar la preservación de estado en una ubicación común</span><span class="sxs-lookup"><span data-stu-id="a2dab-377">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="a2dab-378">Si muchos componentes se basan en el almacenamiento basado en explorador, al volver a implementar el código del proveedor de estado muchas veces se crea la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="a2dab-378">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="a2dab-379">Una opción para evitar la duplicación de código es crear un *componente primario del proveedor de estado* que encapsula la lógica del proveedor de estado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-379">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="a2dab-380">Los componentes secundarios pueden trabajar con datos persistentes sin tener en cuenta el mecanismo de persistencia del estado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-380">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="a2dab-381">En el siguiente ejemplo de un componente `CounterStateProvider`, se conservan los datos del contador en `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="a2dab-381">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

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

<span data-ttu-id="a2dab-382">El componente `CounterStateProvider` controla la fase de carga, ya que no representa su contenido secundario hasta que se completa la carga.</span><span class="sxs-lookup"><span data-stu-id="a2dab-382">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="a2dab-383">Para usar el componente `CounterStateProvider`, encapsule una instancia del componente en torno a cualquier otro componente que requiera acceso al estado del contador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-383">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="a2dab-384">Para que el estado sea accesible a todos los componentes de una aplicación, encapsule el componente `CounterStateProvider` alrededor de <xref:Microsoft.AspNetCore.Components.Routing.Router> en el componente `App` (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="a2dab-384">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="a2dab-385">Los componentes encapsulados reciben y pueden modificar el estado persistente del contador.</span><span class="sxs-lookup"><span data-stu-id="a2dab-385">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="a2dab-386">El siguiente componente `Counter` implementa el patrón:</span><span class="sxs-lookup"><span data-stu-id="a2dab-386">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="a2dab-387">No es necesario que el componente anterior interactúe con `ProtectedBrowserStorage`, ni se trata de una fase de carga.</span><span class="sxs-lookup"><span data-stu-id="a2dab-387">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="a2dab-388">Para tratar la representación previa tal y como se ha descrito anteriormente, `CounterStateProvider` se puede modificar para que todos los componentes que consumen los datos del contador funcionen automáticamente con la representación previa.</span><span class="sxs-lookup"><span data-stu-id="a2dab-388">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="a2dab-389">Para obtener más información, vea la sección [Controlar la representación previa](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="a2dab-389">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="a2dab-390">En general, se recomienda el patrón *componente primario del proveedor de estado*:</span><span class="sxs-lookup"><span data-stu-id="a2dab-390">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="a2dab-391">Para consumir el estado en muchos componentes.</span><span class="sxs-lookup"><span data-stu-id="a2dab-391">To consume state across many components.</span></span>
* <span data-ttu-id="a2dab-392">Si solo hay un objeto de estado de nivel superior para conservar.</span><span class="sxs-lookup"><span data-stu-id="a2dab-392">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="a2dab-393">Para conservar muchos objetos de estado diferentes y consumir distintos subconjuntos de objetos en distintos lugares, es mejor evitar la conservación global del estado.</span><span class="sxs-lookup"><span data-stu-id="a2dab-393">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: zone-end
