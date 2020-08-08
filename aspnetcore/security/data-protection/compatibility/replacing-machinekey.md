---
title: Reemplace ASP.NET machineKey en ASP.NET Core
author: rick-anderson
description: Descubra cómo reemplazar machineKey en ASP.NET para permitir el uso de un sistema de protección de datos nuevo y más seguro.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 6b98244f9f288101a971257c9dab50d8895b8881
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018213"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>Reemplace ASP.NET machineKey en ASP.NET Core

<a name="compatibility-replacing-machinekey"></a>

La implementación del `<machineKey>` elemento en ASP.net [es reemplazable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). Esto permite que la mayoría de las llamadas a ASP.NET rutinas criptográficas se enruten a través de un mecanismo de protección de datos de reemplazo, incluido el nuevo sistema de protección de datos.

## <a name="package-installation"></a>Instalación del paquete

> [!NOTE]
> El nuevo sistema de protección de datos solo se puede instalar en una aplicación existente de ASP.NET que tenga como destino .NET 4.5.1 o posterior. Se producirá un error en la instalación si la aplicación tiene como destino .NET 4,5 o versiones anteriores.

Para instalar el nuevo sistema de protección de datos en un proyecto existente de ASP.NET 4.5.1 +, instale el paquete Microsoft.AspNetCore.DataProtection.SystemWeb. Esto creará una instancia del sistema de protección de datos con los valores de [configuración predeterminados](xref:security/data-protection/configuration/default-settings) .

Al instalar el paquete, inserta una línea en *Web.config* que indica a ASP.net que lo use para [la mayoría de las operaciones de cifrado](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), como la autenticación de formularios, el estado de vista y las llamadas a MachineKey. Protect. La línea que se inserta es como se indica a continuación.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> Puede saber si el nuevo sistema de protección de datos está activo mediante la inspección de campos como `__VIEWSTATE` , que deben comenzar por "CfDJ8" como se muestra en el ejemplo siguiente. "CfDJ8" es la representación en base64 del encabezado Magic "09 F0 C9 F0" que identifica una carga protegida por el sistema de protección de datos.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>Configuración de paquetes

Se crea una instancia del sistema de protección de datos con una configuración de instalación cero predeterminada. Sin embargo, puesto que, de forma predeterminada, las claves se conservan en el sistema de archivos local, esto no funcionará para las aplicaciones que se implementan en una granja. Para resolverlo, puede crear un tipo que subclases DataProtectionStartup e invalide su método ConfigureServices para proporcionar la configuración.

A continuación se muestra un ejemplo de un tipo de inicio de protección de datos personalizado que configuró las claves en las que se conservan y cómo se cifran en reposo. También invalida la Directiva de aislamiento de aplicación predeterminada proporcionando su propio nombre de aplicación.

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> También puede usar `<machineKey applicationName="my-app" ... />` en lugar de una llamada explícita a SetApplicationName. Este es un mecanismo de comodidad para evitar obligar al desarrollador a crear un tipo derivado de DataProtectionStartup si todos querían configurar el nombre de la aplicación.

Para habilitar esta configuración personalizada, vuelva a Web.config y busque el `<appSettings>` elemento que la instalación del paquete ha agregado al archivo de configuración. Tendrá un aspecto similar al siguiente marcado:

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

Rellene el valor en blanco con el nombre calificado con el ensamblado del tipo derivado de DataProtectionStartup que acaba de crear. Si el nombre de la aplicación es DataProtectionDemo, tendrá el siguiente aspecto.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

El sistema de protección de datos recién configurado ya está listo para su uso dentro de la aplicación.
