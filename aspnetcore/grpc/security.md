---
title: Consideraciones de seguridad en gRPC para ASP.NET Core
author: jamesnk
description: Obtenga información sobre las consideraciones de seguridad de gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: ca03a129281377af9271d56e2400d1a238358554
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632660"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Consideraciones de seguridad en gRPC para ASP.NET Core

Por [James Newton-King](https://twitter.com/jamesnk)

En este artículo se proporciona información sobre cómo proteger gRPC con .NET Core.

## <a name="transport-security"></a>Seguridad de transporte

Los mensajes gRPC se envían y se reciben mediante HTTP/2. Es recomendable que:

* Se use [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246) para proteger los mensajes en aplicaciones gRPC de producción.
* Los servicios gRPC solo deben escuchar y responder a través de puertos seguros.

TLS esté configurado en Kestrel. Para obtener más información sobre cómo configurar puntos de conexión de Kestrel, vea [Configuración de puntos de conexión de Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Excepciones

Los mensajes de excepción generalmente se consideran datos confidenciales que no se deben revelar a un cliente. De forma predeterminada, gRPC no envía al cliente los detalles de una excepción iniciada por un servicio gRPC. En su lugar, el cliente recibe un mensaje genérico en el que se indica que se ha producido un error. La entrega de mensajes de excepción al cliente se puede invalidar (por ejemplo, en desarrollo o prueba) con [EnableDetailedErrors](xref:grpc/configuration#configure-services-options). Los mensajes de excepción no se deben exponer al cliente en las aplicaciones de producción.

## <a name="message-size-limits"></a>Límites de tamaño de los mensajes

Los mensajes entrantes a los clientes y servicios gRPC se cargan en la memoria. Los límites de tamaño de los mensajes son un mecanismo que ayuda a evitar que gRPC consuma demasiados recursos.

gRPC usa los límites de tamaño por mensaje para administrar los mensajes entrantes y salientes. De forma predeterminada, gRPC limita los mensajes entrantes a 4 MB. No hay ningún límite para los mensajes salientes.

En el servidor, los límites de los mensajes de gRPC se pueden configurar para todos los servicios de una aplicación con `AddGrpc`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

Los límites también se pueden configurar para un servicio individual mediante `AddServiceOptions<TService>`. Para obtener más información sobre cómo configurar los límites de tamaño de los mensajes, vea [Configuración de gRPC](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Validación de certificados de cliente

Los [certificados de cliente](https://tools.ietf.org/html/rfc5246#section-7.4.4) se validan inicialmente cuando se establece la conexión. De forma predeterminada, Kestrel no realiza ninguna validación adicional del certificado de cliente de una conexión.

Se recomienda que los servicios gRPC protegidos por certificados de cliente usen el paquete [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth). La autenticación de certificación de ASP.NET Core realizará una validación adicional en un certificado de cliente, lo que incluye lo siguiente:

* El certificado tiene un uso mejorado de clave (EKU) válido.
* Está dentro de su período de validez.
* Se comprueba la revocación de certificados.
