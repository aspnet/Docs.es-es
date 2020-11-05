---
title: Migración de servicios gRPC de C-core a ASP.NET Core
author: juntaoluo
description: Obtenga información sobre cómo migrar una aplicación gRPC existente basada en C-core para que se ejecute sobre la pila de ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
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
uid: grpc/migration
ms.openlocfilehash: 1a230e470fa666b2aa6761b4d5dabd09264d2aae
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059837"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migración de servicios gRPC de C-core a ASP.NET Core

Por [John Luo](https://github.com/juntaoluo)

Debido a la implementación de la pila subyacente, no todas las características funcionan de la misma manera entre aplicaciones [gRPC basadas en C-core](https://grpc.io/blog/grpc-stacks) y aplicaciones basadas en ASP.NET Core. En este documento se destacan las diferencias principales para realizar migraciones entre las dos pilas.

## <a name="grpc-service-implementation-lifetime"></a>Duración de la implementación del servicio gRPC

En la pila de ASP.NET Core, los servicios gRPC se crean de forma predeterminada con una [duración restringida](xref:fundamentals/dependency-injection#service-lifetimes). En cambio, gRPC C-core se enlaza de forma predeterminada a un servicio con una [duración singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Una duración restringida permite que la implementación del servicio resuelva otros servicios con duraciones restringidas. Por ejemplo, una duración restringida también puede resolver `DbContext` desde el contenedor de inserción de dependencias a través de la inserción de constructores. Si se usa una duración restringida:

* Se crea una instancia de la implementación del servicio para cada solicitud.
* No es posible compartir el estado entre solicitudes a través de miembros de instancia en el tipo de implementación.
* Lo normal es almacenar los estados compartidos en un servicio singleton en el contenedor de inserción de dependencias. Los estados compartidos almacenados se resuelven en el constructor de la implementación del servicio gRPC.

Para obtener más información sobre la duración de los servicios, consulte <xref:fundamentals/dependency-injection#service-lifetimes>.

### <a name="add-a-singleton-service"></a>Adición de un servicio singleton

Para facilitar la transición de una implementación de gRPC de C-core a ASP.NET Core, se puede cambiar la duración de la implementación del servicio de restringida a singleton. Esto implica agregar una instancia de la implementación del servicio al contenedor de inserción de dependencias:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Sin embargo, una implementación del servicio con una duración singleton ya no puede resolver los servicios restringidos a través de la inserción de constructores.

## <a name="configure-grpc-services-options"></a>Configuración de las opciones de servicios gRPC

En las aplicaciones basadas en C-core, los valores como `grpc.max_receive_message_length` y `grpc.max_send_message_length` se configuran con `ChannelOption` en el momento de [crear la instancia del servidor](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

En ASP.NET Core, gRPC proporciona la configuración a través del tipo `GrpcServiceOptions`. Por ejemplo, el tamaño máximo del mensaje entrante del servicio gRPC se puede configurar mediante `AddGrpc`. En el ejemplo siguiente, se cambia el valor predeterminado de `MaxReceiveMessageSize` de 4 MB a 16 MB:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Para obtener más información sobre la configuración, vea <xref:grpc/configuration>.

## <a name="logging"></a>Registro

Las aplicaciones basadas en C-core usan `GrpcEnvironment` para [configurar el registrador](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) con fines de depuración. La pila de ASP.NET Core proporciona esta funcionalidad a través de la [API de registro](xref:fundamentals/logging/index). Por ejemplo, se puede agregar un registrador al servicio gRPC mediante la inserción de constructores:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Las aplicaciones basadas en C-core configuran HTTPS mediante la [propiedad Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Se usa un concepto similar para configurar servidores en ASP.NET Core. Por ejemplo, Kestrel usa la [configuración de puntos de conexión](xref:fundamentals/servers/kestrel#endpoint-configuration) para esta funcionalidad.

## <a name="grpc-interceptors-vs-middleware"></a>Interceptores de gRPC frente a middleware

El [middleware](xref:fundamentals/middleware/index) de ASP.NET Core ofrece funcionalidades similares a los interceptores de aplicaciones gRPC basadas en C-core. El middleware y los interceptores de ASP.NET Core son conceptualmente similares. Ambos:

* Se usan para construir una canalización que controla una solicitud gRPC.
* Permiten que se realicen tareas antes o después del siguiente componente de la canalización.
* Proporcionan acceso a `HttpContext`:
  * En el middleware, `HttpContext` es un parámetro.
  * En los interceptores, se puede acceder a `HttpContext` mediante el parámetro `ServerCallContext` con el método de extensión `ServerCallContext.GetHttpContext`. Tenga en cuenta que esta característica es específica de los interceptores que se ejecutan en ASP.NET Core.

Diferencias entre los interceptores de gRPC y el middleware de ASP.NET Core:

* Interceptores:
  * Operan en el nivel de abstracción de gRPC mediante la clase [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Proporcionan acceso a los elementos siguientes:
    * Al mensaje deserializado que se envía a una llamada.
    * Al mensaje que devuelve la llamada antes de que se serialice.
  * Pueden detectar y controlar las excepciones que se producen en los servicios gRPC.
* Middleware:
  * Se ejecuta antes que los interceptores de gRPC.
  * Opera en los mensajes HTTP/2 subyacentes.
  * Solo puede acceder a los bytes de las secuencias de solicitud y respuesta.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
