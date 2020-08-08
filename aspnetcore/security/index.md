---
title: Introducción a la seguridad de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre los conceptos básicos de autenticación, autorización y seguridad en ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/index
ms.openlocfilehash: ead5863f0656e182d395fc1e0deaf63ff66034cb
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021372"
---
# <a name="overview-of-aspnet-core-security"></a>Introducción a la seguridad de ASP.NET Core

ASP.NET Core permite a los desarrolladores configurar y administrar con facilidad la seguridad de sus aplicaciones. ASP.NET Core contiene características para administrar la autenticación, autorización, protección de datos, cumplimiento de HTTPS, secretos de aplicación, protección contra falsificación de solicitudes y administración de CORS. Estas características de seguridad permiten compilar aplicaciones de ASP.NET Core sólidas y seguras.

## <a name="aspnet-core-security-features"></a>Características de seguridad de ASP.NET Core

ASP.NET Core proporciona muchas herramientas y bibliotecas para proteger las aplicaciones (por ejemplo, proveedores de identidades integrados), pero puede usar servicios de identidad de terceros como Facebook, Twitter y LinkedIn. Con ASP.NET Core, puede administrar con facilidad los secretos de aplicación, que son una forma de almacenar y usar información confidencial sin tener que exponerla en el código.

## <a name="authentication-vs-authorization"></a>Autenticación frente a Autorización

La autenticación es un proceso en el que un usuario proporciona credenciales que después se comparan con las almacenadas en un sistema operativo, base de datos, aplicación o recurso. Si coinciden, los usuarios se autentican correctamente y, después, pueden realizar las acciones para las que están autorizados durante un proceso de autorización. La autorización se refiere al proceso que determina las acciones que un usuario puede realizar.

La autenticación también se puede considerar una manera de entrar en un espacio (como un servidor, base de datos, aplicación o recurso) mientras que la autorización es qué acciones puede realizar el usuario en qué objetos de ese espacio (servidor, base de datos o aplicación).

## <a name="common-vulnerabilities-in-software"></a>Vulnerabilidades más comunes en software

ASP.NET Core y EF contienen características que ayudan a proteger las aplicaciones y evitar las infracciones de seguridad. La siguiente lista de vínculos le lleva a documentación en la que se detallan técnicas para evitar las vulnerabilidades de seguridad más comunes en las aplicaciones web:

* [Ataques de scripting entre sitios](xref:security/cross-site-scripting)
* [Ataques por inyección de código SQL](/ef/core/querying/raw-sql)
* [Falsificación de solicitudes entre sitios. (CSRF)](xref:security/anti-request-forgery)
* [Ataques de redireccionamiento abierto](xref:security/preventing-open-redirects)

Hay más vulnerabilidades que debe tener en cuenta. Para obtener más información, consulte otros artículos de la sección **Seguridad e Identity** de la tabla de contenido.
