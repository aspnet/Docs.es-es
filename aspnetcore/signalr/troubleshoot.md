---
title: Solución de problemas de conexión de ASP.NET Core SignalR
author: bradygaster
description: Solución de problemas de conexión de ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: 2ffe8bcf4bef5dcb9fc74513c7507e5cb3a6b7cb
ms.sourcegitcommit: fad0cd264c9d07a48a8c6ba1690807e0f8728898
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379568"
---
# <a name="troubleshoot-connection-errors"></a>Solución de problemas de conexión

En esta sección se proporciona ayuda con los errores que se pueden producir al intentar establecer una conexión con un SignalR concentrador de ASP.net Core.

### <a name="response-code-404"></a>Código de respuesta 404

Al usar WebSockets y `skipNegotiation = true`
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* Cuando se usan varios servidores sin sesiones permanentes, la conexión puede iniciarse en un servidor y, a continuación, cambiar a otro servidor. El otro servidor no es consciente de la conexión anterior.
* Compruebe que el cliente se está conectando al extremo correcto. Por ejemplo, el servidor se hospeda en `http://127.0.0.1:5000/hub/myHub` y el cliente intenta conectarse a `http://127.0.0.1:5000/myHub` .
* Si la conexión usa el identificador y tarda demasiado tiempo en enviar una solicitud al servidor después de negociar, el servidor:

  * Elimina el identificador.
  * Devuelve un 404.

### <a name="response-code-400-or-503"></a>Código de respuesta 400 o 503

Para el siguiente error:

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

Este error se debe normalmente a un cliente que usa solo el transporte de WebSockets, pero el protocolo WebSockets no está habilitado en el servidor.

### <a name="response-code-307"></a>Código de respuesta 307

Al usar WebSockets y `skipNegotiation = true`
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

Este error también puede producirse durante la solicitud Negotiate.

Causa común:
* La aplicación está configurada para exigir HTTPS llamando a `UseHttpsRedirection` en `Startup` o aplica https a través de la regla de reescritura de URL.

Solución posible:
* Cambie la dirección URL en el lado cliente de "http" a "https". `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a>Código de respuesta 405

Código de estado http 405-método no permitido

* La aplicación no tiene [CORS](xref:signalr/security#cross-origin-resource-sharing) habilitado

### <a name="response-code-0"></a>Código de respuesta 0

Código de estado http 0: normalmente un problema de [CORS](xref:signalr/security#cross-origin-resource-sharing) , no se proporciona código de estado

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* Agregue los orígenes esperados a `.WithOrigins(...)`

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* Agregue `.AllowCredentials()` a la Directiva de CORS. No se puede usar `.AllowAnyOrigin()` o `.WithOrigins("*")` con esta opción

### <a name="response-code-413"></a>Código de respuesta 413

Código de estado http 413-carga demasiado grande

Esto suele deberse a que hay un token de acceso que está por encima de 4k.

* Si usa el servicio de Azure SignalR , reduzca el tamaño del token mediante la personalización de las notificaciones que se envían a través del servicio con:
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a>Errores de red transitorios

Los errores de red transitorios pueden cerrar la SignalR conexión. El servidor puede interpretar la conexión cerrada como desconexión de cliente correcta. Para obtener más información sobre por qué un cliente se desconecta en esos casos [, recopile los registros del cliente y del servidor](xref:signalr/diagnostics).