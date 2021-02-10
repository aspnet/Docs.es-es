---
title: Novedades de ASP.NET Core 3.0
author: rick-anderson
description: Obtenga información sobre las nuevas características de ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-3.0
ms.openlocfilehash: 161d6aeefe1882b86ce97cdcfa7e5b23048693f0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217510"
---
# <a name="whats-new-in-aspnet-core-30"></a>Novedades de ASP.NET Core 3.0

En este artículo se resaltan los cambios más importantes de ASP.NET Core 3.0, con vínculos a la documentación pertinente.

## Blazor

Blazor es un marco nuevo en ASP.NET Core para la creación de interfaces de usuario web interactivas del lado cliente con .NET:

* Cree interfaces de usuario completamente interactivas con C# en lugar de JavaScript.
* Comparta la lógica de aplicación del lado cliente y servidor escrita con .NET.
* Represente la interfaz de usuario como HTML y CSS para la compatibilidad con todos los exploradores, incluidos los móviles.

Escenarios compatibles con el marco Blazor:

* Componentes de interfaz de usuario reutilizables (componentes de Razor)
* Enrutamiento del lado cliente
* Diseños de componentes
* Compatibilidad con la inserción de dependencias
* Formularios y validación
* Compilación de bibliotecas de componentes con bibliotecas de clases de Razor
* Interoperabilidad de JavaScript

Para obtener más información, vea <xref:blazor/index>.

### Blazor Server

Blazor separa la lógica de representación de componentes del modo en el que se aplican las actualizaciones de la interfaz de usuario. Blazor Server admite el hospedaje de componentes de Razor en el servidor de una aplicación de ASP.NET Core. Las actualizaciones de la interfaz de usuario se administran mediante una conexión de SignalR. Blazor Server se admite en ASP.NET Core 3.0.

### <a name="blazor-webassembly-preview"></a>Blazor WebAssembly (versión preliminar)

Las aplicaciones Blazor también se pueden ejecutar directamente en el explorador mediante un entorno de ejecución .NET basado en WebAssembly. Blazor WebAssembly se encuentra en versión preliminar y *no* se admite en ASP.NET Core 3.0. Blazor WebAssembly se admitirá en una versión futura de ASP.NET Core.

### <a name="razor-components"></a>Componentes de Razor

Las aplicaciones Blazor se crean a partir de componentes. Los componentes son fragmentos independientes de la interfaz de usuario, como una página, un cuadro de diálogo o un formulario. Los componentes son clases .NET normales que definen la lógica de representación de la interfaz de usuario y los controladores de eventos del lado cliente. Puede crear aplicaciones web interactivas enriquecidas sin JavaScript.

Los componentes de Blazor normalmente se crean mediante la sintaxis de Razor, una mezcla natural de HTML y C#. Los componentes de Razor se asemejan a las vistas de Razor Pages y MVC en que ambos usan Razor. A diferencia de las páginas y las vistas, que se basan en un modelo de solicitud y respuesta, los componentes se usan específicamente para controlar la composición de la interfaz de usuario.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* Es un conocido marco RPC (llamada a procedimiento remoto) de alto rendimiento.
* Ofrece un enfoque dogmático de contrato primero para el desarrollo de API.
* Utiliza tecnologías modernas como estas:

  * HTTP/2 para el transporte.
  * Búferes de protocolo como el lenguaje de descripción de la interfaz.
  * Formato de serialización binario.
* Proporciona características como las siguientes:

  * Autenticación
  * Streaming y control de flujo bidireccionales.
  * Cancelación y tiempos de espera.

La funcionalidad gRPC en ASP.NET Core 3.0 incluye:

* [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): un marco ASP.NET Core para hospedar servicios de gRPC. gRPC en ASP.NET Core se integra con características de ASP.NET Core estándar como el registro, la inserción de dependencias (DI), la autenticación y la autorización.
* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): un cliente de gRPC para .NET Core que se basa en la conocida clase `HttpClient`.
* [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): integración del cliente de gRPC con `HttpClientFactory`.

Para obtener más información, vea <xref:grpc/index>.

## SignalR

Consulte [Actualización del código SignalR](xref:migration/22-to-30#signalr) para ver las instrucciones de migración. SignalR ahora usa `System.Text.Json` para serializar o deserializar los mensajes JSON. Consulte [Cambiar a Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) para obtener instrucciones sobre cómo restaurar el serializador basado en `Newtonsoft.Json`.

En los clientes de JavaScript y .NET para SignalR, se agregó compatibilidad para la reconexión automática. De forma predeterminada, el cliente intenta conectarse de nuevo inmediatamente y lo vuelve a intentar después de dos, diez y treinta segundos si es necesario. Si el cliente se vuelve a conectar correctamente, recibe un nuevo identificador de conexión. La reconexión automática es opcional:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

Los intervalos de reconexión se pueden especificar pasando una matriz de duraciones basadas en milisegundos:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Se puede pasar una implementación personalizada para el control total de los intervalos de reconexión.

Si se produce un error en la reconexión después del último intervalo de reconexión:

* El cliente considera que la conexión está desconectada.
* El cliente deja de intentar la reconexión.

Durante los intentos de reconexión, actualice la interfaz de usuario de la aplicación para notificar al usuario que se está intentando la reconexión.

Para proporcionar comentarios sobre la interfaz de usuario cuando la conexión se interrumpe, la API de cliente de SignalR se ha ampliado para incluir los siguientes controladores de eventos:

* `onreconnecting`:  ofrece a los desarrolladores una oportunidad para deshabilitar la interfaz de usuario o para que los usuarios sepan que la aplicación está sin conexión.
* `onreconnected`: ofrece a los desarrolladores una oportunidad para actualizar la interfaz de usuario una vez que se restablece la conexión.

En el código siguiente se usa `onreconnecting` para actualizar la interfaz de usuario mientras se intenta conectar:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

En el código siguiente se usa `onreconnected` para actualizar la interfaz de usuario en la conexión:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR 3.0 y las versiones posteriores proporcionan un recurso personalizado a los controladores de autorización cuando un método Hub requiere autorización. El recurso es una instancia de `HubInvocationContext`. `HubInvocationContext` incluye:

* `HubCallerContext`
* Nombre del método Hub que se va a invocar.
* Argumentos para el método Hub.

Considere el siguiente ejemplo de una aplicación de salón de chat que permite el inicio de sesión de varias organizaciones a través de Azure Active Directory. Cualquier persona con un cuenta de Microsoft puede iniciar sesión en el chat, pero solo los miembros de la organización propietaria pueden prohibir usuarios o ver los historiales de chat de los usuarios. La aplicación podría restringir ciertas funcionalidades de usuarios específicos.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

En el código anterior, `DomainRestrictedRequirement` actúa como `IAuthorizationRequirement` personalizado. Dado que se pasa el parámetro de recurso `HubInvocationContext`, la lógica interna puede:

* Inspeccionar el contexto en el que se llama al método Hub.
* Tomar decisiones sobre cómo permitir que el usuario ejecute métodos Hub individuales.

Los métodos Hub individuales se pueden marcar con el nombre de la directiva que el código comprueba en tiempo de ejecución. Cuando los clientes intentan llamar a métodos Hub individuales, el controlador `DomainRestrictedRequirement` se ejecuta y controla el acceso a los métodos. En función de la forma en que `DomainRestrictedRequirement` controla el acceso:

* Todos los usuarios que han iniciado sesión pueden llamar al método `SendMessage`.
* Solo los usuarios que han iniciado sesión con una dirección de correo electrónico `@jabbr.net` pueden ver los historiales de los usuarios.
* Solo `bob42@jabbr.net` puede prohibir a los usuarios del salón de chat.

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
```

La creación de la directiva `DomainRestricted` puede implicar:

* En *Startup.cs*, agregar la nueva directiva.
* Proporcionar el requisito de `DomainRestrictedRequirement` personalizado como parámetro.
* Registrar `DomainRestricted` con el middleware de autorización.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

Los métodos Hub de SignalR usan el [enrutamiento de punto de conexión](xref:fundamentals/routing). La conexión de los métodos Hub de SignalR se hizo previamente de manera explícita:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

En la versión anterior, los desarrolladores debían conectar controladores, páginas de Razor y centros de conectividad en distintos lugares. La conexión explícita da lugar a una serie de segmentos de enrutamiento casi idénticos:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

Los métodos Hub de SignalR 3.0 se pueden enrutar a través del enrutamiento de punto de conexión. Con el enrutamiento de punto de conexión, normalmente todo el enrutamiento se puede configurar en `UseRouting`:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

Se agregó ASP.NET Core 3.0 SignalR:

Streaming de cliente a servidor. Con el streaming de cliente a servidor, los métodos del lado servidor pueden tomar instancias de `IAsyncEnumerable<T>` o `ChannelReader<T>`. En el ejemplo de C# siguiente, el método `UploadStream` del método Hub recibirá un flujo de cadenas del cliente:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

Las aplicaciones del cliente .NET pueden pasar una instancia de `IAsyncEnumerable<T>` o `ChannelReader<T>` como el argumento `stream` del método Hub `UploadStream` anterior.

Una vez que el bucle `for` se ha completado y la función local se cierra, se envía la finalización de la secuencia:

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Las aplicaciones cliente de JavaScript usan SignalR `Subject` (o [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) como argumento `stream` del método Hub `UploadStream` anterior.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

El código de JavaScript podría usar el método `subject.next` para controlar las cadenas a medida que se capturan y están listas para enviarse al servidor.

```javascript
subject.next("example");
subject.complete();
```

Mediante el uso de código como los dos fragmentos de código anteriores, se pueden crear experiencias de streaming en tiempo real.

## <a name="new-json-serialization"></a>Nueva serialización de JSON

ASP.NET Core 3.0 ahora usa <xref:System.Text.Json> de forma predeterminada para la serialización de JSON:

* Lee y escribe JSON de forma asincrónica.
* Está optimizado para texto UTF-8.
* Normalmente, mayor rendimiento que `Newtonsoft.Json`.

Para agregar Json.NET a ASP.NET Core 3.0, consulte [Adición de compatibilidad con el formato JSON basado en Newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-razor-directives"></a>Nuevas directivas de Razor

La lista siguiente contiene nuevas directivas de Razor:

* [`@attribute`](xref:mvc/views/razor#attribute): la directiva `@attribute` aplica el atributo especificado a la clase de la página o vista generada. Por ejemplo: `@attribute [Authorize]`.
* [`@implements`](xref:mvc/views/razor#implements): La directiva `@implements` implementa una interfaz para la clase generada. Por ejemplo: `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 admite la autenticación y la autorización de SPA y API web

ASP.NET Core 3.0 ofrece autenticación en aplicaciones de página única (SPA) mediante la compatibilidad con la autorización de API web. ASP.NET Core Identity para autenticar y almacenar usuarios se combina con [IdentityServer4](https://identityserver.io/) para implementar OpenID Connect.

IdentityServer4 es un marco de trabajo de OpenID Connect y OAuth 2.0 para ASP.NET Core 3.0. Habilita las características de seguridad siguientes:

* Autenticación como servicio (AaaS)
* Inicio de sesión único (SSO) mediante varios tipos de aplicaciones
* Control de acceso para API
* Federation Gateway

Para más información, vea la [documentación de IdentityServer4](http://docs.identityserver.io/en/latest/index.html) o [Autenticación y autorización para SPA](xref:security/authentication/identity/spa).

## <a name="certificate-and-kerberos-authentication"></a>Autenticación de certificados y Kerberos

La autenticación de certificados requiere:

* Configurar el servidor para que acepte certificados.
* Agregar el middleware de autenticación en `Startup.Configure`.
* Agregar el servicio de autenticación de certificados en `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Entre las opciones de autenticación de certificados se incluye la capacidad de:

* Aceptar certificados autofirmados.
* Comprobar la revocación de certificados.
* Comprobar que el certificado ofrecido tiene las marcas de uso correctas.

Una entidad de seguridad de usuario predeterminada se construye a partir de las propiedades del certificado. La entidad de seguridad de usuario contiene un evento que permite complementar o reemplazar la entidad de seguridad. Para obtener más información, vea <xref:security/authentication/certauth>.

La [autenticación de Windows](/windows-server/security/windows-authentication/windows-authentication-overview) se ha ampliado a Linux y macOS. En versiones anteriores, la autenticación de Windows se limitaba a [IIS](xref:host-and-deploy/iis/index) y [HTTP.sys](xref:fundamentals/servers/httpsys). En ASP.NET Core 3,0, [Kestrel](xref:fundamentals/servers/kestrel) tiene la capacidad de usar Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview) y [NTLM en Windows](/windows-server/security/kerberos/ntlm-overview), Linux y macOS para hosts unidos a un dominio de Windows. La compatibilidad con Kestrel de estos esquemas de autenticación se proporciona mediante el paquete [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate). Al igual que con los demás servicios de autenticación, configure la autenticación en toda la aplicación y luego configure el servicio:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Requisitos del host:

* Los hosts de Windows deben tener [nombres de entidad de seguridad de servicio](/windows/win32/ad/service-principal-names) (SPN) agregados a la cuenta de usuario que hospeda la aplicación.
* Las máquinas Linux y macOS deben estar unidas al dominio.
  * Los SPN se deben crear para el proceso web.
  * Los [archivos keytab](/archive/blogs/pie/all-you-need-to-know-about-keytab-files) se deben generar y configurar en la máquina host.

Para obtener más información, vea <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Cambios en la plantilla

Se ha eliminado lo siguiente de las plantillas de interfaz de usuario web (Razor Pages, MVC con controlador y vistas):

* La interfaz de usuario de consentimiento de cookies ya no está incluida. Para habilitar la función de consentimiento de cookies en una aplicación ASP.NET Core 3.0 generada por plantillas, vea <xref:security/gdpr>.
* Ahora se hace referencia a los scripts y los recursos estáticos relacionados como archivos locales en lugar de usar CDN. Para más información, consulte [Ahora se hace referencia a los scripts y recursos estáticos relacionados como archivos locales en lugar de usar CDN en base al entorno actual (aspnet/AspNetCore.Docs nº 14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).

La plantilla de Angular se ha actualizado para usar Angular 8.

De forma predeterminada, la plantilla de la biblioteca de clases de Razor (RCL) usa el desarrollo de componentes de Razor. Una nueva opción de plantilla en Visual Studio proporciona compatibilidad con plantillas para páginas y vistas. Al crear una biblioteca de clases de Razor a partir de la plantilla en un shell de comandos, pase la opción `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`).

## <a name="generic-host"></a>Host genérico

Las plantillas de ASP.NET Core 3.0 usan <xref:fundamentals/host/generic-host>. Las versiones anteriores usaban <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>. El uso del host genérico de .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) proporciona una mejor integración de las aplicaciones ASP.NET Core con otros escenarios de servidor que no son específicos de la web. Para más información, vea [HostBuilder reemplaza a WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Configuración de host

Antes de la versión de ASP.NET Core 3.0, se cargaron las variables de entorno prefijadas con `ASPNETCORE_` para la configuración del host web. En la versión 3.0, `AddEnvironmentVariables` se usaba para cargar variables de entorno con el prefijo `DOTNET_` para la configuración de host con `CreateDefaultBuilder`.

### <a name="changes-to-startup-constructor-injection"></a>Cambios en la inserción del constructor Startup

El host genérico solo admite los siguientes tipos para la inserción del constructor `Startup`:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Todos los servicios se pueden seguir insertando directamente como argumentos en el método `Startup.Configure`. Para más información, consulte [El host genérico restringe la inserción del constructor Startup (aspnet/Announcements nº 353)](https://github.com/aspnet/Announcements/issues/353).

## <a name="kestrel"></a>Kestrel

* La configuración de Kestrel se ha actualizado para la migración al host genérico. En la versión 3.0, Kestrel se configura en el generador de hosts web proporcionado por `ConfigureWebHostDefaults`.
* Los adaptadores de conexión se han quitado de Kestrel y se han reemplazado por el middleware de conexión, que es similar al middleware HTTP en la canalización de ASP.NET Core, pero para conexiones de nivel inferior.
* La capa de transporte de Kestrel se ha expuesto como una interfaz pública en `Connections.Abstractions`.
* La ambigüedad entre encabezados y finalizadores se ha resuelto moviendo los encabezados finales a una nueva colección.
* Las API de E/S síncronas, como `HttpRequest.Body.Read`, son una fuente común de ausencia de subprocesos que provocan bloqueos en la aplicación. En la versión 3.0, `AllowSynchronousIO` se ha deshabilitado de manera predeterminada.

Para obtener más información, vea <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>HTTP/2 habilitado de manera predeterminada.

HTTP/2 está habilitado de forma predeterminada en Kestrel para puntos finales HTTPS. La compatibilidad con HTTP/2 para IIS o HTTP.sys está habilitada cuando el sistema operativo la admite.

## <a name="eventcounters-on-request"></a>EventCounters a petición

El EventSource de hospedaje, `Microsoft.AspNetCore.Hosting`, emite los siguientes nuevos tipos <xref:System.Diagnostics.Tracing.EventCounter> relacionados con las solicitudes entrantes:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Enrutamiento de puntos de conexión

El enrutamiento de puntos de conexión, que permite que los marcos (por ejemplo, MVC) funcionen bien con middleware, se ha mejorado:

* El orden de los puntos de conexión y el middleware es configurable en la canalización de procesamiento de solicitudes de `Startup.Configure`.
* Los extremos y el middleware se componen bien con otras tecnologías basadas en ASP.NET Core, como las comprobaciones de estado.
* Los puntos finales pueden implementar una directiva, como CORS o la autorización, tanto en el middleware como en MVC.
* Los filtros y atributos se pueden colocar en métodos en los controladores.

Para obtener más información, vea <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Comprobaciones de estado

Las comprobaciones de estado utilizan el enrutamiento de puntos de conexión con el host genérico. En `Startup.Configure`, llame a `MapHealthChecks` en el generador de puntos de conexiones con la dirección URL del punto de conexión o la ruta de acceso relativa:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Los puntos de conexión de las comprobaciones de estado pueden:

* Especificar uno o más hosts o puertos permitidos.
* Requerir autorización.
* Requerir CORS.

Para obtener más información, vea los artículos siguientes:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Canalizaciones en HttpContext

Ahora es posible leer el cuerpo de la solicitud y escribir el cuerpo de la respuesta mediante la API <xref:System.IO.Pipelines>. A la clase <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> La propiedad `HttpRequest.BodyReader` proporciona <xref:System.IO.Pipelines.PipeReader> que se puede usar para leer el cuerpo de la solicitud. A la clase <!-- <xref:Microsoft.AspNetCore.Http.> --> La propiedad `HttpResponse.BodyWriter` proporciona <xref:System.IO.Pipelines.PipeWriter> que se puede usar para escribir el cuerpo de la respuesta. `HttpRequest.BodyReader` es análogo al flujo `HttpRequest.Body`. `HttpResponse.BodyWriter` es análogo al flujo `HttpResponse.Body`.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Informes de errores mejorados en IIS

Los errores de inicio al hospedar aplicaciones ASP.NET Core en IIS ahora producen datos de diagnóstico más completos. Estos errores se informan al registro de eventos de Windows con seguimientos de pila siempre que proceda. Además, todas las advertencias, los errores y las excepciones no controladas se registran en el registro de eventos de Windows.

## <a name="worker-service-and-worker-sdk"></a>Servicio de trabajo y SDK de trabajo

.NET Core 3.0 presenta la nueva plantilla de la aplicación de servicio de trabajo. Esta plantilla proporciona un punto de partida para escribir aplicaciones de servicio de larga duración en .NET Core.

Para obtener más información, consulte:

* [Trabajos de .NET Core como servicios de Windows](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Mejoras del middleware de encabezados reenviados

En versiones anteriores de ASP.NET Core, llamar a <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> y <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> era problemático cuando se implementaba en Azure Linux o detrás de cualquier proxy inverso que no fuera IIS. La corrección para las versiones anteriores se documenta en [Reenvío del esquema para servidores proxy inversos Linux y que no son de IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).

Este escenario se ha corregido en ASP.NET Core 3.0. El host posibilita [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) cuando la variable de entorno `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se establece en `true`. `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se establece en `true` en nuestras imágenes de contenedor.

## <a name="performance-improvements"></a>Mejoras en el rendimiento

ASP.NET Core 3.0 incluye muchas mejoras que reducen el uso de memoria y aumentan el rendimiento:

* Reducción del uso de memoria cuando se usa el contenedor de inserción de dependencias integrado para los servicios de ámbito.
* Reducción de las asignaciones en el marco, incluidos los escenarios de middleware y el enrutamiento.
* Reducción del uso de memoria para las conexiones de WebSocket.
* Reducción de memoria y mejoras de rendimiento para las conexiones HTTPS.
* Nuevo serializador JSON optimizado y totalmente asincrónico.
* Reducción del uso de memoria y mejoras de rendimiento en el análisis de formularios.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3.0 solo se ejecuta en .NET Core 3.0

A partir de ASP.NET Core 3.0, .NET Framework ya no es un marco de destino admitido. Los proyectos que tienen .NET Framework como destino pueden continuar usando la [versión .NET Core 2.1 LTS](https://dotnet.microsoft.com/download/dotnet-core/2.1) de forma totalmente compatible. La mayoría de los paquetes relacionados con ASP.NET Core 2.1.x se admitirán de forma indefinida, más allá del período LTS de tres años para .NET Core 2.1.

Para información sobre la migración, consulte [Realice la portabilidad de su código de .NET Framework a .NET Core](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>Uso del marco compartido de .NET Core

El marco compartido de ASP.NET Core 3.0, incluido en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), ya no requiere un elemento `<PackageReference />` explícito en el archivo de proyecto. Se hace referencia al marco compartido automáticamente al usar el SDK `Microsoft.NET.Sdk.Web` en el archivo de proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Ensamblados quitados del marco compartido de ASP.NET Core

Los ensamblados más importantes que se han quitado del marco compartido de ASP.NET Core 3.0 son:

* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET). Para agregar Json.NET a ASP.NET Core 3.0, consulte [Adición de compatibilidad con el formato JSON basado en Newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.NET Core 3.0 presenta `System.Text.Json` para leer y escribir JSON. Para más información, consulte [Nueva serialización de JSON](#new-json-serialization).
* [Entity Framework Core](/ef/core/)

Para una lista completa de los ensamblados que se han quitado del marco compartido, consulte [Ensamblados quitados de Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755). Para más información sobre la motivación de este cambio, consulte [Últimos cambios en Microsoft.AspNetCore.App en 3.0](https://github.com/aspnet/Announcements/issues/325) y [Un primer vistazo a los cambios que vienen en ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
