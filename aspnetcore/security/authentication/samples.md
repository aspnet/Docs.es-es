---
title: Ejemplos de autenticación para ASP.NET Core
author: rick-anderson
description: Proporciona vínculos a los ejemplos de autenticación en el repositorio de ASP.NET Core.
ms.author: riande
ms.date: 02/21/2021
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
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110123"
---
# <a name="authentication-samples-for-aspnet-core"></a>Ejemplos de autenticación para ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

El [repositorio ASP.net Core](https://github.com/dotnet/aspnetcore) contiene los siguientes ejemplos de autenticación ( `main` bifurcación):

* [Transformación de notificaciones](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* [Cookie autenticación](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)
* [Respuesta de error de autorización personalizada](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [Proveedor de directivas personalizadas: IAuthorizationPolicyProvider](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [Opciones y esquemas de autenticación dinámica](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* [Notificaciones externas](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)
* [Seleccionar entre cookie y otro esquema de autenticación en función de la solicitud](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [Restringe el acceso a los archivos estáticos](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a>Obtención y ejecución de los ejemplos

En los vínculos de ejemplo que se proporcionan en este artículo se proporcionan ejemplos de la próxima versión de ASP.NET Core. Para obtener un ejemplo de la versión actual o de una versión anterior, realice los pasos siguientes:

* Seleccione la rama de versión del [repositorio de ASP.net Core](https://github.com/dotnet/aspnetcore)] ( https://github.com/dotnet/aspnetcore) . Por ejemplo, la `release/5.0` rama contiene los ejemplos de la versión ASP.NET Core 5,0.
* Clone o descargue el repositorio de ASP.NET Core.
* En el sistema local, Compruebe la instalación de la versión de [SDK de .net Core](https://dotnet.microsoft.com/download/dotnet-core) que coincida con el clon del repositorio de ASP.net Core.
* Navegue hasta un ejemplo en la `aspnetcore/src/Security/samples` carpeta y ejecute el ejemplo con el [ `dotnet run` comando](/dotnet/core/tools/dotnet-run).
