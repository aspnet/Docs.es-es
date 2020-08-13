---
title: Configuración gRPC para .NET
author: jamesnk
description: Obtenga información sobre cómo configurar gRPC para aplicaciones .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/26/2020
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
uid: grpc/configuration
ms.openlocfilehash: e6767aedcf9c6dbcbd7eec97e1c7a5b6161992aa
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016198"
---
# <a name="grpc-for-net-configuration"></a>Configuración gRPC para .NET

## <a name="configure-services-options"></a>Configuración de opciones de servicios

Los servicios gRPC se configuran con `AddGrpc` en *Startup.cs*. La tabla siguiente describe las opciones disponibles para configurar los servicios gRPC:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Tamaño máximo de mensaje en bytes que se puede enviar desde el servidor. Al intentar enviar un mensaje que supere el tamaño máximo configurado del mensaje, se produce una excepción. Cuando se establece en `null`, el tamaño del mensaje es ilimitado. |
| MaxReceiveMessageSize | 4 MB | Tamaño máximo de mensaje en bytes que puede recibir el servidor. Si el servidor recibe un mensaje que supere este límite, se produce una excepción. Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria. Cuando se establece en `null`, el tamaño del mensaje es ilimitado. |
| EnableDetailedErrors | `false` | Si es `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produzca una excepción en un método de servicio. De manera predeterminada, es `false`. Si se establece `EnableDetailedErrors` en `true`, se puede perder información confidencial. |
| CompressionProviders | gzip | Colección de proveedores de compresión usados para comprimir y descomprimir mensajes. Los proveedores personalizados de compresión se pueden crear y agregar a la colección. Los proveedores configurados de forma predeterminada admiten la compresión **gzip**. |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Algoritmo de compresión que se usa para comprimir los mensajes enviados desde el servidor. El algoritmo debe coincidir con un proveedor de compresión en `CompressionProviders`. Para que el algoritmo pueda comprimir una respuesta, el cliente debe indicar que es compatible con el algoritmo enviándola en el encabezado **grpc-accept-encoding**. |
| ResponseCompressionLevel | `null` | Nivel de compresión utilizado para comprimir los mensajes enviados desde el servidor. |
| Interceptores | None | Colección de interceptores que se ejecutan con cada llamada a gRPC. Los interceptores se ejecutan en el orden en que se registran. Los interceptores configurados globalmente se ejecutan antes que los interceptores configurados para un servicio único. Para obtener más información sobre los interceptores de gRPC, vea [Interceptores de gRPC frente a middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |
| IgnoreUnknownServices | `false` | Si es `true`, las llamadas a servicios y métodos desconocidos no devuelven el estado **UNIMPLEMENTED** y la solicitud pasa al siguiente middleware registrado en ASP.NET Core. |

Las opciones se pueden configurar para todos los servicios proporcionando un delegado de opciones a la llamada `AddGrpc` en `Startup.ConfigureServices`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Las opciones de un servicio único invalidan las opciones globales proporcionadas en `AddGrpc` y se pueden configurar mediante `AddServiceOptions<TService>`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configuración de opciones de cliente

La configuración de cliente gRPC se establece en `GrpcChannelOptions`. En la tabla siguiente se describen las opciones disponibles para configurar los canales gRPC:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| HttpHandler | Nueva instancia | El elemento `HttpMessageHandler` que se usa para realizar llamadas gRPC. Se puede establecer un cliente para configurar un elemento `HttpClientHandler` personalizado o agregar controladores adicionales a la canalización HTTP para llamadas gRPC. Si no se especifica ningún elemento `HttpMessageHandler`, se creará una nueva instancia de `HttpClientHandler` para el canal con eliminación automática. |
| HttpClient | `null` | El elemento `HttpClient` que se usa para realizar llamadas gRPC. Este valor es una alternativa a `HttpHandler`. |
| DisposeHttpClient | `false` | Si se establece en `true` y se especifica un elemento `HttpMessageHandler` o `HttpClient`, el elemento `HttpHandler` o el elemento `HttpClient` respectivo se eliminará al eliminarse el elemento `GrpcChannel`. |
| LoggerFactory | `null` | El elemento `LoggerFactory` que usa el cliente para registrar información acerca de las llamadas gRPC. Una instancia de `LoggerFactory` se puede resolver a partir de la inserción de dependencia o crearse mediante `LoggerFactory.Create`. Para obtener ejemplos de cómo configurar el registro, vea <xref:grpc/diagnostics#grpc-client-logging>. |
| MaxSendMessageSize | `null` | Tamaño máximo de mensaje en bytes que se puede enviar desde el cliente. Al intentar enviar un mensaje que supere el tamaño máximo configurado del mensaje, se produce una excepción. Cuando se establece en `null`, el tamaño del mensaje es ilimitado. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | Tamaño máximo de mensaje en bytes que puede recibir el cliente. Si el cliente recibe un mensaje que supere este límite, se produce una excepción. Aumentar este valor permite que el cliente reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria. Cuando se establece en `null`, el tamaño del mensaje es ilimitado. |
| Credenciales | `null` | Instancia de `ChannelCredentials`. Las credenciales se usan para agregar metadatos de autenticación a llamadas gRPC. |
| CompressionProviders | gzip | Colección de proveedores de compresión usados para comprimir y descomprimir mensajes. Los proveedores personalizados de compresión se pueden crear y agregar a la colección. Los proveedores configurados de forma predeterminada admiten la compresión **gzip**. |

El código siguiente:

* Establece el tamaño máximo de mensaje de envío y recepción en el canal.
* Crea un cliente.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
