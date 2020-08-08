---
title: Proveedores de autenticación OAuth externos
author: rick-anderson
description: Detectar proveedores de autenticación OAuth externos que funcionan con aplicaciones ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
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
uid: security/authentication/otherlogins
ms.openlocfilehash: 3de76b303c469341ba022fa3d33d3cd2a17581db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014911"
---
# <a name="external-oauth-authentication-providers"></a>Proveedores de autenticación OAuth externos

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd)y [Valeriy Novytskyy](https://github.com/01binary)

La lista siguiente incluye proveedores de autenticación OAuth externos comunes que funcionan con aplicaciones ASP.NET Core. Los paquetes NuGet de terceros, como los mantenidos por [ASPNET-entrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), se pueden usar para complementar los proveedores de autenticación implementados por el equipo de ASP.net Core.

* [LinkedIn](https://www.linkedin.com/developer/apps) ([instrucciones](https://developer.linkedin.com/docs/oauth2))

* [Instagram](https://www.instagram.com/developer/register/) ([instrucciones](https://www.instagram.com/developer/authentication/))

* [Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([instrucciones](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))

* [GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([instrucciones](https://developer.github.com/v3/oauth/))

* [Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([instrucciones](https://developer.yahoo.com/bbauth/user.html))

* [Tumblr](https://www.tumblr.com/oauth/apps) ([instrucciones](https://www.tumblr.com/docs/api/v2#auth))

* [Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([instrucciones](https://developers.pinterest.com/docs/api/overview/?))

* [Pocket](https://getpocket.com/developer/apps/new) ([instrucciones](https://getpocket.com/developer/docs/authentication))

* [Flickr](https://www.flickr.com/services/apps/create) ([instrucciones](https://www.flickr.com/services/api/auth.oauth.html))

* [Dribble](https://dribbble.com/signup) ([instrucciones](https://developer.dribbble.com/v1/oauth/))

* [Vimeo](https://vimeo.com/join) ([instrucciones](https://developer.vimeo.com/api/authentication))

* [Soundcloud](https://soundcloud.com/you/apps/new) ([instrucciones](https://developers.soundcloud.com/blog/we-love-oauth-2))

* [VK](https://vk.com/apps?act=manage) ([instrucciones](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
