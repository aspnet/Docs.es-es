---
title: Personalizar el comportamiento de AuthorizationMiddleware
author: pranavkm
ms.author: prkrishn
description: En este artículo se explica cómo personalizar el control de resultados de AuthorizationMiddleware.
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156773"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="0fcaf-103">Personalizar el comportamiento de AuthorizationMiddleware</span><span class="sxs-lookup"><span data-stu-id="0fcaf-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="0fcaf-104">Las aplicaciones pueden registrar un `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` para personalizar la manera en que el middleware controla los resultados de la autorización.</span><span class="sxs-lookup"><span data-stu-id="0fcaf-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="0fcaf-105">Las aplicaciones pueden usar el middleware personalizado para:</span><span class="sxs-lookup"><span data-stu-id="0fcaf-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="0fcaf-106">Devolver respuestas personalizadas.</span><span class="sxs-lookup"><span data-stu-id="0fcaf-106">Return customized responses.</span></span>
* <span data-ttu-id="0fcaf-107">Mejorar el desafío predeterminado o prohibir las respuestas.</span><span class="sxs-lookup"><span data-stu-id="0fcaf-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="0fcaf-108">En el código siguiente se muestra un ejemplo de un controlador de autorización que devuelve una respuesta personalizada para ciertos tipos de errores de autorización:</span><span class="sxs-lookup"><span data-stu-id="0fcaf-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="0fcaf-109">Registro `MyAuthorizationMiddlewareResultHandler` en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0fcaf-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->