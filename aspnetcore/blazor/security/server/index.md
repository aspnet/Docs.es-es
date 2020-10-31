---
title: 'Protección de aplicaciones de ASP.NET Core :::no-loc(Blazor Server):::'
author: guardrex
description: 'Aprenda a proteger las aplicaciones de :::no-loc(Blazor Server)::: igual que las aplicaciones de ASP.NET Core.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 108fb3a8a24295cad43fd8c83303abd95a7ecd33
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055482"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="9b009-103">Protección de aplicaciones de ASP.NET Core :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="9b009-103">Secure ASP.NET Core :::no-loc(Blazor Server)::: apps</span></span>

<span data-ttu-id="9b009-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9b009-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9b009-105">La seguridad de las aplicaciones de :::no-loc(Blazor Server)::: se configura de la misma forma que la de las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b009-105">:::no-loc(Blazor Server)::: apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="9b009-106">Para obtener más información, vea los artículos en <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="9b009-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="9b009-107">Los temas de esta información general se aplican específicamente a :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="9b009-107">Topics under this overview apply specifically to :::no-loc(Blazor Server):::.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="9b009-108">Plantilla de proyecto de :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="9b009-108">:::no-loc(Blazor Server)::: project template</span></span>

<span data-ttu-id="9b009-109">Se puede configurar la autenticación de la plantilla de proyecto de :::no-loc(Blazor Server)::: cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="9b009-109">The :::no-loc(Blazor Server)::: project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9b009-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b009-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9b009-111">Siga las instrucciones de Visual Studio en <xref:blazor/tooling> para crear un proyecto de :::no-loc(Blazor Server)::: con un mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="9b009-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new :::no-loc(Blazor Server)::: project with an authentication mechanism.</span></span>

<span data-ttu-id="9b009-112">Después de elegir la plantilla de aplicación de **:::no-loc(Blazor Server):::** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core** , seleccione **Cambiar** en **Autenticación** .</span><span class="sxs-lookup"><span data-stu-id="9b009-112">After choosing the **:::no-loc(Blazor Server)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

<span data-ttu-id="9b009-113">Se abre un cuadro de diálogo para ofrecer el mismo conjunto de mecanismos de autenticación disponibles para otros proyectos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9b009-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="9b009-114">**Sin autenticación**</span><span class="sxs-lookup"><span data-stu-id="9b009-114">**No Authentication**</span></span>
* <span data-ttu-id="9b009-115">**Cuentas de usuario individuales** : las cuentas de usuario se pueden almacenar:</span><span class="sxs-lookup"><span data-stu-id="9b009-115">**Individual User Accounts** : User accounts can be stored:</span></span>
  * <span data-ttu-id="9b009-116">Dentro de la aplicación mediante el sistema de [:::no-loc(Identity):::](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b009-116">Within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="9b009-117">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="9b009-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="9b009-118">**Cuentas profesionales o educativas**</span><span class="sxs-lookup"><span data-stu-id="9b009-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="9b009-119">**Autenticación de Windows**</span><span class="sxs-lookup"><span data-stu-id="9b009-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9b009-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b009-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9b009-121">Siga las instrucciones de Visual Studio Code en <xref:blazor/tooling> para crear un proyecto de :::no-loc(Blazor Server)::: con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="9b009-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new :::no-loc(Blazor Server)::: project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9b009-122">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="9b009-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9b009-123">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="9b009-123">Authentication mechanism</span></span> | <span data-ttu-id="9b009-124">Descripción</span><span class="sxs-lookup"><span data-stu-id="9b009-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9b009-125">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="9b009-125">`None` (default)</span></span>         | <span data-ttu-id="9b009-126">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="9b009-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9b009-127">Usuarios almacenados en la aplicación con :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="9b009-127">Users stored in the app with :::no-loc(ASP.NET Core Identity):::</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9b009-128">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="9b009-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9b009-129">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="9b009-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9b009-130">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="9b009-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9b009-131">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="9b009-131">Windows Authentication</span></span> |

<span data-ttu-id="9b009-132">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="9b009-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9b009-133">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="9b009-133">Create a folder for the project.</span></span>
* <span data-ttu-id="9b009-134">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="9b009-134">Name the project.</span></span>

<span data-ttu-id="9b009-135">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b009-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9b009-136">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9b009-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9b009-137">Siga las instrucciones de Visual Studio para Mac en <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="9b009-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="9b009-138">En el paso **Configure your new :::no-loc(Blazor Server)::: App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="9b009-138">On the **Configure your new :::no-loc(Blazor Server)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="9b009-139">La aplicación se crea para usuarios individuales almacenados en la aplicación con :::no-loc(ASP.NET Core Identity):::.</span><span class="sxs-lookup"><span data-stu-id="9b009-139">The app is created for individual users stored in the app with :::no-loc(ASP.NET Core Identity):::.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9b009-140">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="9b009-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9b009-141">Cree un nuevo proyecto de :::no-loc(Blazor Server)::: con un mecanismo de autenticación que use el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="9b009-141">Create a new :::no-loc(Blazor Server)::: project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9b009-142">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="9b009-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9b009-143">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="9b009-143">Authentication mechanism</span></span> | <span data-ttu-id="9b009-144">Descripción</span><span class="sxs-lookup"><span data-stu-id="9b009-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9b009-145">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="9b009-145">`None` (default)</span></span>         | <span data-ttu-id="9b009-146">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="9b009-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9b009-147">Usuarios almacenados en la aplicación con :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="9b009-147">Users stored in the app with :::no-loc(ASP.NET Core Identity):::</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9b009-148">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="9b009-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9b009-149">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="9b009-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9b009-150">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="9b009-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9b009-151">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="9b009-151">Windows Authentication</span></span> |

<span data-ttu-id="9b009-152">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="9b009-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9b009-153">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="9b009-153">Create a folder for the project.</span></span>
* <span data-ttu-id="9b009-154">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="9b009-154">Name the project.</span></span>

<span data-ttu-id="9b009-155">Para obtener más información:</span><span class="sxs-lookup"><span data-stu-id="9b009-155">For more information:</span></span>

* <span data-ttu-id="9b009-156">Consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b009-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="9b009-157">Ejecute el comando de ayuda para la plantilla de :::no-loc(Blazor Server)::: (`blazorserver`) en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="9b009-157">Execute the help command for the :::no-loc(Blazor Server)::: template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="9b009-158">Scaffolding para :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="9b009-158">Scaffold :::no-loc(Identity):::</span></span>

<span data-ttu-id="9b009-159">Scaffolding para :::no-loc(Identity)::: en un proyecto de :::no-loc(Blazor Server)::::</span><span class="sxs-lookup"><span data-stu-id="9b009-159">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project:</span></span>

* <span data-ttu-id="9b009-160">[Sin autorización existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="9b009-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="9b009-161">[Con autorización](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="9b009-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b009-162">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9b009-162">Additional resources</span></span>

* [<span data-ttu-id="9b009-163">Inicio rápido: Adición del inicio de sesión con Microsoft en una aplicación web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b009-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="9b009-164">Inicio rápido: Protección de una API web de ASP.NET Core con la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="9b009-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
