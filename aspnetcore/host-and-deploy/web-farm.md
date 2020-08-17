---
title: Hospedaje de ASP.NET Core en una granja de servidores web
author: rick-anderson
description: Obtenga información sobre cómo hospedar varias instancias de una aplicación ASP.NET Core con recursos compartidos en un entorno de granja de servidores web.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/web-farm
ms.openlocfilehash: 58409b5c47d71c96ece6f4ecfab6f18df47f798b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015444"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Hospedaje de ASP.NET Core en una granja de servidores web

Por [Chris Ross](https://github.com/Tratcher)

Una *granja de servidores web* es un grupo de dos o más servidores web (o *nodos*) que hospedan varias instancias de una aplicación. Cuando llegan solicitudes de usuarios a una granja de servidores web, un *equilibrador de carga* las distribuye a los nodos de la granja de servidores web. Las granjas de servidores web mejoran lo siguiente:

* **Confiabilidad y disponibilidad**: cuando se produce un error en uno o más nodos, el equilibrador de carga puede enrutar las solicitudes a otros nodos en funcionamiento para continuar procesando las solicitudes.
* **Capacidad y rendimiento**: varios nodos pueden procesar más solicitudes que un solo servidor. El equilibrador de carga equilibra la carga de trabajo mediante la distribución de las solicitudes a los distintos nodos.
* **Escalabilidad**: cuando se requiere más o menos capacidad, es posible aumentar o disminuir la cantidad de nodos activos para coincidir con la carga de trabajo. Las tecnologías de plataforma de granjas de servidores web, como [Azure App Service](https://azure.microsoft.com/services/app-service/), pueden agregar o quitar nodos de manera automática si lo solicita el administrador del sistema o sin intervención humana.
* **Mantenimiento**: los nodos de una granja de servidores web se pueden basar en un conjunto de servicios compartidos, lo que facilita la administración del sistema. Por ejemplo, los nodos de una granja de servidores web pueden basarse en un servidor de base de datos única y una ubicación de red común para los recursos estáticos, como imágenes y archivos descargables.

En este tema se describe la configuración y las dependencias de las aplicaciones de ASP.NET Core hospedadas en una granja de servidores web que se basan en los recursos compartidos.

## <a name="general-configuration"></a>Configuración general

<xref:host-and-deploy/index>  
Obtenga información sobre cómo configurar entornos de hospedaje e implementar aplicaciones de ASP.NET Core. Configure un administrador de procesos en cada nodo de la granja de servidores web para automatizar los inicios y reinicios de la aplicación. Cada nodo requiere el tiempo de ejecución de ASP.NET Core. Para más información, consulte los temas que aparecen en el área [Hospedaje e implementación](xref:host-and-deploy/index) de la documentación.

<xref:host-and-deploy/proxy-load-balancer>  
Aprenda sobre la configuración de las aplicaciones hospedadas detrás de los servidores proxy y equilibradores de carga, que con frecuencia ocultan información importante sobre las solicitudes.

<xref:host-and-deploy/azure-apps/index>  
[Azure App Service](https://azure.microsoft.com/services/app-service/) es un [servicio de plataforma de informática en la nube de Microsoft](https://azure.microsoft.com/) que sirve para hospedar aplicaciones web, como ASP.NET Core. App Service es una plataforma completamente administrada que brinda escalado automático, equilibrio de carga, aplicación de revisiones e implementación continua.

## <a name="app-data"></a>Datos de la aplicación

Cuando una aplicación se escala a varias instancias, puede haber un estado de la aplicación que requiera el uso compartido entre los distintos nodos. Si el estado es transitorio, considere el uso compartido de [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). Si es necesario mantener el estado compartido, considere la posibilidad de almacenar el estado compartido en una base de datos.

## <a name="required-configuration"></a>Configuración necesaria

La protección de datos y el almacenamiento en caché requieren la configuración de las aplicaciones implementadas en una granja de servidores web.

### <a name="data-protection"></a>Protección de datos

Las aplicaciones usan el [sistema de protección de datos de ASP.NET Core](xref:security/data-protection/introduction) para proteger los datos. La protección de datos se basa en un conjunto de claves criptográficas almacenadas en un *conjunto de claves*. Cuando se inicializa el sistema de protección de datos, aplica la [configuración predeterminada](xref:security/data-protection/configuration/default-settings) que almacena localmente el conjunto de claves. Según la configuración predeterminada, un conjunto de claves único se almacena en cada nodo de la granja de servidores web. Por tanto, un nodo de granja de servidores web no puede cifrar los datos que una aplicación cifra en otro nodo. Por lo general, la configuración predeterminada no es adecuada para hospedar aplicaciones en una granja de servidores web. Una alternativa a la implementación de un conjunto de claves compartido es enrutar siempre las solicitudes de usuario al mismo nodo. Para más información sobre la configuración del sistema de protección de datos para las implementaciones de granjas de servidores web, consulte <xref:security/data-protection/configuration/overview>.

### <a name="caching"></a>Almacenamiento en memoria caché

En un entorno de granja de servidores web, el mecanismo de almacenamiento en caché debe compartir elementos en caché en todos los nodos de la granja de servidores web. El almacenamiento en caché debe basarse en una instancia común de Redis Cache, en una base de datos SQL Server compartida o en una implementación de almacenamiento en caché personalizada que comparte los elementos en caché en toda la granja de servidores web. Para obtener más información, vea <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Componentes dependientes

Los escenarios siguientes no requieren configuración adicional, pero dependen de tecnologías que sí requiere configuración para las granjas de servidores web.

| Escenario | Depende de &hellip; |
| -------- | ------------------- |
| Autenticación | Protección de datos (consulte <xref:security/data-protection/configuration/overview>).<br><br>Para obtener más información, vea <xref:security/authentication/cookie> y <xref:security/cookie-sharing>. |
| Identity | Autenticación y configuración de base de datos.<br><br>Para obtener más información, vea <xref:security/authentication/identity>. |
| Sesión | Protección de datos (cookies cifradas) (consulte <xref:security/data-protection/configuration/overview>) y almacenamiento en caché (consulte <xref:performance/caching/distributed>).<br><br>Para obtener más información, consulte [Administración del estado y la sesión: Estado de la sesión](xref:fundamentals/app-state#session-state). |
| TempData | Protección de datos (cookies cifradas; consulte <xref:security/data-protection/configuration/overview>) o sesión (consulte [Administración del estado y la sesión: Estado de la sesión](xref:fundamentals/app-state#session-state)).<br><br>Para obtener más información, consulte [Administración del estado y la sesión: TempData](xref:fundamentals/app-state#tempdata). |
| Antifalsificación | Protección de datos (consulte <xref:security/data-protection/configuration/overview>).<br><br>Para obtener más información, vea <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="data-protection-and-caching"></a>Protección y almacenamiento en caché de datos

Cuando la protección de datos o el almacenamiento en caché no están configurados para un entorno de granja de servidores web, se producen errores intermitentes cuando se procesan las solicitudes. Esto ocurre porque los nodos no comparten los mismos recursos y las solicitudes de usuario no se enrutan siempre de vuelta al mismo nodo.

Piense en un usuario que inicia sesión en la aplicación a través de la autenticación de cookies. El usuario inicia sesión en la aplicación en un nodo de la granja de servidores web. Si la solicitud siguiente llega al mismo nodo en el que iniciaron sesión, la aplicación puede descifrar la cookie de autenticación y permite el acceso al recurso de la aplicación. Si la solicitud siguiente llega a otro nodo, la aplicación no puede descifrar la cookie de autenticación desde el nodo donde el usuario inició sesión y se produce un error en la autorización del recurso solicitado.

Cuando cualquiera de los síntomas siguientes se producen de manera **intermitente**, se suele hacer un seguimiento del problema hasta la configuración inadecuada de la protección de datos o del almacenamiento en caché para un entorno de granja de servidores web:

* La autenticación se interrumpe: La cookie de autenticación está configurada de manera incorrecta o no se puede descifrar. Los inicios de sesión de OAuth (Facebook, Microsoft, Twitter) o de OpenIdConnect presentan el error "Correlation failed" (Error de correlación).
* La autorización se interrumpe: Identity se pierde.
* El estado de sesión pierde datos.
* Los elementos en caché desaparecen.
* Error de TempData.
* Error de POST: error en la comprobación antifalsificación.

Para más información sobre la configuración de la protección de datos para las implementaciones de granjas de servidores web, consulte <xref:security/data-protection/configuration/overview>. Para más información sobre la configuración del almacenamiento en caché para las implementaciones de granja de servidores web, consulte <xref:performance/caching/distributed>.

## <a name="obtain-data-from-apps"></a>Obtención de datos de aplicaciones

Si las aplicaciones de la granja de servidores web son capaces de responder a solicitudes, obtenga solicitudes, conexiones y datos adicionales de las aplicaciones mediante el middleware en línea del terminal. Para obtener más información y un código de ejemplo, vea <xref:test/troubleshoot#obtain-data-from-an-app>.

## <a name="additional-resources"></a>Recursos adicionales

* [Extensión de script personalizada para Windows](/azure/virtual-machines/extensions/custom-script-windows): descarga y ejecuta scripts en máquinas virtuales de Azure, lo que resulta útil para la configuración posterior a la implementación y la instalación de software.
* <xref:host-and-deploy/proxy-load-balancer>
 