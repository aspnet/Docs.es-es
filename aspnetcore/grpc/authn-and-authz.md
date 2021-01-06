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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "94673983"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="83f55-103">Autenticación y autorización en gRPC para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83f55-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="83f55-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="83f55-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="83f55-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="83f55-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="83f55-106">Autenticación de los usuarios que llaman a un servicio gRPC</span><span class="sxs-lookup"><span data-stu-id="83f55-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="83f55-107">gRPC se puede usar con la [autenticación de ASP.NET Core](xref:security/authentication/identity) para asociar un usuario a cada llamada.</span><span class="sxs-lookup"><span data-stu-id="83f55-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="83f55-108">El siguiente es un ejemplo de `Startup.Configure` en el que se usa gRPC y la autenticación de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="83f55-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="83f55-109">El orden en el que se registra el middleware de autenticación de ASP.NET Core es importante.</span><span class="sxs-lookup"><span data-stu-id="83f55-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="83f55-110">Llame siempre a `UseAuthentication` y `UseAuthorization` después de `UseRouting` y antes de `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="83f55-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="83f55-111">Es necesario configurar el mecanismo de autenticación que usa la aplicación durante una llamada.</span><span class="sxs-lookup"><span data-stu-id="83f55-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="83f55-112">La configuración de autenticación se agrega en `Startup.ConfigureServices` y será diferente en función del mecanismo de autenticación que use la aplicación.</span><span class="sxs-lookup"><span data-stu-id="83f55-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="83f55-113">Para obtener ejemplos de cómo proteger aplicaciones ASP.NET Core, vea [Ejemplos de autenticación](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="83f55-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="83f55-114">Una vez que se ha configurado la autenticación, se puede acceder al usuario en los métodos de un servicio gRPC mediante `ServerCallContext`.</span><span class="sxs-lookup"><span data-stu-id="83f55-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="83f55-115">Autenticación por token de portador</span><span class="sxs-lookup"><span data-stu-id="83f55-115">Bearer token authentication</span></span>

<span data-ttu-id="83f55-116">El cliente puede proporcionar un token de acceso para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="83f55-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="83f55-117">El servidor valida el token y lo usa para identificar al usuario.</span><span class="sxs-lookup"><span data-stu-id="83f55-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="83f55-118">En el servidor, la autenticación de token de portador se configura mediante el [middleware de portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="83f55-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="83f55-119">En el cliente gRPC de .NET, el token se puede enviar con llamadas usando la colección `Metadata`.</span><span class="sxs-lookup"><span data-stu-id="83f55-119">In the .NET gRPC client, the token can be sent with calls by using the `Metadata` collection.</span></span> <span data-ttu-id="83f55-120">Las entradas de la colección `Metadata` se envían con una llamada gRPC como encabezados HTTP:</span><span class="sxs-lookup"><span data-stu-id="83f55-120">Entries in the `Metadata` collection are sent with a gRPC call as HTTP headers:</span></span>

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

<span data-ttu-id="83f55-121">La configuración de `ChannelCredentials` en un canal es una forma alternativa de enviar el token al servicio con llamadas a gRPC.</span><span class="sxs-lookup"><span data-stu-id="83f55-121">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="83f55-122">Un elemento `ChannelCredentials` puede incluir `CallCredentials`, que proporcionan una manera de establecer `Metadata` de forma automática.</span><span class="sxs-lookup"><span data-stu-id="83f55-122">A `ChannelCredentials` can include `CallCredentials`, which provide a way to automatically set `Metadata`.</span></span>

<span data-ttu-id="83f55-123">`CallCredentials` se ejecuta cada vez que se realiza una llamada gRPC, lo que evita la necesidad de escribir código en varios lugares para pasar el token personalmente.</span><span class="sxs-lookup"><span data-stu-id="83f55-123">`CallCredentials` is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span> <span data-ttu-id="83f55-124">Tenga en cuenta que `CallCredentials` solo se aplican si el canal está protegido con TLS.</span><span class="sxs-lookup"><span data-stu-id="83f55-124">Note that `CallCredentials` are only applied if the channel is secured with TLS.</span></span> <span data-ttu-id="83f55-125">`CallCredentials` no se aplican en los canales sin protección que no son TLS.</span><span class="sxs-lookup"><span data-stu-id="83f55-125">`CallCredentials` aren't applied on unsecured non-TLS channels.</span></span>

<span data-ttu-id="83f55-126">La credencial del ejemplo siguiente configura el canal para enviar el token con cada llamada a gRPC:</span><span class="sxs-lookup"><span data-stu-id="83f55-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="83f55-127">Autenticación de certificados de clientes</span><span class="sxs-lookup"><span data-stu-id="83f55-127">Client certificate authentication</span></span>

<span data-ttu-id="83f55-128">Un cliente podría proporcionar alternativamente un certificado de cliente para la autenticación.</span><span class="sxs-lookup"><span data-stu-id="83f55-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="83f55-129">La [autenticación de certificados](https://tools.ietf.org/html/rfc5246#section-7.4.4) se realiza en el nivel de TLS, mucho antes de que llegue a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83f55-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="83f55-130">Cuando la solicitud entra en ASP.NET Core, el [paquete de autenticación de certificado de cliente](xref:security/authentication/certauth) le permite resolver el certificado en un elemento `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="83f55-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="83f55-131">Configure el servidor para que acepte certificados de cliente.</span><span class="sxs-lookup"><span data-stu-id="83f55-131">Configure the server to accept client certificates.</span></span> <span data-ttu-id="83f55-132">Para obtener información sobre la aceptación de certificados de cliente en Kestrel, IIS y Azure, vea <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span><span class="sxs-lookup"><span data-stu-id="83f55-132">For information on accepting client certificates in Kestrel, IIS, and Azure, see <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span></span>

<span data-ttu-id="83f55-133">En el cliente gRPC de .NET, el certificado de cliente se agrega a `HttpClientHandler`, que después se usa para crear el cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="83f55-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="83f55-134">Otros mecanismos de autenticación</span><span class="sxs-lookup"><span data-stu-id="83f55-134">Other authentication mechanisms</span></span>

<span data-ttu-id="83f55-135">Muchos mecanismos de autenticación admitidos por ASP.NET Core funcionan con gRPC:</span><span class="sxs-lookup"><span data-stu-id="83f55-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="83f55-136">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="83f55-136">Azure Active Directory</span></span>
* <span data-ttu-id="83f55-137">Certificado de cliente</span><span class="sxs-lookup"><span data-stu-id="83f55-137">Client Certificate</span></span>
* <span data-ttu-id="83f55-138">IdentityServidor</span><span class="sxs-lookup"><span data-stu-id="83f55-138">IdentityServer</span></span>
* <span data-ttu-id="83f55-139">Token de JWT</span><span class="sxs-lookup"><span data-stu-id="83f55-139">JWT Token</span></span>
* <span data-ttu-id="83f55-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="83f55-140">OAuth 2.0</span></span>
* <span data-ttu-id="83f55-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="83f55-141">OpenID Connect</span></span>
* <span data-ttu-id="83f55-142">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="83f55-142">WS-Federation</span></span>

<span data-ttu-id="83f55-143">Para obtener más información sobre la configuración de la autenticación en el servidor, vea [Autenticación de ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="83f55-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="83f55-144">La configuración del cliente gRPC para usar la autenticación dependerá del mecanismo de autenticación que se use.</span><span class="sxs-lookup"><span data-stu-id="83f55-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="83f55-145">En los ejemplos anteriores de token de portador y certificado de cliente se muestran un par de formas de configurar el cliente gRPC para enviar metadatos de autenticación con llamadas a gRPC:</span><span class="sxs-lookup"><span data-stu-id="83f55-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="83f55-146">Los clientes gRPC fuertemente tipados usan `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="83f55-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="83f55-147">La autenticación se puede configurar en [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler) o mediante la adición de instancias de [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) personalizadas a `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="83f55-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="83f55-148">Cada llamada a gRPC tiene un argumento `CallOptions` opcional.</span><span class="sxs-lookup"><span data-stu-id="83f55-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="83f55-149">Se pueden enviar encabezados personalizados mediante la colección de encabezados de la opción.</span><span class="sxs-lookup"><span data-stu-id="83f55-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="83f55-150">La autenticación de Windows (NTLM/Kerberos/Negotiate) no se puede usar con gRPC.</span><span class="sxs-lookup"><span data-stu-id="83f55-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="83f55-151">gRPC requiere HTTP/2 y HTTP/2 no admite la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="83f55-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="83f55-152">Autorización a los usuarios para acceder a servicios y métodos de servicio</span><span class="sxs-lookup"><span data-stu-id="83f55-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="83f55-153">De forma predeterminada, los usuarios no autenticados pueden llamar a todos los métodos de un servicio.</span><span class="sxs-lookup"><span data-stu-id="83f55-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="83f55-154">Para requerir la autenticación, aplique el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) al servicio:</span><span class="sxs-lookup"><span data-stu-id="83f55-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="83f55-155">Puede usar los argumentos de constructor y las propiedades del atributo `[Authorize]` para restringir el acceso solo a los usuarios que coincidan con [directivas de autorización](xref:security/authorization/policies) específicas.</span><span class="sxs-lookup"><span data-stu-id="83f55-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="83f55-156">Por ejemplo, si tiene una directiva de autorización personalizada llamada `MyAuthorizationPolicy`, asegúrese de que solo los usuarios que coincidan con esa directiva puedan acceder al servicio mediante el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="83f55-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="83f55-157">A los métodos de servicio individuales también se les puede aplicar el atributo `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="83f55-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="83f55-158">Si el usuario actual no coincide con las directivas aplicadas **tanto** al método como a la clase, se devuelve un error al autor de la llamada:</span><span class="sxs-lookup"><span data-stu-id="83f55-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="83f55-159">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="83f55-159">Additional resources</span></span>

* [<span data-ttu-id="83f55-160">Autenticación de token de portador en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83f55-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="83f55-161">Configuración de la autenticación del certificado de cliente en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83f55-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
