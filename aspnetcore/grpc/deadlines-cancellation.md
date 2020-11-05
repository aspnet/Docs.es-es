---
title: Servicios gRPC confiables con fechas límite y cancelación
author: jamesnk
description: Aprenda a crear servicios gRPC confiables con fechas límite y cancelación en .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059928"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a>Servicios gRPC confiables con fechas límite y cancelación

Por [James Newton-King](https://twitter.com/jamesnk)

Las fechas límite y la cancelación son características usadas por los clientes de gRPC para anular las llamadas en curso. En este artículo se explica por qué las fechas límite y la cancelación son importantes y cómo usarlas en las aplicaciones gRPC de .NET.

## <a name="deadlines"></a>Fechas límite

Una fecha límite permite a un cliente de gRPC especificar el tiempo que esperará a que se complete una llamada. Cuando se supera una fecha límite, se cancela la llamada. Establecer una fecha límite es importante porque proporciona un límite superior para el tiempo durante el que se puede ejecutar una llamada. Impide que los servicios de comportamiento incorrecto se ejecuten indefinidamente y agoten los recursos del servidor. Las fechas límite son una herramienta útil para compilar aplicaciones confiables y deben configurarse.

Configuración de fecha límite:

* Una fecha límite se configura mediante `CallOptions.Deadline` cuando se realiza una llamada.
* No hay ningún valor de fecha límite predeterminado. Las llamadas a gRPC no tienen límite de tiempo a menos que se especifique una fecha límite.
* Una fecha límite es la hora UTC en la que se supera la fecha límite. Por ejemplo, `DateTime.UtcNow.AddSeconds(5)` es una fecha límite de cinco segundos a partir de ahora.
* Si se usa una hora anterior o actual, la llamada inmediatamente supera la fecha límite.
* La fecha límite se envía al servicio con la llamada a gRPC y tanto el cliente como el servicio realizan un seguimiento de forma independiente. Es posible que una llamada a gRPC se complete en un equipo, pero, en el momento en que se devuelva la respuesta al cliente, se habrá superado la fecha límite.

Si se supera una fecha límite, el cliente y el servicio tienen otro comportamiento:

* El cliente anula inmediatamente la solicitud HTTP subyacente y produce un error `DeadlineExceeded`. La aplicación cliente puede optar por detectar el error y mostrar un mensaje de tiempo de espera para el usuario.
* En el servidor, se anula la solicitud HTTP de ejecución y se genera [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken). Aunque la solicitud HTTP se ha anulado, la llamada a gRPC continúa ejecutándose en el servidor hasta que se completa el método. Es importante que el token de cancelación se pase a métodos asincrónicos para que se cancelen junto con la llamada. Por ejemplo, pasar un token de cancelación a consultas asincrónicas de base de datos y solicitudes HTTP. Pasar un token de cancelación permite que la llamada cancelada se complete rápidamente en el servidor y libere recursos para otras llamadas.

Configure `CallOptions.Deadline` para establecer una fecha límite para una llamada a gRPC:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Uso de `ServerCallContext.CancellationToken` en un servicio gRPC:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a>Propagación de fechas límite

Cuando se realiza una llamada a gRPC desde un servicio gRPC en ejecución, se debe propagar la fecha límite. Por ejemplo:

1. La aplicación cliente llama a `FrontendService.GetUser` con una fecha límite.
2. `FrontendService` llama a `UserService.GetUser`. La fecha límite especificada por el cliente debe especificarse con la nueva llamada a gRPC.
3. `UserService.GetUser` recibe la fecha límite. Se agota el tiempo de espera correctamente si se supera la fecha límite de la aplicación cliente.

El contexto de llamada proporciona la fecha límite con `ServerCallContext.Deadline`:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

La propagación manual de fechas límite puede resultar complicada. La fecha límite debe pasarse a cada llamada y es fácil que se pierda accidentalmente. Hay disponible una solución automática con la fábrica de cliente de gRPC. Especifique `EnableCallContextPropagation`:

* Propaga automáticamente la fecha límite y el token de cancelación a las llamadas secundarias.
* La propagación del contexto de llamada es una excelente manera de garantizar que los escenarios complejos de gRPC anidados siempre propagan la fecha límite y la cancelación.

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

Para obtener más información, vea <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.

## <a name="cancellation"></a>Cancelación

La cancelación permite a un cliente de gRPC cancelar llamadas de larga duración que ya no son necesarias. Por ejemplo, una llamada a gRPC que transmite actualizaciones en tiempo real se inicia cuando el usuario visita una página en un sitio web. La secuencia se debe cancelar cuando el usuario navega fuera de la página.

Una llamada a gRPC se puede cancelar en el cliente pasando un token de cancelación con [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) o llamando a `Dispose` en la llamada.

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

Los servicios gRPC que se pueden cancelar deben:
* Pasar `ServerCallContext.CancellationToken` a métodos asincrónicos. La cancelación de métodos asincrónicos permite que la llamada en el servidor se complete rápidamente.
* Propagar el token de cancelación a las llamadas secundarias. La propagación del token de cancelación garantiza que las llamadas secundarias se cancelan con su elemento primario. La [fábrica de cliente de gRPC](xref:grpc/clientfactory) con `EnableCallContextPropagation()` propaga automáticamente el token de cancelación.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:grpc/client>
* <xref:grpc/clientfactory>
