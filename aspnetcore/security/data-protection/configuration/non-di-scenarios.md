---
title: Escenarios no compatibles con DI para la protección de datos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo admitir escenarios de protección de datos en los que no se puede o no se desea usar un servicio proporcionado por la inserción de dependencias.
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
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 5b27d21b046333d7a01f2e81f25d7bd34253cf36
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629176"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Escenarios no compatibles con DI para la protección de datos en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Normalmente, el sistema de protección de datos de ASP.NET Core se [agrega a un contenedor de servicio](xref:security/data-protection/consumer-apis/overview) y lo consumen los componentes dependientes mediante la inserción de dependencias (di). Sin embargo, hay casos en los que esto no es factible o desea, especialmente al importar el sistema en una aplicación existente.

Para admitir estos escenarios, el paquete [Microsoft. AspNetCore. bioprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) proporciona un tipo concreto, [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), que ofrece una manera sencilla de usar la protección de datos sin depender de di. El `DataProtectionProvider` tipo implementa [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). La construcción `DataProtectionProvider` solo requiere proporcionar una instancia de [DirectoryInfo](/dotnet/api/system.io.directoryinfo) para indicar dónde se deben almacenar las claves criptográficas del proveedor, tal como se muestra en el ejemplo de código siguiente:

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

De forma predeterminada, el `DataProtectionProvider` tipo concreto no cifra el material de clave sin formato antes de guardarlo en el sistema de archivos. Esto es para admitir escenarios en los que el desarrollador apunta a un recurso compartido de red y el sistema de protección de datos no puede deducir automáticamente un mecanismo de cifrado de claves en reposo adecuado.

Además, el `DataProtectionProvider` tipo concreto no [aísla las aplicaciones](xref:security/data-protection/configuration/overview#per-application-isolation) de forma predeterminada. Todas las aplicaciones que usan el mismo directorio de claves pueden compartir cargas, siempre y cuando sus [parámetros de propósito](xref:security/data-protection/consumer-apis/purpose-strings) coincidan.

El constructor [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) acepta una devolución de llamada de configuración opcional que se puede utilizar para ajustar los comportamientos del sistema. En el ejemplo siguiente se muestra cómo restaurar el aislamiento con una llamada explícita a [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname). En el ejemplo también se muestra cómo configurar el sistema para que cifre automáticamente las claves persistentes mediante la DPAPI de Windows. Si el directorio apunta a un recurso compartido UNC, puede que desee distribuir un certificado compartido en todas las máquinas relevantes y configurar el sistema para que use el cifrado basado en certificados con una llamada a [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> Las instancias del `DataProtectionProvider` tipo concreto son costosas de crear. Si una aplicación mantiene varias instancias de este tipo y todos usan el mismo directorio de almacenamiento de claves, el rendimiento de la aplicación podría degradarse. Si usa el `DataProtectionProvider` tipo, se recomienda que cree este tipo una vez y lo reutilice lo máximo posible. El `DataProtectionProvider` tipo y todas las instancias de [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) creadas a partir de ella son seguras para subprocesos para varios llamadores.
