---
title: Administrar usuarios y grupos enSignalR
author: bradygaster
description: Información general de ASP.NET Core la SignalR Administración de usuarios y grupos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
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
uid: signalr/groups
ms.openlocfilehash: 3a1182b793c7d05a11f0b5f16f4d406a9063f023
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021034"
---
# <a name="manage-users-and-groups-in-no-locsignalr"></a><span data-ttu-id="e9ae4-103">Administrar usuarios y grupos enSignalR</span><span class="sxs-lookup"><span data-stu-id="e9ae4-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="e9ae4-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="e9ae4-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="e9ae4-105">SignalRpermite que los mensajes se envíen a todas las conexiones asociadas a un usuario específico, así como a grupos de conexiones con nombre.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="e9ae4-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="e9ae4-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-no-locsignalr"></a><span data-ttu-id="e9ae4-107">Usuarios deSignalR</span><span class="sxs-lookup"><span data-stu-id="e9ae4-107">Users in SignalR</span></span>

<span data-ttu-id="e9ae4-108">Un usuario único de SignalR puede tener varias conexiones a una aplicación.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-108">A single user in SignalR can have multiple connections to an app.</span></span> <span data-ttu-id="e9ae4-109">Por ejemplo, un usuario podría estar conectado a su escritorio, así como a su teléfono.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-109">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="e9ae4-110">Cada dispositivo tiene una SignalR conexión independiente, pero todos están asociados al mismo usuario.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-110">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="e9ae4-111">Si se envía un mensaje al usuario, todas las conexiones asociadas a ese usuario reciben el mensaje.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-111">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="e9ae4-112">La propiedad del centro puede tener acceso al identificador de usuario de una conexión `Context.UserIdentifier` .</span><span class="sxs-lookup"><span data-stu-id="e9ae4-112">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="e9ae4-113">De forma predeterminada, SignalR usa el objeto `ClaimTypes.NameIdentifier` de la clase `ClaimsPrincipal` asociada a la conexión como identificador de usuario.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-113">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="e9ae4-114">Para personalizar este comportamiento, consulte [uso de notificaciones para personalizar el control de identidades](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span><span class="sxs-lookup"><span data-stu-id="e9ae4-114">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="e9ae4-115">Envíe un mensaje a un usuario específico pasando el identificador de usuario a la `User` función en un método de concentrador, tal y como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e9ae4-115">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="e9ae4-116">El identificador de usuario distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-no-locsignalr"></a><span data-ttu-id="e9ae4-117">Grupos enSignalR</span><span class="sxs-lookup"><span data-stu-id="e9ae4-117">Groups in SignalR</span></span>

<span data-ttu-id="e9ae4-118">Un grupo es una colección de conexiones asociadas a un nombre.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="e9ae4-119">Los mensajes se pueden enviar a todas las conexiones de un grupo.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="e9ae4-120">Los grupos son el método recomendado para enviar a una conexión o varias conexiones, ya que la aplicación administra los grupos.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="e9ae4-121">Una conexión puede ser miembro de varios grupos.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="e9ae4-122">Los grupos son ideales para algo como una aplicación de chat, donde cada habitación puede representarse como un grupo.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-122">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="e9ae4-123">Las conexiones se agregan o se quitan de los grupos a través de los `AddToGroupAsync` `RemoveFromGroupAsync` métodos y.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-123">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="e9ae4-124">La pertenencia a grupos no se conserva cuando se vuelve a conectar una conexión.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="e9ae4-125">La conexión debe volver a unirse al grupo cuando se restablece.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="e9ae4-126">No es posible contar los miembros de un grupo, puesto que esta información no está disponible si la aplicación se escala a varios servidores.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="e9ae4-127">Para proteger el acceso a los recursos al usar grupos, use la funcionalidad de [autenticación y autorización](xref:signalr/authn-and-authz) en ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="e9ae4-128">Si se agrega un usuario a un grupo solo cuando las credenciales son válidas para ese grupo, los mensajes enviados a ese grupo solo se dirigirán a los usuarios autorizados.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-128">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="e9ae4-129">Sin embargo, los grupos no son una característica de seguridad.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-129">However, groups are not a security feature.</span></span> <span data-ttu-id="e9ae4-130">Las notificaciones de autenticación tienen características que los grupos no, como la expiración y la revocación.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="e9ae4-131">Si se revoca el permiso de un usuario para acceder al grupo, la aplicación debe quitar explícitamente el usuario del grupo.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-131">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="e9ae4-132">Los nombres de grupo distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="e9ae4-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="e9ae4-133">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="e9ae4-133">Related resources</span></span>

* [<span data-ttu-id="e9ae4-134">Introducción</span><span class="sxs-lookup"><span data-stu-id="e9ae4-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e9ae4-135">Concentradores</span><span class="sxs-lookup"><span data-stu-id="e9ae4-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e9ae4-136">Publicación en Azure</span><span class="sxs-lookup"><span data-stu-id="e9ae4-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
