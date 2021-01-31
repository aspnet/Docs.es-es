---
title: Configurar la autenticación de certificados en ASP.NET Core
author: blowdart
description: Obtenga información sobre cómo configurar la autenticación de certificados en ASP.NET Core para IIS y HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
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
uid: security/authentication/certauth
ms.openlocfilehash: c862bc8bff6c4cc80696d92067e814889d6e7782
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217536"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>Configurar la autenticación de certificados en ASP.NET Core

`Microsoft.AspNetCore.Authentication.Certificate` contiene una implementación similar a la [autenticación de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) para ASP.net Core. La autenticación de certificados se realiza en el nivel de TLS, mucho antes de que llegue a ASP.NET Core. Más concretamente, se trata de un controlador de autenticación que valida el certificado y, a continuación, le proporciona un evento en el que puede resolver ese certificado en un `ClaimsPrincipal` . 

[Configure el servidor para la](#configure-your-server-to-require-certificates) autenticación de certificados, ya sea IIS, Kestrel, Azure Web Apps o cualquier otra cosa que esté usando.

## <a name="proxy-and-load-balancer-scenarios"></a>Escenarios de proxy y equilibrador de carga

La autenticación de certificados es un escenario con estado que se usa principalmente en el que un proxy o un equilibrador de carga no controla el tráfico entre clientes y servidores. Si se usa un proxy o un equilibrador de carga, la autenticación de certificado solo funciona si el proxy o el equilibrador de carga:

* Controla la autenticación.
* Pasa la información de autenticación del usuario a la aplicación (por ejemplo, en un encabezado de solicitud), que actúa en la información de autenticación.

Una alternativa a la autenticación de certificados en entornos en los que se usan servidores proxy y equilibradores de carga es Active Directory Federated Services (ADFS) con OpenID Connect (OIDC).

## <a name="get-started"></a>Introducción

Adquiera un certificado HTTPS, aplíquelo y [Configure el servidor](#configure-your-server-to-require-certificates) para que requiera certificados.

En la aplicación Web, agregue una referencia al paquete [Microsoft. AspNetCore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) . Después, en el `Startup.ConfigureServices` método, llame a `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` con sus opciones, proporcionando un delegado para `OnCertificateValidated` que realice cualquier validación complementaria en el certificado de cliente enviado con las solicitudes. Convierta esa información en un `ClaimsPrincipal` y establézcala en la `context.Principal` propiedad.

Si se produce un error en la autenticación, este controlador devuelve una `403 (Forbidden)` respuesta en lugar de `401 (Unauthorized)` , como cabría esperar. La razón es que la autenticación debe realizarse durante la conexión TLS inicial. En el momento en que llega al controlador, es demasiado tarde. No hay ninguna manera de actualizar la conexión de una conexión anónima a una con un certificado.

Agregue también `app.UseAuthentication();` en el `Startup.Configure` método. De lo contrario, `HttpContext.User` no se establecerá en `ClaimsPrincipal` creado a partir del certificado. Por ejemplo:

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

En el ejemplo anterior se muestra la manera predeterminada de agregar la autenticación de certificado. El controlador crea una entidad de seguridad de usuario mediante las propiedades comunes del certificado.

## <a name="configure-certificate-validation"></a>Configurar la validación de certificados

El `CertificateAuthenticationOptions` controlador tiene algunas validaciones integradas que son las validaciones mínimas que se deben realizar en un certificado. Cada una de estas opciones está habilitada de forma predeterminada.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = encadenado, SelfSigned o todos (encadenado | SelfSigned

Valor predeterminado: `CertificateTypes.Chained`

Esta comprobación valida que solo se permite el tipo de certificado adecuado. Si la aplicación usa certificados autofirmados, esta opción debe estar establecida en `CertificateTypes.All` o `CertificateTypes.SelfSigned` .

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Valor predeterminado: `true`

Esta comprobación valida que el certificado presentado por el cliente tiene el uso mejorado de clave (EKU) de autenticación del cliente o no tiene ningún EKU. Como indican las especificaciones, si no se especifica ningún EKU, se considera que todos los EKU son válidos.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Valor predeterminado: `true`

Esta comprobación valida que el certificado está dentro de su período de validez. En cada solicitud, el controlador garantiza que un certificado que era válido cuando se presentó no ha expirado durante su sesión actual.

### <a name="revocationflag"></a>RevocationFlag

Valor predeterminado: `X509RevocationFlag.ExcludeRoot`

Marca que especifica en qué certificados de la cadena se comprueba la revocación.

Las comprobaciones de revocación solo se realizan cuando el certificado está encadenado a un certificado raíz.

### <a name="revocationmode"></a>RevocationMode

Valor predeterminado: `X509RevocationMode.Online`

Marca que especifica cómo se realizan las comprobaciones de revocación.

Si se especifica una comprobación en línea, se puede producir un retraso largo mientras se Contacta con la entidad de certificación.

Las comprobaciones de revocación solo se realizan cuando el certificado está encadenado a un certificado raíz.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>¿Puedo configurar mi aplicación para requerir un certificado solo en determinadas rutas?

Esto no es posible. Recuerde que el intercambio de certificados se realiza al inicio de la conversación HTTPS, se realiza en el servidor antes de que se reciba la primera solicitud en esa conexión, por lo que no es posible el ámbito en función de los campos de solicitud.

## <a name="handler-events"></a>Eventos de controlador

El controlador tiene dos eventos:

* `OnAuthenticationFailed`: Se le llama si se produce una excepción durante la autenticación y le permite reaccionar.
* `OnCertificateValidated`: Se llama después de validar el certificado, se ha aprobado la validación y se ha creado una entidad de seguridad predeterminada. Este evento le permite realizar su propia validación y aumentar o reemplazar la entidad de seguridad. Por ejemplo, se incluyen:
  * Determinar si los servicios conocen el certificado.
  * Construir su propia entidad de seguridad. Considere el ejemplo siguiente de `Startup.ConfigureServices`:

    ```csharp
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate(options =>
        {
            options.Events = new CertificateAuthenticationEvents
            {
                OnCertificateValidated = context =>
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(ClaimTypes.Name,
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };
    
                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
    
                    return Task.CompletedTask;
                }
            };
        });
    ```

Si encuentra que el certificado de entrada no cumple su validación adicional, llame a `context.Fail("failure reason")` con un motivo del error.

En el caso de una funcionalidad real, es probable que quiera llamar a un servicio registrado en la inserción de dependencias que se conecta a una base de datos u otro tipo de almacén de usuario. Obtenga acceso a su servicio mediante el contexto que se pasa al delegado. Considere el ejemplo siguiente de `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetRequiredService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

Conceptualmente, la validación del certificado es un problema de autorización. La adición de una comprobación, por ejemplo, a un emisor o huella digital en una directiva de autorización, en lugar de dentro de `OnCertificateValidated` , es absolutamente aceptable.

## <a name="configure-your-server-to-require-certificates"></a>Configurar el servidor para que requiera certificados

### <a name="kestrel"></a>Kestrel

En *Program.CS*, configure Kestrel como se indica a continuación:

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> no tendrán aplicados los valores predeterminados.

### <a name="iis"></a>IIS

Complete los pasos siguientes en el administrador de IIS:

1. Seleccione su sitio en la pestaña **conexiones** .
1. Haga doble clic en la opción **configuración de SSL** en la ventana **ver características** .
1. Active la casilla **requerir SSL** y seleccione el botón de radio **requerir** en la sección **certificados de cliente** .

![Configuración de certificados de cliente en IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure y proxies web personalizados

Consulte la [documentación de host e implementación](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) para obtener información sobre cómo configurar el middleware de reenvío de certificados.

### <a name="use-certificate-authentication-in-azure-web-apps"></a>Uso de la autenticación de certificados en Azure Web Apps

No se requiere ninguna configuración de reenvío para Azure. Esto ya está configurado en el middleware de reenvío de certificados.

> [!NOTE]
> Esto requiere que el CertificateForwardingMiddleware esté presente.

### <a name="use-certificate-authentication-in-custom-web-proxies"></a>Usar la autenticación de certificados en proxies web personalizados

El `AddCertificateForwarding` método se usa para especificar:

* Nombre del encabezado de cliente.
* Cómo se va a cargar el certificado (mediante la `HeaderConverter` propiedad).

En los proxies web personalizados, el certificado se pasa como un encabezado de solicitud personalizado, por ejemplo `X-SSL-CERT` . Para usarlo, configure el reenvío de certificados en `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;

            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

`Startup.Configure`Después, el método agrega el middleware. `UseCertificateForwarding` se llama a antes de llamar a `UseAuthentication` y `UseAuthorization` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

Se puede usar una clase independiente para implementar la lógica de validación. Dado que en este ejemplo se usa el mismo certificado autofirmado, asegúrese de que solo se puede usar el certificado. Compruebe que las huellas digitales del certificado de cliente y del certificado de servidor coinciden; de lo contrario, se puede usar cualquier certificado y será suficiente para autenticarse. Se utilizará en el `AddCertificate` método. También puede validar el asunto o el emisor aquí si usa certificados intermedios o secundarios.

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a>Implementar un HttpClient mediante un certificado y el HttpClientHandler

`HttpClientHandler`Se puede agregar directamente en el constructor de la `HttpClient` clase. Se debe tener cuidado al crear instancias de `HttpClient` . `HttpClient`A continuación, enviará el certificado con cada solicitud.

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a>Implementar un HttpClient mediante un certificado y un HttpClient con nombre de IHttpClientFactory 

En el ejemplo siguiente, se agrega un certificado de cliente a un `HttpClientHandler` mediante la `ClientCertificates` propiedad del controlador. Este controlador se puede usar en una instancia con nombre de `HttpClient` mediante el `ConfigurePrimaryHttpMessageHandler` método. Se configura en `Startup.ConfigureServices` :

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

`IHttpClientFactory`A continuación, se puede usar para obtener la instancia con nombre con el controlador y el certificado. El `CreateClient` método con el nombre del cliente definido en la `Startup` clase se utiliza para obtener la instancia. La solicitud HTTP se puede enviar mediante el cliente, según sea necesario.

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

Si el certificado correcto se envía al servidor, se devuelven los datos. Si no se envía ningún certificado o el certificado equivocado, se devuelve un código de Estado HTTP 403.

### <a name="create-certificates-in-powershell"></a>Crear certificados en PowerShell

La creación de los certificados es la parte más difícil de configurar este flujo. Se puede crear un certificado raíz mediante el `New-SelfSignedCertificate` cmdlet de PowerShell. Al crear el certificado, use una contraseña segura. Es importante agregar el `KeyUsageProperty` parámetro y el `KeyUsage` parámetro como se muestra.

#### <a name="create-root-ca"></a>Crear CA raíz

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> El `-DnsName` valor del parámetro debe coincidir con el destino de implementación de la aplicación. Por ejemplo, "localhost" para el desarrollo.

#### <a name="install-in-the-trusted-root"></a>Instalación en la raíz de confianza

El certificado raíz debe ser de confianza en el sistema host. De forma predeterminada, no se confiará en un certificado raíz que no haya creado ninguna entidad de certificación. En el vínculo siguiente se explica cómo se puede lograr esto en Windows:

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>Certificado intermedio

Ahora se puede crear un certificado intermedio a partir del certificado raíz. Esto no es necesario para todos los casos de uso, pero es posible que tenga que crear muchos certificados o tener que activar o desactivar grupos de certificados. El `TextExtension` parámetro es necesario para establecer la longitud de la ruta de acceso en las restricciones básicas del certificado.

Después, el certificado intermedio se puede Agregar al certificado intermedio de confianza en el sistema host de Windows.

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a>Crear certificado secundario a partir de un certificado intermedio

Se puede crear un certificado secundario a partir del certificado intermedio. Se trata de la entidad final y no es necesario crear más certificados secundarios.

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a>Crear certificado secundario a partir del certificado raíz

También se puede crear un certificado secundario a partir del certificado raíz directamente. 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a>Ejemplo de certificado intermedio de raíz: certificado

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

Al usar los certificados raíz, intermedios o secundarios, los certificados se pueden validar con la huella digital o la PublicKey según sea necesario.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a>Almacenamiento en caché de validación de certificados

ASP.NET Core 5,0 y versiones posteriores admiten la capacidad de habilitar el almacenamiento en caché de los resultados de la validación. El almacenamiento en caché mejora drásticamente el rendimiento de la autenticación de certificados, ya que la validación es una operación costosa.

De forma predeterminada, la autenticación de certificados deshabilita el almacenamiento en caché. Para habilitar el almacenamiento en caché, llame a `AddCertificateCache` en `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

La implementación de almacenamiento en caché predeterminada almacena los resultados en memoria. Puede proporcionar su propia memoria caché implementando `ICertificateValidationCache` y registrando la inserción de dependencias. Por ejemplo, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.

::: moniker-end

## <a name="optional-client-certificates"></a>Certificados de cliente opcionales

En esta sección se proporciona información para las aplicaciones que deben proteger un subconjunto de la aplicación con un certificado. Por ejemplo, una Razor página o un controlador de la aplicación podrían requerir certificados de cliente. Esto presenta desafíos como certificados de cliente:
  
* Son una característica de TLS, no una característica de HTTP.
* Se negocian por conexión y se deben negociar al inicio de la conexión antes de que estén disponibles los datos HTTP. En el inicio de la conexión, solo se conoce el Indicación de nombre de servidor (SNI) &dagger; . Los certificados de cliente y de servidor se negocian antes de la primera solicitud en una conexión y las solicitudes no suelen ser capaces de renegociar.

La renegociación de TLS era una forma antigua de implementar certificados de cliente opcionales. Ya no se recomienda porque:
- En HTTP/1.1, la renegociación durante una solicitud POST podría producir un interbloqueo en el que el cuerpo de la solicitud llenó la ventana TCP y no se pueden recibir los paquetes de renegociación.
- HTTP/2 [prohíbe explícitamente](https://tools.ietf.org/html/rfc7540#section-9.2.1) la renegociación.
- TLS 1,3 ha [quitado](https://tools.ietf.org/html/rfc8740#section-1) la compatibilidad con la renegociación.

ASP.NET Core 5 Preview 7 y versiones posteriores agregan una compatibilidad más cómoda para los certificados de cliente opcionales. Para obtener más información, vea el [ejemplo de certificados opcionales](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).

El siguiente enfoque admite certificados de cliente opcionales:

::: moniker range=">= aspnetcore-5.0"

* Configure el enlace para el dominio y el subdominio:
  * Por ejemplo, configure los enlaces en `contoso.com` y `myClient.contoso.com` . El `contoso.com` host no requiere un certificado de cliente, pero sí lo `myClient.contoso.com` hace.
  * Para más información, consulte:
    * [Kestrel](/fundamentals/servers/kestrel):
      * [ListenOptions.UseHttps](xref:fundamentals/servers/kestrel/endpoints#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * Nota Kestrel no admite actualmente varias configuraciones de TLS en un enlace, necesitará dos enlaces con direcciones IP o puertos únicos. Consulta https://github.com/dotnet/runtime/issues/31097.
    * IIS
      * [Hospedaje de IIS](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [Configurar la seguridad en IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * HTTP.sys: [configurar Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Configure el enlace para el dominio y el subdominio:
  * Por ejemplo, configure los enlaces en `contoso.com` y `myClient.contoso.com` . El `contoso.com` host no requiere un certificado de cliente, pero sí lo `myClient.contoso.com` hace.
  * Para más información, consulte:
    * [Kestrel](/fundamentals/servers/kestrel):
      * [ListenOptions.UseHttps](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * Nota Kestrel no admite actualmente varias configuraciones de TLS en un enlace, necesitará dos enlaces con direcciones IP o puertos únicos. Consulta https://github.com/dotnet/runtime/issues/31097.
    * IIS
      * [Hospedaje de IIS](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [Configurar la seguridad en IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * HTTP.sys: [configurar Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)

::: moniker-end

* Para las solicitudes a la aplicación web que requieren un certificado de cliente y no tienen una:
  * Redirigir a la misma página mediante el subdominio protegido por el certificado de cliente.
  * Por ejemplo, redirigir a `myClient.contoso.com/requestedPage` . Dado que la solicitud a `myClient.contoso.com/requestedPage` es un nombre de host diferente que `contoso.com/requestedPage` , el cliente establece una conexión diferente y se proporciona el certificado de cliente.
  * Para obtener más información, vea <xref:security/authorization/introduction>.

Deje preguntas, comentarios y otros comentarios sobre los certificados de cliente opcionales en este problema de [discusión de github](https://github.com/dotnet/AspNetCore.Docs/issues/18720) .

&dagger; Indicación de nombre de servidor (SNI) es una extensión de TLS para incluir un dominio virtual como parte de la negociación SSL. Esto significa que el nombre de dominio virtual, o un nombre de host, se puede usar para identificar el extremo de la red.
