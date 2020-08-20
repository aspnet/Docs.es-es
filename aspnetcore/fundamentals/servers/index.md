---
title: Implementaciones de servidores web en ASP.NET Core
author: rick-anderson
description: Detecte los servidores web Kestrel y HTTP.sys de ASP.NET Core. Obtenga más información sobre cómo elegir un servidor y cuándo se debe usar un servidor proxy inverso.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
no-loc:
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
ms.openlocfilehash: 8dde2d663f4eac94173038eaf7df6b95d67ba62e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635039"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="ad112-104">Implementaciones de servidores web en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ad112-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="ad112-105">Por [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) y [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="ad112-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="ad112-106">Una aplicación ASP.NET Core se ejecuta con una implementación de servidor HTTP en proceso.</span><span class="sxs-lookup"><span data-stu-id="ad112-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="ad112-107">La implementación del servidor realiza escuchas de solicitudes HTTP y las muestra en la aplicación como un conjunto de [características de solicitud](xref:fundamentals/request-features) compuestos en un <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="ad112-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="ad112-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="ad112-108">Kestrel</span></span>

<span data-ttu-id="ad112-109">Kestrel es el servidor web predeterminado que se especifica en las plantillas de proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad112-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="ad112-110">Use Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ad112-110">Use Kestrel:</span></span>

* <span data-ttu-id="ad112-111">Por sí solo como un servidor perimetral que procesa solicitudes directamente desde una red, incluida Internet.</span><span class="sxs-lookup"><span data-stu-id="ad112-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel se comunica directamente con Internet sin ningún servidor proxy inverso](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="ad112-113">Con un *servidor proxy inverso*, tal como [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ad112-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="ad112-114">Un servidor proxy inverso recibe las solicitudes HTTP de Internet y las reenvía a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ad112-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel se comunica indirectamente con Internet a través de un servidor proxy inverso, como IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ad112-116">Se admite cualquiera de las configuraciones de hospedaje &mdash;con o sin un servidor proxy inverso&mdash;.</span><span class="sxs-lookup"><span data-stu-id="ad112-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="ad112-117">Si quiere obtener instrucciones e información sobre la configuración de Kestrel y su uso en una configuración de proxy inverso, vea <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="ad112-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="ad112-118">Windows</span><span class="sxs-lookup"><span data-stu-id="ad112-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="ad112-119">ASP.NET Core se suministra con los siguientes componentes:</span><span class="sxs-lookup"><span data-stu-id="ad112-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="ad112-120">El servidor [Kestrel](xref:fundamentals/servers/kestrel) es la implementación de servidor HTTP multiplataforma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="ad112-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="ad112-121">El servidor HTTP de IIS es un [servidor en proceso](#hosting-models) de IIS.</span><span class="sxs-lookup"><span data-stu-id="ad112-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="ad112-122">El [servidor HTTP.sys](xref:fundamentals/servers/httpsys) es un servidor HTTP solo de Windows basado en el [controlador del kernel HTTP.sys y la API de servidor HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="ad112-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="ad112-123">Cuando se usa [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), la aplicación se ejecuta:</span><span class="sxs-lookup"><span data-stu-id="ad112-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="ad112-124">En el mismo proceso que el proceso de trabajo de IIS (el [modelo de hospedaje dentro de proceso](#hosting-models)) con el servidor HTTP de IIS.</span><span class="sxs-lookup"><span data-stu-id="ad112-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="ad112-125">La configuración recomendada es *En proceso*.</span><span class="sxs-lookup"><span data-stu-id="ad112-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="ad112-126">En un proceso distinto al del proceso de trabajo de IIS (el [modelo de hospedaje fuera de proceso](#hosting-models)) con el [servidor Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="ad112-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="ad112-127">El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) es un módulo nativo de IIS que controla las solicitudes de IIS nativas entre IIS y el servidor de IIS en proceso o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ad112-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="ad112-128">Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="ad112-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="ad112-129">Modelos de hospedaje</span><span class="sxs-lookup"><span data-stu-id="ad112-129">Hosting models</span></span>

<span data-ttu-id="ad112-130">Con el hospedaje en proceso, una aplicación ASP.NET Core se ejecuta en el mismo proceso que su proceso de trabajo de IIS.</span><span class="sxs-lookup"><span data-stu-id="ad112-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="ad112-131">El hospedaje en proceso proporciona un rendimiento mejorado con respecto al hospedaje fuera de proceso porque las solicitudes no se realizan mediante proxy en el adaptador de bucle invertido, una interfaz de red que devuelve el tráfico saliente a la misma máquina.</span><span class="sxs-lookup"><span data-stu-id="ad112-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="ad112-132">IIS controla la administración de procesos con el [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ad112-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ad112-133">Con el hospedaje fuera de proceso, las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, y el módulo se encarga de la administración de procesos.</span><span class="sxs-lookup"><span data-stu-id="ad112-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="ad112-134">El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud y reinicia la aplicación si esta se apaga o se bloquea.</span><span class="sxs-lookup"><span data-stu-id="ad112-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="ad112-135">Este comportamiento es básicamente el mismo que el de las aplicaciones que se ejecutan en proceso y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ad112-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ad112-136">Para obtener más información e instrucciones de configuración, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="ad112-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="ad112-137">macOS</span><span class="sxs-lookup"><span data-stu-id="ad112-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="ad112-138">ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.</span><span class="sxs-lookup"><span data-stu-id="ad112-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="ad112-139">Linux</span><span class="sxs-lookup"><span data-stu-id="ad112-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="ad112-140">ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.</span><span class="sxs-lookup"><span data-stu-id="ad112-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="ad112-141">Windows</span><span class="sxs-lookup"><span data-stu-id="ad112-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="ad112-142">ASP.NET Core se suministra con los siguientes componentes:</span><span class="sxs-lookup"><span data-stu-id="ad112-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="ad112-143">El servidor [Kestrel](xref:fundamentals/servers/kestrel) es el servidor HTTP multiplataforma predeterminado.</span><span class="sxs-lookup"><span data-stu-id="ad112-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="ad112-144">El [servidor HTTP.sys](xref:fundamentals/servers/httpsys) es un servidor HTTP solo de Windows basado en el [controlador del kernel HTTP.sys y la API de servidor HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="ad112-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="ad112-145">Al usar [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), la aplicación se ejecuta en un proceso distinto al del proceso de trabajo de IIS (*fuera de proceso*) con el [servidor Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="ad112-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="ad112-146">Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo se encarga de la administración de procesos.</span><span class="sxs-lookup"><span data-stu-id="ad112-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="ad112-147">El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud y reinicia la aplicación si esta se apaga o se bloquea.</span><span class="sxs-lookup"><span data-stu-id="ad112-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="ad112-148">Este comportamiento es básicamente el mismo que el de las aplicaciones que se ejecutan en proceso y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ad112-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ad112-149">En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada fuera de proceso:</span><span class="sxs-lookup"><span data-stu-id="ad112-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="ad112-151">Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel.</span><span class="sxs-lookup"><span data-stu-id="ad112-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="ad112-152">El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ad112-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="ad112-153">El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación, que no es ni 80 ni 443.</span><span class="sxs-lookup"><span data-stu-id="ad112-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="ad112-154">El módulo especifica el puerto a través de la variable de entorno en el inicio, y el [middleware de integración de IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura el servidor para que escuche en `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="ad112-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="ad112-155">Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo.</span><span class="sxs-lookup"><span data-stu-id="ad112-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="ad112-156">El módulo no admite el reenvío de HTTPS, por lo que las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ad112-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="ad112-157">Una vez que Kestrel toma la solicitud del módulo, la envía a la canalización de middleware de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad112-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="ad112-158">La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ad112-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="ad112-159">El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ad112-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="ad112-160">La respuesta de la aplicación se vuelve a pasar a IIS, que la envía de nuevo al cliente HTTP que inició la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ad112-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="ad112-161">Para obtener instrucciones de configuración para IIS y el módulo ASP.NET Core, consulte los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="ad112-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="ad112-162">macOS</span><span class="sxs-lookup"><span data-stu-id="ad112-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="ad112-163">ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.</span><span class="sxs-lookup"><span data-stu-id="ad112-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="ad112-164">Linux</span><span class="sxs-lookup"><span data-stu-id="ad112-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="ad112-165">ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.</span><span class="sxs-lookup"><span data-stu-id="ad112-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="ad112-166">Nginx con Kestrel</span><span class="sxs-lookup"><span data-stu-id="ad112-166">Nginx with Kestrel</span></span>

<span data-ttu-id="ad112-167">Para información sobre cómo usar Nginx en Linux como servidor proxy inverso para Kestrel, consulte <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="ad112-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="ad112-168">Apache con Kestrel</span><span class="sxs-lookup"><span data-stu-id="ad112-168">Apache with Kestrel</span></span>

<span data-ttu-id="ad112-169">Para información sobre cómo usar Apache en Linux como servidor proxy inverso para Kestrel, consulte <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="ad112-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="ad112-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ad112-170">HTTP.sys</span></span>

<span data-ttu-id="ad112-171">Si las aplicaciones ASP.NET Core se ejecutan en Windows, HTTP.sys es una alternativa a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ad112-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="ad112-172">Suele recomendarse Kestrel para un rendimiento óptimo.</span><span class="sxs-lookup"><span data-stu-id="ad112-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="ad112-173">HTTP.sys se puede usar en escenarios en los que la aplicación se expone a Internet y las funcionalidades necesarias son compatibles con HTTP.sys pero no con Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ad112-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="ad112-174">Para obtener más información, vea <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="ad112-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys se comunica directamente con Internet](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="ad112-176">HTTP.sys también se puede usar para las aplicaciones que solo se exponen a una red interna.</span><span class="sxs-lookup"><span data-stu-id="ad112-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys se comunica directamente con la red interna](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="ad112-178">Para obtener instrucciones de configuración de HTTP.sys, vea <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="ad112-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="ad112-179">Infraestructura de servidores de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ad112-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="ad112-180">El elemento <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> disponible en el método `Startup.Configure` expone la propiedad <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> del tipo <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span><span class="sxs-lookup"><span data-stu-id="ad112-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="ad112-181">Kestrel y HTTP.sys solo exponen una característica cada uno (<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>), pero otras implementaciones de servidor pueden exponer funcionalidades adicionales.</span><span class="sxs-lookup"><span data-stu-id="ad112-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="ad112-182">Se puede usar `IServerAddressesFeature` para averiguar a qué puerto se ha enlazado la implementación del servidor en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ad112-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="ad112-183">Servidores personalizados</span><span class="sxs-lookup"><span data-stu-id="ad112-183">Custom servers</span></span>

<span data-ttu-id="ad112-184">Si los servidores integrados no cumplen los requisitos de la aplicación, se puede crear una implementación de servidor personalizado.</span><span class="sxs-lookup"><span data-stu-id="ad112-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="ad112-185">En la [guía de Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) se muestra cómo escribir una implementación de <xref:Microsoft.AspNetCore.Hosting.Server.IServer> basada en [Nowin](https://github.com/Bobris/Nowin).</span><span class="sxs-lookup"><span data-stu-id="ad112-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="ad112-186">Solo las interfaces de la característica que usa la aplicación requieren implementación, aunque como mínimo se debe admitir <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> y <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>.</span><span class="sxs-lookup"><span data-stu-id="ad112-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="ad112-187">Inicio del servidor</span><span class="sxs-lookup"><span data-stu-id="ad112-187">Server startup</span></span>

<span data-ttu-id="ad112-188">El servidor se inicia cuando el entorno de desarrollo integrado (IDE) o editor inicia la aplicación:</span><span class="sxs-lookup"><span data-stu-id="ad112-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="ad112-189">[Visual Studio](https://visualstudio.microsoft.com): los perfiles de inicio se pueden usar para iniciar la aplicación y el servidor con [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[Módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) o la consola.</span><span class="sxs-lookup"><span data-stu-id="ad112-189">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="ad112-190">[Visual Studio Code](https://code.visualstudio.com/): la aplicación y el servidor se inician mediante [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), con lo que se activa el depurador CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="ad112-190">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="ad112-191">[Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/): la aplicación y el servidor se inician mediante [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="ad112-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="ad112-192">Al iniciar la aplicación desde un símbolo del sistema en la carpeta del proyecto, [dotnet run](/dotnet/core/tools/dotnet-run) inicia la aplicación y el servidor (solo Kestrel y HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="ad112-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="ad112-193">La configuración se especifica mediante la opción `-c|--configuration`, que está establecida en `Debug` (valor predeterminado) o `Release`.</span><span class="sxs-lookup"><span data-stu-id="ad112-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="ad112-194">Un archivo *launchSettings.json* proporciona la configuración al iniciar una aplicación con `dotnet run` o con un depurador integrado en las herramientas, como Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ad112-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="ad112-195">Si hay perfiles de inicio en un archivo *launchSettings.json*, use la opción `--launch-profile {PROFILE NAME}` con el comando `dotnet run` o seleccione el perfil en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ad112-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="ad112-196">Para más información, vea [dotnet run](/dotnet/core/tools/dotnet-run) y [Empaquetado de distribución de .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="ad112-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="ad112-197">Compatibilidad con HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ad112-197">HTTP/2 support</span></span>

<span data-ttu-id="ad112-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) es compatible con ASP.NET Core en los escenarios de implementación siguientes:</span><span class="sxs-lookup"><span data-stu-id="ad112-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="ad112-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="ad112-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="ad112-200">Sistema operativo</span><span class="sxs-lookup"><span data-stu-id="ad112-200">Operating system</span></span>
    * <span data-ttu-id="ad112-201">Windows Server 2016/Windows 10 o posterior&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad112-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="ad112-202">Linux con OpenSSL 1.0.2 o posterior (por ejemplo, Ubuntu 16.04 o posterior)</span><span class="sxs-lookup"><span data-stu-id="ad112-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="ad112-203">HTTP/2 se admitirá en una versión futura en macOS.</span><span class="sxs-lookup"><span data-stu-id="ad112-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="ad112-204">Plataforma de destino: .NET Core 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="ad112-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="ad112-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ad112-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="ad112-206">Windows Server 2016/Windows 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="ad112-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="ad112-207">Marco de destino: No aplicable a implementaciones de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="ad112-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="ad112-208">IIS (en proceso)</span><span class="sxs-lookup"><span data-stu-id="ad112-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="ad112-209">Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="ad112-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ad112-210">Plataforma de destino: .NET Core 2.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="ad112-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="ad112-211">IIS (fuera de proceso)</span><span class="sxs-lookup"><span data-stu-id="ad112-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="ad112-212">Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="ad112-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ad112-213">Las conexiones de servidor perimetral de acceso público usan HTTP/2, pero la conexión de proxy inverso a Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ad112-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="ad112-214">Marco de destino: No aplicable a implementaciones fuera de proceso de IIS.</span><span class="sxs-lookup"><span data-stu-id="ad112-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="ad112-215">&dagger;Kestrel tiene compatibilidad limitada para HTTP/2 en Windows Server 2012 R2 y Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="ad112-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="ad112-216">La compatibilidad es limitada porque la lista de conjuntos de cifrado TLS admitidos y disponibles en estos sistemas operativos está limitada.</span><span class="sxs-lookup"><span data-stu-id="ad112-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="ad112-217">Se puede requerir un certificado generado mediante Elliptic Curve Digital Signature Algorithm (ECDSA) para proteger las conexiones TLS.</span><span class="sxs-lookup"><span data-stu-id="ad112-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="ad112-218">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ad112-218">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="ad112-219">Windows Server 2016/Windows 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="ad112-219">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="ad112-220">Marco de destino: No aplicable a implementaciones de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="ad112-220">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="ad112-221">IIS (fuera de proceso)</span><span class="sxs-lookup"><span data-stu-id="ad112-221">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="ad112-222">Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="ad112-222">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ad112-223">Las conexiones de servidor perimetral de acceso público usan HTTP/2, pero la conexión de proxy inverso a Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ad112-223">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="ad112-224">Marco de destino: No aplicable a implementaciones fuera de proceso de IIS.</span><span class="sxs-lookup"><span data-stu-id="ad112-224">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="ad112-225">Una conexión HTTP/2 debe usar [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) y TLS 1.2 o posterior.</span><span class="sxs-lookup"><span data-stu-id="ad112-225">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="ad112-226">Para más información, vea los temas que pertenecen a los escenarios de implementación de servidor.</span><span class="sxs-lookup"><span data-stu-id="ad112-226">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ad112-227">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ad112-227">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
