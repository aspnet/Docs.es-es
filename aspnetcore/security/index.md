---
title: Introducción a la seguridad de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre los conceptos básicos de autenticación, autorización y seguridad en ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/index
ms.openlocfilehash: 3c86c66bebe8a5ce1c195ebf931193e7e2a73fef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051634"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="bf167-103">Introducción a la seguridad de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf167-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="bf167-104">ASP.NET Core permite a los desarrolladores configurar y administrar con facilidad la seguridad de sus aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="bf167-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="bf167-105">ASP.NET Core contiene características para administrar la autenticación, autorización, protección de datos, cumplimiento de HTTPS, secretos de aplicación, prevención de XSRF/CSRF y administración de CORS.</span><span class="sxs-lookup"><span data-stu-id="bf167-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, XSRF/CSRF prevention, and CORS management.</span></span> <span data-ttu-id="bf167-106">Estas características de seguridad permiten compilar aplicaciones de ASP.NET Core sólidas y seguras.</span><span class="sxs-lookup"><span data-stu-id="bf167-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="bf167-107">Características de seguridad de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf167-107">ASP.NET Core security features</span></span>

<span data-ttu-id="bf167-108">ASP.NET Core proporciona muchas herramientas y bibliotecas para proteger las aplicaciones (por ejemplo, proveedores de identidades integrados), pero puede usar servicios de identidad de terceros como Facebook, Twitter y LinkedIn.</span><span class="sxs-lookup"><span data-stu-id="bf167-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="bf167-109">Con ASP.NET Core, puede administrar con facilidad los secretos de aplicación, que son una forma de almacenar y usar información confidencial sin tener que exponerla en el código.</span><span class="sxs-lookup"><span data-stu-id="bf167-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="bf167-110">Autenticación frente a Autorización</span><span class="sxs-lookup"><span data-stu-id="bf167-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="bf167-111">La autenticación es un proceso en el que un usuario proporciona credenciales que después se comparan con las almacenadas en un sistema operativo, base de datos, aplicación o recurso.</span><span class="sxs-lookup"><span data-stu-id="bf167-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="bf167-112">Si coinciden, los usuarios se autentican correctamente y, después, pueden realizar las acciones para las que están autorizados durante un proceso de autorización.</span><span class="sxs-lookup"><span data-stu-id="bf167-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="bf167-113">La autorización se refiere al proceso que determina las acciones que un usuario puede realizar.</span><span class="sxs-lookup"><span data-stu-id="bf167-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="bf167-114">La autenticación también se puede considerar una manera de entrar en un espacio (como un servidor, base de datos, aplicación o recurso) mientras que la autorización es qué acciones puede realizar el usuario en qué objetos de ese espacio (servidor, base de datos o aplicación).</span><span class="sxs-lookup"><span data-stu-id="bf167-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="bf167-115">Vulnerabilidades más comunes en software</span><span class="sxs-lookup"><span data-stu-id="bf167-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="bf167-116">ASP.NET Core y EF contienen características que ayudan a proteger las aplicaciones y evitar las infracciones de seguridad.</span><span class="sxs-lookup"><span data-stu-id="bf167-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="bf167-117">La siguiente lista de vínculos le lleva a documentación en la que se detallan técnicas para evitar las vulnerabilidades de seguridad más comunes en las aplicaciones web:</span><span class="sxs-lookup"><span data-stu-id="bf167-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="bf167-118">Ataque de scripts de sitios (XSS)</span><span class="sxs-lookup"><span data-stu-id="bf167-118">Cross-Site Scripting (XSS) attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="bf167-119">Ataques por inyección de código SQL</span><span class="sxs-lookup"><span data-stu-id="bf167-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="bf167-120">Ataques de falsificación de solicitud entre sitios (XSRF/CSRF)</span><span class="sxs-lookup"><span data-stu-id="bf167-120">Cross-Site Request Forgery (XSRF/CSRF) attacks</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="bf167-121">Ataques de redireccionamiento abierto</span><span class="sxs-lookup"><span data-stu-id="bf167-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="bf167-122">Hay más vulnerabilidades que debe tener en cuenta.</span><span class="sxs-lookup"><span data-stu-id="bf167-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="bf167-123">Para obtener más información, consulte otros artículos de la sección **Seguridad e :::no-loc(Identity):::** de la tabla de contenido.</span><span class="sxs-lookup"><span data-stu-id="bf167-123">For more information, see the other articles in the **Security and :::no-loc(Identity):::** section of the table of contents.</span></span>
