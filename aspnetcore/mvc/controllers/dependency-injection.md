---
title: Inserción de dependencias en controladores en ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo los controladores de ASP.NET Core MVC solicitan sus dependencias explícitamente a través de sus constructores por medio de la inserción de dependencias en ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 1282cd984584be423fba755e64e5d2f1afd2af89
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060617"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>Inserción de dependencias en controladores en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) y [Steve Smith](https://github.com/ardalis)

Los controladores de ASP.NET Core MVC solicitan las dependencias de forma explícita a través de constructores. ASP.NET Core tiene compatibilidad integrada con la [inserción de dependencias](xref:fundamentals/dependency-injection). La inserción de dependencias facilita las pruebas y el mantenimiento de las aplicaciones.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Inserción de constructores

Los servicios se agregan como un parámetro de constructor y el runtime resuelve el servicio desde el contenedor de servicios. Normalmente, los servicios se definen mediante interfaces. Por ejemplo, considere una aplicación que requiere la hora actual. En la interfaz siguiente se expone el servicio `IDateTime`:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

En el código siguiente se implementa la interfaz `IDateTime`:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Agregue el servicio al contenedor de servicios:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Para más información sobre <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, vea [Duraciones de servicio de DI](xref:fundamentals/dependency-injection#service-lifetimes).

En el código siguiente se muestra un saludo al usuario según la hora del día:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Ejecute la aplicación y se mostrará un mensaje en función de la hora.

## <a name="action-injection-with-fromservices"></a>Inserción de acción con FromServices

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> permite la inserción de un servicio directamente en un método de acción sin usar la inserción de constructores:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Acceso a la configuración desde un controlador

El acceso a la configuración de la aplicación o a los valores de configuración desde un controlador es un patrón habitual. El *patrón de opciones* que se describe en <xref:fundamentals/configuration/options> es el enfoque preferido para administrar la configuración. Por lo general, <xref:Microsoft.Extensions.Configuration.IConfiguration> no se inserta directamente en un controlador.

Cree una clase que represente las opciones. Por ejemplo:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Agregue la clase de configuración a la colección de servicios:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Configure la aplicación para leer la configuración de un archivo con formato JSON:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

En el código siguiente se solicita la configuración `IOptions<SampleWebSettings>` del contenedor de servicios y se usa en el método `Index`:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionales

* Vea <xref:mvc/controllers/testing> para obtener información sobre cómo solicitar explícitamente dependencias en controladores para facilitar la comprobación del código.

* [Reemplazo del contenedor de inserción de dependencias predeterminado con una implementación de terceros](xref:fundamentals/dependency-injection#default-service-container-replacement).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) y [Steve Smith](https://github.com/ardalis)

Los controladores de ASP.NET Core MVC solicitan las dependencias de forma explícita a través de constructores. ASP.NET Core tiene compatibilidad integrada con la [inserción de dependencias](xref:fundamentals/dependency-injection). La inserción de dependencias facilita las pruebas y el mantenimiento de las aplicaciones.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Inserción de constructores

Los servicios se agregan como un parámetro de constructor y el runtime resuelve el servicio desde el contenedor de servicios. Normalmente, los servicios se definen mediante interfaces. Por ejemplo, considere una aplicación que requiere la hora actual. En la interfaz siguiente se expone el servicio `IDateTime`:

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

En el código siguiente se implementa la interfaz `IDateTime`:

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Agregue el servicio al contenedor de servicios:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Para más información sobre <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, vea [Duraciones de servicio de DI](xref:fundamentals/dependency-injection#service-lifetimes).

En el código siguiente se muestra un saludo al usuario según la hora del día:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Ejecute la aplicación y se mostrará un mensaje en función de la hora.

## <a name="action-injection-with-fromservices"></a>Inserción de acción con FromServices

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> permite la inserción de un servicio directamente en un método de acción sin usar la inserción de constructores:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Acceso a la configuración desde un controlador

El acceso a la configuración de la aplicación o a los valores de configuración desde un controlador es un patrón habitual. El *patrón de opciones* que se describe en <xref:fundamentals/configuration/options> es el enfoque preferido para administrar la configuración. Por lo general, <xref:Microsoft.Extensions.Configuration.IConfiguration> no se inserta directamente en un controlador.

Cree una clase que represente las opciones. Por ejemplo:

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Agregue la clase de configuración a la colección de servicios:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Configure la aplicación para leer la configuración de un archivo con formato JSON:

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

En el código siguiente se solicita la configuración `IOptions<SampleWebSettings>` del contenedor de servicios y se usa en el método `Index`:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionales

* Vea <xref:mvc/controllers/testing> para obtener información sobre cómo solicitar explícitamente dependencias en controladores para facilitar la comprobación del código.

* [Reemplazo del contenedor de inserción de dependencias predeterminado con una implementación de terceros](xref:fundamentals/dependency-injection#default-service-container-replacement).

::: moniker-end