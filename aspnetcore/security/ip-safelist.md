---
title: La dirección IP de cliente de la ASP.NET Core
author: damienbod
description: Aprenda a escribir filtros de middleware o de acción para validar direcciones IP remotas en una lista de direcciones IP aprobadas.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
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
uid: security/ip-safelist
ms.openlocfilehash: f89191cff1faa5b55ee9031a6a6d0749a74e43c0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587546"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>La dirección IP de cliente de la ASP.NET Core

Por [Damien Bowden](https://twitter.com/damien_bod) y [Tom Dykstra](https://github.com/tdykstra)
 
En este artículo se muestran tres maneras de implementar una lista de direcciones IP seguras (también conocida como lista de permitidos) en una aplicación ASP.NET Core. En una aplicación de ejemplo adjunta se muestran los tres enfoques. Puede usar:

* Middleware para comprobar la dirección IP remota de cada solicitud.
* Filtros de acción de MVC para comprobar la dirección IP remota de las solicitudes de controladores o métodos de acción específicos.
* Razor Páginas filtros para comprobar la dirección IP remota de las solicitudes de Razor páginas.

En cada caso, una cadena que contiene direcciones IP de cliente aprobadas se almacena en una configuración de aplicación. El middleware o filtro:

* Analiza la cadena en una matriz. 
* Comprueba si la dirección IP remota existe en la matriz.

Se permite el acceso si la matriz contiene la dirección IP. De lo contrario, se devuelve un código de Estado HTTP 403 prohibido.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/ip-safelist/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>Dirección IP segura

En la aplicación de ejemplo, la dirección IP safelist es:

* Definido por la `AdminSafeList` propiedad en el *appsettings.json* archivo.
* Cadena delimitada por signos de punto y coma que puede contener direcciones del [Protocolo de Internet versión 4 (IPv4)](https://wikipedia.org/wiki/IPv4) y del [Protocolo de Internet versión 6 (IPv6)](https://wikipedia.org/wiki/IPv6) .

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

En el ejemplo anterior, se permiten las direcciones IPv4 de `127.0.0.1` y `192.168.1.5` y la dirección de bucle invertido IPv6 de `::1` (formato comprimido para `0:0:0:0:0:0:0:1` ).

## <a name="middleware"></a>Software intermedio

El `Startup.Configure` método agrega el `AdminSafeListMiddleware` tipo de middleware personalizado a la canalización de solicitudes de la aplicación. La clave segura se recupera con el proveedor de configuración de .NET Core y se pasa como un parámetro de constructor.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

El middleware analiza la cadena en una matriz y busca la dirección IP remota en la matriz. Si no se encuentra la dirección IP remota, el middleware devuelve el HTTP 403 prohibido. Este proceso de validación se omite para las solicitudes HTTP GET.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtro de acción

Si desea un control de acceso controlado por la misma para controladores MVC o métodos de acción específicos, use un filtro de acción. Por ejemplo:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

En `Startup.ConfigureServices` , agregue el filtro de acción a la colección de filtros de MVC. En el ejemplo siguiente, `ClientIpCheckActionFilter` se agrega un filtro de acción. Una instancia de safelist y una instancia del registrador de consola se pasan como parámetros de constructor.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

A continuación, el filtro de acción se puede aplicar a un controlador o un método de acción con el atributo [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

En la aplicación de ejemplo, el filtro de acción se aplica al método de acción del controlador `Get` . Al probar la aplicación mediante el envío de:

* Una solicitud HTTP GET, el `[ServiceFilter]` atributo valida la dirección IP del cliente. Si se permite el acceso al `Get` método de acción, el filtro de acción y el método de acción producen una variación de la siguiente salida de consola:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Un verbo de solicitud HTTP que no sea GET, el `AdminSafeListMiddleware` middleware valida la dirección IP del cliente.

## <a name="razor-pages-filter"></a>Razor Filtro de páginas

Si desea un control de acceso basado en la aplicación segura para una Razor aplicación de páginas, use un Razor filtro de páginas. Por ejemplo:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

En `Startup.ConfigureServices` , habilite el Razor filtro de páginas agregándolo a la colección de filtros de MVC. En el ejemplo siguiente, `ClientIpCheckPageFilter` Razor se agrega un filtro de páginas. Una instancia de safelist y una instancia del registrador de consola se pasan como parámetros de constructor.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Cuando se solicita la página de *Índice* de la aplicación de ejemplo Razor , el Razor filtro de páginas valida la dirección IP del cliente. El filtro genera una variación de la siguiente salida de la consola:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/middleware/index>
* [Filtros de acciones](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
