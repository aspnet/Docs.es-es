---
title: Implementaciones de servidores web en ASP.NET Core
author: rick-anderson
description: Detecte los servidores web Kestrel y HTTP.sys de ASP.NET Core. Obtenga más información sobre cómo elegir un servidor y cuándo se debe usar un servidor proxy inverso.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 2acddd212639ac0a82b3c46f2225ff66d0999dd0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217562"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="a7dc8-104">Implementaciones de servidores web en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7dc8-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="a7dc8-105">Por [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) y [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="a7dc8-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="a7dc8-106">Una aplicación ASP.NET Core se ejecuta con una implementación de servidor HTTP en proceso.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="a7dc8-107">La implementación del servidor realiza escuchas de solicitudes HTTP y las muestra en la aplicación como un conjunto de [características de solicitud](xref:fundamentals/request-features) compuestos en un <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="a7dc8-108">Windows</span><span class="sxs-lookup"><span data-stu-id="a7dc8-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="a7dc8-109">ASP.NET Core se suministra con los siguientes componentes:</span><span class="sxs-lookup"><span data-stu-id="a7dc8-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="a7dc8-110">El servidor [Kestrel](xref:fundamentals/servers/kestrel) es la implementación de servidor HTTP multiplataforma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="a7dc8-111">Kestrel proporciona el mejor rendimiento y uso de memoria, pero carece de algunas de las características avanzadas de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-111">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="a7dc8-112">Para obtener más información, vea [Diferencias entre Kestrel y HTTP.sys](#korh) en este documento.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-112">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>
* <span data-ttu-id="a7dc8-113">El servidor HTTP de IIS es un [servidor en proceso](#hosting-models) de IIS.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-113">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="a7dc8-114">El [servidor HTTP.sys](xref:fundamentals/servers/httpsys) es un servidor HTTP solo de Windows basado en el [controlador del kernel HTTP.sys y la API de servidor HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="a7dc8-115">Cuando se usa [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), la aplicación se ejecuta:</span><span class="sxs-lookup"><span data-stu-id="a7dc8-115">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="a7dc8-116">En el mismo proceso que el proceso de trabajo de IIS (el [modelo de hospedaje dentro de proceso](#hosting-models)) con el servidor HTTP de IIS.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-116">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="a7dc8-117">La configuración recomendada es *En proceso*.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-117">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="a7dc8-118">En un proceso distinto al del proceso de trabajo de IIS (el [modelo de hospedaje fuera de proceso](#hosting-models)) con el [servidor Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-118">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="a7dc8-119">El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) es un módulo nativo de IIS que controla las solicitudes de IIS nativas entre IIS y el servidor de IIS en proceso o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-119">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="a7dc8-120">Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-120">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a><span data-ttu-id="a7dc8-121">Diferencias entre Kestrel y HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7dc8-121">Kestrel vs. HTTP.sys</span></span>

<span data-ttu-id="a7dc8-122">Kestrel tiene las ventajas siguientes con respecto a HTTP.sys:</span><span class="sxs-lookup"><span data-stu-id="a7dc8-122">Kestrel has the following advantages over HTTP.sys:</span></span>

  * <span data-ttu-id="a7dc8-123">Mejor rendimiento y uso de memoria</span><span class="sxs-lookup"><span data-stu-id="a7dc8-123">Better performance and memory utilization.</span></span>
  * <span data-ttu-id="a7dc8-124">Multiplataforma</span><span class="sxs-lookup"><span data-stu-id="a7dc8-124">Cross platform</span></span>
  * <span data-ttu-id="a7dc8-125">Agilidad, ya que se desarrolla y se revisa de forma independiente al sistema operativo</span><span class="sxs-lookup"><span data-stu-id="a7dc8-125">Agility, it's developed and patched independent of the OS.</span></span>
  * <span data-ttu-id="a7dc8-126">Configuración de puertos y TLS mediante programación</span><span class="sxs-lookup"><span data-stu-id="a7dc8-126">Programmatic port and TLS configuration</span></span>
  * <span data-ttu-id="a7dc8-127">Extensibilidad que permite protocolos como [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) y transportes alternativos</span><span class="sxs-lookup"><span data-stu-id="a7dc8-127">Extensibility that allows for protocols like [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) and alternate transports.</span></span>

<span data-ttu-id="a7dc8-128">HTTP.sys funciona como un componente de modo de kernel compartido con las siguientes características de las que carece Kestrel:</span><span class="sxs-lookup"><span data-stu-id="a7dc8-128">Http.Sys operates as a shared kernel mode component with the following features that kestrel does not have:</span></span>

  * <span data-ttu-id="a7dc8-129">Uso compartido de puertos</span><span class="sxs-lookup"><span data-stu-id="a7dc8-129">Port sharing</span></span>
  * <span data-ttu-id="a7dc8-130">Autenticación de Windows de modo de kernel;</span><span class="sxs-lookup"><span data-stu-id="a7dc8-130">Kernel mode windows authentication.</span></span> <span data-ttu-id="a7dc8-131">[Kestrel solo admite la autenticación de modo usuario](xref:security/authentication/windowsauth#kestrel)</span><span class="sxs-lookup"><span data-stu-id="a7dc8-131">[Kestrel supports only user-mode authentication](xref:security/authentication/windowsauth#kestrel).</span></span>
  * <span data-ttu-id="a7dc8-132">Proxy rápido mediante transferencias de colas</span><span class="sxs-lookup"><span data-stu-id="a7dc8-132">Fast proxying via queue transfers</span></span>
  * <span data-ttu-id="a7dc8-133">Transmisión directa de archivos</span><span class="sxs-lookup"><span data-stu-id="a7dc8-133">Direct file transmission</span></span>
  * <span data-ttu-id="a7dc8-134">Almacenamiento en caché de respuestas</span><span class="sxs-lookup"><span data-stu-id="a7dc8-134">Response caching</span></span>

## <a name="hosting-models"></a><span data-ttu-id="a7dc8-135">Modelos de hospedaje</span><span class="sxs-lookup"><span data-stu-id="a7dc8-135">Hosting models</span></span>

<span data-ttu-id="a7dc8-136">Con el hospedaje en proceso, una aplicación ASP.NET Core se ejecuta en el mismo proceso que su proceso de trabajo de IIS.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-136">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="a7dc8-137">El hospedaje en proceso proporciona un rendimiento mejorado con respecto al hospedaje fuera de proceso porque las solicitudes no se realizan mediante proxy en el adaptador de bucle invertido, una interfaz de red que devuelve el tráfico saliente a la misma máquina.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-137">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="a7dc8-138">IIS controla la administración de procesos con el [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-138">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="a7dc8-139">Con el hospedaje fuera de proceso, las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, y el módulo se encarga de la administración de procesos.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-139">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="a7dc8-140">El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud y reinicia la aplicación si esta se apaga o se bloquea.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-140">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="a7dc8-141">Este comportamiento es básicamente el mismo que el de las aplicaciones que se ejecutan en proceso y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-141">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="a7dc8-142">Para obtener más información e instrucciones de configuración, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="a7dc8-142">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="a7dc8-143">macOS</span><span class="sxs-lookup"><span data-stu-id="a7dc8-143">macOS</span></span>](#tab/macos)

<span data-ttu-id="a7dc8-144">ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-144">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="a7dc8-145">Linux</span><span class="sxs-lookup"><span data-stu-id="a7dc8-145">Linux</span></span>](#tab/linux)

<span data-ttu-id="a7dc8-146">ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-146">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="a7dc8-147">Kestrel</span><span class="sxs-lookup"><span data-stu-id="a7dc8-147">Kestrel</span></span>

 <span data-ttu-id="a7dc8-148">El servidor [Kestrel](xref:fundamentals/servers/kestrel) es la implementación de servidor HTTP multiplataforma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-148">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="a7dc8-149">Kestrel proporciona el mejor rendimiento y uso de memoria, pero carece de algunas de las características avanzadas de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-149">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="a7dc8-150">Para obtener más información, vea [Diferencias entre Kestrel y HTTP.sys](#korh) en este documento.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-150">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>

<span data-ttu-id="a7dc8-151">Use Kestrel:</span><span class="sxs-lookup"><span data-stu-id="a7dc8-151">Use Kestrel:</span></span>

* <span data-ttu-id="a7dc8-152">Por sí solo como un servidor perimetral que procesa solicitudes directamente desde una red, incluida Internet.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-152">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel se comunica directamente con Internet sin ningún servidor proxy inverso](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="a7dc8-154">Con un *servidor proxy inverso*, tal como [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-154">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="a7dc8-155">Un servidor proxy inverso recibe las solicitudes HTTP de Internet y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-155">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel se comunica indirectamente con Internet a través de un servidor proxy inverso, como IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="a7dc8-157">Se admite cualquiera de las configuraciones de hospedaje &mdash;con o sin un servidor proxy inverso&mdash;.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-157">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="a7dc8-158">Si quiere obtener instrucciones e información sobre la configuración de Kestrel y su uso en una configuración de proxy inverso, vea <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-158">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="a7dc8-159">Windows</span><span class="sxs-lookup"><span data-stu-id="a7dc8-159">Windows</span></span>](#tab/windows)

<span data-ttu-id="a7dc8-160">ASP.NET Core se suministra con los siguientes componentes:</span><span class="sxs-lookup"><span data-stu-id="a7dc8-160">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="a7dc8-161">El servidor [Kestrel](xref:fundamentals/servers/kestrel) es el servidor HTTP multiplataforma predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-161">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="a7dc8-162">El [servidor HTTP.sys](xref:fundamentals/servers/httpsys) es un servidor HTTP solo de Windows basado en el [controlador del kernel HTTP.sys y la API de servidor HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-162">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="a7dc8-163">Al usar [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), la aplicación se ejecuta en un proceso distinto al del proceso de trabajo de IIS (*fuera de proceso*) con el [servidor Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-163">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="a7dc8-164">Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo se encarga de la administración de procesos.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-164">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="a7dc8-165">El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud y reinicia la aplicación si esta se apaga o se bloquea.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-165">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="a7dc8-166">Este comportamiento es básicamente el mismo que el de las aplicaciones que se ejecutan en proceso y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-166">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="a7dc8-167">En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada fuera de proceso:</span><span class="sxs-lookup"><span data-stu-id="a7dc8-167">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="a7dc8-169">Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-169">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="a7dc8-170">El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-170">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="a7dc8-171">El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación, que no es ni 80 ni 443.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-171">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="a7dc8-172">El módulo especifica el puerto a través de la variable de entorno en el inicio, y el [middleware de integración de IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura el servidor para que escuche en `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-172">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="a7dc8-173">Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-173">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="a7dc8-174">El módulo no admite el reenvío de HTTPS, por lo que las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-174">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="a7dc8-175">Una vez que Kestrel toma la solicitud del módulo, la envía a la canalización de middleware de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-175">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="a7dc8-176">La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-176">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="a7dc8-177">El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-177">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="a7dc8-178">La respuesta de la aplicación se vuelve a pasar a IIS, que la envía de nuevo al cliente HTTP que inició la solicitud.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-178">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="a7dc8-179">Para obtener instrucciones de configuración para IIS y el módulo ASP.NET Core, consulte los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="a7dc8-179">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="a7dc8-180">macOS</span><span class="sxs-lookup"><span data-stu-id="a7dc8-180">macOS</span></span>](#tab/macos)

<span data-ttu-id="a7dc8-181">ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-181">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="a7dc8-182">Linux</span><span class="sxs-lookup"><span data-stu-id="a7dc8-182">Linux</span></span>](#tab/linux)

<span data-ttu-id="a7dc8-183">ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-183">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="a7dc8-184">Nginx con Kestrel</span><span class="sxs-lookup"><span data-stu-id="a7dc8-184">Nginx with Kestrel</span></span>

<span data-ttu-id="a7dc8-185">Para información sobre cómo usar Nginx en Linux como servidor proxy inverso para Kestrel, consulte <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-185">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="a7dc8-186">Apache con Kestrel</span><span class="sxs-lookup"><span data-stu-id="a7dc8-186">Apache with Kestrel</span></span>

<span data-ttu-id="a7dc8-187">Para información sobre cómo usar Apache en Linux como servidor proxy inverso para Kestrel, consulte <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-187">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="a7dc8-188">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7dc8-188">HTTP.sys</span></span>

<span data-ttu-id="a7dc8-189">Si las aplicaciones ASP.NET Core se ejecutan en Windows, HTTP.sys es una alternativa a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-189">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="a7dc8-190">Se recomienda Kestrel antes que HTTP.sys, salvo si la aplicación necesita características que no están disponibles en Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-190">Kestrel is recommended over HTTP.sys unless the app requires features not available in Kestrel.</span></span> <span data-ttu-id="a7dc8-191">Para obtener más información, vea <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-191">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys se comunica directamente con Internet](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="a7dc8-193">HTTP.sys también se puede usar para las aplicaciones que solo se exponen a una red interna.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-193">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys se comunica directamente con la red interna](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="a7dc8-195">Para obtener instrucciones de configuración de HTTP.sys, vea <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-195">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="a7dc8-196">Infraestructura de servidores de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7dc8-196">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="a7dc8-197">El elemento <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> disponible en el método `Startup.Configure` expone la propiedad <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> del tipo <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-197">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="a7dc8-198">Kestrel y HTTP.sys solo exponen una característica cada uno (<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>), pero otras implementaciones de servidor pueden exponer funcionalidades adicionales.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-198">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="a7dc8-199">Se puede usar `IServerAddressesFeature` para averiguar a qué puerto se ha enlazado la implementación del servidor en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-199">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="a7dc8-200">Servidores personalizados</span><span class="sxs-lookup"><span data-stu-id="a7dc8-200">Custom servers</span></span>

<span data-ttu-id="a7dc8-201">Si los servidores integrados no cumplen los requisitos de la aplicación, se puede crear una implementación de servidor personalizado.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-201">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="a7dc8-202">En la [guía de Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) se muestra cómo escribir una implementación de <xref:Microsoft.AspNetCore.Hosting.Server.IServer> basada en [Nowin](https://github.com/Bobris/Nowin).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-202">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="a7dc8-203">Solo las interfaces de la característica que usa la aplicación requieren implementación, aunque como mínimo se debe admitir <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> y <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-203">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="a7dc8-204">Inicio del servidor</span><span class="sxs-lookup"><span data-stu-id="a7dc8-204">Server startup</span></span>

<span data-ttu-id="a7dc8-205">El servidor se inicia cuando el entorno de desarrollo integrado (IDE) o editor inicia la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a7dc8-205">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="a7dc8-206">[Visual Studio](https://visualstudio.microsoft.com): los perfiles de inicio se pueden usar para iniciar la aplicación y el servidor con [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[Módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) o la consola.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-206">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="a7dc8-207">[Visual Studio Code](https://code.visualstudio.com/): la aplicación y el servidor se inician mediante [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), con lo que se activa el depurador CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-207">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="a7dc8-208">[Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/): la aplicación y el servidor se inician mediante [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-208">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="a7dc8-209">Al iniciar la aplicación desde un símbolo del sistema en la carpeta del proyecto, [dotnet run](/dotnet/core/tools/dotnet-run) inicia la aplicación y el servidor (solo Kestrel y HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-209">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="a7dc8-210">La configuración se especifica mediante la opción `-c|--configuration`, que está establecida en `Debug` (valor predeterminado) o `Release`.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-210">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="a7dc8-211">Un archivo *launchSettings.json* proporciona la configuración al iniciar una aplicación con `dotnet run` o con un depurador integrado en las herramientas, como Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-211">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="a7dc8-212">Si hay perfiles de inicio en un archivo *launchSettings.json*, use la opción `--launch-profile {PROFILE NAME}` con el comando `dotnet run` o seleccione el perfil en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-212">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="a7dc8-213">Para más información, vea [dotnet run](/dotnet/core/tools/dotnet-run) y [Empaquetado de distribución de .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="a7dc8-213">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="a7dc8-214">Compatibilidad con HTTP/2</span><span class="sxs-lookup"><span data-stu-id="a7dc8-214">HTTP/2 support</span></span>

<span data-ttu-id="a7dc8-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html) es compatible con ASP.NET Core en los escenarios de implementación siguientes:</span><span class="sxs-lookup"><span data-stu-id="a7dc8-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="a7dc8-216">Kestrel</span><span class="sxs-lookup"><span data-stu-id="a7dc8-216">Kestrel</span></span>](xref:fundamentals/servers/kestrel/http2)
  * <span data-ttu-id="a7dc8-217">Sistema operativo</span><span class="sxs-lookup"><span data-stu-id="a7dc8-217">Operating system</span></span>
    * <span data-ttu-id="a7dc8-218">Windows Server 2016/Windows 10 o posterior&dagger;</span><span class="sxs-lookup"><span data-stu-id="a7dc8-218">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="a7dc8-219">Linux con OpenSSL 1.0.2 o posterior (por ejemplo, Ubuntu 16.04 o posterior)</span><span class="sxs-lookup"><span data-stu-id="a7dc8-219">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="a7dc8-220">HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-220">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="a7dc8-221">Plataforma de destino: .NET Core 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-221">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="a7dc8-222">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7dc8-222">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="a7dc8-223">Windows Server 2016/Windows 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-223">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="a7dc8-224">Marco de destino: No aplicable a implementaciones de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-224">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="a7dc8-225">IIS (en proceso)</span><span class="sxs-lookup"><span data-stu-id="a7dc8-225">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="a7dc8-226">Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-226">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="a7dc8-227">Plataforma de destino: .NET Core 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-227">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="a7dc8-228">IIS (fuera de proceso)</span><span class="sxs-lookup"><span data-stu-id="a7dc8-228">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="a7dc8-229">Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-229">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="a7dc8-230">Las conexiones de servidor perimetral de acceso público usan HTTP/2, pero la conexión de proxy inverso a Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-230">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="a7dc8-231">Marco de destino: No aplicable a implementaciones fuera de proceso de IIS.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-231">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="a7dc8-232">&dagger;Kestrel tiene compatibilidad limitada para HTTP/2 en Windows Server 2012 R2 y Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-232">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="a7dc8-233">La compatibilidad es limitada porque la lista de conjuntos de cifrado TLS admitidos y disponibles en estos sistemas operativos está limitada.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-233">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="a7dc8-234">Se puede requerir un certificado generado mediante Elliptic Curve Digital Signature Algorithm (ECDSA) para proteger las conexiones TLS.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-234">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [<span data-ttu-id="a7dc8-235">Kestrel</span><span class="sxs-lookup"><span data-stu-id="a7dc8-235">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="a7dc8-236">Sistema operativo</span><span class="sxs-lookup"><span data-stu-id="a7dc8-236">Operating system</span></span>
    * <span data-ttu-id="a7dc8-237">Windows Server 2016/Windows 10 o posterior&dagger;</span><span class="sxs-lookup"><span data-stu-id="a7dc8-237">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="a7dc8-238">Linux con OpenSSL 1.0.2 o posterior (por ejemplo, Ubuntu 16.04 o posterior)</span><span class="sxs-lookup"><span data-stu-id="a7dc8-238">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="a7dc8-239">HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-239">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="a7dc8-240">Plataforma de destino: .NET Core 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-240">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="a7dc8-241">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7dc8-241">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="a7dc8-242">Windows Server 2016/Windows 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-242">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="a7dc8-243">Marco de destino: No aplicable a implementaciones de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-243">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="a7dc8-244">IIS (en proceso)</span><span class="sxs-lookup"><span data-stu-id="a7dc8-244">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="a7dc8-245">Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-245">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="a7dc8-246">Plataforma de destino: .NET Core 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-246">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="a7dc8-247">IIS (fuera de proceso)</span><span class="sxs-lookup"><span data-stu-id="a7dc8-247">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="a7dc8-248">Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-248">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="a7dc8-249">Las conexiones de servidor perimetral de acceso público usan HTTP/2, pero la conexión de proxy inverso a Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-249">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="a7dc8-250">Marco de destino: No aplicable a implementaciones fuera de proceso de IIS.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-250">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="a7dc8-251">&dagger;Kestrel tiene compatibilidad limitada para HTTP/2 en Windows Server 2012 R2 y Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-251">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="a7dc8-252">La compatibilidad es limitada porque la lista de conjuntos de cifrado TLS admitidos y disponibles en estos sistemas operativos está limitada.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-252">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="a7dc8-253">Se puede requerir un certificado generado mediante Elliptic Curve Digital Signature Algorithm (ECDSA) para proteger las conexiones TLS.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-253">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="a7dc8-254">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7dc8-254">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="a7dc8-255">Windows Server 2016/Windows 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-255">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="a7dc8-256">Marco de destino: No aplicable a implementaciones de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-256">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="a7dc8-257">IIS (fuera de proceso)</span><span class="sxs-lookup"><span data-stu-id="a7dc8-257">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="a7dc8-258">Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="a7dc8-258">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="a7dc8-259">Las conexiones de servidor perimetral de acceso público usan HTTP/2, pero la conexión de proxy inverso a Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-259">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="a7dc8-260">Marco de destino: No aplicable a implementaciones fuera de proceso de IIS.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-260">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="a7dc8-261">Una conexión HTTP/2 debe usar [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) y TLS 1.2 o posterior.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-261">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="a7dc8-262">Para más información, vea los temas que pertenecen a los escenarios de implementación de servidor.</span><span class="sxs-lookup"><span data-stu-id="a7dc8-262">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7dc8-263">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a7dc8-263">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
