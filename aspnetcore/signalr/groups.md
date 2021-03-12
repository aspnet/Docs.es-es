---
title: Administrar usuarios y grupos en SignalR
author: bradygaster
description: Información general de ASP.NET Core la SignalR Administración de usuarios y grupos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
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
uid: signalr/groups
ms.openlocfilehash: 8f9e91f34386bb5b4cf79bee429a39ae27c03c5e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589223"
---
# <a name="manage-users-and-groups-in-signalr"></a>Administrar usuarios y grupos en SignalR

Por [Brennan Conroy](https://github.com/BrennanConroy)

SignalR permite que los mensajes se envíen a todas las conexiones asociadas a un usuario específico, así como a grupos de conexiones con nombre.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/groups/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Usuarios de SignalR

Un usuario único de SignalR puede tener varias conexiones a una aplicación. Por ejemplo, un usuario podría estar conectado a su escritorio, así como a su teléfono. Cada dispositivo tiene una SignalR conexión independiente, pero todos están asociados al mismo usuario. Si se envía un mensaje al usuario, todas las conexiones asociadas a ese usuario reciben el mensaje. La propiedad del centro puede tener acceso al identificador de usuario de una conexión `Context.UserIdentifier` .

De forma predeterminada, SignalR usa el objeto `ClaimTypes.NameIdentifier` de la clase `ClaimsPrincipal` asociada a la conexión como identificador de usuario. Para personalizar este comportamiento, consulte [uso de notificaciones para personalizar el control de identidades](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).

Envíe un mensaje a un usuario específico pasando el identificador de usuario a la `User` función en un método de concentrador, tal y como se muestra en el ejemplo siguiente:

> [!NOTE]
> El identificador de usuario distingue entre mayúsculas y minúsculas.

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>Grupos en SignalR

Un grupo es una colección de conexiones asociadas a un nombre. Los mensajes se pueden enviar a todas las conexiones de un grupo. Los grupos son el método recomendado para enviar a una conexión o varias conexiones, ya que la aplicación administra los grupos. Una conexión puede ser miembro de varios grupos. Los grupos son ideales para algo como una aplicación de chat, donde cada habitación puede representarse como un grupo. Las conexiones se agregan o se quitan de los grupos a través de los `AddToGroupAsync` `RemoveFromGroupAsync` métodos y.

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

La pertenencia a grupos no se conserva cuando se vuelve a conectar una conexión. La conexión debe volver a unirse al grupo cuando se restablece. No es posible contar los miembros de un grupo, puesto que esta información no está disponible si la aplicación se escala a varios servidores.

Para proteger el acceso a los recursos al usar grupos, use la funcionalidad de [autenticación y autorización](xref:signalr/authn-and-authz) en ASP.net Core. Si se agrega un usuario a un grupo solo cuando las credenciales son válidas para ese grupo, los mensajes enviados a ese grupo solo se dirigirán a los usuarios autorizados. Sin embargo, los grupos no son una característica de seguridad. Las notificaciones de autenticación tienen características que los grupos no, como la expiración y la revocación. Si se revoca el permiso de un usuario para acceder al grupo, la aplicación debe quitar explícitamente el usuario del grupo.

> [!NOTE]
> Los nombres de grupo distinguen mayúsculas de minúsculas.

## <a name="related-resources"></a>Recursos relacionados

* [Introducción](xref:tutorials/signalr)
* [Concentradores](xref:signalr/hubs)
* [Publicar en Azure](xref:signalr/publish-to-azure-web-app)
