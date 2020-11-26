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
ms.openlocfilehash: a27fdd70963830d22b3501972d6150dde5e1ea54
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174602"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Implementaciones de servidores web en ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) y [Chris Ross](https://github.com/Tratcher)

Una aplicación ASP.NET Core se ejecuta con una implementación de servidor HTTP en proceso. La implementación del servidor realiza escuchas de solicitudes HTTP y las muestra en la aplicación como un conjunto de [características de solicitud](xref:fundamentals/request-features) compuestos en un <xref:Microsoft.AspNetCore.Http.HttpContext>.

## <a name="kestrel"></a>Kestrel

Kestrel es el servidor web predeterminado que se especifica en las plantillas de proyecto de ASP.NET Core.

Use Kestrel:

* Por sí solo como un servidor perimetral que procesa solicitudes directamente desde una red, incluida Internet.

  ![Kestrel se comunica directamente con Internet sin ningún servidor proxy inverso](kestrel/_static/kestrel-to-internet2.png)

* Con un *servidor proxy inverso*, tal como [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/). Un servidor proxy inverso recibe las solicitudes HTTP de Internet y las reenvía a Kestrel.

  ![Kestrel se comunica indirectamente con Internet a través de un servidor proxy inverso, como IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

Se admite cualquiera de las configuraciones de hospedaje &mdash;con o sin un servidor proxy inverso&mdash;.

Si quiere obtener instrucciones e información sobre la configuración de Kestrel y su uso en una configuración de proxy inverso, vea <xref:fundamentals/servers/kestrel>.

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core se suministra con los siguientes componentes:

* El servidor [Kestrel](xref:fundamentals/servers/kestrel) es la implementación de servidor HTTP multiplataforma predeterminada.
* El servidor HTTP de IIS es un [servidor en proceso](#hosting-models) de IIS.
* El [servidor HTTP.sys](xref:fundamentals/servers/httpsys) es un servidor HTTP solo de Windows basado en el [controlador del kernel HTTP.sys y la API de servidor HTTP](/windows/desktop/Http/http-api-start-page).

Cuando se usa [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), la aplicación se ejecuta:

* En el mismo proceso que el proceso de trabajo de IIS (el [modelo de hospedaje dentro de proceso](#hosting-models)) con el servidor HTTP de IIS. La configuración recomendada es *En proceso*.
* En un proceso distinto al del proceso de trabajo de IIS (el [modelo de hospedaje fuera de proceso](#hosting-models)) con el [servidor Kestrel](#kestrel).

El [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) es un módulo nativo de IIS que controla las solicitudes de IIS nativas entre IIS y el servidor de IIS en proceso o Kestrel. Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module>.

## <a name="hosting-models"></a>Modelos de hospedaje

Con el hospedaje en proceso, una aplicación ASP.NET Core se ejecuta en el mismo proceso que su proceso de trabajo de IIS. El hospedaje en proceso proporciona un rendimiento mejorado con respecto al hospedaje fuera de proceso porque las solicitudes no se realizan mediante proxy en el adaptador de bucle invertido, una interfaz de red que devuelve el tráfico saliente a la misma máquina. IIS controla la administración de procesos con el [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Con el hospedaje fuera de proceso, las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, y el módulo se encarga de la administración de procesos. El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud y reinicia la aplicación si esta se apaga o se bloquea. Este comportamiento es básicamente el mismo que el de las aplicaciones que se ejecutan en proceso y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Para obtener más información e instrucciones de configuración, vea los temas siguientes:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core se suministra con los siguientes componentes:

* El servidor [Kestrel](xref:fundamentals/servers/kestrel) es el servidor HTTP multiplataforma predeterminado.
* El [servidor HTTP.sys](xref:fundamentals/servers/httpsys) es un servidor HTTP solo de Windows basado en el [controlador del kernel HTTP.sys y la API de servidor HTTP](/windows/desktop/Http/http-api-start-page).

Al usar [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), la aplicación se ejecuta en un proceso distinto al del proceso de trabajo de IIS (*fuera de proceso*) con el [servidor Kestrel](#kestrel).

Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo se encarga de la administración de procesos. El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud y reinicia la aplicación si esta se apaga o se bloquea. Este comportamiento es básicamente el mismo que el de las aplicaciones que se ejecutan en proceso y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada fuera de proceso:

![Módulo ASP.NET Core](_static/ancm-outofprocess.png)

Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel. El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS). El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación, que no es ni 80 ni 443.

El módulo especifica el puerto a través de la variable de entorno en el inicio, y el [middleware de integración de IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura el servidor para que escuche en `http://localhost:{port}`. Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo. El módulo no admite el reenvío de HTTPS, por lo que las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.

Una vez que Kestrel toma la solicitud del módulo, la envía a la canalización de middleware de ASP.NET Core. La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación. El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel. La respuesta de la aplicación se vuelve a pasar a IIS, que la envía de nuevo al cliente HTTP que inició la solicitud.

Para obtener instrucciones de configuración para IIS y el módulo ASP.NET Core, consulte los temas siguientes:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core se distribuye con el [servidor Kestrel](xref:fundamentals/servers/kestrel), que es el servidor HTTP multiplataforma predeterminado.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx con Kestrel

Para información sobre cómo usar Nginx en Linux como servidor proxy inverso para Kestrel, consulte <xref:host-and-deploy/linux-nginx>.

### <a name="apache-with-kestrel"></a>Apache con Kestrel

Para información sobre cómo usar Apache en Linux como servidor proxy inverso para Kestrel, consulte <xref:host-and-deploy/linux-apache>.

## <a name="httpsys"></a>HTTP.sys

Si las aplicaciones ASP.NET Core se ejecutan en Windows, HTTP.sys es una alternativa a Kestrel. Suele recomendarse Kestrel para un rendimiento óptimo. HTTP.sys se puede usar en escenarios en los que la aplicación se expone a Internet y las funcionalidades necesarias son compatibles con HTTP.sys pero no con Kestrel. Para obtener más información, vea <xref:fundamentals/servers/httpsys>.

![HTTP.sys se comunica directamente con Internet](httpsys/_static/httpsys-to-internet.png)

HTTP.sys también se puede usar para las aplicaciones que solo se exponen a una red interna.

![HTTP.sys se comunica directamente con la red interna](httpsys/_static/httpsys-to-internal.png)

Para obtener instrucciones de configuración de HTTP.sys, vea <xref:fundamentals/servers/httpsys>.

## <a name="aspnet-core-server-infrastructure"></a>Infraestructura de servidores de ASP.NET Core

El elemento <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> disponible en el método `Startup.Configure` expone la propiedad <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> del tipo <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>. Kestrel y HTTP.sys solo exponen una característica cada uno (<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>), pero otras implementaciones de servidor pueden exponer funcionalidades adicionales.

Se puede usar `IServerAddressesFeature` para averiguar a qué puerto se ha enlazado la implementación del servidor en tiempo de ejecución.

## <a name="custom-servers"></a>Servidores personalizados

Si los servidores integrados no cumplen los requisitos de la aplicación, se puede crear una implementación de servidor personalizado. En la [guía de Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) se muestra cómo escribir una implementación de <xref:Microsoft.AspNetCore.Hosting.Server.IServer> basada en [Nowin](https://github.com/Bobris/Nowin). Solo las interfaces de la característica que usa la aplicación requieren implementación, aunque como mínimo se debe admitir <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> y <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>.

## <a name="server-startup"></a>Inicio del servidor

El servidor se inicia cuando el entorno de desarrollo integrado (IDE) o editor inicia la aplicación:

* [Visual Studio](https://visualstudio.microsoft.com): los perfiles de inicio se pueden usar para iniciar la aplicación y el servidor con [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[Módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module) o la consola.
* [Visual Studio Code](https://code.visualstudio.com/): la aplicación y el servidor se inician mediante [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), con lo que se activa el depurador CoreCLR.
* [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/): la aplicación y el servidor se inician mediante [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).

Al iniciar la aplicación desde un símbolo del sistema en la carpeta del proyecto, [dotnet run](/dotnet/core/tools/dotnet-run) inicia la aplicación y el servidor (solo Kestrel y HTTP.sys). La configuración se especifica mediante la opción `-c|--configuration`, que está establecida en `Debug` (valor predeterminado) o `Release`.

Un archivo *launchSettings.json* proporciona la configuración al iniciar una aplicación con `dotnet run` o con un depurador integrado en las herramientas, como Visual Studio. Si hay perfiles de inicio en un archivo *launchSettings.json*, use la opción `--launch-profile {PROFILE NAME}` con el comando `dotnet run` o seleccione el perfil en Visual Studio. Para más información, vea [dotnet run](/dotnet/core/tools/dotnet-run) y [Empaquetado de distribución de .NET Core](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>Compatibilidad con HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) es compatible con ASP.NET Core en los escenarios de implementación siguientes:

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * Sistema operativo
    * Windows Server 2016/Windows 10 o posterior&dagger;
    * Linux con OpenSSL 1.0.2 o posterior (por ejemplo, Ubuntu 16.04 o posterior)
    * HTTP/2 se admitirá en una versión futura en macOS.
  * Plataforma de destino: .NET Core 2.2 o posterior
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 o posterior
  * Marco de destino: No aplicable a implementaciones de HTTP.sys.
* [IIS (en proceso)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior
  * Plataforma de destino: .NET Core 2.2 o posterior
* [IIS (fuera de proceso)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior
  * Las conexiones de servidor perimetral de acceso público usan HTTP/2, pero la conexión de proxy inverso a Kestrel usa HTTP/1.1.
  * Marco de destino: No aplicable a implementaciones fuera de proceso de IIS.

&dagger;Kestrel tiene compatibilidad limitada para HTTP/2 en Windows Server 2012 R2 y Windows 8.1. La compatibilidad es limitada porque la lista de conjuntos de cifrado TLS admitidos y disponibles en estos sistemas operativos está limitada. Se puede requerir un certificado generado mediante Elliptic Curve Digital Signature Algorithm (ECDSA) para proteger las conexiones TLS.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 o posterior
  * Marco de destino: No aplicable a implementaciones de HTTP.sys.
* [IIS (fuera de proceso)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior
  * Las conexiones de servidor perimetral de acceso público usan HTTP/2, pero la conexión de proxy inverso a Kestrel usa HTTP/1.1.
  * Marco de destino: No aplicable a implementaciones fuera de proceso de IIS.

::: moniker-end

Una conexión HTTP/2 debe usar [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) y TLS 1.2 o posterior. Para más información, vea los temas que pertenecen a los escenarios de implementación de servidor.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
