---
title: Compatibilidad con WebSockets en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo empezar a usar WebSockets en ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
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
uid: fundamentals/websockets
ms.openlocfilehash: 83a41d503b2d56bca3f1bac14eeb9d54a8257642
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057783"
---
# <a name="websockets-support-in-aspnet-core"></a>Compatibilidad con WebSockets en ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra) y [Andrew Stanton-Nurse](https://github.com/anurse)

En este artículo se ofrece una introducción a WebSockets en ASP.NET Core. [WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) es un protocolo que habilita canales de comunicación bidireccional persistentes a través de conexiones TCP. Se usa en aplicaciones que sacan partido de comunicaciones rápidas y en tiempo real, como las aplicaciones de chat, panel y juegos.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)). [Cómo ejecutar](#sample-app).

## SignalR

[ASP.NET CoreSignalR](xref:signalr/introduction) es una biblioteca que simplifica la adición de funcionalidad web en tiempo real a las aplicaciones. Usa WebSockets siempre que sea posible.

Para la mayoría de las aplicaciones, se recomienda SignalR en lugar de WebSockets sin procesar. SignalR proporciona transporte de reserva para entornos donde WebSockets no está disponible. También proporciona un modelo básico de aplicación de llamada a procedimiento remoto. Además, en la mayoría de los escenarios, SignalR no tiene ninguna desventaja significativa de rendimiento en comparación con WebSockets sin procesar.

En algunas aplicaciones, [gRPC en .NET](xref:grpc/index) proporciona una alternativa a WebSockets.

## <a name="prerequisites"></a>Requisitos previos

* Cualquier sistema operativo que admita ASP.NET Core:  
  * Windows 7/Windows Server 2008 o posterior
  * Linux
  * macOS  
* Si la aplicación se ejecuta en Windows con IIS:
  * Windows 8/Windows Server 2012 o versiones posteriores
  * IIS 8/Express IIS 8
  * WebSockets debe estar habilitado. Vea la sección [Compatibilidad con IIS/IIS Express](#iisiis-express-support).  
* Si la aplicación se ejecuta en [HTTP.sys](xref:fundamentals/servers/httpsys):
  * Windows 8/Windows Server 2012 o versiones posteriores
* Para saber qué exploradores son compatibles, vea https://caniuse.com/#feat=websockets.

## <a name="configure-the-middleware"></a>Configurar el middleware

Agregue el middleware de WebSockets al método `Configure` de la clase `Startup`:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

Se pueden configurar estas opciones:

* `KeepAliveInterval`: la frecuencia con que se envían marcos "ping" al cliente, para asegurarse de que los servidores proxy mantienen abierta la conexión. El valor predeterminado es de dos minutos.
* `ReceiveBufferSize`: el tamaño del búfer usado para recibir datos. Puede que los usuarios avanzados tengan que cambiar estas opciones para ajustar el rendimiento según el tamaño de los datos. El valor predeterminado es 4 KB.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Se pueden configurar estas opciones:

* `KeepAliveInterval`: la frecuencia con que se envían marcos "ping" al cliente, para asegurarse de que los servidores proxy mantienen abierta la conexión. El valor predeterminado es de dos minutos.
* <xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>: el tamaño del búfer usado para recibir datos. Puede que los usuarios avanzados tengan que cambiar estas opciones para ajustar el rendimiento según el tamaño de los datos. El valor predeterminado es 4 KB.
* `AllowedOrigins` - Una lista de valores de encabezado de origen permitidos para las solicitudes WebSocket. De forma predeterminada, se permiten todos los orígenes. Consulte "Restricción de los orígenes de WebSocket" a continuación para obtener información detallada.

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>Aceptar solicitudes WebSocket

En algún momento posterior del ciclo de solicitudes (más adelante en el método `Configure` o en un método de acción, por ejemplo) debe comprobar si se trata de una solicitud WebSocket y aceptarla.

El siguiente ejemplo se corresponde con un momento más adelante en el método `Configure`:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Una solicitud WebSocket puede proceder de cualquier dirección URL, pero este código de ejemplo solo acepta solicitudes de `/ws`.

Cuando se usa un WebSocket, **debe** mantener la canalización de middleware en ejecución durante la duración de la conexión. Si intenta enviar o recibir un mensaje de WebSocket después de que finalice la canalización de middleware, es posible que obtenga una excepción como la siguiente:

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

Si utiliza un servicio en segundo plano para escribir datos en un WebSocket, asegúrese de mantener en ejecución el canal de middleware. Para ello, utilice un <xref:System.Threading.Tasks.TaskCompletionSource%601>. Pase `TaskCompletionSource` a su servicio de segundo plano y pídale que llame a <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> cuando termine con WebSocket. Después, espere (con `await`) la propiedad <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durante la solicitud, como se muestra en el ejemplo siguiente:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

La excepción de cierre de WebSocket también puede producirse si la devolución de un método de acción ocurre demasiado pronto. Al aceptar un socket en un método de acción, espere a que finalice el código que usa el socket antes de devolver el método de acción.

No use nunca `Task.Wait`, `Task.Result` ni llamadas de bloqueo similares para esperar a que se complete el socket, ya que pueden causar graves problemas de subprocesamiento. Use siempre `await`.

## <a name="send-and-receive-messages"></a>Enviar y recibir mensajes

El método `AcceptWebSocketAsync` actualiza la conexión TCP a una conexión WebSocket y proporciona un objeto [WebSocket](/dotnet/core/api/system.net.websockets.websocket). Use el objeto `WebSocket` para enviar y recibir mensajes.

El código antes mostrado que acepta la solicitud WebSocket pasa el objeto `WebSocket` a un método `Echo`. El código recibe un mensaje y devuelve inmediatamente el mismo mensaje. Los mensajes se envían y reciben en un bucle hasta que el cliente cierra la conexión:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

Cuando la conexión WebSocket se acepta antes de que el bucle comience, la canalización de middleware finaliza. Tras cerrar el socket, se desenreda la canalización. Es decir, la solicitud deja de avanzar en la canalización cuando WebSocket se acepta, pero cuando el bucle termina y el socket se cierra, la solicitud vuelve a recorrer la canalización.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>Control de las desconexiones del cliente

No se informa automáticamente al servidor cuando el cliente se desconecta debido a la pérdida de conectividad. El servidor recibe un mensaje de desconexión solo si el cliente lo envía, acción que no se puede realizar si se pierde la conexión a Internet. Si desea realizar alguna acción cuando eso suceda, establezca un tiempo de expiración después de que no se reciba del cliente dentro de un determinado período.

Si el cliente no siempre está enviando mensajes y no quiere que se agote el tiempo de expiración solo porque la conexión está inactiva, haga que el cliente utilice un temporizador para enviar un mensaje de ping cada equis segundos. En el servidor, si aún no ha llegado un mensaje dentro de 2\*X segundos después del anterior, termine la conexión e informe que ha desconectado el cliente. Espere el doble del intervalo de tiempo esperado para dejar tiempo extra para los retrasos de la red que podrían retener el mensaje de ping.

## <a name="websocket-origin-restriction"></a>Restricción de los orígenes de WebSocket

Las protecciones proporcionadas por CORS no se aplican a WebSockets. Los exploradores **no** hacen lo siguiente:

* Efectúan solicitudes preparatorias CORS.
* Respetan las restricciones especificadas en los encabezados `Access-Control` al efectuar solicitudes de WebSocket.

En cambio, sí que envían el encabezado `Origin` al emitir solicitudes de WebSocket. Las aplicaciones deben configurarse para validar estos encabezados a fin de garantizar que solo se permitan los WebSockets procedentes de los orígenes esperados.

Si hospeda su servidor en "https://server.com" y su cliente en "https://client.com", agregue "https://client.com" a la lista `AllowedOrigins` de WebSockets para efectuar la comprobación.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> El encabezado `Origin` está controlado por el cliente y, al igual que el encabezado `Referer`, se puede falsificar. **No** use estos encabezados como mecanismo de autenticación.

::: moniker-end

## <a name="iisiis-express-support"></a>Compatibilidad con IIS/IIS Express

El protocolo WebSocket se puede usar en Windows Server 2012 o posterior, y en Windows 8 o posterior con IIS o IIS Express 8 o posterior.

> [!NOTE]
> WebSocket siempre está habilitado cuando se usa IIS Express.

### <a name="enabling-websockets-on-iis"></a>Habilitación de WebSocket en IIS

Para habilitar la compatibilidad con el protocolo WebSocket en Windows Server 2012 o posterior:

> [!NOTE]
> Estos pasos no son necesarios cuando se usa IIS Express

1. Use el asistente **Agregar roles y características** del menú **Administrar** o el vínculo de **Administrador del servidor**.
1. Seleccione **Instalación basada en características o en roles**. Seleccione **Siguiente**.
1. Seleccione el servidor que corresponda (el servidor local está seleccionado de forma predeterminada). Seleccione **Siguiente**.
1. Expanda **Servidor web (IIS)** en el árbol **Roles**, expanda **Servidor web** y, por último, expanda **Desarrollo de aplicaciones**.
1. Seleccione **Protocolo WebSocket**. Seleccione **Siguiente**.
1. Si no necesita más características, haga clic en **Siguiente**.
1. Haga clic en **Instalar**.
1. Cuando la instalación finalice, haga clic en **Cerrar** para salir del asistente.

Para habilitar la compatibilidad con el protocolo WebSocket en Windows Server 8 o posterior:

> [!NOTE]
> Estos pasos no son necesarios cuando se usa IIS Express

1. Vaya a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).
1. Abra los nodos siguientes: **Internet Information Services** > **Servicios World Wide Web** > **Características de desarrollo de aplicaciones**.
1. Seleccione la característica **Protocolo WebSocket**. Seleccione **Aceptar**.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Deshabilitar WebSocket al usar socket.io en Node.js

Si usa la compatibilidad de WebSocket en [socket.io](https://socket.io/) en [Node.js](https://nodejs.org/), deshabilite el módulo IIS WebSocket predeterminado, usando para ello el elemento `webSocket` de *web.config* o de *applicationHost.config*. Si este paso no se lleva a cabo, el módulo IIS WebSocket intenta controlar la comunicación de WebSocket en lugar de Node.js y la aplicación.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>Aplicación de ejemplo

La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) que acompaña a este artículo es una aplicación de eco. Tiene una página web que realiza las conexiones WebSocket y el servidor reenvía de vuelta al cliente todos los mensajes que reciba. Ejecute la aplicación desde un símbolo del sistema (no está configurada para ejecutarse desde Visual Studio con IIS Express) y vaya a http://localhost:5000. En la página web se muestra el estado de conexión en la parte superior izquierda:

![Estado inicial de la página web](websockets/_static/start.png)

Seleccione **Connect** (Conectar) para enviar una solicitud WebSocket para la URL mostrada. Escriba un mensaje de prueba y seleccione **Send** (Enviar). Cuando haya terminado, seleccione **Close Socket** (Cerrar socket). Los informes de la sección **Communication Log** (Registro de comunicación) informan de cada acción de abrir, enviar y cerrar a medida que se producen.

![Estado inicial de la página web](websockets/_static/end.png)

