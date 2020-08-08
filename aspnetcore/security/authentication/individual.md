---
title: Artículos basados en proyectos de ASP.NET Core creados con cuentas de usuario individuales
author: rick-anderson
description: Descubra artículos basados en ASP.NET Core proyectos creados con cuentas de usuario individuales.
ms.author: riande
ms.date: 12/11/2019
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
uid: security/authentication/individual
ms.openlocfilehash: 6b72612c12d52cfc1736c141bdad95ace6c84546
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022295"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Artículos basados en proyectos de ASP.NET Core creados con cuentas de usuario individuales

ASP.NET Core Identity se incluye en las plantillas de proyecto de Visual Studio con la opción "cuentas de usuario individuales".

Las plantillas de autenticación están disponibles en CLI de .NET Core con `-au Individual` :

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Consulte [este problema de github para la](https://github.com/dotnet/AspNetCore/issues/5833) autenticación de API Web.

<a name="no"></a>

## <a name="no-authentication"></a>Sin autenticación

La autenticación se especifica en el CLI de .NET Core con la `-au` opción. En Visual Studio, el cuadro de diálogo **cambiar autenticación** está disponible para las nuevas aplicaciones Web. El valor predeterminado para las nuevas aplicaciones web en Visual Studio **no es autenticación**.

Proyectos creados sin autenticación:

* No contenga páginas web y la interfaz de usuario para iniciar sesión y cerrar sesión.
* No contenga código de autenticación.

<a name="win"></a>

## <a name="windows-authentication"></a>Autenticación de Windows

La autenticación de Windows se especifica para las nuevas aplicaciones web en el CLI de .NET Core con la `-au Windows` opción. En Visual Studio, el cuadro de diálogo **cambiar autenticación** proporciona las opciones de **autenticación de Windows** .

Si se selecciona autenticación de Windows, la aplicación se configura para usar el [módulo IIS de autenticación de Windows](xref:host-and-deploy/iis/modules). La autenticación de Windows está pensada para sitios web de la intranet.

## <a name="dotnet-new-webapp-authentication-options"></a>Opciones de autenticación de dotnet New webapp

En la tabla siguiente se muestran las opciones de autenticación disponibles para las nuevas aplicaciones web:

| Opción | Tipo de autenticación | Vínculo para más información |
 | ----------------- | ------------ | ---------- |
| Ninguno            |  Sin autenticación | | 
| Individual      |  Autenticación individual | <xref:security/authentication/identity>
| IndividualB2C   |  Autenticación individual hospedada en la nube con Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| SingleOrg       |  Autenticación organizativa para un solo inquilino | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| MultiOrg        |  Autenticación organizativa para varios inquilinos | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Autenticación de Windows | [Autenticación de Windows](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Opciones de autenticación nuevas de Visual Studio

En la tabla siguiente se muestran las opciones de autenticación disponibles al crear una nueva aplicación web con Visual Studio:

| Opción | Tipo de autenticación | Vínculo para más información |
 | ----------------- | ------------ | ---------- |
| Ninguno            |  Sin autenticación | | 
| Cuentas de usuario individuales/almacenar cuentas de usuario en la aplicación |  Autenticación individual | <xref:security/authentication/identity> |
| Cuentas de usuario individuales/conectarse a un almacén de usuario existente en la nube |  Autenticación individual hospedada en la nube con Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| Nube profesional o educativa/organización única  |  Autenticación organizativa para un solo inquilino | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Nube profesional o educativa/varias organizaciones |  Autenticación organizativa para varios inquilinos | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Autenticación de Windows | [Autenticación de Windows](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>Recursos adicionales

En los artículos siguientes se muestra cómo usar el código generado en ASP.NET Core plantillas que utilizan cuentas de usuario individuales:

* [Confirmación de las cuentas y recuperación de contraseñas en ASP.NET Core](xref:security/authentication/accconfirm)
* [Creación de una aplicación ASP.NET Core con datos de usuario protegidos por autorización](xref:security/authorization/secure-data)
