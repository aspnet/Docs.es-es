---
title: Autenticación y autorización en gRPC para ASP.NET Core
author: jamesnk
description: Obtenga información sobre cómo usar la autenticación y autorización en gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/authn-and-authz
ms.openlocfilehash: 833114a12c8c1ac67097b3592cf410d7a69bb628
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673983"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Autenticación y autorización en gRPC para ASP.NET Core

Por [James Newton-King](https://twitter.com/jamesnk)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Autenticación de los usuarios que llaman a un servicio gRPC

gRPC se puede usar con la [autenticación de ASP.NET Core](xref:security/authentication/identity) para asociar un usuario a cada llamada.

El siguiente es un ejemplo de `Startup.Configure` en el que se usa gRPC y la autenticación de ASP.NET Core:

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> El orden en el que se registra el middleware de autenticación de ASP.NET Core es importante. Llame siempre a `UseAuthentication` y `UseAuthorization` después de `UseRouting` y antes de `UseEndpoints`.

Es necesario configurar el mecanismo de autenticación que usa la aplicación durante una llamada. La configuración de autenticación se agrega en `Startup.ConfigureServices` y será diferente en función del mecanismo de autenticación que use la aplicación. Para obtener ejemplos de cómo proteger aplicaciones ASP.NET Core, vea [Ejemplos de autenticación](xref:security/authentication/samples).

Una vez que se ha configurado la autenticación, se puede acceder al usuario en los métodos de un servicio gRPC mediante `ServerCallContext`.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Autenticación por token de portador

El cliente puede proporcionar un token de acceso para la autenticación. El servidor valida el token y lo usa para identificar al usuario.

En el servidor, la autenticación de token de portador se configura mediante el [middleware de portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

En el cliente gRPC de .NET, el token se puede enviar con llamadas usando la colección `Metadata`. Las entradas de la colección `Metadata` se envían con una llamada gRPC como encabezados HTTP:

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

La configuración de `ChannelCredentials` en un canal es una forma alternativa de enviar el token al servicio con llamadas a gRPC. Un elemento `ChannelCredentials` puede incluir `CallCredentials`, que proporcionan una manera de establecer `Metadata` de forma automática.

`CallCredentials` se ejecuta cada vez que se realiza una llamada gRPC, lo que evita la necesidad de escribir código en varios lugares para pasar el token personalmente. Tenga en cuenta que `CallCredentials` solo se aplican si el canal está protegido con TLS. `CallCredentials` no se aplican en los canales sin protección que no son TLS.

La credencial del ejemplo siguiente configura el canal para enviar el token con cada llamada a gRPC:

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>Autenticación de certificados de clientes

Un cliente podría proporcionar alternativamente un certificado de cliente para la autenticación. La [autenticación de certificados](https://tools.ietf.org/html/rfc5246#section-7.4.4) se realiza en el nivel de TLS, mucho antes de que llegue a ASP.NET Core. Cuando la solicitud entra en ASP.NET Core, el [paquete de autenticación de certificado de cliente](xref:security/authentication/certauth) le permite resolver el certificado en un elemento `ClaimsPrincipal`.

> [!NOTE]
> Configure el servidor para que acepte certificados de cliente. Para obtener información sobre la aceptación de certificados de cliente en Kestrel, IIS y Azure, vea <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.

En el cliente gRPC de .NET, el certificado de cliente se agrega a `HttpClientHandler`, que después se usa para crear el cliente gRPC:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Otros mecanismos de autenticación

Muchos mecanismos de autenticación admitidos por ASP.NET Core funcionan con gRPC:

* Azure Active Directory
* Certificado de cliente
* IdentityServidor
* Token de JWT
* OAuth 2.0
* OpenID Connect
* WS-Federation

Para obtener más información sobre la configuración de la autenticación en el servidor, vea [Autenticación de ASP.NET Core](xref:security/authentication/identity).

La configuración del cliente gRPC para usar la autenticación dependerá del mecanismo de autenticación que se use. En los ejemplos anteriores de token de portador y certificado de cliente se muestran un par de formas de configurar el cliente gRPC para enviar metadatos de autenticación con llamadas a gRPC:

* Los clientes gRPC fuertemente tipados usan `HttpClient` internamente. La autenticación se puede configurar en [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler) o mediante la adición de instancias de [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) personalizadas a `HttpClient`.
* Cada llamada a gRPC tiene un argumento `CallOptions` opcional. Se pueden enviar encabezados personalizados mediante la colección de encabezados de la opción.

> [!NOTE]
> La autenticación de Windows (NTLM/Kerberos/Negotiate) no se puede usar con gRPC. gRPC requiere HTTP/2 y HTTP/2 no admite la autenticación de Windows.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorización a los usuarios para acceder a servicios y métodos de servicio

De forma predeterminada, los usuarios no autenticados pueden llamar a todos los métodos de un servicio. Para requerir la autenticación, aplique el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) al servicio:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Puede usar los argumentos de constructor y las propiedades del atributo `[Authorize]` para restringir el acceso solo a los usuarios que coincidan con [directivas de autorización](xref:security/authorization/policies) específicas. Por ejemplo, si tiene una directiva de autorización personalizada llamada `MyAuthorizationPolicy`, asegúrese de que solo los usuarios que coincidan con esa directiva puedan acceder al servicio mediante el código siguiente:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

A los métodos de servicio individuales también se les puede aplicar el atributo `[Authorize]`. Si el usuario actual no coincide con las directivas aplicadas **tanto** al método como a la clase, se devuelve un error al autor de la llamada:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>Recursos adicionales

* [Autenticación de token de portador en ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Configuración de la autenticación del certificado de cliente en ASP.NET Core](xref:security/authentication/certauth)
