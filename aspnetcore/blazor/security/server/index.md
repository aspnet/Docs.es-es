---
title: Protección de aplicaciones de ASP.NET Core Blazor Server
author: guardrex
description: Aprenda a proteger las aplicaciones de Blazor Server igual que las aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 147ebbeb84e1755307d627ef428d92d1b0248c74
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394868"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Protección de aplicaciones de ASP.NET Core Blazor Server

La seguridad de las aplicaciones de Blazor Server se configura de la misma forma que la de las aplicaciones de ASP.NET Core. Para obtener más información, vea los artículos en <xref:security/index>. Los temas de esta información general se aplican específicamente a Blazor Server.

## <a name="blazor-server-project-template"></a>Plantilla de proyecto de Blazor Server

Se puede configurar la autenticación de la [plantilla de proyecto de Blazor Server](xref:blazor/project-structure) cuando se crea el proyecto.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Siga las instrucciones de Visual Studio en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación.

Después de elegir la plantilla de aplicación de **Blazor Server** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.

Se abre un cuadro de diálogo para ofrecer el mismo conjunto de mecanismos de autenticación disponibles para otros proyectos ASP.NET Core:

* **Sin autenticación**
* **Cuentas de usuario individuales**: las cuentas de usuario se pueden almacenar:
  * Dentro de la aplicación mediante el sistema de [Identity](xref:security/authentication/identity) de ASP.NET Core.
  * Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c)
* **Cuentas profesionales o educativas**
* **Autenticación de Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Siga las instrucciones de Visual Studio Code en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).

| Mecanismo de autenticación | Descripción |
| ------------------------ | ----------- |
| `None` (valor predeterminado)         | Sin autenticación |
| `Individual`             | Usuarios almacenados en la aplicación con ASP.NET Core Identity |
| `IndividualB2C`          | Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticación organizativa para un solo inquilino |
| `MultiOrg`               | Autenticación organizativa para varios inquilinos |
| `Windows`                | Autenticación de Windows |

Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:

* Cree una carpeta para el proyecto.
* Asigne el nombre al proyecto.

Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Siga las instrucciones de Visual Studio para Mac en <xref:blazor/tooling>.

1. En el paso **Configure your new Blazor Server App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).

1. La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core Identity.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

Cree un nuevo proyecto de Blazor Server con un mecanismo de autenticación que use el siguiente comando en un shell de comandos:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).

| Mecanismo de autenticación | Descripción |
| ------------------------ | ----------- |
| `None` (valor predeterminado)         | Sin autenticación |
| `Individual`             | Usuarios almacenados en la aplicación con ASP.NET Core Identity |
| `IndividualB2C`          | Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticación organizativa para un solo inquilino |
| `MultiOrg`               | Autenticación organizativa para varios inquilinos |
| `Windows`                | Autenticación de Windows |

Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:

* Cree una carpeta para el proyecto.
* Asigne el nombre al proyecto.

Para obtener más información:

* Consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en la guía de .NET Core.
* Ejecute el comando de ayuda para la plantilla de Blazor Server (`blazorserver`) en un shell de comandos:

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a>Scaffolding para Identity

Scaffolding para Identity en un proyecto de Blazor Server:

* [Sin autorización existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)
* [Con autorización](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)

## <a name="additional-claims-and-tokens-from-external-providers"></a>Notificaciones y tokens adicionales de proveedores externos

Para almacenar notificaciones adicionales de proveedores externos, consulte <xref:security/authentication/social/additional-claims>.

## <a name="azure-app-service-on-linux-with-identity-server"></a>Azure App Service en Linux con Identity Server

Al realizar una implementación en Azure App Service en Linux con Identity Server, especifique el emisor de forma explícita. Para obtener más información, vea <xref:security/authentication/identity/spa#azure-app-service-on-linux>.

## <a name="additional-resources"></a>Recursos adicionales

* [Inicio rápido: Adición del inicio de sesión con Microsoft en una aplicación web de ASP.NET Core](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [Inicio rápido: Protección de una API web de ASP.NET Core con la plataforma de identidad de Microsoft](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <xref:host-and-deploy/proxy-load-balancer>; incluye instrucciones relativas a lo siguiente:
  * Uso de middleware de encabezados reenviados para retener la información de esquemas HTTPS en servidores proxy y redes internas.
  * Escenarios y casos de uso adicionales, incluidos la configuración manual de esquemas, los cambios en las rutas de las solicitudes para corregir el enrutamiento de las solicitudes y el reenvío de esquemas de solicitudes para Linux y proxies inversos que no sean de tipo IIS.
