---
title: Hospedaje en proceso con IIS y ASP.NET Core
author: rick-anderson
description: Obtenga información sobre el hospedaje en proceso con IIS y el módulo de ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058485"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>Hospedaje en proceso con IIS y ASP.NET Core 

El hospedaje en proceso ejecuta una aplicación de ASP.NET Core en el mismo proceso que su proceso de trabajo de IIS. El hospedaje en proceso proporciona un rendimiento mejorado con respecto al hospedaje fuera de proceso porque las solicitudes no se realizan mediante proxy en el adaptador de bucle invertido, una interfaz de red que devuelve el tráfico saliente a la misma máquina.

En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación hospedada en proceso:

![Módulo ASP.NET Core en el escenario de hospedaje dentro de proceso](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>Habilitación del hospedaje en proceso

A partir de ASP.NET Core 3.0, el hospedaje en proceso se ha habilitado de forma predeterminada en todas las aplicaciones implementadas en IIS.

Para configurar explícitamente una aplicación para un hospedaje fuera de proceso, establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `InProcess` en el archivo del proyecto (`.csproj`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>Arquitectura general

El flujo general de una solicitud es el siguiente:

1. Una solicitud llega de Internet al controlador HTTP.sys en modo kernel.
1. El controlador enruta la solicitud nativa a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS).
1. El módulo ASP.NET Core recibe la solicitud nativa y la pasa a IIS HTTP Server (`IISHttpServer`). El servidor HTTP de IIS es una implementación de servidor en proceso para IIS que convierte una solicitud nativa en administrada.

Una vez que el servidor HTTP de IIS procesa la solicitud:

1. La solicitud se envía a la canalización de middleware de ASP.NET Core.
1. La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación.
1. La respuesta de la aplicación se pasa a IIS a través del servidor HTTP de IIS.
1. IIS envía la respuesta al cliente que inició la solicitud.

`CreateDefaultBuilder` agrega una instancia <xref:Microsoft.AspNetCore.Hosting.Server.IServer> mediante una llamada al método <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> para iniciar [CoreCLR](/dotnet/standard/glossary#coreclr) y hospedar la aplicación dentro del proceso de trabajo de IIS (`w3wp.exe` o `iisexpress.exe`). Las pruebas de rendimiento indican que el hospedaje de una aplicación .NET Core en proceso proporciona un rendimiento de solicitud considerablemente mayor en comparación con el hospedaje de solicitudes de aplicaciones fuera de proceso y de proxy para [Kestrel](xref:fundamentals/servers/kestrel).

Las aplicaciones publicadas como un único archivo ejecutable no se pueden cargar por el modelo de hospedaje en proceso.

## <a name="application-configuration"></a>Configuración de aplicación

Para configurar las opciones de IIS, incluya una configuración de servicio para <xref:Microsoft.AspNetCore.Builder.IISServerOptions> en <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. Con el ejemplo siguiente se deshabilita AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Opción | Default | Parámetro |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | Si es `true`, el servidor IIS establece el `HttpContext.User` autenticado mediante la [autenticación de Windows](xref:security/authentication/windowsauth). Si es `false`, el servidor solo proporciona una identidad para `HttpContext.User` y responde a los desafíos cuando se le solicita explícitamente mediante el `AuthenticationScheme`. Autenticación de Windows debe estar habilitado en IIS para que `AutomaticAuthentication` funcione. Para obtener más información, vea [Autenticación de Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName` | `null` | Establece el nombre para mostrar que se muestra a los usuarios en las páginas de inicio de sesión. |
| `AllowSynchronousIO` | `false` | Si se permiten la E/S sincrónica para `HttpContext.Request` y `HttpContext.Response`. |
| `MaxRequestBodySize` | `30000000` | Obtiene o establece el tamaño máximo del cuerpo de solicitud para `HttpRequest`. Tenga en cuenta que el propio IIS tiene el límite de `maxAllowedContentLength`, que se procesará antes que el valor de `MaxRequestBodySize` establecido en `IISServerOptions`. El cambio de `MaxRequestBodySize` no afectará a `maxAllowedContentLength`. Para aumentar `maxAllowedContentLength`, agregue una entrada al archivo `web.config` para establecer `maxAllowedContentLength` en un valor superior. Para más información, consulte [Configuración](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>Diferencias entre el hospedaje en proceso y fuera de proceso

Al hospedar en proceso, se aplican las siguientes características:

* En lugar del servidor HTTP de IIS (`IISHttpServer`) se usa el servidor [Kestrel](xref:fundamentals/servers/kestrel). Dentro del proceso, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> para:

  * Registrar el `IISHttpServer`.
  * Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.
  * Configurar el host para capturar errores de inicio.

* El [atributo `requestTimeout`](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) se aplica al hospedaje en proceso.

* No se admite el uso compartido de un grupo de aplicaciones entre aplicaciones. Se usa un grupo de aplicaciones por aplicación.

* La arquitectura (valor de bits) de la aplicación y el runtime instalado (x64 o x86) deben coincidir con la arquitectura del grupo de aplicaciones. Por ejemplo, las aplicaciones publicadas para 32 bits (x86) deben tener habilitados 32 bits para los grupos de aplicaciones de IIS. Para más información, consulte la sección [Creación del sitio de IIS](xref:tutorials/publish-to-iis#create-the-iis-site).

* Se detectan las desconexiones del cliente. El token de cancelación de [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) se cancela cuando el cliente se desconecta.

* Cuando se hospeda en el proceso, no se llama a <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> de forma interna para inicializar un usuario. Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada. Al transformar notificaciones con una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, llame a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> para agregar servicios de autenticación:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* No se admiten [implementaciones de paquetes web (archivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages).
