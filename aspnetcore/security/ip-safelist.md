---
title: La dirección IP de cliente de la ASP.NET Core
author: damienbod
description: Aprenda a escribir filtros de middleware o de acción para validar direcciones IP remotas en una lista de direcciones IP aprobadas.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/ip-safelist
ms.openlocfilehash: dfc134b97bb0976bc682a53d536cd27785550c7d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059668"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="9f29c-103">La dirección IP de cliente de la ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9f29c-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="9f29c-104">Por [Damien Bowden](https://twitter.com/damien_bod) y [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="9f29c-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="9f29c-105">En este artículo se muestran tres maneras de implementar una lista de direcciones IP seguras (también conocida como lista de permitidos) en una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9f29c-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="9f29c-106">En una aplicación de ejemplo adjunta se muestran los tres enfoques.</span><span class="sxs-lookup"><span data-stu-id="9f29c-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="9f29c-107">Puede usar:</span><span class="sxs-lookup"><span data-stu-id="9f29c-107">You can use:</span></span>

* <span data-ttu-id="9f29c-108">Middleware para comprobar la dirección IP remota de cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="9f29c-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="9f29c-109">Filtros de acción de MVC para comprobar la dirección IP remota de las solicitudes de controladores o métodos de acción específicos.</span><span class="sxs-lookup"><span data-stu-id="9f29c-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="9f29c-110">Razor Páginas filtros para comprobar la dirección IP remota de las solicitudes de Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="9f29c-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="9f29c-111">En cada caso, una cadena que contiene direcciones IP de cliente aprobadas se almacena en una configuración de aplicación.</span><span class="sxs-lookup"><span data-stu-id="9f29c-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="9f29c-112">El middleware o filtro:</span><span class="sxs-lookup"><span data-stu-id="9f29c-112">The middleware or filter:</span></span>

* <span data-ttu-id="9f29c-113">Analiza la cadena en una matriz.</span><span class="sxs-lookup"><span data-stu-id="9f29c-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="9f29c-114">Comprueba si la dirección IP remota existe en la matriz.</span><span class="sxs-lookup"><span data-stu-id="9f29c-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="9f29c-115">Se permite el acceso si la matriz contiene la dirección IP.</span><span class="sxs-lookup"><span data-stu-id="9f29c-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="9f29c-116">De lo contrario, se devuelve un código de Estado HTTP 403 prohibido.</span><span class="sxs-lookup"><span data-stu-id="9f29c-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="9f29c-117">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f29c-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="9f29c-118">Dirección IP segura</span><span class="sxs-lookup"><span data-stu-id="9f29c-118">IP address safelist</span></span>

<span data-ttu-id="9f29c-119">En la aplicación de ejemplo, la dirección IP safelist es:</span><span class="sxs-lookup"><span data-stu-id="9f29c-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="9f29c-120">Definido por la `AdminSafeList` propiedad en el *appsettings.json* archivo.</span><span class="sxs-lookup"><span data-stu-id="9f29c-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="9f29c-121">Cadena delimitada por signos de punto y coma que puede contener direcciones del [Protocolo de Internet versión 4 (IPv4)](https://wikipedia.org/wiki/IPv4) y del [Protocolo de Internet versión 6 (IPv6)](https://wikipedia.org/wiki/IPv6) .</span><span class="sxs-lookup"><span data-stu-id="9f29c-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="9f29c-122">En el ejemplo anterior, se permiten las direcciones IPv4 de `127.0.0.1` y `192.168.1.5` y la dirección de bucle invertido IPv6 de `::1` (formato comprimido para `0:0:0:0:0:0:0:1` ).</span><span class="sxs-lookup"><span data-stu-id="9f29c-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="9f29c-123">Software intermedio</span><span class="sxs-lookup"><span data-stu-id="9f29c-123">Middleware</span></span>

<span data-ttu-id="9f29c-124">El `Startup.Configure` método agrega el `AdminSafeListMiddleware` tipo de middleware personalizado a la canalización de solicitudes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9f29c-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="9f29c-125">La clave segura se recupera con el proveedor de configuración de .NET Core y se pasa como un parámetro de constructor.</span><span class="sxs-lookup"><span data-stu-id="9f29c-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="9f29c-126">El middleware analiza la cadena en una matriz y busca la dirección IP remota en la matriz.</span><span class="sxs-lookup"><span data-stu-id="9f29c-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="9f29c-127">Si no se encuentra la dirección IP remota, el middleware devuelve el HTTP 403 prohibido.</span><span class="sxs-lookup"><span data-stu-id="9f29c-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="9f29c-128">Este proceso de validación se omite para las solicitudes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9f29c-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="9f29c-129">Filtro de acción</span><span class="sxs-lookup"><span data-stu-id="9f29c-129">Action filter</span></span>

<span data-ttu-id="9f29c-130">Si desea un control de acceso controlado por la misma para controladores MVC o métodos de acción específicos, use un filtro de acción.</span><span class="sxs-lookup"><span data-stu-id="9f29c-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="9f29c-131">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9f29c-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="9f29c-132">En `Startup.ConfigureServices` , agregue el filtro de acción a la colección de filtros de MVC.</span><span class="sxs-lookup"><span data-stu-id="9f29c-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="9f29c-133">En el ejemplo siguiente, `ClientIpCheckActionFilter` se agrega un filtro de acción.</span><span class="sxs-lookup"><span data-stu-id="9f29c-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="9f29c-134">Una instancia de safelist y una instancia del registrador de consola se pasan como parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="9f29c-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="9f29c-135">A continuación, el filtro de acción se puede aplicar a un controlador o un método de acción con el atributo [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :</span><span class="sxs-lookup"><span data-stu-id="9f29c-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="9f29c-136">En la aplicación de ejemplo, el filtro de acción se aplica al método de acción del controlador `Get` .</span><span class="sxs-lookup"><span data-stu-id="9f29c-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="9f29c-137">Al probar la aplicación mediante el envío de:</span><span class="sxs-lookup"><span data-stu-id="9f29c-137">When you test the app by sending:</span></span>

* <span data-ttu-id="9f29c-138">Una solicitud HTTP GET, el `[ServiceFilter]` atributo valida la dirección IP del cliente.</span><span class="sxs-lookup"><span data-stu-id="9f29c-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="9f29c-139">Si se permite el acceso al `Get` método de acción, el filtro de acción y el método de acción producen una variación de la siguiente salida de consola:</span><span class="sxs-lookup"><span data-stu-id="9f29c-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="9f29c-140">Un verbo de solicitud HTTP que no sea GET, el `AdminSafeListMiddleware` middleware valida la dirección IP del cliente.</span><span class="sxs-lookup"><span data-stu-id="9f29c-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="no-locrazor-pages-filter"></a><span data-ttu-id="9f29c-141">Razor Filtro de páginas</span><span class="sxs-lookup"><span data-stu-id="9f29c-141">Razor Pages filter</span></span>

<span data-ttu-id="9f29c-142">Si desea un control de acceso basado en la aplicación segura para una Razor aplicación de páginas, use un Razor filtro de páginas.</span><span class="sxs-lookup"><span data-stu-id="9f29c-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="9f29c-143">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9f29c-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="9f29c-144">En `Startup.ConfigureServices` , habilite el Razor filtro de páginas agregándolo a la colección de filtros de MVC.</span><span class="sxs-lookup"><span data-stu-id="9f29c-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="9f29c-145">En el ejemplo siguiente, `ClientIpCheckPageFilter` Razor se agrega un filtro de páginas.</span><span class="sxs-lookup"><span data-stu-id="9f29c-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="9f29c-146">Una instancia de safelist y una instancia del registrador de consola se pasan como parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="9f29c-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="9f29c-147">Cuando se solicita la página de *Índice* de la aplicación de ejemplo Razor , el Razor filtro de páginas valida la dirección IP del cliente.</span><span class="sxs-lookup"><span data-stu-id="9f29c-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="9f29c-148">El filtro genera una variación de la siguiente salida de la consola:</span><span class="sxs-lookup"><span data-stu-id="9f29c-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="9f29c-149">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9f29c-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="9f29c-150">Filtros de acciones</span><span class="sxs-lookup"><span data-stu-id="9f29c-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
