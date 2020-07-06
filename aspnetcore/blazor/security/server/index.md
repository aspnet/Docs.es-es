---
title: Protección de aplicaciones de ASP.NET Core Blazor Server
author: guardrex
description: Aprenda a proteger las aplicaciones de Blazor Server igual que las aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: 69a24fc955a0f2fb524ec817eb50372052f538a1
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944261"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="b469b-103">Protección de aplicaciones de ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b469b-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="b469b-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b469b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b469b-105">La seguridad de las aplicaciones de Blazor Server se configura de la misma forma que la de las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b469b-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="b469b-106">Para obtener más información, vea los artículos en <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="b469b-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="b469b-107">Los temas de esta información general se aplican específicamente a Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b469b-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a><span data-ttu-id="b469b-108">Plantilla de proyecto de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b469b-108">Blazor Server project template</span></span>

<span data-ttu-id="b469b-109">Se puede configurar la autenticación de la plantilla de proyecto de Blazor Server cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="b469b-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b469b-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b469b-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b469b-111">Siga las instrucciones de Visual Studio en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="b469b-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="b469b-112">Después de elegir la plantilla de aplicación de **Blazor Server** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="b469b-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="b469b-113">Se abre un cuadro de diálogo para ofrecer el mismo conjunto de mecanismos de autenticación disponibles para otros proyectos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b469b-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="b469b-114">**Sin autenticación**</span><span class="sxs-lookup"><span data-stu-id="b469b-114">**No Authentication**</span></span>
* <span data-ttu-id="b469b-115">**Cuentas de usuario individuales**: las cuentas de usuario se pueden almacenar:</span><span class="sxs-lookup"><span data-stu-id="b469b-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="b469b-116">Dentro de la aplicación mediante el sistema de [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b469b-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="b469b-117">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="b469b-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="b469b-118">**Cuentas profesionales o educativas**</span><span class="sxs-lookup"><span data-stu-id="b469b-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="b469b-119">**Autenticación de Windows**</span><span class="sxs-lookup"><span data-stu-id="b469b-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b469b-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b469b-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b469b-121">Siga las instrucciones de Visual Studio Code en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="b469b-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b469b-122">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="b469b-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b469b-123">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="b469b-123">Authentication mechanism</span></span> | <span data-ttu-id="b469b-124">Descripción</span><span class="sxs-lookup"><span data-stu-id="b469b-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b469b-125">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="b469b-125">`None` (default)</span></span>         | <span data-ttu-id="b469b-126">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="b469b-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b469b-127">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="b469b-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b469b-128">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="b469b-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b469b-129">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="b469b-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b469b-130">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="b469b-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b469b-131">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="b469b-131">Windows Authentication</span></span> |

<span data-ttu-id="b469b-132">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="b469b-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b469b-133">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="b469b-133">Create a folder for the project.</span></span>
* <span data-ttu-id="b469b-134">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="b469b-134">Name the project.</span></span>

<span data-ttu-id="b469b-135">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b469b-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b469b-136">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b469b-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b469b-137">Siga las instrucciones de Visual Studio para Mac en <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="b469b-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="b469b-138">En el paso **Configure your new Blazor Server App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="b469b-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="b469b-139">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="b469b-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b469b-140">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="b469b-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b469b-141">Cree un nuevo proyecto de Blazor Server con un mecanismo de autenticación que use el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="b469b-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b469b-142">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="b469b-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b469b-143">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="b469b-143">Authentication mechanism</span></span> | <span data-ttu-id="b469b-144">Descripción</span><span class="sxs-lookup"><span data-stu-id="b469b-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b469b-145">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="b469b-145">`None` (default)</span></span>         | <span data-ttu-id="b469b-146">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="b469b-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b469b-147">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="b469b-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b469b-148">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="b469b-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b469b-149">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="b469b-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b469b-150">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="b469b-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b469b-151">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="b469b-151">Windows Authentication</span></span> |

<span data-ttu-id="b469b-152">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="b469b-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b469b-153">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="b469b-153">Create a folder for the project.</span></span>
* <span data-ttu-id="b469b-154">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="b469b-154">Name the project.</span></span>

<span data-ttu-id="b469b-155">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b469b-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="b469b-156">Scaffolding para Identity</span><span class="sxs-lookup"><span data-stu-id="b469b-156">Scaffold Identity</span></span>

<span data-ttu-id="b469b-157">Scaffolding para Identity en un proyecto de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="b469b-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="b469b-158">[Sin autorización existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="b469b-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="b469b-159">[Con autorización](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="b469b-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
