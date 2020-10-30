---
title: Versión de compatibilidad para ASP.NET Core MVC
author: rick-anderson
description: Descubra cómo la clase Startup de ASP.NET Core configura los servicios y la canalización de solicitudes de la aplicación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
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
uid: mvc/compatibility-version
ms.openlocfilehash: 9123bd70dfee1578912f682faf0520735fd55776
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051296"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>Versión de compatibilidad para ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

El método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> es una operación inefectiva para las aplicaciones ASP.NET Core 3.0. Es decir, llamar a `SetCompatibilityVersion` con cualquier valor de <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> no tiene ningún impacto en la aplicación.

* La siguiente versión secundaria ASP.NET Core puede proporcionar un nuevo valor de `CompatibilityVersion`.
* Los valores `Version_2_0` a `Version_2_2` de `CompatibilityVersion` están marcados como `[Obsolete(...)]`.
* Consulte [Cambios importantes en la API en Antiforgery, CORS, Diagnostics, Mvc y Routing](https://github.com/aspnet/Announcements/issues/387). Esta lista incluye cambios importantes para los modificadores de compatibilidad.

Para ver cómo funciona `SetCompatibilityVersion` con las aplicaciones ASP.NET Core 2.x, seleccione la [versión ASP.NET Core 2.2 de este artículo](?view=aspnetcore-2.2).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

El método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite a una aplicación ASP.NET Core 2.x participar o no en los cambios de comportamiento importantes incorporados en ASP.NET Core MVC 2.1 o 2.2. Estos cambios de comportamiento importantes suelen estar relacionados con cómo se comporta el subsistema de MVC y cómo el tiempo de ejecución llama al **código** . Si la aplicación participa, obtendrá el comportamiento más reciente y a largo plazo de ASP.NET Core.

El siguiente código establece el modo de compatibilidad en ASP.NET Core 2.2:

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

Le recomendamos que pruebe la aplicación con la versión más reciente (`CompatibilityVersion.Latest`). Prevemos que la mayoría de las aplicaciones no tendrán cambios de comportamiento importantes usando la versión más reciente.

Las aplicaciones que llaman a `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` están protegidas frente a los cambios de comportamiento importantes incorporados en ASP.NET Core 2.1/2.2 MVC. Esta protección:

* No es aplicable a todos los cambios de 2.1 y versiones posteriores, sino que tiene como destino los cambios importantes de comportamiento en tiempo de ejecución de ASP.NET Core en el subsistema de MVC.
* No se extiende a ASP.NET Core 3.0.

La compatibilidad predeterminada de las aplicaciones ASP.NET Core 2.1 y 2.2 que **no** llaman a `SetCompatibilityVersion` es la compatibilidad 2.0. Es decir, no llamar a `SetCompatibilityVersion` es igual que llamar a `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.

El siguiente código establece el modo de compatibilidad en ASP.NET Core 2.2, salvo en los siguientes comportamientos:

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

En el caso de las aplicaciones que encuentran cambios de comportamiento importantes, si se usan los modificadores de compatibilidad adecuados:

* Se podrá usar la versión más reciente y descartar cambios de comportamiento importantes específicos.
* Se dispondrá de tiempo para actualizar la aplicación para que funcione con los cambios más recientes.

En la documentación de <xref:Microsoft.AspNetCore.Mvc.MvcOptions> se incluye una completa explicación de los cambios y por qué son una mejora para la mayoría de los usuarios.

Con ASP.NET Core 3.0, los comportamientos anteriores admitidos por los modificadores de compatibilidad se han quitado. Estamos convencidos de que estos son cambios positivos que beneficiarán a prácticamente todos los usuarios. Al introducir estos cambios en 2.1 y 2.2, la mayoría de las aplicaciones pueden beneficiarse, mientras que otras tienen tiempo para actualizarse.
::: moniker-end