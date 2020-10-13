---
title: Hospedaje fuera de proceso con IIS y ASP.NET Core
author: rick-anderson
description: Obtenga información sobre el hospedaje fuera de proceso con IIS y el módulo de ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 048a18349de4d784ae4abb33bd86a2d9c08c609d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755213"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>Hospedaje fuera de proceso con IIS y ASP.NET Core 

Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo ASP.NET Core se encarga de la administración de procesos. El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud y reinicia la aplicación si esta se apaga o se bloquea. Este comportamiento es básicamente el mismo que el de las aplicaciones que se ejecutan en proceso y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada fuera de proceso:

![Módulo ASP.NET Core en el escenario de hospedaje fuera de proceso](index/_static/ancm-outofprocess.png)

1. Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel.
1. El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS).
1. El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación, que no es ni 80 ni 443.

<!-- make this a bullet list -->
El módulo ASP.NET Core especifica el puerto a través de una variable de entorno en el inicio. La extensión de <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> configura el servidor para que escuche en `http://localhost:{PORT}`. Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo. El módulo no es compatible con el reenvío de HTTPS. Las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.

Una vez que Kestrel toma la solicitud del módulo, la reenvía a la canalización de middleware de ASP.NET Core. La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación. El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel. La respuesta de la aplicación se vuelve a pasar a IIS, que la reenvía de nuevo al cliente HTTP que inició la solicitud.

Para obtener instrucciones sobre la configuración del módulo ASP.NET Core, vea <xref:host-and-deploy/aspnet-core-module>.

Para obtener más información sobre el hospedaje, consulte [Hospedaje en ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Configuración de aplicación

### <a name="enable-the-iisintegration-components"></a>Habilitación de los componentes de integración con IIS

Al compilar un host en `CreateHostBuilder` (`Program.cs`), llame a <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> para permitir la integración de IIS:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Para más información sobre `CreateDefaultBuilder`, consulte <xref:fundamentals/host/generic-host#default-builder-settings>.


**Modelo de hospedaje fuera de proceso**

Para configurar las opciones de IIS, incluya una configuración de servicio para <xref:Microsoft.AspNetCore.Builder.IISOptions> en <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. En el ejemplo siguiente se impide que la aplicación rellene `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opción                         | Default | Parámetro |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si es `true`, el [middleware de integración con IIS](#enable-the-iisintegration-components) establece el `HttpContext.User` autenticado mediante [autenticación de Windows](xref:security/authentication/windowsauth). Si es `false`, el middleware solo proporciona una identidad para `HttpContext.User` y responde a los desafíos cuando se le solicita explícitamente mediante el `AuthenticationScheme`. Autenticación de Windows debe estar habilitado en IIS para que `AutomaticAuthentication` funcione. Para más información, consulte el tema [Autenticación de Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Establece el nombre para mostrar que se muestra a los usuarios en las páginas de inicio de sesión. |
| `ForwardClientCertificate`     | `true`  | Si `HttpContext.Connection.ClientCertificate` y el encabezado de solicitud `true` está presente, se rellena `MS-ASPNETCORE-CLIENTCERT`. |


### <a name="proxy-server-and-load-balancer-scenarios"></a>Escenarios de servidor proxy y equilibrador de carga

El [middleware de integración de IIS](#enable-the-iisintegration-components) y el módulo de ASP.NET Core están configurados para reenviar:

* Esquema (HTTP/HTTPS).
* Dirección IP remota donde se originó la solicitud.

El [middleware de integración de IIS](#enable-the-iisintegration-components) configura el middleware de encabezados reenviados.

Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga adicionales. Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).


### <a name="out-of-process-hosting-model"></a>Modelo de hospedaje fuera de proceso

Para configurar una aplicación para un hospedaje fuera de proceso, establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `OutOfProcess` en el archivo del proyecto ( `.csproj`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

El hospedaje en proceso se establece con `InProcess`, que es el valor predeterminado.

El valor de `<AspNetCoreHostingModel>` no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.

En lugar del servidor [Kestrel](xref:fundamentals/servers/kestrel) se usa el servidor HTTP de IIS (`IISHttpServer`).

Fuera del proceso, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> para:

* Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.
* Configurar el host para capturar errores de inicio.

### <a name="process-name"></a>Nombre del proceso

`Process.GetCurrentProcess().ProcessName` informa a `w3wp`/`iisexpress` (en proceso) o `dotnet` (fuera de proceso).

Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos. Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.

El módulo ASP.NET Core también puede:

* Establecer variables de entorno para un proceso de trabajo.
* Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.
* Reenviar tokens de autenticación de Windows.
