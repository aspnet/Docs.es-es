---
title: Protección de aplicaciones de ASP.NET Core Blazor Server
author: guardrex
description: Aprenda a proteger las aplicaciones de Blazor Server igual que las aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 4dc9040b9410304eb33e5df7c47db2f9a42152d3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014001"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="350ae-103">Protección de aplicaciones de ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="350ae-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="350ae-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="350ae-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="350ae-105">La seguridad de las aplicaciones de Blazor Server se configura de la misma forma que la de las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="350ae-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="350ae-106">Para obtener más información, vea los artículos en <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="350ae-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="350ae-107">Los temas de esta información general se aplican específicamente a Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="350ae-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="350ae-108">Plantilla de proyecto de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="350ae-108">Blazor Server project template</span></span>

<span data-ttu-id="350ae-109">Se puede configurar la autenticación de la plantilla de proyecto de Blazor Server cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="350ae-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="350ae-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="350ae-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="350ae-111">Siga las instrucciones de Visual Studio en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="350ae-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="350ae-112">Después de elegir la plantilla de aplicación de **Blazor Server** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="350ae-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="350ae-113">Se abre un cuadro de diálogo para ofrecer el mismo conjunto de mecanismos de autenticación disponibles para otros proyectos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="350ae-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="350ae-114">**Sin autenticación**</span><span class="sxs-lookup"><span data-stu-id="350ae-114">**No Authentication**</span></span>
* <span data-ttu-id="350ae-115">**Cuentas de usuario individuales**: las cuentas de usuario se pueden almacenar:</span><span class="sxs-lookup"><span data-stu-id="350ae-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="350ae-116">Dentro de la aplicación mediante el sistema de [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="350ae-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="350ae-117">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="350ae-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="350ae-118">**Cuentas profesionales o educativas**</span><span class="sxs-lookup"><span data-stu-id="350ae-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="350ae-119">**Autenticación de Windows**</span><span class="sxs-lookup"><span data-stu-id="350ae-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="350ae-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="350ae-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="350ae-121">Siga las instrucciones de Visual Studio Code en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="350ae-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="350ae-122">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="350ae-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="350ae-123">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="350ae-123">Authentication mechanism</span></span> | <span data-ttu-id="350ae-124">Descripción</span><span class="sxs-lookup"><span data-stu-id="350ae-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="350ae-125">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="350ae-125">`None` (default)</span></span>         | <span data-ttu-id="350ae-126">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="350ae-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="350ae-127">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="350ae-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="350ae-128">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="350ae-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="350ae-129">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="350ae-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="350ae-130">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="350ae-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="350ae-131">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="350ae-131">Windows Authentication</span></span> |

<span data-ttu-id="350ae-132">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="350ae-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="350ae-133">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="350ae-133">Create a folder for the project.</span></span>
* <span data-ttu-id="350ae-134">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="350ae-134">Name the project.</span></span>

<span data-ttu-id="350ae-135">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="350ae-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="350ae-136">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="350ae-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="350ae-137">Siga las instrucciones de Visual Studio para Mac en <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="350ae-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="350ae-138">En el paso **Configure your new Blazor Server App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="350ae-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="350ae-139">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="350ae-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="350ae-140">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="350ae-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="350ae-141">Cree un nuevo proyecto de Blazor Server con un mecanismo de autenticación que use el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="350ae-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="350ae-142">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="350ae-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="350ae-143">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="350ae-143">Authentication mechanism</span></span> | <span data-ttu-id="350ae-144">Descripción</span><span class="sxs-lookup"><span data-stu-id="350ae-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="350ae-145">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="350ae-145">`None` (default)</span></span>         | <span data-ttu-id="350ae-146">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="350ae-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="350ae-147">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="350ae-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="350ae-148">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="350ae-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="350ae-149">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="350ae-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="350ae-150">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="350ae-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="350ae-151">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="350ae-151">Windows Authentication</span></span> |

<span data-ttu-id="350ae-152">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="350ae-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="350ae-153">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="350ae-153">Create a folder for the project.</span></span>
* <span data-ttu-id="350ae-154">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="350ae-154">Name the project.</span></span>

<span data-ttu-id="350ae-155">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="350ae-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="350ae-156">Scaffolding para Identity</span><span class="sxs-lookup"><span data-stu-id="350ae-156">Scaffold Identity</span></span>

<span data-ttu-id="350ae-157">Scaffolding para Identity en un proyecto de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="350ae-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="350ae-158">[Sin autorización existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="350ae-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="350ae-159">[Con autorización](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="350ae-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
