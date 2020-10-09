---
title: Protección de aplicaciones de ASP.NET Core Blazor Server
author: guardrex
description: Aprenda a proteger las aplicaciones de Blazor Server igual que las aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: d6d0f6f859dbaef98c6c8a9c53fe9858705cdc0a
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805510"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="3e693-103">Protección de aplicaciones de ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="3e693-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="3e693-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3e693-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3e693-105">La seguridad de las aplicaciones de Blazor Server se configura de la misma forma que la de las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e693-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="3e693-106">Para obtener más información, vea los artículos en <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="3e693-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="3e693-107">Los temas de esta información general se aplican específicamente a Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="3e693-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="3e693-108">Plantilla de proyecto de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="3e693-108">Blazor Server project template</span></span>

<span data-ttu-id="3e693-109">Se puede configurar la autenticación de la plantilla de proyecto de Blazor Server cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="3e693-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e693-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e693-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3e693-111">Siga las instrucciones de Visual Studio en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="3e693-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="3e693-112">Después de elegir la plantilla de aplicación de **Blazor Server** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="3e693-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="3e693-113">Se abre un cuadro de diálogo para ofrecer el mismo conjunto de mecanismos de autenticación disponibles para otros proyectos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3e693-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="3e693-114">**Sin autenticación**</span><span class="sxs-lookup"><span data-stu-id="3e693-114">**No Authentication**</span></span>
* <span data-ttu-id="3e693-115">**Cuentas de usuario individuales**: las cuentas de usuario se pueden almacenar:</span><span class="sxs-lookup"><span data-stu-id="3e693-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="3e693-116">Dentro de la aplicación mediante el sistema de [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e693-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="3e693-117">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="3e693-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="3e693-118">**Cuentas profesionales o educativas**</span><span class="sxs-lookup"><span data-stu-id="3e693-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="3e693-119">**Autenticación de Windows**</span><span class="sxs-lookup"><span data-stu-id="3e693-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e693-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e693-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3e693-121">Siga las instrucciones de Visual Studio Code en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="3e693-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="3e693-122">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="3e693-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="3e693-123">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="3e693-123">Authentication mechanism</span></span> | <span data-ttu-id="3e693-124">Descripción</span><span class="sxs-lookup"><span data-stu-id="3e693-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="3e693-125">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="3e693-125">`None` (default)</span></span>         | <span data-ttu-id="3e693-126">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="3e693-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="3e693-127">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="3e693-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="3e693-128">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="3e693-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="3e693-129">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="3e693-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="3e693-130">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="3e693-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="3e693-131">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="3e693-131">Windows Authentication</span></span> |

<span data-ttu-id="3e693-132">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="3e693-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="3e693-133">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="3e693-133">Create a folder for the project.</span></span>
* <span data-ttu-id="3e693-134">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="3e693-134">Name the project.</span></span>

<span data-ttu-id="3e693-135">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e693-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e693-136">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3e693-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3e693-137">Siga las instrucciones de Visual Studio para Mac en <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="3e693-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="3e693-138">En el paso **Configure your new Blazor Server App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="3e693-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="3e693-139">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="3e693-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3e693-140">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3e693-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3e693-141">Cree un nuevo proyecto de Blazor Server con un mecanismo de autenticación que use el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="3e693-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="3e693-142">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="3e693-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="3e693-143">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="3e693-143">Authentication mechanism</span></span> | <span data-ttu-id="3e693-144">Descripción</span><span class="sxs-lookup"><span data-stu-id="3e693-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="3e693-145">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="3e693-145">`None` (default)</span></span>         | <span data-ttu-id="3e693-146">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="3e693-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="3e693-147">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="3e693-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="3e693-148">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="3e693-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="3e693-149">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="3e693-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="3e693-150">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="3e693-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="3e693-151">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="3e693-151">Windows Authentication</span></span> |

<span data-ttu-id="3e693-152">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="3e693-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="3e693-153">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="3e693-153">Create a folder for the project.</span></span>
* <span data-ttu-id="3e693-154">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="3e693-154">Name the project.</span></span>

<span data-ttu-id="3e693-155">Para obtener más información:</span><span class="sxs-lookup"><span data-stu-id="3e693-155">For more information:</span></span>

* <span data-ttu-id="3e693-156">Consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e693-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="3e693-157">Ejecute el comando de ayuda para la plantilla de Blazor Server (`blazorserver`) en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="3e693-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="3e693-158">Scaffolding para Identity</span><span class="sxs-lookup"><span data-stu-id="3e693-158">Scaffold Identity</span></span>

<span data-ttu-id="3e693-159">Scaffolding para Identity en un proyecto de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="3e693-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="3e693-160">[Sin autorización existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="3e693-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="3e693-161">[Con autorización](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="3e693-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e693-162">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3e693-162">Additional resources</span></span>

* [<span data-ttu-id="3e693-163">Inicio rápido: Adición del inicio de sesión con Microsoft en una aplicación web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e693-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="3e693-164">Inicio rápido: Protección de una API web de ASP.NET Core con la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="3e693-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
