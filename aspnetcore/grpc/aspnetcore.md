---
title: Servicios gRPC con ASP.NET Core
author: juntaoluo
description: Conozca los conceptos básicos a la hora de escribir servicios gRPC con ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: 1a5510364ee46165e275d07073ab087d79d65313
ms.sourcegitcommit: 50d3e939a90c5480df480f651dda032901468dd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819047"
---
# <a name="grpc-services-with-aspnet-core"></a>Servicios gRPC con ASP.NET Core

En este documento se muestra cómo empezar a usar servicios gRPC con ASP.NET Core.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Introducción al servicio gRPC en ASP.NET Core

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Consulte el artículo [Introducción a los servicios gRPC](xref:tutorials/grpc/grpc-start) para obtener instrucciones detalladas sobre cómo crear un proyecto gRPC.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Ejecute `dotnet new grpc -o GrpcGreeter` desde la línea de comandos.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Incorporación de servicios gRPC a una aplicación de ASP.NET Core

gRPC requiere el paquete [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore).

### <a name="configure-grpc"></a>Configuración de gRPC

En *Startup.cs*:

* gRPC se habilita con el método `AddGrpc`.
* Cada servicio gRPC se agrega a la canalización de enrutamiento a través del método `MapGrpcService`.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

El middleware y las características de ASP.NET Core comparten la canalización de enrutamiento, por lo que se puede configurar una aplicación para que preste servicio a controladores de solicitudes adicionales. Los controladores de solicitudes adicionales, como los controladores MVC, trabajan en paralelo con los servicios gRPC configurados.

## <a name="server-options"></a>Opciones de servidor

Los servicios gRPC se pueden hospedar por todos los servidores de ASP.NET Core integrados.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS requiere .NET 5 y Windows 10, compilación 20241 o posterior.

&Dagger;HTTP.sys requiere .NET 5 y Windows 10, compilación 19529 o posterior.

Para obtener más información sobre cómo elegir el servidor adecuado para una aplicación ASP.NET Core, vea <xref:fundamentals/servers/index>.

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) es un servidor web multiplataforma de ASP.NET Core. Kestrel proporciona el mejor rendimiento y uso de memoria, pero carece de algunas de las características avanzadas de HTTP.sys (como el uso compartido de puertos).

Los puntos de conexión para gRPC de Kestrel:

* Requieren HTTP/2.
* Deben protegerse con [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246).

### <a name="http2"></a>HTTP/2

gRPC requiere HTTP/2. gRPC para ASP.NET Core valida que [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) sea `HTTP/2`.

Kestrel [admite HTTP/2](xref:fundamentals/servers/kestrel/http2) en la mayoría de los sistemas operativos modernos. Los puntos de conexión de Kestrel se configuran para admitir conexiones HTTP/1.1 y HTTP/2 de forma predeterminada.

### <a name="tls"></a>TLS

Los puntos de conexión de Kestrel usados para gRPC deben protegerse con TLS. En la fase de desarrollo, se crea automáticamente un punto de conexión protegido con TLS en `https://localhost:5001` cuando el certificado de desarrollo de ASP.NET Core está presente. No se requiere ninguna configuración. Un prefijo `https` comprueba que el punto de conexión de Kestrel está usando TLS.

En un entorno de producción, se debe configurar TLS explícitamente. En el siguiente ejemplo de *appsettings.json* , se proporciona un punto de conexión HTTP/2 protegido con TLS:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Como alternativa, se pueden configurar puntos de conexión de Kestrel en *Program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>Negociación del protocolo

TLS no solo se usa para proteger la comunicación. El protocolo de enlace [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) de TLS se usa para negociar el protocolo de conexión entre el cliente y el servidor cuando un punto de conexión admite varios protocolos. Esta negociación determina si la conexión usa HTTP/1.1 o HTTP/2.

Si un punto de conexión HTTP/2 se configura sin TLS, el valor [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) del punto de conexión debe establecerse en `HttpProtocols.Http2`. No se puede usar un punto de conexión con varios protocolos (por ejemplo, `HttpProtocols.Http1AndHttp2`) sin TLS, porque no hay ninguna negociación. Se usa HTTP/1.1 de forma predeterminada para todas las conexiones al punto de conexión no seguro y se produce un error en las llamadas a gRPC.

Para obtener más información sobre cómo habilitar HTTP/2 y TLS con Kestrel, consulte la sección sobre la [configuración del punto de conexión de Kestrel](xref:fundamentals/servers/kestrel/endpoints).

> [!NOTE]
> macOS no admite gRPC de ASP.NET Core con TLS. Se requiere configuración adicional para ejecutar correctamente servicios gRPC en macOS. Para obtener más información, vea [No se puede iniciar la aplicación gRPC de ASP.NET Core en macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="iis"></a>IIS

[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) es un servidor web flexible, seguro y administrable para el hospedaje de aplicaciones web, incluido ASP.NET Core. Se requieren .NET 5 y Windows 10, compilación 20241 o posterior, para hospedar servicios de gRPC con IIS.

IIS debe estar configurado para usar TLS y HTTP/2. Para obtener más información, vea <xref:host-and-deploy/iis/protocols>.

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) es un servidor web de ASP.NET Core que solo se ejecuta en Windows. Se requieren .NET 5 y Windows 10, compilación 19529 o posterior, para hospedar servicios de gRPC con HTTP.sys.

HTTP.sys debe estar configurado para usar TLS y HTTP/2. Para obtener más información, vea [Compatibilidad con HTTP/2 del servidor web HTTP.sys](xref:fundamentals/servers/httpsys#http2-support).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) es un servidor web multiplataforma de ASP.NET Core. Kestrel proporciona el mejor rendimiento y uso de memoria, pero carece de algunas de las características avanzadas de HTTP.sys (como el uso compartido de puertos).

Los puntos de conexión para gRPC de Kestrel:

* Requieren HTTP/2.
* Deben protegerse con [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246).

### <a name="http2"></a>HTTP/2

gRPC requiere HTTP/2. gRPC para ASP.NET Core valida que [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) sea `HTTP/2`.

Kestrel [admite HTTP/2](xref:fundamentals/servers/kestrel#http2-support) en la mayoría de los sistemas operativos modernos. Los puntos de conexión de Kestrel se configuran para admitir conexiones HTTP/1.1 y HTTP/2 de forma predeterminada.

### <a name="tls"></a>TLS

Los puntos de conexión de Kestrel usados para gRPC deben protegerse con TLS. En la fase de desarrollo, se crea automáticamente un punto de conexión protegido con TLS en `https://localhost:5001` cuando el certificado de desarrollo de ASP.NET Core está presente. No se requiere ninguna configuración. Un prefijo `https` comprueba que el punto de conexión de Kestrel está usando TLS.

En un entorno de producción, se debe configurar TLS explícitamente. En el siguiente ejemplo de *appsettings.json* , se proporciona un punto de conexión HTTP/2 protegido con TLS:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Como alternativa, se pueden configurar puntos de conexión de Kestrel en *Program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>Negociación del protocolo

TLS no solo se usa para proteger la comunicación. El protocolo de enlace [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) de TLS se usa para negociar el protocolo de conexión entre el cliente y el servidor cuando un punto de conexión admite varios protocolos. Esta negociación determina si la conexión usa HTTP/1.1 o HTTP/2.

Si un punto de conexión HTTP/2 se configura sin TLS, el valor [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) del punto de conexión debe establecerse en `HttpProtocols.Http2`. No se puede usar un punto de conexión con varios protocolos (por ejemplo, `HttpProtocols.Http1AndHttp2`) sin TLS, porque no hay ninguna negociación. Se usa HTTP/1.1 de forma predeterminada para todas las conexiones al punto de conexión no seguro y se produce un error en las llamadas a gRPC.

Para obtener más información sobre cómo habilitar HTTP/2 y TLS con Kestrel, consulte la sección sobre la [configuración del punto de conexión de Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> macOS no admite gRPC de ASP.NET Core con TLS. Se requiere configuración adicional para ejecutar correctamente servicios gRPC en macOS. Para obtener más información, vea [No se puede iniciar la aplicación gRPC de ASP.NET Core en macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a>Integración con las API de ASP.NET Core

Los servicios gRPC tienen acceso total a las características de ASP.NET Core, como la [inserción de dependencias](xref:fundamentals/dependency-injection) (ID) y los [registros](xref:fundamentals/logging/index). Por ejemplo, la implementación de los servicios puede resolver un servicio del registrador desde el contenedor de inserción de dependencias mediante el constructor:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

De forma predeterminada, la implementación de los servicios gRPC puede resolver otros servicios de inserción de dependencias con cualquier duración (singleton, restringida o transitoria).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Resolución de HttpContext en métodos gRPC

La API de gRPC proporciona acceso a algunos datos de mensajes HTTP/2, como el método, el host, el encabezado y los finalizadores. El acceso se realiza a través del argumento `ServerCallContext` que se pasa a cada método gRPC:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` no proporciona acceso completo a `HttpContext` en todas las API de ASP.NET. Sin embargo, el método de extensión `GetHttpContext` sí proporciona acceso completo al objeto `HttpContext` que representa el mensaje HTTP/2 subyacente en las API de ASP.NET:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
