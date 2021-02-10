---
title: Configuración de puntos de conexión para el servidor web Kestrel de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la configuración de puntos de conexión para Kestrel, el servidor web multiplataforma de ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: f9d82409f4b31a5564c7cdfa48beb303d784e213
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057153"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a>Configuración de puntos de conexión para el servidor web Kestrel de ASP.NET Core

ASP.NET Core enlaza de forma predeterminada a:

* `http://localhost:5000`
* `https://localhost:5001` (cuando hay presente un certificado de desarrollo local)

Especifique direcciones URL mediante los siguientes elementos:

* La variable de entorno `ASPNETCORE_URLS`.
* El argumento de la línea de comandos `--urls`.
* La clave de configuración de host `urls`.
* El método de extensión `UseUrls`.

El valor que estos métodos suministran puede ser uno o más puntos de conexión HTTP y HTTPS (este último, si hay disponible un certificado predeterminado). Configure el valor como una lista separada por punto y coma (por ejemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).

Para más información sobre estos enfoques, consulte [Direcciones URL del servidor](xref:fundamentals/host/web-host#server-urls) e [Invalidar la configuración](xref:fundamentals/host/web-host#override-configuration).

Un certificado de desarrollo se crea:

* Al instalar el [SDK de .NET](/dotnet/core/sdk).
* Para crear un certificado, se usa la [herramienta dev-certs](xref:aspnetcore-2.1#https).

Algunos exploradores necesitan que se conceda permiso explícito para confiar en el certificado de desarrollo local.

Las plantillas de proyecto configuran aplicaciones para que se ejecuten en HTTPS de forma predeterminada e incluyen [redirección de HTTPS y compatibilidad con HSTS](xref:security/enforcing-ssl).

Llame a los métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar los puertos y los prefijos de dirección URL para Kestrel.

`UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` y la variable de entorno `ASPNETCORE_URLS` también funcionan, pero tienen las limitaciones que se indican más adelante en esta sección (debe haber disponible un certificado predeterminado para la configuración de puntos de conexión HTTPS).

Configuración de `KestrelServerOptions`:

## <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults(Action\<ListenOptions>)

Especifica una `Action` de configuración para que se ejecute con cada punto de conexión especificado. Al llamar a `ConfigureEndpointDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> no tendrán aplicados los valores predeterminados.

## <a name="configureiconfiguration"></a>Configure(IConfiguration)

Crea un cargador de configuración para configurar Kestrel que toma una <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada. El ámbito de la configuración debe corresponderse con la sección de configuración de Kestrel.

`CreateDefaultBuilder` llama a `Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)

Especifica una `Action` de configuración para que se ejecute con cada punto de conexión HTTPS. Al llamar a `ConfigureHttpsDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> no tendrán aplicados los valores predeterminados.

## <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Configure Kestrel para que use HTTPS.

Extensiones de `ListenOptions.UseHttps`:

* `UseHttps`: configure Kestrel para que use HTTPS con el certificado predeterminado. Produce una excepción si no hay ningún certificado predeterminado configurado.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

Parámetros de `ListenOptions.UseHttps`:

* `filename` es la ruta de acceso y el nombre de archivo de un archivo de certificado correspondiente al directorio donde están los archivos de contenido de la aplicación.
* `password` es la contraseña necesaria para obtener acceso a los datos del certificado X.509.
* `configureOptions` es una `Action` para configurar `HttpsConnectionAdapterOptions`. Devuelve `ListenOptions`.
* `storeName` es el almacén de certificados desde el que se carga el certificado.
* `subject` es el nombre del sujeto del certificado.
* `allowInvalid` indica si se deben tener en cuenta los certificados no válidos, como los certificados autofirmados.
* `location` es la ubicación del almacén desde el que se carga el certificado.
* `serverCertificate` es el certificado X.509.

En un entorno de producción, HTTPS se debe configurar explícitamente. Como mínimo, debe existir un certificado predeterminado.

Estas son las configuraciones compatibles:

* Sin configuración
* Reemplazar el certificado predeterminado de configuración
* Cambiar los valores predeterminados en el código

### <a name="no-configuration"></a>Sin configuración

Kestrel escucha en `http://localhost:5000` y en `https://localhost:5001` (si hay disponible un certificado predeterminado).

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a>Reemplazar el certificado predeterminado de configuración

Hay disponible un esquema de configuración de aplicación HTTPS predeterminado para Kestrel. Configure varios puntos de conexión (incluidas las direcciones URL y los certificados que va a usar) desde un archivo en disco o desde un almacén de certificados.

En el ejemplo *appsettings.json* siguiente:

* Establezca `AllowInvalid` en `true` para permitir el uso de certificados no válidos (por ejemplo, certificados autofirmados).
* Cualquier punto de conexión HTTPS que no especifique un certificado (`HttpsDefaultCert` en el siguiente ejemplo) revierte al certificado definido en `Certificates:Default` o al certificado de desarrollo.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertAndKeyFile": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Path": "<path to .pem/.crt file>",
          "KeyPath": "<path to .key file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5003",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5004"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Notas sobre el esquema:

* En los nombres de los puntos de conexión [se distingue entre mayúsculas y minúsculas](xref:fundamentals/configuration/index#configuration-keys-and-values). Por ejemplo, `HTTPS` and `Https` son equivalentes.
* El parámetro `Url` es necesario en cada punto de conexión. El formato de este parámetro es el mismo que el del parámetro de configuración `Urls` de nivel superior, excepto por el hecho de que está limitado a un único valor.
* En vez de agregarse, estos puntos de conexión reemplazan a los que están definidos en la configuración `Urls` de nivel superior. Los puntos de conexión definidos en el código a través de `Listen` son acumulativos con respecto a los puntos de conexión definidos en la sección de configuración.
* La sección `Certificate` es opcional. Si la sección `Certificate` no se especifica, se usan los valores predeterminados definidos en `Certificates:Default`. Si no hay valores predeterminados disponibles, se utiliza el certificado de desarrollo. Si no hay valores predeterminados y el certificado de desarrollo no está presente, el servidor produce una excepción y no se inicia.
* En la sección `Certificate` se admiten varios [orígenes de certificados](#certificate-sources).
* Se puede definir el número de puntos de conexión que se quiera en la [configuración](xref:fundamentals/configuration/index), siempre y cuando no produzcan conflictos de puerto.

#### <a name="certificate-sources"></a>Orígenes de certificados

Los nodos de certificado se pueden configurar para cargar certificados de varios orígenes:

* `Path` y `Password` para cargar archivos *.pfx*.
* `Path`, `KeyPath` y `Password` para cargar archivos *.pem*/ *.crt* y *.key*.
* `Subject` y `Store` cargar desde el almacén de certificados.

Por ejemplo, el certificado en `Certificates:Default` se puede especificar así:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a>ConfigurationLoader

`options.Configure(context.Configuration.GetSection("{SECTION}"))` devuelve un <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> con un método `.Endpoint(string name, listenOptions => { })` que se puede usar para complementar la configuración de un punto de conexión configurado:

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

Se puede acceder directamente a `KestrelServerOptions.ConfigurationLoader` para seguir con la iteración en el cargador existente, como el proporcionado por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.

* La sección de configuración de cada punto de conexión está disponible en las opciones del método `Endpoint` para que se pueda leer la configuración personalizada.
* Se pueden cargar varias configuraciones volviendo a llamar a `options.Configure(context.Configuration.GetSection("{SECTION}"))` con otra sección. Se usa la última configuración, a menos que se llame explícitamente a `Load` en instancias anteriores. El metapaquete no llama a `Load`, con lo cual su sección de configuración predeterminada se puede reemplazar.
* `KestrelConfigurationLoader` refleja la familia `Listen` de API de `KestrelServerOptions` como sobrecargas de `Endpoint`, por lo que los puntos de conexión de configuración y código se pueden configurar en el mismo lugar. En estas sobrecargas no se usan nombres y solo consumen valores predeterminados de la configuración.

### <a name="change-the-defaults-in-code"></a>Cambiar los valores predeterminados en el código

`ConfigureEndpointDefaults` y `ConfigureHttpsDefaults` se pueden usar para cambiar la configuración predeterminada de `ListenOptions` y `HttpsConnectionAdapterOptions`, incluido sustituir el certificado predeterminado especificado en el escenario anterior. Se debe llamar a `ConfigureEndpointDefaults` y a `ConfigureHttpsDefaults` antes de que se configure algún punto de conexión.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a>Configuración de puntos de conexión mediante Indicación de nombre de servidor

[Indicación de nombre de servidor (SNI)](https://tools.ietf.org/html/rfc6066#section-3) se puede usar para hospedar varios dominios en la misma dirección IP y puerto. Para que SNI funcione, el cliente envía el nombre de host de la sesión segura al servidor durante el protocolo de enlace TLS para que, de este modo, el servidor pueda proporcionar el certificado correcto. El cliente usa el certificado proporcionado para la comunicación cifrada con el servidor durante la sesión segura que sigue al protocolo de enlace TLS.

Kestrel admite SNI a través de la devolución de llamada `ServerCertificateSelector`. La devolución de llamada se invoca una vez por conexión para permitir que la aplicación inspeccione el nombre de host y seleccione el certificado adecuado. El siguiente código de devolución de llamada se puede usar en la llamada al método `ConfigureWebHostDefaults` del archivo *Program.cs* de un proyecto:

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

La compatibilidad con SNI requiere lo siguiente:

* Ejecutarse en el marco de destino `netcoreapp2.1` o posterior. En `net461` o posterior, se invoca la devolución de llamada, pero `name` siempre es `null`. `name` también será `null` si el cliente no proporciona el parámetro de nombre de host en el protocolo de enlace TLS.
* Todos los sitios web deben ejecutarse en la misma instancia de Kestrel. Kestrel no admite el uso compartido de una dirección IP y un puerto entre varias instancias sin un proxy inverso.

## <a name="ssltls-protocols"></a>Protocolos SSL/TLS

Los protocolos SSL son protocolos que se utilizan para cifrar y descifrar el tráfico entre dos elementos del mismo nivel, tradicionalmente un cliente y un servidor.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls13;
    });
});
```

El valor predeterminado, `SslProtocols.None`, hace que Kestrel use los valores predeterminados del sistema operativo para elegir el mejor protocolo. A menos que tenga una razón específica para seleccionar un protocolo, utilice el valor predeterminado.
## <a name="connection-logging"></a>Registro de conexiones

Llame a <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> para emitir registros de nivel de depuración para la comunicación a nivel de bytes en una conexión. El registro de conexiones es útil para solucionar problemas en la comunicación de bajo nivel, como durante el cifrado TLS y detrás de los servidores proxy. Si `UseConnectionLogging` se coloca antes de `UseHttps`, se registra el tráfico cifrado. Si `UseConnectionLogging` se coloca después de `UseHttps`, se registra el tráfico descifrado. Este es el [middleware de conexión](#connection-middleware) integrado.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a>Enlazar a un socket TCP

El método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> se enlaza a un socket TCP y una expresión lambda de opciones permite configurar un certificado X.509:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

En el ejemplo se configura HTTPS para un punto de conexión con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Use la misma API para configurar otras opciones de Kestrel para puntos de conexión específicos.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a>Enlazar a un socket de Unix

Escuche en un socket de Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> para mejorar el rendimiento con Nginx, tal como se muestra en este ejemplo:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* En el archivo de configuración de Nginx, establezca la entrada `server` > `location` > `proxy_pass` en `http://unix:/tmp/{KESTREL SOCKET}:/;`. `{KESTREL SOCKET}` es el nombre del socket proporcionado para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (por ejemplo, `kestrel-test.sock` en el ejemplo anterior).
* Asegúrese de que el socket es grabable por Nginx (por ejemplo, `chmod go+w /tmp/kestrel-test.sock`).

## <a name="port-0"></a>Puerto 0

Cuando se especifica el número de puerto `0`, Kestrel se enlaza de forma dinámica a un puerto disponible. En el ejemplo siguiente se muestra cómo determinar a qué puerto se enlaza Kestrel en tiempo de ejecución:

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Cuando la aplicación se ejecuta, la salida de la ventana de consola indica el puerto dinámico en el que se puede tener acceso a la aplicación:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a>Limitaciones

Configure puntos de conexión con los siguientes métodos:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* El argumento de la línea de comandos `--urls`
* La clave de configuración de host `urls`
* La variable de entorno `ASPNETCORE_URLS`

Estos métodos son útiles para que el código funcione con servidores que no sean de Kestrel. Sin embargo, tenga en cuenta las siguientes limitaciones:

* HTTPS no se puede usar con estos enfoques, a menos que se proporcione un certificado predeterminado en la configuración del punto de conexión HTTPS (por ejemplo, mediante la configuración `KestrelServerOptions` o un archivo de configuración, como se ha mostrado antes en este artículo).
* Cuando los métodos `Listen` y `UseUrls` se usan al mismo tiempo, los puntos de conexión de `Listen` sustituyen a los de `UseUrls`.

## <a name="iis-endpoint-configuration"></a>Configuración de puntos de conexión IIS

Cuando se usa IIS, los enlaces de direcciones URL de IIS reemplazan a los enlaces que se hayan establecido por medio de `Listen` o de `UseUrls`. Para más información, vea [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) (Módulo de ASP.NET Core).

## <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

La propiedad `Protocols` establece los protocolos HTTP (`HttpProtocols`) habilitados en un punto de conexión o para el servidor. Asigne un valor a la propiedad `Protocols` desde el valor de enumeración `HttpProtocols`.

| Valor de enumeración `HttpProtocols` | Protocolo de conexión permitido |
| -------------------------- | ----------------------------- |
| `Http1`                    | HTTP/1.1 solo. Puede usarse con o sin TLS. |
| `Http2`                    | HTTP/2 solo. Se pueden utilizar sin TLS solo si el cliente admite un [modo de conocimientos previos](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 y HTTP/2. HTTP/2 necesita que el cliente seleccione HTTP/2 en el protocolo de enlace [Negociación de protocolo de nivel de aplicación (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) de TLS; en caso contrario, el valor predeterminado de la conexión es HTTP/1.1. |

El valor `ListenOptions.Protocols` predeterminado de cualquier punto de conexión es `HttpProtocols.Http1AndHttp2`.

Restricciones de TLS para HTTP/2:

* TLS 1.2 o versiones posteriores
* Renegociación deshabilitada
* Compresión deshabilitada
* Tamaños de intercambio de claves efímeras mínimos:
  * Curva elíptica Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits como mínimo
  * Campo finito Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits como mínimo
* Conjunto de cifrado no prohibido. 

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; con la curva elíptica P-256 &lbrack;`FIPS186`&rbrack; es compatible de forma predeterminada.

El siguiente ejemplo permite conexiones HTTP/1.1 y HTTP/2 en el puerto 8000. Las conexiones se protegen mediante TLS con un certificado proporcionado:

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

En Linux, se puede usar <xref:System.Net.Security.CipherSuitesPolicy> para filtrar los protocolos de enlace TLS por conexión:

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a>Middleware de conexión

Si es necesario, el middleware de conexión personalizado puede filtrar por cifrados específicos los protocolos de enlace TLS por cada conexión.

En el ejemplo siguiente se produce una excepción <xref:System.NotSupportedException> con cualquier algoritmo de cifrado que no admita la aplicación. Como alternativa, defina y compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) con una lista de conjuntos de cifrado aceptables.

No se usa ningún cifrado con un algoritmo de cifrado [CipherAlgorithmType.Null ](xref:System.Security.Authentication.CipherAlgorithmType).

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

El filtrado de conexiones también puede configurarse mediante una función lambda <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a>Establecimiento del protocolo HTTP a partir de la configuración

`CreateDefaultBuilder` llama a `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.

En el siguiente ejemplo de *appsettings.json* , se establece HTTP/1.1 como el protocolo de conexión predeterminado para todos los puntos de conexión:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

En el siguiente ejemplo de *appsettings.json* se establece el protocolo de conexión HTTP/1.1 para un punto de conexión específico:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

Los protocolos especificados en el código invalidan los valores establecidos por la configuración.

## <a name="url-prefixes"></a>Prefijos de URL

Al usar `UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` o una variable de entorno `ASPNETCORE_URLS`, los prefijos de dirección URL pueden tener cualquiera de estos formatos.

Solo son válidos los prefijos de dirección URL HTTP. Kestrel no admite HTTPS al configurar enlaces de dirección URL con `UseUrls`.

* Dirección IPv4 con número de puerto

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` es un caso especial que enlaza a todas las direcciones IPv4.

* Dirección IPv6 con número de puerto

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` es el equivalente en IPv6 de `0.0.0.0` en IPv4.

* Nombre de host con número de puerto

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Los nombres de host, `*` y `+` no son especiales. Todo lo que no se identifique como una dirección IP o un `localhost` válido se enlaza a todas las direcciones IP de IPv6 e IPv4. Para enlazar otros nombres de host a otras aplicaciones ASP.NET Core en el mismo puerto, use [HTTP.sys](xref:fundamentals/servers/httpsys) o un servidor proxy inverso. Entre los ejemplos de servidor proxy inverso se incluyen IIS, Nginx o Apache.

  > [!WARNING]
  > El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](xref:fundamentals/servers/kestrel/host-filtering).

* Nombre `localhost` del host con el número de puerto o la IP de bucle invertido con el número de puerto

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Cuando se especifica `localhost`, Kestrel intenta enlazar a las interfaces de bucle invertido de IPv4 e IPv6. Si el puerto solicitado lo está usando otro servicio en cualquier interfaz de bucle invertido, Kestrel no se puede iniciar. Si ninguna de estas interfaces de bucle invertido está disponible por cualquier otra razón (normalmente porque no se admite IPv6), Kestrel registra una advertencia.
