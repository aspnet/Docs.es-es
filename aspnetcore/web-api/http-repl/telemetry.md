---
title: Telemetría de HttpRepl
author: scottaddie
description: Obtenga información sobre la telemetría recopilada por el HttpRepl.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550613"
---
# <a name="httprepl-telemetry"></a>Telemetría de HttpRepl

[HttpRepl](xref:web-api/http-repl) incluye una característica de telemetría que recopila datos de uso. Es importante que el equipo de HttpRepl comprenda cómo se usa la herramienta para que se pueda mejorar.

## <a name="how-to-opt-out"></a>Cómo desactivar la característica

La característica de telemetría HttpRepl está habilitada de forma predeterminada. Para desactivar la característica de telemetría, establezca la variable de entorno `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` en `1` o `true`.

## <a name="disclosure"></a>Divulgación

El HttpRepl muestra texto similar al siguiente cuando se ejecuta por primera vez la herramienta. El texto puede variar ligeramente en función de la versión de la herramienta que se esté ejecutando. Esta experiencia de "primera vista" es la forma en que Microsoft le notifica sobre la recopilación de datos.

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

Para suprimir el texto de la experiencia "primera ejecución", establezca la `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` variable de entorno en `1` o `true` .

## <a name="data-points"></a>Puntos de datos

La característica de telemetría no:

* Recopile datos personales, como nombres de usuario, direcciones de correo electrónico o direcciones URL.
* Examine las solicitudes o respuestas HTTP.

Los datos se envían de forma segura a los servidores de Microsoft y se conservan con acceso restringido.

La protección de su privacidad es importante para nosotros. Si sospecha que la característica de telemetría está recopilando información confidencial o los datos se están administrando de forma no segura o inadecuada, realice una de las siguientes acciones:

* Archivo de un problema en el repositorio [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .
* Envíe un correo electrónico a [dotnet@microsoft.com](mailto:dotnet@microsoft.com) para su investigación.

La característica de telemetría recopila los datos siguientes.

| Versiones del SDK de .NET | data |
|--------------|------|
| >= 5,0        | Marca de tiempo de la invocación. |
| >= 5,0        | Dirección IP de tres octetos que se usa para determinar la ubicación geográfica. |
| >= 5,0        | Sistema operativo y versión. |
| >= 5,0        | IDENTIFICADOR en tiempo de ejecución (RID) en el que se ejecuta la herramienta. |
| >= 5,0        | Si la herramienta se ejecuta en un contenedor. |
| >= 5,0        | Dirección de Access Control de medios (MAC) con hash: identificador único y hash criptográficamente (SHA256) de un equipo. |
| >= 5,0        | Versión de kernel. |
| >= 5,0        | Versión de HttpRepl. |
| >= 5,0        | Si la herramienta se inició con `help` los `run` argumentos, o `connect` . No se recopilan los valores de argumento reales. |
| >= 5,0        | Comando invocado (por ejemplo, `get` ) y si se ha realizado correctamente. |
| >= 5,0        | Para el `connect` comando, si `root` `base` `openapi` se proporcionaron los argumentos, o. No se recopilan los valores de argumento reales. |
| >= 5,0        | Para el `pref` comando, si `get` `set` se emitió un o y a qué preferencia se obtuvo acceso. Si no se trata de una preferencia conocida, se aplica un algoritmo hash al nombre. El valor no se recopila. |
| >= 5,0        | En el caso del `set header` comando, se establece el nombre del encabezado. Si no es un encabezado conocido, se aplica un algoritmo hash al nombre. El valor no se recopila. |
| >= 5,0        | Para el `connect` comando, si se usó un caso especial para `dotnet new webapi` y, si se omitió a través de la preferencia. |
| >= 5,0        | Para todos los comandos HTTP (por ejemplo, GET, POST, PUT), si se especificó cada una de las opciones. No se recopilan los valores de las opciones. |

## <a name="additional-resources"></a>Recursos adicionales

* [Telemetría del SDK de .NET Core](/dotnet/core/tools/telemetry)
* [CLI de .NET Core datos de telemetría](https://dotnet.microsoft.com/platform/telemetry)
