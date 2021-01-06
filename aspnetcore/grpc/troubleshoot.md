---
title: Solución de problemas de gRPC en .NET Core
author: jamesnk
description: Solución de problemas al usar gRPC en .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
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
uid: grpc/troubleshoot
ms.openlocfilehash: cbce85caf7ba792253ba62c6be084c8905acd00f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058719"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Solución de problemas de gRPC en .NET Core

Por [James Newton-King](https://twitter.com/jamesnk)

En este documento se describen los problemas que suelen surgir al desarrollar aplicaciones de gRPC en .NET.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Falta de coincidencia entre la configuración de cliente y servicio de SSL/TLS

La plantilla y los ejemplos de gRPC usan [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246) de forma predeterminada para proteger los servicios gRPC. Los clientes de gRPC deben usar una conexión segura para llamar correctamente a los servicios gRPC protegidos.

Puede comprobar si el servicio gRPC de ASP.NET Core usa TLS en los registros que se escriben al iniciar la aplicación. El servicio escuchará en un punto de conexión HTTPS:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

El cliente de .NET Core debe usar `https` en la dirección del servidor para realizar llamadas con una conexión segura:

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Todas las implementaciones de cliente de gRPC admiten TLS. Los clientes de gRPC de otros lenguajes de programación normalmente requieren que el canal esté configurado con `SslCredentials`. `SslCredentials` especifica el certificado que usará el cliente, y debe usarse en lugar de credenciales no seguras. Para obtener ejemplos de configuración de las distintas implementaciones de cliente de gRPC para usar TLS, vea [Autenticación en gRPC](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Llamada a un servicio gRPC con un certificado no válido o que no es de confianza

El cliente de gRPC de .NET requiere que el servicio tenga un certificado de confianza. Cuando se llama a un servicio gRPC sin un certificado de confianza, se devuelve el siguiente mensaje de error:

> Excepción no controlada. System.Net.Http.HttpRequestException: No se ha podido establecer la conexión SSL, vea la excepción interna.
> ---> System.Security.Authentication.AuthenticationException: El certificado remoto no es válido según el procedimiento de validación.

Puede aparecer este error si está probando la aplicación de forma local y el certificado de desarrollo HTTPS de ASP.NET Core no es de confianza. Para instrucciones sobre cómo corregir este problema, consulte [Confiar en el certificado de desarrollo de ASP.NET Core HTTPS en Windows y macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Si está llamando a un servicio gRPC en otro equipo y no puede confiar en el certificado, el cliente de gRPC se puede configurar para que omita el certificado no válido. En el código siguiente se usa [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) para permitir llamadas sin un certificado de confianza:

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> Los certificados que no son de confianza solo se deben usar durante el desarrollo de aplicaciones. Las aplicaciones de producción siempre deben usar certificados válidos.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Llamada a servicios gRPC no seguros con el cliente de .NET Core

Cuando una aplicación usa .NET Core 3.x, se requiere una configuración adicional para llamar a servicios gRPC no seguros con el cliente de .NET Core. El cliente de gRPC debe establecer el modificador `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` en `true` y usar `http` en la dirección del servidor:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

Las aplicaciones de .NET 5 no necesitan configuración adicional, pero para llamar a los servicios de gRPC no seguros deben usar la versión 2.32.0 o posterior de `Grpc.Net.Client`.

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>No se puede iniciar la aplicación gRPC de ASP.NET Core en macOS

Kestrel no admite HTTP/2 con TLS en macOS ni en versiones anteriores de Windows, como Windows 7. La plantilla y los ejemplos de gRPC de ASP.NET Core usan TLS de forma predeterminada. Al intentar iniciar el servidor de gRPC, verá el siguiente mensaje de error:

> No se puede enlazar a https://localhost:5001 en la interfaz de bucle invertido de IPv4: "No se admite HTTP/2 a través de TLS en macOS debido a la falta de compatibilidad con ALPN".

Para solucionar este problema, configure Kestrel y el cliente de gRPC para que use HTTP/2 *sin* TLS. Esto solo debe realizarse durante el desarrollo. Si no se usa TLS, se enviarán mensajes gRPC sin cifrado.

Kestrel debe configurar un punto de conexión HTTP/2 sin TLS en *Program.cs*:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Si un punto de conexión HTTP/2 se configura sin TLS, el valor [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) del punto de conexión debe establecerse en `HttpProtocols.Http2`. `HttpProtocols.Http1AndHttp2` no se puede usar porque se requiere TLS para negociar HTTP/2. Sin TLS, se usa HTTP/1.1 de forma predeterminada para todas las conexiones al punto de conexión y se produce un error en las llamadas a gRPC.

El cliente de gRPC también debe estar configurado para no usar TLS. Para obtener más información, consulte la sección [Llamada a servicios gRPC no seguros con el cliente de .NET Core](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> Solo se debe usar HTTP/2 sin TLS durante el desarrollo de aplicaciones. Las aplicaciones de producción siempre deben usar seguridad de transporte. Para obtener más información, vea [Consideraciones de seguridad en gRPC para ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>Recursos de C# para gRPC no generados a partir de archivos .proto

Para generar código de gRPC de clientes concretos y clases base de servicio es necesario hacer referencia a los archivos y las herramientas de Protobuf desde un proyecto. Debe incluir:

* Los archivos *.proto* que quiere usar en el grupo de elementos `<Protobuf>`. El proyecto debe hacer referencia a los [archivos *.proto* importados](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions).
* Una referencia de paquete al paquete de herramientas de gRPC [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).

Para obtener más información sobre la generación de recursos de C# para gRPC, consulte <xref:grpc/basics>.

Una aplicación web de ASP.NET Core que hospeda servicios gRPC solo necesita la clase base de servicio generada:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Una aplicación cliente de gRPC que realiza llamadas a gRPC solo necesita el cliente concreto generado:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>Imposibilidad para los proyectos de WPF de generar recursos de C# para gRPC a partir de archivos .proto

Los proyectos de WPF tienen un [problema conocido](https://github.com/dotnet/wpf/issues/810) que impide que la generación de código de gRPC se realice correctamente. Los tipos de gRPC generados en un proyecto de WPF haciendo referencia a `Grpc.Tools` y a archivos *.proto* producirán errores de compilación cuando se usen:

> Error CS0246: No se ha encontrado el tipo o el nombre del espacio de nombres "MyGrpcServices" (¿falta una directiva "using" o una referencia de ensamblado?).

Para solucionar este problema:

1. Cree un proyecto de biblioteca de clases .NET Core.
2. En el nuevo proyecto, agregue referencias para habilitar la [generación de código de C# a partir de archivos *\*.proto*](xref:grpc/basics#generated-c-assets):
    * Agregue una referencia de paquete al paquete [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).
    * Agregue archivos *\*.proto* al grupo de elementos `<Protobuf>`.
3. En la aplicación de WPF, agregue una referencia al nuevo proyecto.

La aplicación de WPF puede usar los tipos generados por gRPC a partir del nuevo proyecto de biblioteca de clases.

[!INCLUDE[](~/includes/gRPCazure.md)]
