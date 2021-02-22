---
title: Configuración del recortador de ASP.NET Core Blazor
author: guardrex
description: Aprenda a controlar el enlazador de lenguaje intermedio (IL) (recortador) al compilar una aplicación Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975225"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a>Configuración del recortador de ASP.NET Core Blazor

Blazor WebAssembly realiza un recorte de [lenguaje intermedio (IL)](/dotnet/standard/managed-code#intermediate-language--execution) para reducir el tamaño de la salida publicada. De forma predeterminada, el recorte se produce cuando se publica una aplicación.

El recorte podría tener efectos perjudiciales. En las aplicaciones que usan reflexión, el recortador a menudo no puede determinar los tipos necesarios para la reflexión en tiempo de ejecución. Para recortar las aplicaciones que usan reflexión, se debe informar al recortador sobre los tipos necesarios para la reflexión en el código de la aplicación y en los paquetes o marcos de los que esta depende. El recortador tampoco puede reaccionar a un comportamiento dinámico de la aplicación en tiempo de ejecución. Para asegurarse de que la aplicación recortada funciona correctamente una vez implementada, pruebe la salida publicada con frecuencia durante el desarrollo.

Para configurar el recortador, consulte el artículo [Opciones de recorte](/dotnet/core/deploying/trimming-options) en la documentación sobre aspectos básicos de .NET, que incluye instrucciones sobre los temas siguientes:

* Deshabilitación del recorte para toda la aplicación con la propiedad `<PublishTrimmed>` en el archivo de proyecto
* Control de la agresividad con la que el recortador descarta el IL sin usar
* Interrupción del recorte de ensamblados específicos por parte del recortador
* Ensamblados "raíz" para recortar
* Obtención de advertencias para los tipos reflejados mediante el establecimiento de la propiedad `<SuppressTrimAnalysisWarnings>` en `false` en el archivo de proyecto
* Control del recorte de símbolos y compatibilidad con el depurador
* Establecimiento de características del recortador para recortar características de la biblioteca de marcos.

## <a name="additional-resources"></a>Recursos adicionales

* [Recorte de implementaciones autocontenidas y ejecutables](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
