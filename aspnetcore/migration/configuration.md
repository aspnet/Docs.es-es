---
title: Migrar la configuración a ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo migrar la configuración de un proyecto de ASP.NET MVC a un proyecto de MVC de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: migration/configuration
ms.openlocfilehash: 8cbb174539234039a05306ec5ab89026bc90da23
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631100"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Migrar la configuración a ASP.NET Core

Por [Steve Smith](https://ardalis.com/) y [Scott Addie](https://scottaddie.com)

En el artículo anterior, comenzamos a [migrar un proyecto de ASP.NET MVC a ASP.net Core MVC](xref:migration/mvc). En este artículo se migra la configuración.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Configuración

ASP.NET Core ya no usa los archivos *global. asax* y *web.config* que usaban las versiones anteriores de ASP.net. En las versiones anteriores de ASP.NET, la lógica de inicio de la aplicación se colocó en un `Application_StartUp` método dentro de *global. asax*. Más adelante, en ASP.NET MVC, se incluyó un archivo *Startup.CS* en la raíz del proyecto; y se llamó al iniciar la aplicación. ASP.NET Core ha adoptado este enfoque completamente colocando toda la lógica de inicio en el archivo *Startup.CS* .

El archivo *web.config* también se ha reemplazado en ASP.net Core. La configuración se puede configurar ahora, como parte del procedimiento de inicio de la aplicación que se describe en *Startup.CS*. La configuración puede seguir usando archivos XML, pero normalmente ASP.NET Core proyectos colocarán los valores de configuración en un archivo con formato JSON, como *appsettings.jsen*. El sistema de configuración de ASP.NET Core también puede acceder fácilmente a las variables de entorno, lo que puede proporcionar una [ubicación más segura y robusta](xref:security/app-secrets) para los valores específicos del entorno. Esto es especialmente cierto para secretos como cadenas de conexión y claves de API que no se deben proteger en el control de código fuente. Consulte [configuración](xref:fundamentals/configuration/index) para obtener más información sobre la configuración en ASP.net Core.

En este artículo, vamos a empezar con el proyecto ASP.NET Core parcialmente migrado del [artículo anterior](xref:migration/mvc). Para configurar la configuración, agregue el siguiente constructor y la propiedad al archivo *Startup.CS* que se encuentra en la raíz del proyecto:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Tenga en cuenta que, en este momento, el archivo *Startup.CS* no se compilará, ya que todavía es necesario agregar la siguiente `using` instrucción:

```csharp
using Microsoft.Extensions.Configuration;
```

Agregue un *appsettings.jsen* el archivo a la raíz del proyecto mediante la plantilla de elemento adecuada:

![Agregar JSON AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Migrar las opciones de configuración desde web.config

Nuestro proyecto ASP.NET MVC incluía la cadena de conexión de base de datos necesaria en *web.config*, en el `<connectionStrings>` elemento. En nuestro proyecto de ASP.NET Core, vamos a almacenar esta información en el *appsettings.jsen* el archivo. Abra *appsettings.jsen*y tenga en cuenta que ya incluye lo siguiente:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

En la línea resaltada descrita anteriormente, cambie el nombre de la base de datos de **_CHANGE_ME** por el nombre de la base de datos.

## <a name="summary"></a>Resumen

ASP.NET Core coloca toda la lógica de inicio de la aplicación en un único archivo, en el que se pueden definir y configurar los servicios y dependencias necesarios. Reemplaza el archivo de *web.config* con una característica de configuración flexible que puede aprovechar una variedad de formatos de archivo, como JSON, y las variables de entorno.
