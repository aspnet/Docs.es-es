---
title: Uso de la plantilla de proyecto React-with-Redux con ASP.NET Core
author: SteveSandersonMS
description: Aprenda cómo comenzar a trabajar con la plantilla de proyecto de aplicación de página única (SPA) de ASP.NET Core para React with Redux y create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
no-loc:
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
uid: spa/react-with-redux
ms.openlocfilehash: ac5b5d7161e79f133a1f107251fa6707b752c568
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628734"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="ebd0e-103">Uso de la plantilla de proyecto React-with-Redux con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebd0e-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="ebd0e-104">La plantilla de proyecto actualizada de React-with-Redux ofrece un práctico punto de partida para las aplicaciones ASP.NET Core que usan React, Redux y las convenciones [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) para implementar una completa interfaz de usuario (UI) en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="ebd0e-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="ebd0e-105">A excepción del comando de creación del proyecto, toda la información sobre la plantilla de React-with-Redux es igual que la de la plantilla de React.</span><span class="sxs-lookup"><span data-stu-id="ebd0e-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="ebd0e-106">Para crear este tipo de proyecto, ejecute `dotnet new reactredux` en lugar de `dotnet new react`.</span><span class="sxs-lookup"><span data-stu-id="ebd0e-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="ebd0e-107">Para más información sobre la funcionalidad común a ambas plantillas basadas en React, consulte la [documentación de la plantilla de React](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="ebd0e-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="ebd0e-108">Para obtener información sobre la configuración de una aplicación secundaria React-with-Redux en IIS, vea [ReactRedux Template 2.1: No se puede usar SPA en IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="ebd0e-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
