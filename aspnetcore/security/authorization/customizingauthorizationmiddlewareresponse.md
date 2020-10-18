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
# <a name="customize-the-behavior-of-authorizationmiddleware"></a>Personalizar el comportamiento de AuthorizationMiddleware

Las aplicaciones pueden registrar un `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` para personalizar la manera en que el middleware controla los resultados de la autorización. Las aplicaciones pueden usar el middleware personalizado para:

* Devolver respuestas personalizadas.
* Mejorar el desafío predeterminado o prohibir las respuestas.

En el código siguiente se muestra un ejemplo de un controlador de autorización que devuelve una respuesta personalizada para ciertos tipos de errores de autorización:

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

Registro `MyAuthorizationMiddlewareResultHandler` en `Startup.ConfigureServices` :

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->