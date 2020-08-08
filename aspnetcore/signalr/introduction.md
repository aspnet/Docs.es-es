---
title: Introducción a ASP.NET Core SignalR
author: bradygaster
description: Obtenga información sobre cómo la biblioteca de ASP.NET Core SignalR simplifica la incorporación de funcionalidad en tiempo real a las aplicaciones.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
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
uid: signalr/introduction
ms.openlocfilehash: 6f3cec83c9af5ec6e820db4a15061eddac613f36
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022178"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a><span data-ttu-id="9a254-103">Introducción a ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="9a254-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-no-locsignalr"></a><span data-ttu-id="9a254-104">¿Qué es SignalR?</span><span class="sxs-lookup"><span data-stu-id="9a254-104">What is SignalR?</span></span>

<span data-ttu-id="9a254-105">ASP.NET Core SignalR es una biblioteca de código abierto que simplifica la incorporación de funcionalidades Web en tiempo real a las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="9a254-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="9a254-106">La funcionalidad web en tiempo real permite que el código del lado servidor Inserte contenido a los clientes al instante.</span><span class="sxs-lookup"><span data-stu-id="9a254-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="9a254-107">Buenos candidatos para SignalR :</span><span class="sxs-lookup"><span data-stu-id="9a254-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="9a254-108">Aplicaciones que requieren actualizaciones desde el servidor con mucha frecuencia.</span><span class="sxs-lookup"><span data-stu-id="9a254-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="9a254-109">Algunos ejemplos son juegos, redes sociales, aplicaciones de votación, subastas, mapas y GPS.</span><span class="sxs-lookup"><span data-stu-id="9a254-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="9a254-110">Paneles y aplicaciones de supervisión.</span><span class="sxs-lookup"><span data-stu-id="9a254-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="9a254-111">Los ejemplos incluyen paneles empresariales, actualizaciones de venta instantáneas o alertas de viaje.</span><span class="sxs-lookup"><span data-stu-id="9a254-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="9a254-112">Aplicaciones de colaboración.</span><span class="sxs-lookup"><span data-stu-id="9a254-112">Collaborative apps.</span></span> <span data-ttu-id="9a254-113">Las aplicaciones de pizarra y el software de reuniones de equipo son ejemplos de aplicaciones de colaboración.</span><span class="sxs-lookup"><span data-stu-id="9a254-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="9a254-114">Aplicaciones que requieren notificaciones.</span><span class="sxs-lookup"><span data-stu-id="9a254-114">Apps that require notifications.</span></span> <span data-ttu-id="9a254-115">Redes sociales, correo electrónico, chat, juegos, alertas de viaje y muchas otras aplicaciones utilizan notificaciones.</span><span class="sxs-lookup"><span data-stu-id="9a254-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="9a254-116">SignalRproporciona una API para crear [llamadas a procedimiento remoto (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)de servidor a cliente.</span><span class="sxs-lookup"><span data-stu-id="9a254-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="9a254-117">Las RPC llaman a funciones de JavaScript en los clientes desde el código de .NET Core del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="9a254-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="9a254-118">Estas son algunas características de SignalR para ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="9a254-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="9a254-119">Controla la administración de conexiones automáticamente.</span><span class="sxs-lookup"><span data-stu-id="9a254-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="9a254-120">Envía mensajes a todos los clientes conectados simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="9a254-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="9a254-121">Por ejemplo, un salón de chat.</span><span class="sxs-lookup"><span data-stu-id="9a254-121">For example, a chat room.</span></span>
* <span data-ttu-id="9a254-122">Envía mensajes a clientes o grupos de clientes específicos.</span><span class="sxs-lookup"><span data-stu-id="9a254-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="9a254-123">Escala para controlar el aumento del tráfico.</span><span class="sxs-lookup"><span data-stu-id="9a254-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="9a254-124">El origen se hospeda en un [ SignalR repositorio de github](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span><span class="sxs-lookup"><span data-stu-id="9a254-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="9a254-125">Transportes</span><span class="sxs-lookup"><span data-stu-id="9a254-125">Transports</span></span>

<span data-ttu-id="9a254-126">SignalRadmite las siguientes técnicas para controlar la comunicación en tiempo real (en orden de reserva correcta):</span><span class="sxs-lookup"><span data-stu-id="9a254-126">SignalR supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="9a254-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="9a254-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="9a254-128">Eventos enviados por el servidor</span><span class="sxs-lookup"><span data-stu-id="9a254-128">Server-Sent Events</span></span>
* <span data-ttu-id="9a254-129">Sondeo largo</span><span class="sxs-lookup"><span data-stu-id="9a254-129">Long Polling</span></span>

<span data-ttu-id="9a254-130">SignalRelige automáticamente el mejor método de transporte que se encuentra dentro de las capacidades del servidor y del cliente.</span><span class="sxs-lookup"><span data-stu-id="9a254-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="9a254-131">Concentradores</span><span class="sxs-lookup"><span data-stu-id="9a254-131">Hubs</span></span>

<span data-ttu-id="9a254-132">SignalRusa *hubs* para la comunicación entre clientes y servidores.</span><span class="sxs-lookup"><span data-stu-id="9a254-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="9a254-133">Un concentrador es una canalización de alto nivel que permite a un cliente y un servidor llamar a métodos entre sí.</span><span class="sxs-lookup"><span data-stu-id="9a254-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="9a254-134">SignalRcontrola el envío automático de los límites de la máquina, lo que permite a los clientes llamar a métodos en el servidor y viceversa.</span><span class="sxs-lookup"><span data-stu-id="9a254-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="9a254-135">Puede pasar parámetros fuertemente tipados a métodos, lo que permite el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="9a254-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="9a254-136">SignalRproporciona dos protocolos de concentrador integrados: un protocolo de texto basado en JSON y un protocolo binario basado en [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="9a254-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="9a254-137">MessagePack suele crear mensajes más pequeños en comparación con JSON.</span><span class="sxs-lookup"><span data-stu-id="9a254-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="9a254-138">Los exploradores más antiguos deben admitir el [nivel 2 de XHR](https://caniuse.com/#feat=xhr2) para proporcionar compatibilidad con el protocolo MessagePack.</span><span class="sxs-lookup"><span data-stu-id="9a254-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="9a254-139">Los concentradores llaman a código de cliente mediante el envío de mensajes que contienen el nombre y los parámetros del método del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="9a254-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="9a254-140">Los objetos enviados como parámetros de método se deserializan mediante el protocolo configurado.</span><span class="sxs-lookup"><span data-stu-id="9a254-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="9a254-141">El cliente intenta hacer coincidir el nombre con un método en el código del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="9a254-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="9a254-142">Cuando el cliente encuentra una coincidencia, llama al método y le pasa los datos del parámetro deserializado.</span><span class="sxs-lookup"><span data-stu-id="9a254-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9a254-143">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9a254-143">Additional resources</span></span>

* [<span data-ttu-id="9a254-144">Introducción a SignalR for ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="9a254-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="9a254-145">Plataformas compatibles</span><span class="sxs-lookup"><span data-stu-id="9a254-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="9a254-146">Concentradores</span><span class="sxs-lookup"><span data-stu-id="9a254-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="9a254-147">Cliente de JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a254-147">JavaScript client</span></span>](xref:signalr/javascript-client)
