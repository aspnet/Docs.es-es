---
title: Hospedaje e implementación de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo configurar entornos de hospedaje e implementar aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/index
ms.openlocfilehash: 1084cdb9120b1b40acd21fa2de45c7f280337da3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015600"
---
# <a name="host-and-deploy-aspnet-core"></a>Hospedaje e implementación de ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

En general, para implementar una aplicación de ASP.NET Core en un entorno de hospedaje:

* Implemente la aplicación publicada en una carpeta en el servidor de hospedaje.
* Configure un administrador de procesos que inicie la aplicación cuando lleguen las solicitudes y la reinicie si se bloquea o si se reinicia el servidor.
* Para configurar un proxy inverso, configure un proxy inverso para reenviar solicitudes a la aplicación.

## <a name="publish-to-a-folder"></a>Publicar la aplicación en una carpeta

El comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila el código de la aplicación y copia los archivos necesarios para ejecutar la aplicación en una carpeta *publish*. Al efectuar una implementación desde Visual Studio, el paso `dotnet publish` se lleva a cabo de forma automática antes de que los archivos se copien en el destino de implementación.

### <a name="folder-contents"></a>Contenido de la carpeta

La carpeta *publish* contiene uno o varios archivos de ensamblado de aplicaciones, dependencias y, opcionalmente, el entorno de ejecución. NET.

Se puede publicar una aplicación .NET Core como *implementación independiente* o *implementación dependiente del marco*. Si la aplicación es independiente, los archivos de ensamblado que contienen el entorno de ejecución .NET se incluyen en la carpeta *publish*. Si la aplicación es dependiente del marco, los archivos del tiempo de ejecución .NET no se incluyen porque la aplicación tiene una referencia a una versión de .NET que está instalada en el servidor. El modelo de implementación predeterminado es dependiente del marco. Para más información, vea [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).

Además de los archivos *.exe* y *.dll*, la carpeta *publish* de una aplicación ASP.NET Core suele contener archivos de configuración, recursos estáticos y vistas de MVC. Para obtener más información, vea <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Configurar un administrador de procesos

Una aplicación de ASP.NET Core es una aplicación de consola que se debe iniciar cuando se inicia un servidor y se debe reiniciar si este se bloquea. Para automatizar los inicios y los reinicios, se necesita un administrador de procesos. Los administradores de procesos más comunes para ASP.NET Core son:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Servicio de Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Configurar un proxy inverso

Si la aplicación usa el servidor [Kestrel](xref:fundamentals/servers/kestrel), se puede usar [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index) como servidor proxy inverso. Un servidor proxy inverso recibe las solicitudes HTTP de Internet y las reenvía a Kestrel.

Cualquiera de las configuraciones, &mdash;con o sin un servidor proxy inverso&mdash;, es una configuración de hospedaje admitida. Para más información, vea [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy) (Cuándo se debe usar Kestrel con un proxy inverso).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Escenarios de servidor proxy y equilibrador de carga

Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga. Sin una configuración adicional, una aplicación podría no tener acceso al esquema (HTTP/HTTPS) y la dirección IP remota donde se originó una solicitud. Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Uso de Visual Studio y MSBuild para automatizar implementaciones

La implementación a menudo requiere tareas adicionales además de copiar el resultado del comando [dotnet publish](/dotnet/core/tools/dotnet-publish) en un servidor. Por ejemplo, podrían necesitarse o eliminarse archivos adicionales de la carpeta *publish*. Para la implementación web, Visual Studio usa MSBuild, que puede personalizar de modo que lleve a cabo muchas otras tareas durante la implementación. Para más información, consulte <xref:host-and-deploy/visual-studio-publish-profiles> y el libro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Uso de MSBuild y Team Foundation Build).

Mediante la [característica de publicación web](xref:tutorials/publish-to-azure-webapp-using-vs) o la [compatibilidad integrada con Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment), puede implementar las aplicaciones directamente desde Visual Studio en Azure App Service. Azure DevOps Services es compatible con la [implementación continua en Azure App Service](/azure/devops/pipelines/targets/webapp). Para más información, consulte [DevOps con ASP.NET Core y Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publicar en Azure

Consulte <xref:tutorials/publish-to-azure-webapp-using-vs> para obtener instrucciones sobre cómo publicar una aplicación en Azure con Visual Studio. [Crear una aplicación web de ASP.NET Core en Azure](/azure/app-service/app-service-web-get-started-dotnet) proporciona un ejemplo adicional.

## <a name="publish-with-msdeploy-on-windows"></a>Publicar con MSDeploy en Windows

Consulte <xref:host-and-deploy/visual-studio-publish-profiles> para ver instrucciones sobre cómo publicar una aplicación con un perfil de publicación de Visual Studio, incluyendo desde un símbolo del sistema de Windows mediante el comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Para las implementaciones en Internet Information Services (IIS) con la configuración proporcionada por el archivo *web.config*, consulte los artículos en <xref:host-and-deploy/iis/index>.

## <a name="host-in-a-web-farm"></a>Hospedaje en una granja de servidores web

Para obtener más información sobre la configuración del hospedaje de aplicaciones de ASP.NET Core en un entorno de granja de servidores web (por ejemplo, para implementar varias instancias de la aplicación para escalabilidad), consulte <xref:host-and-deploy/web-farm>.

## <a name="host-on-docker"></a>Host en Docker

Para obtener más información, vea <xref:host-and-deploy/docker/index>.

## <a name="perform-health-checks"></a>Realización de comprobaciones de estado

Use middleware de comprobación de estado para realizar comprobaciones de estado en una aplicación y sus dependencias. Para obtener más información, vea <xref:host-and-deploy/health-checks>.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/troubleshoot>
* [Hospedaje de ASP.NET](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

En general, para implementar una aplicación de ASP.NET Core en un entorno de hospedaje:

* Implemente la aplicación publicada en una carpeta en el servidor de hospedaje.
* Configure un administrador de procesos que inicie la aplicación cuando lleguen las solicitudes y la reinicie si se bloquea o si se reinicia el servidor.
* Para configurar un proxy inverso, configure un proxy inverso para reenviar solicitudes a la aplicación.

## <a name="publish-to-a-folder"></a>Publicar la aplicación en una carpeta

El comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila el código de la aplicación y copia los archivos necesarios para ejecutar la aplicación en una carpeta *publish*. Al efectuar una implementación desde Visual Studio, el paso `dotnet publish` se lleva a cabo de forma automática antes de que los archivos se copien en el destino de implementación.

### <a name="folder-contents"></a>Contenido de la carpeta

La carpeta *publish* contiene uno o varios archivos de ensamblado de aplicaciones, dependencias y, opcionalmente, el entorno de ejecución. NET.

Se puede publicar una aplicación .NET Core como *implementación independiente* o *implementación dependiente del marco*. Si la aplicación es independiente, los archivos de ensamblado que contienen el entorno de ejecución .NET se incluyen en la carpeta *publish*. Si la aplicación es dependiente del marco, los archivos del tiempo de ejecución .NET no se incluyen porque la aplicación tiene una referencia a una versión de .NET que está instalada en el servidor. El modelo de implementación predeterminado es dependiente del marco. Para más información, vea [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).

Además de los archivos *.exe* y *.dll*, la carpeta *publish* de una aplicación ASP.NET Core suele contener archivos de configuración, recursos estáticos y vistas de MVC. Para obtener más información, vea <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Configurar un administrador de procesos

Una aplicación de ASP.NET Core es una aplicación de consola que se debe iniciar cuando se inicia un servidor y se debe reiniciar si este se bloquea. Para automatizar los inicios y los reinicios, se necesita un administrador de procesos. Los administradores de procesos más comunes para ASP.NET Core son:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Servicio de Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Configurar un proxy inverso

Si la aplicación usa el servidor [Kestrel](xref:fundamentals/servers/kestrel), se puede usar [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index) como servidor proxy inverso. Un servidor proxy inverso recibe las solicitudes HTTP de Internet y las reenvía a Kestrel.

Cualquiera de las configuraciones, &mdash;con o sin un servidor proxy inverso&mdash;, es una configuración de hospedaje admitida. Para más información, vea [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy) (Cuándo se debe usar Kestrel con un proxy inverso).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Escenarios de servidor proxy y equilibrador de carga

Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga. Sin una configuración adicional, una aplicación podría no tener acceso al esquema (HTTP/HTTPS) y la dirección IP remota donde se originó una solicitud. Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Uso de Visual Studio y MSBuild para automatizar implementaciones

La implementación a menudo requiere tareas adicionales además de copiar el resultado del comando [dotnet publish](/dotnet/core/tools/dotnet-publish) en un servidor. Por ejemplo, podrían necesitarse o eliminarse archivos adicionales de la carpeta *publish*. Para la implementación web, Visual Studio usa MSBuild, que puede personalizar de modo que lleve a cabo muchas otras tareas durante la implementación. Para más información, consulte <xref:host-and-deploy/visual-studio-publish-profiles> y el libro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Uso de MSBuild y Team Foundation Build).

Mediante la [característica de publicación web](xref:tutorials/publish-to-azure-webapp-using-vs) o la [compatibilidad integrada con Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment), puede implementar las aplicaciones directamente desde Visual Studio en Azure App Service. Azure DevOps Services es compatible con la [implementación continua en Azure App Service](/azure/devops/pipelines/targets/webapp). Para más información, consulte [DevOps con ASP.NET Core y Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publicar en Azure

Consulte <xref:tutorials/publish-to-azure-webapp-using-vs> para obtener instrucciones sobre cómo publicar una aplicación en Azure con Visual Studio. [Crear una aplicación web de ASP.NET Core en Azure](/azure/app-service/app-service-web-get-started-dotnet) proporciona un ejemplo adicional.

## <a name="publish-with-msdeploy-on-windows"></a>Publicar con MSDeploy en Windows

Consulte <xref:host-and-deploy/visual-studio-publish-profiles> para ver instrucciones sobre cómo publicar una aplicación con un perfil de publicación de Visual Studio, incluyendo desde un símbolo del sistema de Windows mediante el comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Para las implementaciones en Internet Information Services (IIS) con la configuración proporcionada por el archivo *web.config*, consulte los artículos en <xref:host-and-deploy/iis/index>.

## <a name="host-in-a-web-farm"></a>Hospedaje en una granja de servidores web

Para obtener más información sobre la configuración del hospedaje de aplicaciones de ASP.NET Core en un entorno de granja de servidores web (por ejemplo, para implementar varias instancias de la aplicación para escalabilidad), consulte <xref:host-and-deploy/web-farm>.

## <a name="host-on-docker"></a>Host en Docker

Para obtener más información, vea <xref:host-and-deploy/docker/index>.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/troubleshoot>
* [Hospedaje de ASP.NET](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
