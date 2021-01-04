---
title: Modelos de hospedaje Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre los modelos de hospedaje Blazor WebAssembly y Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
uid: blazor/hosting-models
ms.openlocfilehash: a6f1c88b8e93c0d8ccfebca482895ebab8d18a81
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506921"
---
# <a name="aspnet-core-no-locblazor-hosting-models"></a>Modelos de hospedaje Blazor en ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27)

Blazor es un marco web diseñado para ejecutar el lado cliente en el explorador en un entorno de tiempo de ejecución de .NET basado en [WebAssembly](https://webassembly.org/) ( *Blazor WebAssembly* ) o el lado servidor en ASP.NET Core ( *Blazor Server* ). Independientemente del modelo de hospedaje, los modelos de la aplicación y los componentes *son los mismos*.

## Blazor WebAssembly

El modelo de hospedaje principal de Blazor se ejecuta del lado cliente en el explorador en WebAssembly. La aplicación Blazor, sus dependencias y el entorno de ejecución de .NET se descargan en el explorador. La aplicación se ejecuta directamente en el subproceso de interfaz de usuario del explorador. Las actualizaciones de la interfaz de usuario y el control de eventos se producen en el mismo proceso. Los recursos de la aplicación se implementan como archivos estáticos en un servidor o servicio web capaz de servir contenido estático a los clientes.

![Blazor WebAssembly: La aplicación Blazor se ejecuta en un subproceso de interfaz de usuario dentro del explorador.](hosting-models/_static/blazor-webassembly.png)

Cuando la aplicación Blazor WebAssembly se crea para su implementación sin una aplicación de back-end ASP.NET Core, se denomina aplicación Blazor WebAssembly *independiente*. Cuando la aplicación se crea para su implementación con una aplicación de back-end para dar servicio a sus archivos, la aplicación se denomina aplicación Blazor WebAssembly *hospedada*. Una aplicación Blazor WebAssembly hospedada suele interactuar con el servidor a través de la red mediante llamadas API web o [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>).

El script `blazor.webassembly.js` lo proporciona el marco y controla:

* La descarga del tiempo de ejecución de .NET, la aplicación y las dependencias de la aplicación.
* La inicialización del tiempo de ejecución para ejecutar la aplicación.

El modelo de hospedaje de Blazor WebAssembly ofrece varias ventajas:

* No hay ninguna dependencia del lado servidor .NET. La aplicación funciona totalmente después de descargarla en el cliente.
* Los recursos y capacidades del cliente se aprovechan completamente.
* El trabajo se descarga del servidor al cliente.
* No es necesario un servidor web ASP.NET Core para hospedar la aplicación. Los escenarios de implementación sin servidor son posibles, como dar servicio a la aplicación desde una instancia de Content Delivery Network (CDN).

El modelo de hospedaje de Blazor WebAssembly tiene las limitaciones siguientes:

* La aplicación está restringida a las capacidades del explorador.
* Se necesita hardware y software del cliente compatible (por ejemplo, compatibilidad con WebAssembly).
* El tamaño de descarga es mayor y las aplicaciones tardan más tiempo en cargarse.
* La compatibilidad con las herramientas y el tiempo de ejecución de .NET está menos desarrollada. Por ejemplo, existen limitaciones en la compatibilidad y la depuración de [.NET Standard](/dotnet/standard/net-standard).

Para crear una aplicación Blazor WebAssembly, consulte <xref:blazor/tooling>.

El modelo de aplicación hospedada de Blazor admite los [contenedores de Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). A efectos de compatibilidad con Docker en Visual Studio, haga clic con el botón derecho en el proyecto `Server` de una solución Blazor WebAssembly hospedada y seleccione **Agregar** > **Compatibilidad con Docker**.

## Blazor Server

Con el modelo de hospedaje de Blazor Server, la aplicación se ejecuta en el servidor desde una aplicación ASP.NET Core. Las actualizaciones de la interfaz de usuario, el control de eventos y las llamadas de JavaScript se controlan mediante una conexión [SignalR](xref:signalr/introduction).

![El explorador interactúa con la aplicación (hospedada en una aplicación ASP.NET Core) en el servidor a través de una conexión SignalR.](hosting-models/_static/blazor-server.png)

La aplicación ASP.NET Core hace referencia a la clase `Startup` de la aplicación para agregar lo siguiente:

* Servicios del lado servidor.
* La aplicación a la canalización de administración de solicitudes.

En el cliente, el script `blazor.server.js` establece la conexión SignalR con el servidor. El script se sirve a la aplicación del lado cliente desde un recurso incrustado en el marco compartido de ASP.NET Core. La aplicación del lado cliente es responsable de conservar y restaurar el estado de la aplicación según sea necesario. 

El modelo de hospedaje de Blazor Server ofrece varias ventajas:

* El tamaño de la descarga es bastante menor que una aplicación Blazor WebAssembly y la aplicación se carga mucho más rápido.
* La aplicación aprovecha al máximo las capacidades del servidor, incluido el uso de cualquier API compatible con .NET Core.
* .NET Core en el servidor se usa para ejecutar la aplicación, por lo que las herramientas de .NET existentes, como la depuración, funcionan según lo previsto.
* Se admiten clientes ligeros. Por ejemplo, las aplicaciones Blazor Server funcionan con los exploradores que no admiten WebAssembly y en los dispositivos con restricción de recursos.
* La base del código de la aplicación .NET/C#, incluido el código de componente de la aplicación, no se sirve a los clientes.

> [!IMPORTANT]
> Una aplicación Blazor Server se representa previamente en respuesta a la primera solicitud de cliente, que crea el estado de la interfaz de usuario en el servidor. Cuando el cliente intenta crear una conexión SignalR, **debe volver a conectarse al mismo servidor**. Las aplicaciones Blazor Server que usen más de un servidor back-end deben implementar *sesiones permanentes* para conexiones SignalR. Para obtener más información, vea la sección [Conexión al servidor](#connection-to-the-server).

El modelo de hospedaje de Blazor Server tiene las limitaciones siguientes:

* Normalmente existe una mayor latencia. Cada interacción del usuario implica un salto de red.
* No hay soporte técnico sin conexión. Si se produce un error en la conexión del cliente, la aplicación deja de funcionar.
* En el caso de aplicaciones con muchos usuarios, la escalabilidad supone un reto. El servidor debe administrar varias conexiones de cliente y controlar el estado del cliente.
* Se necesita un servidor ASP.NET Core para atender la aplicación. Los escenarios de implementación sin servidor no son posibles, como dar servicio a la aplicación desde una instancia de Content Delivery Network (CDN).

Para crear una aplicación Blazor Server, consulte <xref:blazor/tooling>.

El modelo de la aplicación Blazor Server admite los [contenedores de Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). A efectos de compatibilidad con Docker en Visual Studio, haga clic con el botón derecho en el proyecto en Visual Studio y seleccione **Agregar** > **Compatibilidad con Docker**.

### <a name="comparison-to-server-rendered-ui"></a>Comparación con la interfaz de usuario representada por el servidor

Una forma de entender las aplicaciones Blazor Server es comprender cómo difieren de los modelos tradicionales para la representación de la interfaz de usuario de las aplicaciones de ASP.NET Core mediante vistas de Razor o Razor Pages. Ambos modelos usan el [lenguaje Razor](xref:mvc/views/razor) para describir el contenido HTML, pero difieren bastante en *cómo* se representa el marcado.

Cuando se representa una página de Razor o una vista, cada línea de código de Razor emite HTML en forma de texto. Después de la representación, el servidor desecha la instancia de la página o la vista, incluido cualquier estado que se haya producido. Cuando se produce otra solicitud para la página, por ejemplo, cuando se produce un error en la validación del servidor y se muestra el resumen de la validación, ocurre lo siguiente:

* La página completa se vuelve a representar en texto HTML de nuevo.
* Se envía la página al cliente.

Una aplicación Blazor se compone de elementos reutilizables de la interfaz de usuario denominados *componentes*. Un componente contiene código de C#, marcado y otros componentes. Cuando se representa un componente, Blazor genera un gráfico de los componentes incluidos de forma similar a un Document Object Model (DOM) HTML o XML. Este gráfico incluye el estado del componente contenido en propiedades y campos. Blazor evalúa el gráfico de componentes para generar una representación binaria del marcado. El formato binario puede ser lo siguiente:

* Se convierte en texto HTML (durante la representación previa&dagger;).
* Se usa para actualizar de forma eficaz el marcado durante la representación normal.

&dagger;*Representación previa*: El componente de Razor solicitado se compila en el servidor en HTML estático y se envía al cliente, donde se representa al usuario. Una vez realizada la conexión entre el cliente y el servidor, los elementos estáticos del componente representados previamente se reemplazan por elementos interactivos. La representación previa hace que la aplicación tenga más capacidad de respuesta respecto al usuario.

Una actualización de la interfaz de usuario en Blazor se desencadena mediante lo siguiente:

* Interacción del usuario, como la selección de un botón.
* Desencadenadores de aplicaciones, como un temporizador.

El gráfico de componentes se vuelve a representar y se calcula un valor *diff* (diferencia) de interfaz de usuario. Esta diferencia es el conjunto más pequeño de ediciones DOM necesarias para actualizar la interfaz de usuario en el cliente. La diferencia se envía al cliente en un formato binario y se aplica mediante el explorador.

Se desecha un componente después de que el usuario salga de él en el cliente. Mientras que un usuario interactúa con un componente, el estado del componente (servicios, recursos) debe mantenerse en la memoria del servidor. Dado que el servidor puede mantener el estado de muchos componentes simultáneamente, el agotamiento de la memoria es una preocupación que se debe abordar. Para obtener instrucciones sobre cómo crear una aplicación Blazor Server con el fin de garantizar el mejor uso de la memoria del servidor, vea <xref:blazor/security/server/threat-mitigation>.

### <a name="circuits"></a>Circuitos

Una aplicación Blazor Server se basa en [ASP.NET Core SignalR](xref:signalr/introduction). Cada cliente se comunica con el servidor a través de una o varias conexiones SignalR denominadas *circuitos*. Un circuito es la abstracción de Blazor en conexiones SignalR que pueden tolerar interrupciones de red temporales. Cuando un cliente Blazor ve que la conexión SignalR está desconectada, intenta volver a conectarse al servidor mediante una nueva conexión SignalR.

Cada pantalla del explorador (pestaña del explorador o iframe) que está conectada a una aplicación Blazor Server usa una conexión SignalR. Esta es otra diferencia importante en comparación con las aplicaciones típicas representadas por el servidor. En una aplicación representada por el servidor, la apertura de la misma aplicación en varias pantallas del explorador normalmente no se traduce en demandas adicionales de recursos en el servidor. En una aplicación Blazor Server, cada pantalla del explorador requiere un circuito e instancias independientes del estado del componente que administra el servidor.

Blazor considera el cierre de una pestaña del explorador o la navegación a una dirección URL externa una terminación *correcta*. En el caso de una terminación correcta, el circuito y los recursos asociados se liberan inmediatamente. Un cliente también puede desconectarse de manera no correcta, por ejemplo, debido a una interrupción en la red. Blazor Server almacena los circuitos desconectados durante un intervalo configurable para permitir que el cliente vuelva a conectarse.

Blazor Server permite que el código defina un *controlador de circuito*, que permite ejecutar el código en los cambios realizados en el estado del circuito de un usuario. Para obtener más información, vea <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Latencia de la interfaz de usuario

La latencia de la interfaz de usuario es el tiempo que tarda desde una acción iniciada hasta el momento en que esta interfaz de usuario se actualiza. Los valores más pequeños de la latencia de la interfaz de usuario son imprescindibles para que una aplicación pueda responder a un usuario. En una aplicación Blazor Server, cada acción se envía al servidor, se procesa y se devuelve una diferencia de interfaz de usuario. Por lo tanto, la latencia de la interfaz de usuario es la suma de la latencia de red y la del servidor en el procesamiento de la acción.

En el caso de una aplicación empresarial que esté limitada a una red corporativa privada, los efectos en las percepciones del usuario sobre la latencia debido a la latencia de red suelen ser imperceptibles. En el caso de una aplicación implementada a través de Internet, es posible que los usuarios perciban la latencia, especialmente si los usuarios están ampliamente distribuidos geográficamente.

El uso de memoria también puede contribuir a la latencia de la aplicación. El aumento del uso de memoria tiene como resultado la recolección frecuente de elementos no utilizados o la paginación de memoria en el disco. Ambas consecuencias degradan el rendimiento de la aplicación y, por lo tanto, aumentan la latencia de la interfaz de usuario.

Las aplicaciones Blazor Server deben optimizarse para minimizar la latencia de la interfaz de usuario mediante la reducción de la latencia de red y el uso de memoria. Para obtener información acerca de cómo medir la latencia de red, vea <xref:blazor/host-and-deploy/server#measure-network-latency>. Para obtener más información sobre SignalR y Blazor, vea lo siguiente:

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a>Conexión al servidor

Las aplicaciones Blazor Server requieren una conexión SignalR activa al servidor. Si se pierde la conexión, la aplicación intenta volver a conectarse al servidor. Siempre que el estado del cliente todavía esté en la memoria, la sesión del cliente se reanudará sin perder el estado.

Una aplicación Blazor Server se representa previamente en respuesta a la primera solicitud de cliente, que crea el estado de la interfaz de usuario en el servidor. Cuando el cliente intenta crear una conexión SignalR, este debe volver a conectarse al mismo servidor. Las aplicaciones Blazor Server que usen más de un servidor back-end deben implementar *sesiones permanentes* para conexiones SignalR.

Se recomienda usar [Azure SignalR Service](/azure/azure-signalr) para las aplicaciones Blazor Server. El servicio permite el escalado vertical de una aplicación Blazor Server a un gran número de conexiones SignalR simultáneas. Las sesiones permanentes están habilitadas para el Servicio SignalR de Azure estableciendo la opción `ServerStickyMode` del servicio o el valor de configuración en `Required`. Para obtener más información, vea <xref:blazor/host-and-deploy/server#signalr-configuration>.

Cuando se usa IIS, se habilitan sesiones temporales con el módulo *Enrutamiento de solicitud de aplicaciones*. Para más información, vea [Equilibrio de carga HTTP mediante el enrutamiento de solicitud de aplicaciones](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/tooling>
* <xref:signalr/introduction>
* <xref:blazor/fundamentals/additional-scenarios>
* <xref:tutorials/signalr-blazor-webassembly>
