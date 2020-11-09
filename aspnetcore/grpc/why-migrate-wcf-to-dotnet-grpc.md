---
title: Por qué migrar WCF a gRPC de ASP.NET Core
author: markrendle
description: En este artículo se ofrece un resumen de por qué el servicio gRPC de ASP.NET Core es una buena opción para los desarrolladores de Windows Communication Foundation (WCF) que quieren migrar a arquitecturas y plataformas modernas.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058693"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="c1e7e-103">gRPC para desarrolladores de Windows Communication Foundation (WCF)</span><span class="sxs-lookup"><span data-stu-id="c1e7e-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="c1e7e-104">En este artículo se ofrece un resumen de por qué el servicio gRPC de ASP.NET Core es una buena opción para los desarrolladores de Windows Communication Foundation (WCF) que quieren migrar a arquitecturas y plataformas modernas.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="c1e7e-105">Comparación con WCF</span><span class="sxs-lookup"><span data-stu-id="c1e7e-105">Comparison to WCF</span></span>

<span data-ttu-id="c1e7e-106">Aunque la implementación y el enfoque son diferentes para gRPC, la experiencia de desarrollo y consumo de servicios con gRPC debe ser intuitiva para los desarrolladores de WCF.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="c1e7e-107">WCF y gRPC son marcos de RPC (llamada a procedimiento remoto) con los mismos objetivos:</span><span class="sxs-lookup"><span data-stu-id="c1e7e-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="c1e7e-108">Hacer posible el código como si el cliente y el servidor estuvieran en la misma plataforma.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="c1e7e-109">Ofrecer una API de redes portátiles simplificada.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="c1e7e-110">Ambas plataformas comparten el requisito de declarar e implementar una interfaz, aunque el proceso para declarar la interfaz es diferente.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="c1e7e-111">Los muchos tipos de llamadas RPC que gRPC admite se asignan bien a los enlaces disponibles en servicios WCF.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="c1e7e-112">Para más información y ejemplos, consulte [Migración de una solución WCF a gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span><span class="sxs-lookup"><span data-stu-id="c1e7e-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="c1e7e-113">Ventajas de gRPC</span><span class="sxs-lookup"><span data-stu-id="c1e7e-113">Benefits of gRPC</span></span>

<span data-ttu-id="c1e7e-114">gRPC ofrece un marco mejor que otros enfoques por los siguientes motivos.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="c1e7e-115">Rendimiento</span><span class="sxs-lookup"><span data-stu-id="c1e7e-115">Performance</span></span>

<span data-ttu-id="c1e7e-116">gRPC usa HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="c1e7e-117">A diferencia de HTTP/1.1, HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="c1e7e-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="c1e7e-118">Es un protocolo binario más pequeño y más rápido.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="c1e7e-119">Es más eficaz para que los equipos lo analicen.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="c1e7e-120">Admite solicitudes de multiplexación a través de una única conexión.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="c1e7e-121">La multiplexación permite que se envíen varias solicitudes a través de una conexión sin que las solicitudes se bloqueen entre sí.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="c1e7e-122">En HTTP/1.1, el bloqueo se conoce como "bloqueo de encabezado de línea (HOL)".</span><span class="sxs-lookup"><span data-stu-id="c1e7e-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="c1e7e-123">gRPC usa Protobuf, un formato binario y eficaz, para serializar mensajes.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="c1e7e-124">Los mensajes de Protobuf:</span><span class="sxs-lookup"><span data-stu-id="c1e7e-124">Protobuf messages are:</span></span>
* <span data-ttu-id="c1e7e-125">Son rápidos para serializar y deserializar.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="c1e7e-126">Usan menos ancho de banda que los formatos basados en texto.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="c1e7e-127">gRPC es una buena solución para dispositivos móviles y redes con restricciones de ancho de banda.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="c1e7e-128">Interoperabilidad</span><span class="sxs-lookup"><span data-stu-id="c1e7e-128">Interoperability</span></span>

<span data-ttu-id="c1e7e-129">Hay herramientas y bibliotecas de gRPC para todos los lenguajes de programación y plataformas principales, como .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby y PHP.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="c1e7e-130">Gracias al formato de conexión binaria de Protobuf y la generación de código eficaz para cada plataforma, los desarrolladores pueden crear aplicaciones de rendimiento multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="c1e7e-131">Facilidad de uso y productividad</span><span class="sxs-lookup"><span data-stu-id="c1e7e-131">Usability and productivity</span></span>

<span data-ttu-id="c1e7e-132">gRPC es una solución completa de RPC.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="c1e7e-133">Funciona de forma coherente en varios lenguajes y plataformas.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="c1e7e-134">También ofrece excelentes herramientas, con gran parte del código reutilizable que se genera automáticamente.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="c1e7e-135">Como WCF, gRPC genera automáticamente mensajes y un cliente fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="c1e7e-136">Se libera tiempo al desarrollador para que se centre en la lógica empresarial.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="c1e7e-137">Streaming</span><span class="sxs-lookup"><span data-stu-id="c1e7e-137">Streaming</span></span>

<span data-ttu-id="c1e7e-138">gRPC tiene un streaming bidireccional completo, que proporciona una funcionalidad similar a los servicios dúplex completo de WCF.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="c1e7e-139">La transmisión por secuencias de gRPC puede funcionar a través de conexiones de Internet normales, equilibradores de carga y mallas de servicio.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="c1e7e-140">Fechas límite, tiempos de expiración y cancelación</span><span class="sxs-lookup"><span data-stu-id="c1e7e-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="c1e7e-141">gRPC permite a los clientes especificar el tiempo máximo en que un RPC debe finalizar.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="c1e7e-142">Si se supera la fecha límite especificada, el servidor puede cancelar la operación independientemente del cliente.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="c1e7e-143">Las fechas límite y cancelaciones se pueden propagar a través de las llamadas subsiguientes de gRPC para ayudar a aplicar los límites de uso de recursos.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="c1e7e-144">Los clientes pueden detener las operaciones cuando se supera una fecha límite, o antes, si es necesario.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="c1e7e-145">Por ejemplo, los clientes pueden detener las operaciones debido a una interacción del usuario.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="c1e7e-146">Seguridad</span><span class="sxs-lookup"><span data-stu-id="c1e7e-146">Security</span></span>

<span data-ttu-id="c1e7e-147">gRPC puede usar TLS y HTTP/2 para proporcionar una conexión cifrada de un extremo a otro entre el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="c1e7e-148">La compatibilidad con la autenticación de certificados de cliente aumenta aún más la seguridad y la confianza entre el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="c1e7e-149">gRPC como ruta de acceso de migración de WCF a .NET Core y .NET 5</span><span class="sxs-lookup"><span data-stu-id="c1e7e-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="c1e7e-150">.NET Core y .NET 5 marcan un cambio en la forma en que Microsoft ofrece soluciones de comunicación remota a los desarrolladores que quieren ofrecer servicios a través de una variedad de plataformas.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="c1e7e-151">.NET Core y .NET 5 admiten [llamadas a servicios WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), pero no ofrecen compatibilidad del lado servidor para hospedar WCF.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="c1e7e-152">Hay dos rutas de acceso recomendadas para modernizar aplicaciones WCF:</span><span class="sxs-lookup"><span data-stu-id="c1e7e-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="c1e7e-153">gRPC se basa en tecnologías modernas y ha surgido como la opción más popular en la comunidad de desarrolladores para aplicaciones RPC.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="c1e7e-154">A partir de .NET Core 3.0, las plataformas .NET modernas tienen una excelente compatibilidad con gRPC.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="c1e7e-155">La migración de servicios WCF para usar gRPC ayuda a proporcionar las características de RPC, el rendimiento, una interoperabilidad necesaria en las aplicaciones modernas.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="c1e7e-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) es un esfuerzo de la comunidad por ofrecer compatibilidad con el hospedaje de servicios WCF en .NET Core y .NET 5.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="c1e7e-157">Hay disponible una versión preliminar y el proyecto avanza para estar preparado para la producción.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="c1e7e-158">CoreWCF solo admite un subconjunto de características de WCF y las aplicaciones de .NET Framework que se migran para usarlas necesitarán cambios en el código y pruebas para que se realicen correctamente.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="c1e7e-159">CoreWCF es una buena opción si una aplicación tiene que mantener la compatibilidad con los clientes existentes que llaman a servicios WCF.</span><span class="sxs-lookup"><span data-stu-id="c1e7e-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="c1e7e-160">Introducción</span><span class="sxs-lookup"><span data-stu-id="c1e7e-160">Get started</span></span>

<span data-ttu-id="c1e7e-161">Para obtener instrucciones detalladas sobre cómo crear servicios gRPC en ASP.NET Core para desarrolladores de WCF, consulte [gRPC de ASP.NET Core para desarrolladores de WCF](/dotnet/architecture/grpc-for-wcf-developers).</span><span class="sxs-lookup"><span data-stu-id="c1e7e-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
