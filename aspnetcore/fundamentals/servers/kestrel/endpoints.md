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
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="e32f4-103">Configuración de puntos de conexión para el servidor web Kestrel de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e32f4-103">Configure endpoints for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="e32f4-104">ASP.NET Core enlaza de forma predeterminada a:</span><span class="sxs-lookup"><span data-stu-id="e32f4-104">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="e32f4-105">`https://localhost:5001` (cuando hay presente un certificado de desarrollo local)</span><span class="sxs-lookup"><span data-stu-id="e32f4-105">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="e32f4-106">Especifique direcciones URL mediante los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="e32f4-106">Specify URLs using the:</span></span>

* <span data-ttu-id="e32f4-107">La variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-107">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="e32f4-108">El argumento de la línea de comandos `--urls`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-108">`--urls` command-line argument.</span></span>
* <span data-ttu-id="e32f4-109">La clave de configuración de host `urls`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-109">`urls` host configuration key.</span></span>
* <span data-ttu-id="e32f4-110">El método de extensión `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-110">`UseUrls` extension method.</span></span>

<span data-ttu-id="e32f4-111">El valor que estos métodos suministran puede ser uno o más puntos de conexión HTTP y HTTPS (este último, si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="e32f4-111">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="e32f4-112">Configure el valor como una lista separada por punto y coma (por ejemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="e32f4-112">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="e32f4-113">Para más información sobre estos enfoques, consulte [Direcciones URL del servidor](xref:fundamentals/host/web-host#server-urls) e [Invalidar la configuración](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="e32f4-113">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="e32f4-114">Un certificado de desarrollo se crea:</span><span class="sxs-lookup"><span data-stu-id="e32f4-114">A development certificate is created:</span></span>

* <span data-ttu-id="e32f4-115">Al instalar el [SDK de .NET](/dotnet/core/sdk).</span><span class="sxs-lookup"><span data-stu-id="e32f4-115">When the [.NET SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="e32f4-116">Para crear un certificado, se usa la [herramienta dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="e32f4-116">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="e32f4-117">Algunos exploradores necesitan que se conceda permiso explícito para confiar en el certificado de desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="e32f4-117">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="e32f4-118">Las plantillas de proyecto configuran aplicaciones para que se ejecuten en HTTPS de forma predeterminada e incluyen [redirección de HTTPS y compatibilidad con HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="e32f4-118">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="e32f4-119">Llame a los métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar los puertos y los prefijos de dirección URL para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e32f4-119">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="e32f4-120">`UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` y la variable de entorno `ASPNETCORE_URLS` también funcionan, pero tienen las limitaciones que se indican más adelante en esta sección (debe haber disponible un certificado predeterminado para la configuración de puntos de conexión HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e32f4-120">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="e32f4-121">Configuración de `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="e32f4-121">`KestrelServerOptions` configuration:</span></span>

## <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="e32f4-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="e32f4-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="e32f4-123">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión especificado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-123">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="e32f4-124">Al llamar a `ConfigureEndpointDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="e32f4-124">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="e32f4-125">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="e32f4-125">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> won't have the defaults applied.</span></span>

## <a name="configureiconfiguration"></a><span data-ttu-id="e32f4-126">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="e32f4-126">Configure(IConfiguration)</span></span>

<span data-ttu-id="e32f4-127">Crea un cargador de configuración para configurar Kestrel que toma una <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="e32f4-127">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="e32f4-128">El ámbito de la configuración debe corresponderse con la sección de configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e32f4-128">The configuration must be scoped to the configuration section for Kestrel.</span></span>

<span data-ttu-id="e32f4-129">`CreateDefaultBuilder` llama a `Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e32f4-129">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

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

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="e32f4-130">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="e32f4-130">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="e32f4-131">Especifica una `Action` de configuración para que se ejecute con cada punto de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e32f4-131">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="e32f4-132">Al llamar a `ConfigureHttpsDefaults` varias veces, se reemplazan las `Action` anteriores por la última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="e32f4-132">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="e32f4-133">Los puntos de conexión que se crean mediante una llamada a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **antes** de llamar a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> no tendrán aplicados los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="e32f4-133">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> won't have the defaults applied.</span></span>

## <a name="listenoptionsusehttps"></a><span data-ttu-id="e32f4-134">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="e32f4-134">ListenOptions.UseHttps</span></span>

<span data-ttu-id="e32f4-135">Configure Kestrel para que use HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e32f4-135">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="e32f4-136">Extensiones de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="e32f4-136">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="e32f4-137">`UseHttps`: configure Kestrel para que use HTTPS con el certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-137">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="e32f4-138">Produce una excepción si no hay ningún certificado predeterminado configurado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-138">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="e32f4-139">Parámetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="e32f4-139">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="e32f4-140">`filename` es la ruta de acceso y el nombre de archivo de un archivo de certificado correspondiente al directorio donde están los archivos de contenido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e32f4-140">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="e32f4-141">`password` es la contraseña necesaria para obtener acceso a los datos del certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="e32f4-141">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="e32f4-142">`configureOptions` es una `Action` para configurar `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-142">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="e32f4-143">Devuelve `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-143">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="e32f4-144">`storeName` es el almacén de certificados desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-144">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="e32f4-145">`subject` es el nombre del sujeto del certificado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-145">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="e32f4-146">`allowInvalid` indica si se deben tener en cuenta los certificados no válidos, como los certificados autofirmados.</span><span class="sxs-lookup"><span data-stu-id="e32f4-146">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="e32f4-147">`location` es la ubicación del almacén desde el que se carga el certificado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-147">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="e32f4-148">`serverCertificate` es el certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="e32f4-148">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="e32f4-149">En un entorno de producción, HTTPS se debe configurar explícitamente.</span><span class="sxs-lookup"><span data-stu-id="e32f4-149">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="e32f4-150">Como mínimo, debe existir un certificado predeterminado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-150">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="e32f4-151">Estas son las configuraciones compatibles:</span><span class="sxs-lookup"><span data-stu-id="e32f4-151">Supported configurations described next:</span></span>

* <span data-ttu-id="e32f4-152">Sin configuración</span><span class="sxs-lookup"><span data-stu-id="e32f4-152">No configuration</span></span>
* <span data-ttu-id="e32f4-153">Reemplazar el certificado predeterminado de configuración</span><span class="sxs-lookup"><span data-stu-id="e32f4-153">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="e32f4-154">Cambiar los valores predeterminados en el código</span><span class="sxs-lookup"><span data-stu-id="e32f4-154">Change the defaults in code</span></span>

### <a name="no-configuration"></a><span data-ttu-id="e32f4-155">Sin configuración</span><span class="sxs-lookup"><span data-stu-id="e32f4-155">No configuration</span></span>

<span data-ttu-id="e32f4-156">Kestrel escucha en `http://localhost:5000` y en `https://localhost:5001` (si hay disponible un certificado predeterminado).</span><span class="sxs-lookup"><span data-stu-id="e32f4-156">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a><span data-ttu-id="e32f4-157">Reemplazar el certificado predeterminado de configuración</span><span class="sxs-lookup"><span data-stu-id="e32f4-157">Replace the default certificate from configuration</span></span>

<span data-ttu-id="e32f4-158">Hay disponible un esquema de configuración de aplicación HTTPS predeterminado para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e32f4-158">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="e32f4-159">Configure varios puntos de conexión (incluidas las direcciones URL y los certificados que va a usar) desde un archivo en disco o desde un almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="e32f4-159">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="e32f4-160">En el ejemplo *appsettings.json* siguiente:</span><span class="sxs-lookup"><span data-stu-id="e32f4-160">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="e32f4-161">Establezca `AllowInvalid` en `true` para permitir el uso de certificados no válidos (por ejemplo, certificados autofirmados).</span><span class="sxs-lookup"><span data-stu-id="e32f4-161">Set `AllowInvalid` to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="e32f4-162">Cualquier punto de conexión HTTPS que no especifique un certificado (`HttpsDefaultCert` en el siguiente ejemplo) revierte al certificado definido en `Certificates:Default` o al certificado de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="e32f4-162">Any HTTPS endpoint that doesn't specify a certificate (`HttpsDefaultCert` in the example that follows) falls back to the cert defined under `Certificates:Default` or the development certificate.</span></span>

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

<span data-ttu-id="e32f4-163">Notas sobre el esquema:</span><span class="sxs-lookup"><span data-stu-id="e32f4-163">Schema notes:</span></span>

* <span data-ttu-id="e32f4-164">En los nombres de los puntos de conexión [se distingue entre mayúsculas y minúsculas](xref:fundamentals/configuration/index#configuration-keys-and-values).</span><span class="sxs-lookup"><span data-stu-id="e32f4-164">Endpoints names are [case-insensitive](xref:fundamentals/configuration/index#configuration-keys-and-values).</span></span> <span data-ttu-id="e32f4-165">Por ejemplo, `HTTPS` and `Https` son equivalentes.</span><span class="sxs-lookup"><span data-stu-id="e32f4-165">For example, `HTTPS` and `Https` are equivalent.</span></span>
* <span data-ttu-id="e32f4-166">El parámetro `Url` es necesario en cada punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="e32f4-166">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="e32f4-167">El formato de este parámetro es el mismo que el del parámetro de configuración `Urls` de nivel superior, excepto por el hecho de que está limitado a un único valor.</span><span class="sxs-lookup"><span data-stu-id="e32f4-167">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="e32f4-168">En vez de agregarse, estos puntos de conexión reemplazan a los que están definidos en la configuración `Urls` de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="e32f4-168">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="e32f4-169">Los puntos de conexión definidos en el código a través de `Listen` son acumulativos con respecto a los puntos de conexión definidos en la sección de configuración.</span><span class="sxs-lookup"><span data-stu-id="e32f4-169">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="e32f4-170">La sección `Certificate` es opcional.</span><span class="sxs-lookup"><span data-stu-id="e32f4-170">The `Certificate` section is optional.</span></span> <span data-ttu-id="e32f4-171">Si la sección `Certificate` no se especifica, se usan los valores predeterminados definidos en `Certificates:Default`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-171">If the `Certificate` section isn't specified, the defaults defined in `Certificates:Default` are used.</span></span> <span data-ttu-id="e32f4-172">Si no hay valores predeterminados disponibles, se utiliza el certificado de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="e32f4-172">If no defaults are available, the development certificate is used.</span></span> <span data-ttu-id="e32f4-173">Si no hay valores predeterminados y el certificado de desarrollo no está presente, el servidor produce una excepción y no se inicia.</span><span class="sxs-lookup"><span data-stu-id="e32f4-173">If there are no defaults and the development certificate isn't present, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="e32f4-174">En la sección `Certificate` se admiten varios [orígenes de certificados](#certificate-sources).</span><span class="sxs-lookup"><span data-stu-id="e32f4-174">The `Certificate` section supports multiple [certificate sources](#certificate-sources).</span></span>
* <span data-ttu-id="e32f4-175">Se puede definir el número de puntos de conexión que se quiera en la [configuración](xref:fundamentals/configuration/index), siempre y cuando no produzcan conflictos de puerto.</span><span class="sxs-lookup"><span data-stu-id="e32f4-175">Any number of endpoints may be defined in [Configuration](xref:fundamentals/configuration/index) as long as they don't cause port conflicts.</span></span>

#### <a name="certificate-sources"></a><span data-ttu-id="e32f4-176">Orígenes de certificados</span><span class="sxs-lookup"><span data-stu-id="e32f4-176">Certificate sources</span></span>

<span data-ttu-id="e32f4-177">Los nodos de certificado se pueden configurar para cargar certificados de varios orígenes:</span><span class="sxs-lookup"><span data-stu-id="e32f4-177">Certificate nodes can be configured to load certificates from a number of sources:</span></span>

* <span data-ttu-id="e32f4-178">`Path` y `Password` para cargar archivos *.pfx*.</span><span class="sxs-lookup"><span data-stu-id="e32f4-178">`Path` and `Password` to load *.pfx* files.</span></span>
* <span data-ttu-id="e32f4-179">`Path`, `KeyPath` y `Password` para cargar archivos *.pem*/ *.crt* y *.key*.</span><span class="sxs-lookup"><span data-stu-id="e32f4-179">`Path`, `KeyPath` and `Password` to load *.pem*/*.crt* and *.key* files.</span></span>
* <span data-ttu-id="e32f4-180">`Subject` y `Store` cargar desde el almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="e32f4-180">`Subject` and `Store` to load from the certificate store.</span></span>

<span data-ttu-id="e32f4-181">Por ejemplo, el certificado en `Certificates:Default` se puede especificar así:</span><span class="sxs-lookup"><span data-stu-id="e32f4-181">For example, the `Certificates:Default` certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a><span data-ttu-id="e32f4-182">ConfigurationLoader</span><span class="sxs-lookup"><span data-stu-id="e32f4-182">ConfigurationLoader</span></span>

<span data-ttu-id="e32f4-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` devuelve un <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> con un método `.Endpoint(string name, listenOptions => { })` que se puede usar para complementar la configuración de un punto de conexión configurado:</span><span class="sxs-lookup"><span data-stu-id="e32f4-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="e32f4-184">Se puede acceder directamente a `KestrelServerOptions.ConfigurationLoader` para seguir con la iteración en el cargador existente, como el proporcionado por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span><span class="sxs-lookup"><span data-stu-id="e32f4-184">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span>

* <span data-ttu-id="e32f4-185">La sección de configuración de cada punto de conexión está disponible en las opciones del método `Endpoint` para que se pueda leer la configuración personalizada.</span><span class="sxs-lookup"><span data-stu-id="e32f4-185">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="e32f4-186">Se pueden cargar varias configuraciones volviendo a llamar a `options.Configure(context.Configuration.GetSection("{SECTION}"))` con otra sección.</span><span class="sxs-lookup"><span data-stu-id="e32f4-186">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="e32f4-187">Se usa la última configuración, a menos que se llame explícitamente a `Load` en instancias anteriores.</span><span class="sxs-lookup"><span data-stu-id="e32f4-187">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="e32f4-188">El metapaquete no llama a `Load`, con lo cual su sección de configuración predeterminada se puede reemplazar.</span><span class="sxs-lookup"><span data-stu-id="e32f4-188">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="e32f4-189">`KestrelConfigurationLoader` refleja la familia `Listen` de API de `KestrelServerOptions` como sobrecargas de `Endpoint`, por lo que los puntos de conexión de configuración y código se pueden configurar en el mismo lugar.</span><span class="sxs-lookup"><span data-stu-id="e32f4-189">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="e32f4-190">En estas sobrecargas no se usan nombres y solo consumen valores predeterminados de la configuración.</span><span class="sxs-lookup"><span data-stu-id="e32f4-190">These overloads don't use names and only consume default settings from configuration.</span></span>

### <a name="change-the-defaults-in-code"></a><span data-ttu-id="e32f4-191">Cambiar los valores predeterminados en el código</span><span class="sxs-lookup"><span data-stu-id="e32f4-191">Change the defaults in code</span></span>

<span data-ttu-id="e32f4-192">`ConfigureEndpointDefaults` y `ConfigureHttpsDefaults` se pueden usar para cambiar la configuración predeterminada de `ListenOptions` y `HttpsConnectionAdapterOptions`, incluido sustituir el certificado predeterminado especificado en el escenario anterior.</span><span class="sxs-lookup"><span data-stu-id="e32f4-192">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="e32f4-193">Se debe llamar a `ConfigureEndpointDefaults` y a `ConfigureHttpsDefaults` antes de que se configure algún punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="e32f4-193">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

## <a name="configure-endpoints-using-server-name-indication"></a><span data-ttu-id="e32f4-194">Configuración de puntos de conexión mediante Indicación de nombre de servidor</span><span class="sxs-lookup"><span data-stu-id="e32f4-194">Configure endpoints using Server Name Indication</span></span>

<span data-ttu-id="e32f4-195">[Indicación de nombre de servidor (SNI)](https://tools.ietf.org/html/rfc6066#section-3) se puede usar para hospedar varios dominios en la misma dirección IP y puerto.</span><span class="sxs-lookup"><span data-stu-id="e32f4-195">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="e32f4-196">Para que SNI funcione, el cliente envía el nombre de host de la sesión segura al servidor durante el protocolo de enlace TLS para que, de este modo, el servidor pueda proporcionar el certificado correcto.</span><span class="sxs-lookup"><span data-stu-id="e32f4-196">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="e32f4-197">El cliente usa el certificado proporcionado para la comunicación cifrada con el servidor durante la sesión segura que sigue al protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="e32f4-197">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="e32f4-198">Kestrel admite SNI a través de la devolución de llamada `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-198">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="e32f4-199">La devolución de llamada se invoca una vez por conexión para permitir que la aplicación inspeccione el nombre de host y seleccione el certificado adecuado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-199">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span> <span data-ttu-id="e32f4-200">El siguiente código de devolución de llamada se puede usar en la llamada al método `ConfigureWebHostDefaults` del archivo *Program.cs* de un proyecto:</span><span class="sxs-lookup"><span data-stu-id="e32f4-200">The following callback code can be used in the `ConfigureWebHostDefaults` method call of a project's *Program.cs* file:</span></span>

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

<span data-ttu-id="e32f4-201">La compatibilidad con SNI requiere lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e32f4-201">SNI support requires:</span></span>

* <span data-ttu-id="e32f4-202">Ejecutarse en el marco de destino `netcoreapp2.1` o posterior.</span><span class="sxs-lookup"><span data-stu-id="e32f4-202">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="e32f4-203">En `net461` o posterior, se invoca la devolución de llamada, pero `name` siempre es `null`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-203">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="e32f4-204">`name` también será `null` si el cliente no proporciona el parámetro de nombre de host en el protocolo de enlace TLS.</span><span class="sxs-lookup"><span data-stu-id="e32f4-204">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="e32f4-205">Todos los sitios web deben ejecutarse en la misma instancia de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e32f4-205">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="e32f4-206">Kestrel no admite el uso compartido de una dirección IP y un puerto entre varias instancias sin un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="e32f4-206">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

## <a name="ssltls-protocols"></a><span data-ttu-id="e32f4-207">Protocolos SSL/TLS</span><span class="sxs-lookup"><span data-stu-id="e32f4-207">SSL/TLS Protocols</span></span>

<span data-ttu-id="e32f4-208">Los protocolos SSL son protocolos que se utilizan para cifrar y descifrar el tráfico entre dos elementos del mismo nivel, tradicionalmente un cliente y un servidor.</span><span class="sxs-lookup"><span data-stu-id="e32f4-208">SSL Protocols are protocols used for encrypting and decrypting traffic between two peers, traditionally a client and a server.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls13;
    });
});
```

<span data-ttu-id="e32f4-209">El valor predeterminado, `SslProtocols.None`, hace que Kestrel use los valores predeterminados del sistema operativo para elegir el mejor protocolo.</span><span class="sxs-lookup"><span data-stu-id="e32f4-209">The default value, `SslProtocols.None`, causes Kestrel to use the operating system defaults to choose the best protocol.</span></span> <span data-ttu-id="e32f4-210">A menos que tenga una razón específica para seleccionar un protocolo, utilice el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-210">Unless you have a specific reason to select a protocol, use the default.</span></span>
## <a name="connection-logging"></a><span data-ttu-id="e32f4-211">Registro de conexiones</span><span class="sxs-lookup"><span data-stu-id="e32f4-211">Connection logging</span></span>

<span data-ttu-id="e32f4-212">Llame a <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> para emitir registros de nivel de depuración para la comunicación a nivel de bytes en una conexión.</span><span class="sxs-lookup"><span data-stu-id="e32f4-212">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="e32f4-213">El registro de conexiones es útil para solucionar problemas en la comunicación de bajo nivel, como durante el cifrado TLS y detrás de los servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="e32f4-213">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="e32f4-214">Si `UseConnectionLogging` se coloca antes de `UseHttps`, se registra el tráfico cifrado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-214">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="e32f4-215">Si `UseConnectionLogging` se coloca después de `UseHttps`, se registra el tráfico descifrado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-215">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span> <span data-ttu-id="e32f4-216">Este es el [middleware de conexión](#connection-middleware) integrado.</span><span class="sxs-lookup"><span data-stu-id="e32f4-216">This is built-in [Connection Middleware](#connection-middleware).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="e32f4-217">Enlazar a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="e32f4-217">Bind to a TCP socket</span></span>

<span data-ttu-id="e32f4-218">El método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> se enlaza a un socket TCP y una expresión lambda de opciones permite configurar un certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="e32f4-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="e32f4-219">En el ejemplo se configura HTTPS para un punto de conexión con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="e32f4-219">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="e32f4-220">Use la misma API para configurar otras opciones de Kestrel para puntos de conexión específicos.</span><span class="sxs-lookup"><span data-stu-id="e32f4-220">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a><span data-ttu-id="e32f4-221">Enlazar a un socket de Unix</span><span class="sxs-lookup"><span data-stu-id="e32f4-221">Bind to a Unix socket</span></span>

<span data-ttu-id="e32f4-222">Escuche en un socket de Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> para mejorar el rendimiento con Nginx, tal como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e32f4-222">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="e32f4-223">En el archivo de configuración de Nginx, establezca la entrada `server` > `location` > `proxy_pass` en `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-223">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="e32f4-224">`{KESTREL SOCKET}` es el nombre del socket proporcionado para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (por ejemplo, `kestrel-test.sock` en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="e32f4-224">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="e32f4-225">Asegúrese de que el socket es grabable por Nginx (por ejemplo, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="e32f4-225">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

## <a name="port-0"></a><span data-ttu-id="e32f4-226">Puerto 0</span><span class="sxs-lookup"><span data-stu-id="e32f4-226">Port 0</span></span>

<span data-ttu-id="e32f4-227">Cuando se especifica el número de puerto `0`, Kestrel se enlaza de forma dinámica a un puerto disponible.</span><span class="sxs-lookup"><span data-stu-id="e32f4-227">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="e32f4-228">En el ejemplo siguiente se muestra cómo determinar a qué puerto se enlaza Kestrel en tiempo de ejecución:</span><span class="sxs-lookup"><span data-stu-id="e32f4-228">The following example shows how to determine which port Kestrel bound at runtime:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="e32f4-229">Cuando la aplicación se ejecuta, la salida de la ventana de consola indica el puerto dinámico en el que se puede tener acceso a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="e32f4-229">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a><span data-ttu-id="e32f4-230">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="e32f4-230">Limitations</span></span>

<span data-ttu-id="e32f4-231">Configure puntos de conexión con los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="e32f4-231">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* <span data-ttu-id="e32f4-232">El argumento de la línea de comandos `--urls`</span><span class="sxs-lookup"><span data-stu-id="e32f4-232">`--urls` command-line argument</span></span>
* <span data-ttu-id="e32f4-233">La clave de configuración de host `urls`</span><span class="sxs-lookup"><span data-stu-id="e32f4-233">`urls` host configuration key</span></span>
* <span data-ttu-id="e32f4-234">La variable de entorno `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="e32f4-234">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="e32f4-235">Estos métodos son útiles para que el código funcione con servidores que no sean de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e32f4-235">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="e32f4-236">Sin embargo, tenga en cuenta las siguientes limitaciones:</span><span class="sxs-lookup"><span data-stu-id="e32f4-236">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="e32f4-237">HTTPS no se puede usar con estos enfoques, a menos que se proporcione un certificado predeterminado en la configuración del punto de conexión HTTPS (por ejemplo, mediante la configuración `KestrelServerOptions` o un archivo de configuración, como se ha mostrado antes en este artículo).</span><span class="sxs-lookup"><span data-stu-id="e32f4-237">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this article).</span></span>
* <span data-ttu-id="e32f4-238">Cuando los métodos `Listen` y `UseUrls` se usan al mismo tiempo, los puntos de conexión de `Listen` sustituyen a los de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-238">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

## <a name="iis-endpoint-configuration"></a><span data-ttu-id="e32f4-239">Configuración de puntos de conexión IIS</span><span class="sxs-lookup"><span data-stu-id="e32f4-239">IIS endpoint configuration</span></span>

<span data-ttu-id="e32f4-240">Cuando se usa IIS, los enlaces de direcciones URL de IIS reemplazan a los enlaces que se hayan establecido por medio de `Listen` o de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-240">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="e32f4-241">Para más información, vea [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) (Módulo de ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="e32f4-241">For more information, see [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

## <a name="listenoptionsprotocols"></a><span data-ttu-id="e32f4-242">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="e32f4-242">ListenOptions.Protocols</span></span>

<span data-ttu-id="e32f4-243">La propiedad `Protocols` establece los protocolos HTTP (`HttpProtocols`) habilitados en un punto de conexión o para el servidor.</span><span class="sxs-lookup"><span data-stu-id="e32f4-243">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="e32f4-244">Asigne un valor a la propiedad `Protocols` desde el valor de enumeración `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-244">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="e32f4-245">Valor de enumeración `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="e32f4-245">`HttpProtocols` enum value</span></span> | <span data-ttu-id="e32f4-246">Protocolo de conexión permitido</span><span class="sxs-lookup"><span data-stu-id="e32f4-246">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="e32f4-247">HTTP/1.1 solo.</span><span class="sxs-lookup"><span data-stu-id="e32f4-247">HTTP/1.1 only.</span></span> <span data-ttu-id="e32f4-248">Puede usarse con o sin TLS.</span><span class="sxs-lookup"><span data-stu-id="e32f4-248">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="e32f4-249">HTTP/2 solo.</span><span class="sxs-lookup"><span data-stu-id="e32f4-249">HTTP/2 only.</span></span> <span data-ttu-id="e32f4-250">Se pueden utilizar sin TLS solo si el cliente admite un [modo de conocimientos previos](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="e32f4-250">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="e32f4-251">HTTP/1.1 y HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e32f4-251">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="e32f4-252">HTTP/2 necesita que el cliente seleccione HTTP/2 en el protocolo de enlace [Negociación de protocolo de nivel de aplicación (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) de TLS; en caso contrario, el valor predeterminado de la conexión es HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="e32f4-252">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="e32f4-253">El valor `ListenOptions.Protocols` predeterminado de cualquier punto de conexión es `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-253">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="e32f4-254">Restricciones de TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="e32f4-254">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="e32f4-255">TLS 1.2 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="e32f4-255">TLS version 1.2 or later</span></span>
* <span data-ttu-id="e32f4-256">Renegociación deshabilitada</span><span class="sxs-lookup"><span data-stu-id="e32f4-256">Renegotiation disabled</span></span>
* <span data-ttu-id="e32f4-257">Compresión deshabilitada</span><span class="sxs-lookup"><span data-stu-id="e32f4-257">Compression disabled</span></span>
* <span data-ttu-id="e32f4-258">Tamaños de intercambio de claves efímeras mínimos:</span><span class="sxs-lookup"><span data-stu-id="e32f4-258">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="e32f4-259">Curva elíptica Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits como mínimo</span><span class="sxs-lookup"><span data-stu-id="e32f4-259">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="e32f4-260">Campo finito Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits como mínimo</span><span class="sxs-lookup"><span data-stu-id="e32f4-260">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="e32f4-261">Conjunto de cifrado no prohibido.</span><span class="sxs-lookup"><span data-stu-id="e32f4-261">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="e32f4-262">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; con la curva elíptica P-256 &lbrack;`FIPS186`&rbrack; es compatible de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e32f4-262">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="e32f4-263">El siguiente ejemplo permite conexiones HTTP/1.1 y HTTP/2 en el puerto 8000.</span><span class="sxs-lookup"><span data-stu-id="e32f4-263">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="e32f4-264">Las conexiones se protegen mediante TLS con un certificado proporcionado:</span><span class="sxs-lookup"><span data-stu-id="e32f4-264">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="e32f4-265">En Linux, se puede usar <xref:System.Net.Security.CipherSuitesPolicy> para filtrar los protocolos de enlace TLS por conexión:</span><span class="sxs-lookup"><span data-stu-id="e32f4-265">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

## <a name="connection-middleware"></a><span data-ttu-id="e32f4-266">Middleware de conexión</span><span class="sxs-lookup"><span data-stu-id="e32f4-266">Connection Middleware</span></span>

<span data-ttu-id="e32f4-267">Si es necesario, el middleware de conexión personalizado puede filtrar por cifrados específicos los protocolos de enlace TLS por cada conexión.</span><span class="sxs-lookup"><span data-stu-id="e32f4-267">Custom connection middleware can filter TLS handshakes on a per-connection basis for specific ciphers if necessary.</span></span>

<span data-ttu-id="e32f4-268">En el ejemplo siguiente se produce una excepción <xref:System.NotSupportedException> con cualquier algoritmo de cifrado que no admita la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e32f4-268">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="e32f4-269">Como alternativa, defina y compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) con una lista de conjuntos de cifrado aceptables.</span><span class="sxs-lookup"><span data-stu-id="e32f4-269">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="e32f4-270">No se usa ningún cifrado con un algoritmo de cifrado [CipherAlgorithmType.Null ](xref:System.Security.Authentication.CipherAlgorithmType).</span><span class="sxs-lookup"><span data-stu-id="e32f4-270">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="e32f4-271">El filtrado de conexiones también puede configurarse mediante una función lambda <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:</span><span class="sxs-lookup"><span data-stu-id="e32f4-271">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

## <a name="set-the-http-protocol-from-configuration"></a><span data-ttu-id="e32f4-272">Establecimiento del protocolo HTTP a partir de la configuración</span><span class="sxs-lookup"><span data-stu-id="e32f4-272">Set the HTTP protocol from configuration</span></span>

<span data-ttu-id="e32f4-273">`CreateDefaultBuilder` llama a `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` de forma predeterminada para cargar la configuración de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e32f4-273">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="e32f4-274">En el siguiente ejemplo de *appsettings.json* , se establece HTTP/1.1 como el protocolo de conexión predeterminado para todos los puntos de conexión:</span><span class="sxs-lookup"><span data-stu-id="e32f4-274">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="e32f4-275">En el siguiente ejemplo de *appsettings.json* se establece el protocolo de conexión HTTP/1.1 para un punto de conexión específico:</span><span class="sxs-lookup"><span data-stu-id="e32f4-275">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="e32f4-276">Los protocolos especificados en el código invalidan los valores establecidos por la configuración.</span><span class="sxs-lookup"><span data-stu-id="e32f4-276">Protocols specified in code override values set by configuration.</span></span>

## <a name="url-prefixes"></a><span data-ttu-id="e32f4-277">Prefijos de URL</span><span class="sxs-lookup"><span data-stu-id="e32f4-277">URL prefixes</span></span>

<span data-ttu-id="e32f4-278">Al usar `UseUrls`, el argumento de línea de comandos `--urls`, la clave de configuración de host `urls` o una variable de entorno `ASPNETCORE_URLS`, los prefijos de dirección URL pueden tener cualquiera de estos formatos.</span><span class="sxs-lookup"><span data-stu-id="e32f4-278">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="e32f4-279">Solo son válidos los prefijos de dirección URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="e32f4-279">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="e32f4-280">Kestrel no admite HTTPS al configurar enlaces de dirección URL con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="e32f4-280">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="e32f4-281">Dirección IPv4 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="e32f4-281">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="e32f4-282">`0.0.0.0` es un caso especial que enlaza a todas las direcciones IPv4.</span><span class="sxs-lookup"><span data-stu-id="e32f4-282">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="e32f4-283">Dirección IPv6 con número de puerto</span><span class="sxs-lookup"><span data-stu-id="e32f4-283">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="e32f4-284">`[::]` es el equivalente en IPv6 de `0.0.0.0` en IPv4.</span><span class="sxs-lookup"><span data-stu-id="e32f4-284">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="e32f4-285">Nombre de host con número de puerto</span><span class="sxs-lookup"><span data-stu-id="e32f4-285">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="e32f4-286">Los nombres de host, `*` y `+` no son especiales.</span><span class="sxs-lookup"><span data-stu-id="e32f4-286">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="e32f4-287">Todo lo que no se identifique como una dirección IP o un `localhost` válido se enlaza a todas las direcciones IP de IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="e32f4-287">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="e32f4-288">Para enlazar otros nombres de host a otras aplicaciones ASP.NET Core en el mismo puerto, use [HTTP.sys](xref:fundamentals/servers/httpsys) o un servidor proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="e32f4-288">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server.</span></span> <span data-ttu-id="e32f4-289">Entre los ejemplos de servidor proxy inverso se incluyen IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="e32f4-289">Reverse proxy server examples include IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="e32f4-290">El hospedaje en una configuración de proxy inverso requiere [filtrado de hosts](xref:fundamentals/servers/kestrel/host-filtering).</span><span class="sxs-lookup"><span data-stu-id="e32f4-290">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

* <span data-ttu-id="e32f4-291">Nombre `localhost` del host con el número de puerto o la IP de bucle invertido con el número de puerto</span><span class="sxs-lookup"><span data-stu-id="e32f4-291">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="e32f4-292">Cuando se especifica `localhost`, Kestrel intenta enlazar a las interfaces de bucle invertido de IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="e32f4-292">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="e32f4-293">Si el puerto solicitado lo está usando otro servicio en cualquier interfaz de bucle invertido, Kestrel no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="e32f4-293">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="e32f4-294">Si ninguna de estas interfaces de bucle invertido está disponible por cualquier otra razón (normalmente porque no se admite IPv6), Kestrel registra una advertencia.</span><span class="sxs-lookup"><span data-stu-id="e32f4-294">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>
