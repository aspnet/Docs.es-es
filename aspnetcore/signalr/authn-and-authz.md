---
title: Autenticación y autorización en ASP.NET Core SignalR
author: bradygaster
description: Aprenda a usar la autenticación y autorización en ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
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
uid: signalr/authn-and-authz
ms.openlocfilehash: 3a2ae5c7bc4853bad7b94af0d26ad5cd0358688f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628942"
---
# <a name="authentication-and-authorization-in-aspnet-core-no-locsignalr"></a>Autenticación y autorización en ASP.NET Core SignalR

Por [Andrew Stanton-enfermera](https://twitter.com/anurse)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-no-locsignalr-hub"></a>Autenticar a los usuarios que se conectan a un SignalR centro

SignalR se puede usar con la [autenticación de ASP.net Core](xref:security/authentication/identity) para asociar un usuario a cada conexión. En un concentrador, se puede tener acceso a los datos de autenticación desde la propiedad [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) . La autenticación permite que el concentrador llame a métodos en todas las conexiones asociadas a un usuario. Para obtener más información, vea [administrar usuarios y grupos SignalR en ](xref:signalr/groups). Es posible que varias conexiones estén asociadas a un solo usuario.

El siguiente es un ejemplo del `Startup.Configure` uso de SignalR y la autenticación ASP.net Core:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> El orden en el que se registran SignalR y ASP.net Core middleware de autenticación es importante. Llame siempre a `UseAuthentication` antes de `UseSignalR` para que SignalR tenga un usuario en `HttpContext` .

::: moniker-end

### <a name="no-loccookie-authentication"></a>autenticación Cookie

En una aplicación basada en explorador, cookie la autenticación permite que las credenciales de usuario existentes fluyan automáticamente a SignalR las conexiones. Cuando se usa el cliente del explorador, no se necesita ninguna configuración adicional. Si el usuario ha iniciado sesión en la aplicación, la SignalR conexión hereda automáticamente esta autenticación.

Cookies son una forma específica del explorador de enviar tokens de acceso, pero los clientes que no son de explorador pueden enviarlos. Cuando se usa el [cliente de .net](xref:signalr/dotnet-client), la `Cookies` propiedad se puede configurar en la `.WithUrl` llamada para proporcionar un cookie . Sin embargo, cookie el uso de la autenticación desde el cliente .net requiere que la aplicación proporcione una API para intercambiar los datos de autenticación de cookie .

### <a name="bearer-token-authentication"></a>Autenticación por token de portador

El cliente puede proporcionar un token de acceso en lugar de usar un cookie . El servidor valida el token y lo usa para identificar al usuario. Esta validación se realiza solo cuando se establece la conexión. Durante la vida de la conexión, el servidor no se vuelve a validar automáticamente para comprobar la revocación de tokens.

En el servidor, la autenticación de token de portador se configura mediante el [middleware de portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

En el cliente de JavaScript, el token se puede proporcionar mediante la opción [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

En el cliente de .NET, hay una propiedad [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) similar que se puede usar para configurar el token:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Se llama a la función de token de acceso que se proporciona antes de **cada** solicitud HTTP realizada por SignalR . Si necesita renovar el token para mantener la conexión activa (porque puede expirar durante la conexión), hágalo desde esta función y devuelva el token actualizado.

En las API Web estándar, los tokens de portador se envían en un encabezado HTTP. Sin embargo, SignalR no puede establecer estos encabezados en exploradores cuando se usan algunos transportes. Cuando se usan WebSockets y eventos enviados por el servidor, el token se transmite como un parámetro de cadena de consulta. Para admitir esto en el servidor, se requiere una configuración adicional:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> La cadena de consulta se usa en exploradores al conectarse con WebSockets y eventos enviados por el servidor debido a las limitaciones de la API del explorador. Cuando se usa HTTPS, los valores de cadena de consulta están protegidos por la conexión TLS. Sin embargo, muchos servidores registran valores de cadena de consulta. Para obtener más información, vea [consideraciones de seguridad SignalR en ASP.net Core ](xref:signalr/security). SignalR utiliza encabezados para transmitir tokens en entornos que los admiten (como los clientes de .NET y Java).

### <a name="no-loccookies-vs-bearer-tokens"></a>Cookies frente a tokens de portador 

Cookies son específicos de los exploradores. Enviarlos desde otros tipos de clientes agrega complejidad en comparación con el envío de tokens de portador. Por lo tanto, cookie no se recomienda la autenticación a menos que la aplicación solo necesite autenticar a los usuarios desde el cliente del explorador. La autenticación de token de portador es el enfoque recomendado al usar clientes que no sean el cliente del explorador.

### <a name="windows-authentication"></a>Autenticación de Windows

Si la [autenticación de Windows](xref:security/authentication/windowsauth) está configurada en la aplicación, SignalR puede usar esa identidad para proteger los concentradores. Sin embargo, para enviar mensajes a usuarios individuales, debe agregar un proveedor de ID. de usuario personalizado. El sistema de autenticación de Windows no proporciona la demanda de "identificador de nombre". SignalR usa la Claim para determinar el nombre de usuario.

Agregue una nueva clase que implemente `IUserIdProvider` y recupere una de las notificaciones del usuario que se va a usar como identificador. Por ejemplo, para usar la notificaciones "Name" (que es el nombre de usuario de Windows en el formulario `[Domain]\[Username]` ), cree la clase siguiente:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

En lugar de `ClaimTypes.Name` , puede usar cualquier valor de `User` (como el identificador de SID de Windows, etc.).

> [!NOTE]
> El valor que elija debe ser único entre todos los usuarios del sistema. De lo contrario, un mensaje destinado a un usuario podría acabar pasando a otro usuario.

Registre este componente en el `Startup.ConfigureServices` método.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

En el cliente de .NET, la autenticación de Windows debe habilitarse estableciendo la propiedad [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

La autenticación de Windows se admite en Internet Explorer y Microsoft Edge, pero no en todos los exploradores. Por ejemplo, en Chrome y Safari, se produce un error al intentar usar la autenticación de Windows y WebSockets. Cuando se produce un error en la autenticación de Windows, el cliente intenta revertir a otros transportes que podrían funcionar.

### <a name="use-claims-to-customize-identity-handling"></a>Usar notificaciones para personalizar el control de identidades

Una aplicación que autentica a los usuarios puede derivar los SignalR ID. de usuario de las notificaciones de usuario. Para especificar cómo SignalR crea los ID. de usuario, implementa `IUserIdProvider` y registra la implementación.

En el código de ejemplo se muestra cómo usar las notificaciones para seleccionar la dirección de correo electrónico del usuario como la propiedad que identifica. 

> [!NOTE]
> El valor que elija debe ser único entre todos los usuarios del sistema. De lo contrario, un mensaje destinado a un usuario podría acabar pasando a otro usuario.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

El registro de la cuenta agrega una demanda con `ClaimsTypes.Email` el tipo a la base de datos de identidad de ASP.net.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Registre este componente en su `Startup.ConfigureServices` .

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autorización para que los usuarios accedan a los concentradores y métodos de concentrador

De forma predeterminada, los usuarios no autenticados pueden llamar a todos los métodos de un concentrador. Para requerir la autenticación, aplique el atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) al centro:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Puede usar los argumentos de constructor y las propiedades del atributo `[Authorize]` para restringir el acceso solo a los usuarios que coincidan con [directivas de autorización](xref:security/authorization/policies) específicas. Por ejemplo, si tiene una directiva de autorización personalizada denominada, `MyAuthorizationPolicy` puede asegurarse de que solo los usuarios que coincidan con esa Directiva puedan tener acceso al centro mediante el código siguiente:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Los métodos de concentrador individuales también pueden tener el `[Authorize]` atributo aplicado. Si el usuario actual no coincide con la Directiva que se aplica al método, se devuelve un error al autor de la llamada:

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Usar controladores de autorización para personalizar la autorización del método de concentrador

SignalR proporciona un recurso personalizado a los controladores de autorización cuando un método de concentrador requiere autorización. El recurso es una instancia de `HubInvocationContext`. `HubInvocationContext`Incluye `HubCallerContext` , el nombre del método de concentrador que se va a invocar y los argumentos para el método de concentrador.

Considere el ejemplo de un salón de chat que permite el inicio de sesión en varias organizaciones a través de Azure Active Directory. Cualquier persona con un cuenta de Microsoft puede iniciar sesión en el chat, pero solo los miembros de la organización propietaria deben ser capaces de prohibir a los usuarios o ver el historial de chat de los usuarios. Además, es posible que quiera restringir ciertas funciones de determinados usuarios. El uso de las características actualizadas en ASP.NET Core 3,0 es todo lo posible. Observe cómo `DomainRestrictedRequirement` actúa como un personalizado `IAuthorizationRequirement` . Ahora que `HubInvocationContext` se pasa el parámetro de recurso, la lógica interna puede inspeccionar el contexto en el que se llama al centro y tomar decisiones sobre cómo permitir al usuario ejecutar métodos de concentrador individuales.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

En `Startup.ConfigureServices` , agregue la nueva Directiva y proporcione el `DomainRestrictedRequirement` requisito personalizado como parámetro para crear la `DomainRestricted` Directiva.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

En el ejemplo anterior, la `DomainRestrictedRequirement` clase es y la `IAuthorizationRequirement` suya propia `AuthorizationHandler` para ese requisito. Es aceptable dividir estos dos componentes en clases independientes para separar los problemas. Una ventaja del enfoque del ejemplo es que no es necesario insertar `AuthorizationHandler` durante el inicio, ya que el requisito y el controlador son los mismos.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* [Autenticación de token de portador en ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Autorización basada en recursos](xref:security/authorization/resourcebased)
