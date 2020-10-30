---
title: SignalRClientes ASP.net Core
author: bradygaster
description: Obtenga información acerca de qué características son compatibles con los distintos clientes de ASP.NET Core SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/client-features
ms.openlocfilehash: 1d1778f95c53179ca05eaced3d1913f208220df4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050984"
---
# <a name="aspnet-core-no-locsignalr-clients"></a>SignalRClientes ASP.net Core

## <a name="versioning-support-and-compatibility"></a>Control de versiones, compatibilidad y compatibilidad

Los SignalR clientes se incluyen junto con los componentes del servidor y tienen versiones para que coincidan. Cualquier cliente compatible puede conectarse de forma segura a cualquier servidor compatible y se considera que los problemas de compatibilidad se han corregido. SignalR los clientes se admiten en el mismo ciclo de vida de soporte técnico que el resto de .NET Core. Consulte [la Directiva de soporte técnico de .net Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) para obtener más información.

Muchas características requieren un cliente **y** un servidor compatibles. Consulte a continuación una tabla que muestra las versiones mínimas de las distintas características.

Las versiones 1. x de SignalR se asignan a las versiones 2,1 y 2,2 de .net Core y tienen la misma duración. En el caso de la versión 3. x y versiones posteriores, la SignalR versión coincide exactamente con el resto de .net y tiene el mismo ciclo de vida de soporte técnico.

| Versión de SignalR | Versión de .NET Core | Nivel de compatibilidad | Finalización del soporte técnico |
| - | - | - | - |
| 1.0. x | 2.1.x | Compatibilidad a largo plazo | 21 de agosto de 2021 |
| 1.1. x | 2.2.x | Fin de la vida útil | 23 de diciembre de 2019 |
| 3. x o superior | *igual que la SignalR versión* | Consulte la [Directiva de soporte técnico de .net Core.](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) |

**Nota:** En ASP.NET Core 3,0, el cliente de JavaScript se ha *migrado* al `@microsoft/signalr` paquete NPM.

## <a name="feature-distribution"></a>Distribución de características

En la tabla siguiente se muestran las características y la compatibilidad de los clientes que ofrecen compatibilidad en tiempo real. Para cada característica, se muestra la versión *mínima* que admite esta característica. Si no aparece ninguna versión, no se admite la característica.

| Característica | Servidor | Cliente .NET | Cliente de JavaScript | Cliente de Java |
| ---- | :-: | :-: | :-: | :-: |
| SignalRCompatibilidad con servicios de Azure |2.1.0|1.0.0|1.0.0|1.0.0|
| [Streaming de servidor a cliente](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [Streaming de cliente a servidor](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| Reconexión automática ([.net](./dotnet-client.md?tabs=visual-studio&view=aspnetcore-3.0#handle-lost-connection), [JavaScript](./javascript-client.md?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|3.0.0|❌|
| Transporte de WebSockets |2.1.0|1.0.0|1.0.0|1.0.0|
| Transporte de eventos Server-Sent |2.1.0|1.0.0|1.0.0|❌|
| Transporte de sondeo prolongado |2.1.0|1.0.0|1.0.0|3.0.0|
| Protocolo de concentrador de JSON |2.1.0|1.0.0|1.0.0|1.0.0|
| Protocolo de concentrador MessagePack |2.1.0|1.0.0|1.0.0|❌|

Se realiza un seguimiento de la compatibilidad con la habilitación de características de cliente adicionales en [nuestro seguimiento de problemas](https://github.com/dotnet/AspNetCore/issues).

## <a name="additional-resources"></a>Recursos adicionales

* [Introducción a SignalR for ASP.net Core](xref:tutorials/signalr)
* [Plataformas compatibles](xref:signalr/supported-platforms)
* [Concentradores](xref:signalr/hubs)
* [Cliente de JavaScript](xref:signalr/javascript-client)