---
title: Administración de estado de Blazor en ASP.NET Core
author: guardrex
description: Aprenda a conservar el estado en las aplicaciones Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: a6c646425145855538f408ec6cafdb151cd24b86
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401953"
---
# <a name="aspnet-core-blazor-state-management"></a><span data-ttu-id="039a2-103">Administración de estado de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="039a2-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="039a2-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="039a2-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="039a2-105">El servidor Blazor Server es un marco para aplicaciones con estado.</span><span class="sxs-lookup"><span data-stu-id="039a2-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="039a2-106">La mayoría de las veces, la aplicación mantiene una conexión continua con el servidor.</span><span class="sxs-lookup"><span data-stu-id="039a2-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="039a2-107">El estado del usuario se mantiene en la memoria del servidor en un *circuito*.</span><span class="sxs-lookup"><span data-stu-id="039a2-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="039a2-108">Entre los ejemplos de estado que se mantiene para el circuito de un usuario se incluyen:</span><span class="sxs-lookup"><span data-stu-id="039a2-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="039a2-109">La interfaz de usuario representada: la jerarquía de instancias de componente y su salida de representación más reciente.</span><span class="sxs-lookup"><span data-stu-id="039a2-109">The rendered UI: The hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="039a2-110">Los valores de los campos y las propiedades de las instancias del componente.</span><span class="sxs-lookup"><span data-stu-id="039a2-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="039a2-111">Los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) que se encuentran en el ámbito del circuito.</span><span class="sxs-lookup"><span data-stu-id="039a2-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="039a2-112">En este artículo se aborda la persistencia de estado en aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="039a2-112">This article addresses state persistence in Blazor Server apps.</span></span> <span data-ttu-id="039a2-113">Las aplicaciones Blazor WebAssembly pueden aprovechar las ventajas de la [persistencia de estado del lado cliente en el explorador](#client-side-in-the-browser), pero requieren soluciones personalizadas o paquetes de terceros más allá del ámbito de este artículo.</span><span class="sxs-lookup"><span data-stu-id="039a2-113">Blazor WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="blazor-circuits"></a><span data-ttu-id="039a2-114">Circuitos de Blazor</span><span class="sxs-lookup"><span data-stu-id="039a2-114">Blazor circuits</span></span>

<span data-ttu-id="039a2-115">Si un usuario experimenta una pérdida de conexión de red temporal, Blazor intenta volver a conectar al usuario con su circuito original para poder seguir usando la aplicación.</span><span class="sxs-lookup"><span data-stu-id="039a2-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="039a2-116">Sin embargo, no siempre es posible volver a conectar a un usuario con su circuito original en la memoria del servidor:</span><span class="sxs-lookup"><span data-stu-id="039a2-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="039a2-117">El servidor no puede mantener un circuito desconectado para siempre.</span><span class="sxs-lookup"><span data-stu-id="039a2-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="039a2-118">El servidor debe liberar un circuito desconectado después de un tiempo de espera o cuando el servidor esté bajo presión de memoria.</span><span class="sxs-lookup"><span data-stu-id="039a2-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="039a2-119">En entornos de implementación multiservidor y de carga equilibrada, las solicitudes de procesamiento de servidor pueden dejar de estar disponibles en un momento dado.</span><span class="sxs-lookup"><span data-stu-id="039a2-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="039a2-120">Se puede producir un error en los servidores individuales o estos se pueden quitar automáticamente cuando ya no sean necesarios para controlar el volumen total de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="039a2-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="039a2-121">Es posible que el servidor original no esté disponible cuando el usuario intente volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="039a2-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="039a2-122">El usuario podría cerrar y volver a abrir el explorador o recargar la página, lo que elimina cualquier estado que se mantenga en la memoria del explorador.</span><span class="sxs-lookup"><span data-stu-id="039a2-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="039a2-123">Por ejemplo, los valores establecidos mediante llamadas de interoperabilidad de JavaScript se pierden.</span><span class="sxs-lookup"><span data-stu-id="039a2-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="039a2-124">Cuando un usuario no se puede volver a conectar a su circuito original, el usuario recibe un circuito nuevo con un estado vacío.</span><span class="sxs-lookup"><span data-stu-id="039a2-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="039a2-125">Esto es equivalente a cerrar y volver a abrir una aplicación de escritorio.</span><span class="sxs-lookup"><span data-stu-id="039a2-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="039a2-126">Conservar el estado entre circuitos</span><span class="sxs-lookup"><span data-stu-id="039a2-126">Preserve state across circuits</span></span>

<span data-ttu-id="039a2-127">En algunos escenarios, es deseable conservar el estado entre los circuitos.</span><span class="sxs-lookup"><span data-stu-id="039a2-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="039a2-128">Una aplicación puede conservar datos importantes para un usuario si:</span><span class="sxs-lookup"><span data-stu-id="039a2-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="039a2-129">El servidor web deja de estar disponible.</span><span class="sxs-lookup"><span data-stu-id="039a2-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="039a2-130">El explorador del usuario se ve obligado a iniciar un nuevo circuito con un nuevo servidor web.</span><span class="sxs-lookup"><span data-stu-id="039a2-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="039a2-131">En general, mantener el estado en los circuitos se aplica a los escenarios en los que los usuarios crean activamente datos, no simplemente leyendo los datos que ya existen.</span><span class="sxs-lookup"><span data-stu-id="039a2-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="039a2-132">Para conservar el estado más allá de un solo circuito, *no almacene simplemente los datos en la memoria del servidor*.</span><span class="sxs-lookup"><span data-stu-id="039a2-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="039a2-133">La aplicación debe conservar los datos en otra ubicación de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="039a2-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="039a2-134">La persistencia del estado no es automática.</span><span class="sxs-lookup"><span data-stu-id="039a2-134">State persistence isn't automatic.</span></span> <span data-ttu-id="039a2-135">Debe seguir los pasos al desarrollar la aplicación para implementar la persistencia de datos con estado.</span><span class="sxs-lookup"><span data-stu-id="039a2-135">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="039a2-136">La persistencia de los datos normalmente solo es necesaria para el estado de alto valor que los usuarios han gastado en crear.</span><span class="sxs-lookup"><span data-stu-id="039a2-136">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="039a2-137">En los ejemplos siguientes, el estado persistente ahorra tiempo o ayuda en las actividades comerciales:</span><span class="sxs-lookup"><span data-stu-id="039a2-137">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="039a2-138">Formulario web de varios pasos: durante un proceso que consta de varios pasos, en caso de que la información se pierda, los usuarios tienen que dedicar mucho tiempo a volver a escribir los datos que ya han proporcionado.</span><span class="sxs-lookup"><span data-stu-id="039a2-138">Multistep webform: It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="039a2-139">Un usuario pierde el estado en este escenario si se sale del formulario de varios pasos y vuelve al formulario más adelante.</span><span class="sxs-lookup"><span data-stu-id="039a2-139">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="039a2-140">Carro de la compra: se puede mantener cualquier componente comercial importante de una aplicación que represente ingresos potenciales.</span><span class="sxs-lookup"><span data-stu-id="039a2-140">Shopping cart: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="039a2-141">Un usuario que pierde su estado y, por tanto, su carro de la compra, puede comprar menos productos o servicios cuando vuelva al sitio más adelante.</span><span class="sxs-lookup"><span data-stu-id="039a2-141">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="039a2-142">Normalmente no es necesario conservar el estado de fácil creación, como el nombre de usuario especificado en un cuadro de diálogo de inicio de sesión que no se ha enviado.</span><span class="sxs-lookup"><span data-stu-id="039a2-142">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="039a2-143">Una aplicación solo puede conservar el *estado de la aplicación*.</span><span class="sxs-lookup"><span data-stu-id="039a2-143">An app can only persist *app state*.</span></span> <span data-ttu-id="039a2-144">Las interfaces de usuario no se pueden conservar, como instancias de componentes y sus árboles de representación.</span><span class="sxs-lookup"><span data-stu-id="039a2-144">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="039a2-145">Los componentes y los árboles de representación no suelen ser serializables.</span><span class="sxs-lookup"><span data-stu-id="039a2-145">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="039a2-146">Para conservar algo similar al estado de la interfaz de usuario, como los nodos expandidos de una vista de árbol, la aplicación debe tener código personalizado para modelar el comportamiento como estado de la aplicación serializable.</span><span class="sxs-lookup"><span data-stu-id="039a2-146">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="039a2-147">Dónde conservar el estado</span><span class="sxs-lookup"><span data-stu-id="039a2-147">Where to persist state</span></span>

<span data-ttu-id="039a2-148">Existen tres ubicaciones comunes para el estado persistente en una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="039a2-148">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="039a2-149">Cada enfoque es más adecuado para distintos escenarios y tiene advertencias diferentes:</span><span class="sxs-lookup"><span data-stu-id="039a2-149">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="039a2-150">Del lado servidor en una base de datos</span><span class="sxs-lookup"><span data-stu-id="039a2-150">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="039a2-151">URL</span><span class="sxs-lookup"><span data-stu-id="039a2-151">URL</span></span>](#url)
* [<span data-ttu-id="039a2-152">Del lado cliente en el explorador</span><span class="sxs-lookup"><span data-stu-id="039a2-152">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="039a2-153">Del lado servidor en una base de datos</span><span class="sxs-lookup"><span data-stu-id="039a2-153">Server-side in a database</span></span>

<span data-ttu-id="039a2-154">Para la persistencia de datos permanente o para cualquier dato que deba abarcar varios usuarios o dispositivos, una base de datos independiente en el servidor es casi seguro la mejor opción.</span><span class="sxs-lookup"><span data-stu-id="039a2-154">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="039a2-155">Las opciones son:</span><span class="sxs-lookup"><span data-stu-id="039a2-155">Options include:</span></span>

* <span data-ttu-id="039a2-156">Base de datos SQL relacional</span><span class="sxs-lookup"><span data-stu-id="039a2-156">Relational SQL database</span></span>
* <span data-ttu-id="039a2-157">almacén de pares clave-valor</span><span class="sxs-lookup"><span data-stu-id="039a2-157">Key-value store</span></span>
* <span data-ttu-id="039a2-158">Almacén de blobs</span><span class="sxs-lookup"><span data-stu-id="039a2-158">Blob store</span></span>
* <span data-ttu-id="039a2-159">Almacén de tablas</span><span class="sxs-lookup"><span data-stu-id="039a2-159">Table store</span></span>

<span data-ttu-id="039a2-160">Una vez guardados los datos en la base de datos, un usuario puede iniciar un nuevo circuito en cualquier momento.</span><span class="sxs-lookup"><span data-stu-id="039a2-160">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="039a2-161">Los datos del usuario se conservan y están disponibles en cualquier circuito nuevo.</span><span class="sxs-lookup"><span data-stu-id="039a2-161">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="039a2-162">Para más información sobre las opciones de almacenamiento de datos de Azure, consulte la [documentación de Azure Storage](/azure/storage/) y las [bases de datos de Azure](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="039a2-162">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="039a2-163">Resolución</span><span class="sxs-lookup"><span data-stu-id="039a2-163">URL</span></span>

<span data-ttu-id="039a2-164">Para los datos transitorios que representan el estado de navegación, modele los datos como parte de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="039a2-164">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="039a2-165">Entre los ejemplos de estado modelado en la dirección URL se incluyen:</span><span class="sxs-lookup"><span data-stu-id="039a2-165">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="039a2-166">El identificador de una entidad visualizada.</span><span class="sxs-lookup"><span data-stu-id="039a2-166">The ID of a viewed entity.</span></span>
* <span data-ttu-id="039a2-167">El número de la página actual en una cuadrícula paginada.</span><span class="sxs-lookup"><span data-stu-id="039a2-167">The current page number in a paged grid.</span></span>

<span data-ttu-id="039a2-168">Se conserva el contenido de la barra de direcciones del explorador:</span><span class="sxs-lookup"><span data-stu-id="039a2-168">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="039a2-169">Si el usuario recarga manualmente la página.</span><span class="sxs-lookup"><span data-stu-id="039a2-169">If the user manually reloads the page.</span></span>
* <span data-ttu-id="039a2-170">Si el servidor web deja de estar disponible y el usuario se ve obligado a volver a cargar la página para conectarse a un servidor diferente.</span><span class="sxs-lookup"><span data-stu-id="039a2-170">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="039a2-171">Para obtener información sobre cómo definir patrones de direcciones URL con la directiva `@page`, vea <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="039a2-171">For information on defining URL patterns with the `@page` directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="039a2-172">Del lado cliente en el explorador</span><span class="sxs-lookup"><span data-stu-id="039a2-172">Client-side in the browser</span></span>

<span data-ttu-id="039a2-173">En el caso de los datos transitorios que el usuario está creando activamente, una memoria auxiliar común son las colecciones `localStorage` y `sessionStorage` del explorador.</span><span class="sxs-lookup"><span data-stu-id="039a2-173">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="039a2-174">No es necesario que la aplicación administre o borre el estado almacenado si se abandona el circuito, lo que supone una ventaja sobre el almacenamiento del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="039a2-174">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="039a2-175">"Del lado cliente" en esta sección se refiere a los escenarios del lado cliente en el explorador, no al [modelo de hospedaje de Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="039a2-175">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="039a2-176">`localStorage` y `sessionStorage` se pueden usar en aplicaciones Blazor WebAssembly, pero solo escribiendo código personalizado o usando un paquete de terceros.</span><span class="sxs-lookup"><span data-stu-id="039a2-176">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="039a2-177">`localStorage` y `sessionStorage` difieren de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="039a2-177">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="039a2-178">`localStorage` está en el ámbito del explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="039a2-178">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="039a2-179">Si el usuario recarga la página o la cierra y vuelve a abrir el explorador, el estado se conserva.</span><span class="sxs-lookup"><span data-stu-id="039a2-179">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="039a2-180">Si el usuario abre varias pestañas del explorador, el estado se comparte entre las pestañas.</span><span class="sxs-lookup"><span data-stu-id="039a2-180">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="039a2-181">Los datos se conservan en `localStorage` hasta que se borran explícitamente.</span><span class="sxs-lookup"><span data-stu-id="039a2-181">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="039a2-182">`sessionStorage` está en el ámbito de la pestaña del explorador del usuario. Si el usuario recarga la pestaña, el estado se conserva.</span><span class="sxs-lookup"><span data-stu-id="039a2-182">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="039a2-183">Si el usuario cierra la pestaña o el explorador, se pierde el estado.</span><span class="sxs-lookup"><span data-stu-id="039a2-183">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="039a2-184">Si el usuario abre varias pestañas del explorador, cada pestaña tendrá su propia versión independiente de los datos.</span><span class="sxs-lookup"><span data-stu-id="039a2-184">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="039a2-185">Por lo general, es más seguro usar `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="039a2-185">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="039a2-186">`sessionStorage` evita el riesgo de que un usuario abra varias pestañas y encuentre lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="039a2-186">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="039a2-187">Errores en el almacenamiento de estado en todas las pestañas.</span><span class="sxs-lookup"><span data-stu-id="039a2-187">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="039a2-188">Comportamiento confuso cuando una pestaña sobrescribe el estado de otras.</span><span class="sxs-lookup"><span data-stu-id="039a2-188">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="039a2-189">`localStorage` es la mejor opción si la aplicación debe conservar el estado en el cierre y volver a abrir el explorador.</span><span class="sxs-lookup"><span data-stu-id="039a2-189">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="039a2-190">Advertencias sobre el uso del almacenamiento del explorador:</span><span class="sxs-lookup"><span data-stu-id="039a2-190">Caveats for using browser storage:</span></span>

* <span data-ttu-id="039a2-191">De forma similar al uso de una base de datos del lado servidor, la carga y el almacenamiento de datos son asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="039a2-191">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="039a2-192">A diferencia de las bases de datos del lado servidor, el almacenamiento no está disponible durante la representación previa porque la página solicitada no existe en el explorador durante la fase de representación previa.</span><span class="sxs-lookup"><span data-stu-id="039a2-192">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="039a2-193">Es razonable almacenar unos pocos kilobytes de datos para las aplicaciones Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="039a2-193">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="039a2-194">Más allá de unos pocos kilobytes, debe tener en cuenta las implicaciones de rendimiento porque los datos se cargan y se guardan en la red.</span><span class="sxs-lookup"><span data-stu-id="039a2-194">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="039a2-195">Los usuarios pueden ver o alterar los datos.</span><span class="sxs-lookup"><span data-stu-id="039a2-195">Users may view or tamper with the data.</span></span> <span data-ttu-id="039a2-196">La [protección de datos](xref:security/data-protection/introduction) de ASP.NET Core puede mitigar el riesgo.</span><span class="sxs-lookup"><span data-stu-id="039a2-196">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="039a2-197">Soluciones de almacenamiento de explorador de terceros</span><span class="sxs-lookup"><span data-stu-id="039a2-197">Third-party browser storage solutions</span></span>

<span data-ttu-id="039a2-198">Los paquetes NuGet de terceros proporcionan API para trabajar con `localStorage` y `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="039a2-198">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="039a2-199">Merece la pena tener en cuenta la elección de un paquete que utiliza de forma transparente la [protección de datos](xref:security/data-protection/introduction) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="039a2-199">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="039a2-200">La protección de datos de ASP.NET Core cifra los datos almacenados y reduce el riesgo potencial de alteración de los datos almacenados.</span><span class="sxs-lookup"><span data-stu-id="039a2-200">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="039a2-201">Si los datos serializados con JSON se almacenan en texto sin formato, los usuarios pueden ver los datos mediante las herramientas de desarrollo del explorador y también modificar los datos almacenados.</span><span class="sxs-lookup"><span data-stu-id="039a2-201">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="039a2-202">La protección de datos no siempre es un problema, ya que los datos pueden ser bastante triviales.</span><span class="sxs-lookup"><span data-stu-id="039a2-202">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="039a2-203">Por ejemplo, la lectura o modificación del color almacenado de un elemento de la interfaz de usuario no supone un riesgo de seguridad significativo para el usuario o la organización.</span><span class="sxs-lookup"><span data-stu-id="039a2-203">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="039a2-204">Evite que los usuarios puedan inspeccionar o manipular *datos confidenciales*.</span><span class="sxs-lookup"><span data-stu-id="039a2-204">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="039a2-205">Paquete experimental del almacenamiento de explorador protegido</span><span class="sxs-lookup"><span data-stu-id="039a2-205">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="039a2-206">Un ejemplo de paquete NuGet que proporciona [protección de datos](xref:security/data-protection/introduction) para `localStorage` y `sessionStorage` es [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="039a2-206">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="039a2-207">`Microsoft.AspNetCore.ProtectedBrowserStorage` es un paquete experimental no compatible que no es adecuado para su uso en producción en este momento.</span><span class="sxs-lookup"><span data-stu-id="039a2-207">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="039a2-208">Instalación</span><span class="sxs-lookup"><span data-stu-id="039a2-208">Installation</span></span>

<span data-ttu-id="039a2-209">Para instalar el paquete de `Microsoft.AspNetCore.ProtectedBrowserStorage`:</span><span class="sxs-lookup"><span data-stu-id="039a2-209">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="039a2-210">En el proyecto de aplicación Blazor Server, agregue una referencia de paquete a [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="039a2-210">In the Blazor Server app project, add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="039a2-211">En el código HTML de nivel superior (por ejemplo, en el archivo `Pages/_Host.cshtml` de la plantilla de proyecto predeterminada), agregue la siguiente etiqueta de `<script>`:</span><span class="sxs-lookup"><span data-stu-id="039a2-211">In the top-level HTML (for example, in the `Pages/_Host.cshtml` file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="039a2-212">En el método `Startup.ConfigureServices`, llame a `AddProtectedBrowserStorage` para agregar los servicios `localStorage` y `sessionStorage` a la colección de servicios:</span><span class="sxs-lookup"><span data-stu-id="039a2-212">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="039a2-213">Guardar y cargar datos dentro de un componente</span><span class="sxs-lookup"><span data-stu-id="039a2-213">Save and load data within a component</span></span>

<span data-ttu-id="039a2-214">En cualquier componente que requiera cargar o guardar datos en el almacenamiento del explorador, use [`@inject`](xref:mvc/views/razor#inject) para insertar una instancia de uno de los elementos siguientes:</span><span class="sxs-lookup"><span data-stu-id="039a2-214">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:mvc/views/razor#inject) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="039a2-215">La elección depende de la memoria auxiliar que quiera usar.</span><span class="sxs-lookup"><span data-stu-id="039a2-215">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="039a2-216">En el ejemplo siguiente, se usa `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="039a2-216">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="039a2-217">La instrucción `@using` se puede colocar en un archivo `_Imports.razor`, en lugar de en el componente.</span><span class="sxs-lookup"><span data-stu-id="039a2-217">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="039a2-218">El uso del archivo `_Imports.razor` hace que el espacio de nombres esté disponible para segmentos más grandes de la aplicación o para toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="039a2-218">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="039a2-219">Para conservar el valor de `currentCount` en el componente `Counter` de la plantilla de proyecto, modifique el método `IncrementCount` para usar `ProtectedSessionStore.SetAsync`:</span><span class="sxs-lookup"><span data-stu-id="039a2-219">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="039a2-220">En aplicaciones más grandes y realistas, el almacenamiento de campos individuales es un escenario improbable.</span><span class="sxs-lookup"><span data-stu-id="039a2-220">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="039a2-221">Es más probable que las aplicaciones almacenen objetos de modelo completos que incluyen un estado complejo.</span><span class="sxs-lookup"><span data-stu-id="039a2-221">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="039a2-222">`ProtectedSessionStore` serializa y deserializa automáticamente los datos de JSON.</span><span class="sxs-lookup"><span data-stu-id="039a2-222">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="039a2-223">En el ejemplo de código anterior, los datos de `currentCount` se almacenan como `sessionStorage['count']` en el explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="039a2-223">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="039a2-224">Los datos no se almacenan en texto no cifrado, sino que se protegen mediante la [protección de datos](xref:security/data-protection/introduction) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="039a2-224">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="039a2-225">Los datos cifrados pueden verse si se evalúa `sessionStorage['count']` en la consola del desarrollador del explorador.</span><span class="sxs-lookup"><span data-stu-id="039a2-225">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="039a2-226">Para recuperar los datos de `currentCount` si el usuario vuelve al componente `Counter` más adelante (incluso si está en un circuito totalmente nuevo), use `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="039a2-226">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="039a2-227">Si los parámetros del componente incluyen el estado de navegación, llame a `ProtectedSessionStore.GetAsync` y asigne el resultado en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="039a2-227">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="039a2-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> solo se llama una vez la primera vez que se crea una instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="039a2-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="039a2-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> no se llama de nuevo más tarde si el usuario navega a una dirección URL diferente mientras permanece en la misma página.</span><span class="sxs-lookup"><span data-stu-id="039a2-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="039a2-230">Para obtener más información, vea <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="039a2-230">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="039a2-231">Los ejemplos de esta sección solo funcionan si el servidor no tiene habilitada la representación previa.</span><span class="sxs-lookup"><span data-stu-id="039a2-231">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="039a2-232">Con la representación previa habilitada, se genera un error similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="039a2-232">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="039a2-233">No se pueden emitir llamadas de interoperabilidad de JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="039a2-233">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="039a2-234">Esto se debe a que el componente se está representando previamente.</span><span class="sxs-lookup"><span data-stu-id="039a2-234">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="039a2-235">Deshabilite la representación previa o agregue código adicional para trabajar con la representación previa.</span><span class="sxs-lookup"><span data-stu-id="039a2-235">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="039a2-236">Para obtener más información sobre cómo escribir código que funcione con la representación previa, consulte la sección [Controlar la representación previa](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="039a2-236">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="039a2-237">Controlar el estado de carga</span><span class="sxs-lookup"><span data-stu-id="039a2-237">Handle the loading state</span></span>

<span data-ttu-id="039a2-238">Dado que el almacenamiento del explorador es asincrónico (al que se accede a través de una conexión de red), siempre hay un período de tiempo antes de que los datos se carguen y estén disponibles para que los use un componente.</span><span class="sxs-lookup"><span data-stu-id="039a2-238">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="039a2-239">Para obtener los mejores resultados, represente un mensaje de estado de carga mientras la carga está en curso, en lugar de mostrar datos en blanco o predeterminados.</span><span class="sxs-lookup"><span data-stu-id="039a2-239">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="039a2-240">Un enfoque consiste en realizar un seguimiento de si los datos son `null` (aún se están cargando) o no.</span><span class="sxs-lookup"><span data-stu-id="039a2-240">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="039a2-241">En el componente `Counter` predeterminado, el recuento se mantiene en `int`.</span><span class="sxs-lookup"><span data-stu-id="039a2-241">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="039a2-242">Haga que `currentCount` admita valores NULL agregando un signo de interrogación (`?`) al tipo (`int`):</span><span class="sxs-lookup"><span data-stu-id="039a2-242">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="039a2-243">En lugar de mostrar de manera incondicional el recuento y el botón **`Increment`** , elija que estos elementos se muestren solo si se cargan los datos:</span><span class="sxs-lookup"><span data-stu-id="039a2-243">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="039a2-244">Controlar la representación previa</span><span class="sxs-lookup"><span data-stu-id="039a2-244">Handle prerendering</span></span>

<span data-ttu-id="039a2-245">Durante la representación previa:</span><span class="sxs-lookup"><span data-stu-id="039a2-245">During prerendering:</span></span>

* <span data-ttu-id="039a2-246">No existe ninguna conexión interactiva con el explorador del usuario.</span><span class="sxs-lookup"><span data-stu-id="039a2-246">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="039a2-247">El explorador todavía no tiene ninguna página en la que se pueda ejecutar código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="039a2-247">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="039a2-248">`localStorage` o `sessionStorage` no están disponibles durante la representación previa.</span><span class="sxs-lookup"><span data-stu-id="039a2-248">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="039a2-249">Si el componente intenta interactuar con el almacenamiento, se genera un error similar a:</span><span class="sxs-lookup"><span data-stu-id="039a2-249">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="039a2-250">No se pueden emitir llamadas de interoperabilidad de JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="039a2-250">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="039a2-251">Esto se debe a que el componente se está representando previamente.</span><span class="sxs-lookup"><span data-stu-id="039a2-251">This is because the component is being prerendered.</span></span>

<span data-ttu-id="039a2-252">Una manera de resolver el error es deshabilitar la representación previa.</span><span class="sxs-lookup"><span data-stu-id="039a2-252">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="039a2-253">Esta suele ser la mejor opción si la aplicación hace un uso intensivo del almacenamiento basado en explorador.</span><span class="sxs-lookup"><span data-stu-id="039a2-253">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="039a2-254">La representación previa agrega complejidad y no beneficia a la aplicación porque esta no puede representar previamente ningún contenido útil hasta que `localStorage` o `sessionStorage` estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="039a2-254">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="039a2-255">Para deshabilitar la representación previa, abra el archivo `Pages/_Host.cshtml` y cambie el valor de `render-mode` del [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) a <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span><span class="sxs-lookup"><span data-stu-id="039a2-255">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="039a2-256">La representación previa puede resultar útil para otras páginas que no utilizan `localStorage` o `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="039a2-256">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="039a2-257">Para mantener habilitada la representación previa, postergue la operación de carga hasta que el explorador esté conectado al circuito.</span><span class="sxs-lookup"><span data-stu-id="039a2-257">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="039a2-258">El siguiente es un ejemplo para almacenar un valor de contador:</span><span class="sxs-lookup"><span data-stu-id="039a2-258">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="039a2-259">Factorizar la preservación de estado en una ubicación común</span><span class="sxs-lookup"><span data-stu-id="039a2-259">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="039a2-260">Si muchos componentes se basan en el almacenamiento basado en explorador, al volver a implementar el código del proveedor de estado muchas veces se crea la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="039a2-260">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="039a2-261">Una opción para evitar la duplicación de código es crear un *componente primario del proveedor de estado* que encapsula la lógica del proveedor de estado.</span><span class="sxs-lookup"><span data-stu-id="039a2-261">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="039a2-262">Los componentes secundarios pueden trabajar con datos persistentes sin tener en cuenta el mecanismo de persistencia del estado.</span><span class="sxs-lookup"><span data-stu-id="039a2-262">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="039a2-263">En el siguiente ejemplo de un componente `CounterStateProvider`, se conservan los datos del contador:</span><span class="sxs-lookup"><span data-stu-id="039a2-263">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
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
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="039a2-264">El componente `CounterStateProvider` controla la fase de carga, ya que no representa su contenido secundario hasta que se completa la carga.</span><span class="sxs-lookup"><span data-stu-id="039a2-264">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="039a2-265">Para usar el componente `CounterStateProvider`, encapsule una instancia del componente en torno a cualquier otro componente que requiera acceso al estado del contador.</span><span class="sxs-lookup"><span data-stu-id="039a2-265">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="039a2-266">Para que el estado sea accesible a todos los componentes de una aplicación, encapsule el componente `CounterStateProvider` alrededor de <xref:Microsoft.AspNetCore.Components.Routing.Router> en el componente `App` (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="039a2-266">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="039a2-267">Los componentes encapsulados reciben y pueden modificar el estado persistente del contador.</span><span class="sxs-lookup"><span data-stu-id="039a2-267">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="039a2-268">El siguiente componente `Counter` implementa el patrón:</span><span class="sxs-lookup"><span data-stu-id="039a2-268">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="039a2-269">No es necesario que el componente anterior interactúe con `ProtectedBrowserStorage`, ni se trata de una fase de carga.</span><span class="sxs-lookup"><span data-stu-id="039a2-269">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="039a2-270">Para tratar la representación previa tal y como se ha descrito anteriormente, `CounterStateProvider` se puede modificar para que todos los componentes que consumen los datos del contador funcionen automáticamente con la representación previa.</span><span class="sxs-lookup"><span data-stu-id="039a2-270">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="039a2-271">Para obtener más información, consulte la sección [Controlar la representación previa](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="039a2-271">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="039a2-272">En general, se recomienda el patrón *componente primario del proveedor de estado*:</span><span class="sxs-lookup"><span data-stu-id="039a2-272">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="039a2-273">Para consumir el estado en muchos otros componentes.</span><span class="sxs-lookup"><span data-stu-id="039a2-273">To consume state in many other components.</span></span>
* <span data-ttu-id="039a2-274">Si solo hay un objeto de estado de nivel superior para conservar.</span><span class="sxs-lookup"><span data-stu-id="039a2-274">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="039a2-275">Para conservar muchos objetos de estado diferentes y consumir distintos subconjuntos de objetos en distintos lugares, es mejor evitar el control de la carga y el guardado de estado globalmente.</span><span class="sxs-lookup"><span data-stu-id="039a2-275">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
