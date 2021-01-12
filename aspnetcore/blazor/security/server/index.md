---
title: Protección de aplicaciones de ASP.NET Core Blazor Server
author: guardrex
description: Aprenda a proteger las aplicaciones de Blazor Server igual que las aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: aa24def1a003a2c2608691e6168066c740f47205
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024631"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="8c85f-103">Protección de aplicaciones de ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="8c85f-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="8c85f-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8c85f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8c85f-105">La seguridad de las aplicaciones de Blazor Server se configura de la misma forma que la de las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c85f-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="8c85f-106">Para obtener más información, vea los artículos en <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="8c85f-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="8c85f-107">Los temas de esta información general se aplican específicamente a Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="8c85f-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="8c85f-108">Plantilla de proyecto de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="8c85f-108">Blazor Server project template</span></span>

<span data-ttu-id="8c85f-109">Se puede configurar la autenticación de la plantilla de proyecto de Blazor Server cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="8c85f-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c85f-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c85f-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c85f-111">Siga las instrucciones de Visual Studio en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="8c85f-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="8c85f-112">Después de elegir la plantilla de aplicación de **Blazor Server** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="8c85f-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="8c85f-113">Se abre un cuadro de diálogo para ofrecer el mismo conjunto de mecanismos de autenticación disponibles para otros proyectos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8c85f-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="8c85f-114">**Sin autenticación**</span><span class="sxs-lookup"><span data-stu-id="8c85f-114">**No Authentication**</span></span>
* <span data-ttu-id="8c85f-115">**Cuentas de usuario individuales**: las cuentas de usuario se pueden almacenar:</span><span class="sxs-lookup"><span data-stu-id="8c85f-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="8c85f-116">Dentro de la aplicación mediante el sistema de [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c85f-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="8c85f-117">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="8c85f-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="8c85f-118">**Cuentas profesionales o educativas**</span><span class="sxs-lookup"><span data-stu-id="8c85f-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="8c85f-119">**Autenticación de Windows**</span><span class="sxs-lookup"><span data-stu-id="8c85f-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c85f-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c85f-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8c85f-121">Siga las instrucciones de Visual Studio Code en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="8c85f-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="8c85f-122">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="8c85f-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="8c85f-123">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="8c85f-123">Authentication mechanism</span></span> | <span data-ttu-id="8c85f-124">Descripción</span><span class="sxs-lookup"><span data-stu-id="8c85f-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="8c85f-125">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="8c85f-125">`None` (default)</span></span>         | <span data-ttu-id="8c85f-126">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="8c85f-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="8c85f-127">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="8c85f-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="8c85f-128">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="8c85f-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="8c85f-129">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="8c85f-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="8c85f-130">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="8c85f-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="8c85f-131">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="8c85f-131">Windows Authentication</span></span> |

<span data-ttu-id="8c85f-132">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="8c85f-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="8c85f-133">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="8c85f-133">Create a folder for the project.</span></span>
* <span data-ttu-id="8c85f-134">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="8c85f-134">Name the project.</span></span>

<span data-ttu-id="8c85f-135">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c85f-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c85f-136">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8c85f-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8c85f-137">Siga las instrucciones de Visual Studio para Mac en <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="8c85f-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="8c85f-138">En el paso **Configure your new Blazor Server App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="8c85f-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="8c85f-139">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="8c85f-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8c85f-140">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="8c85f-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="8c85f-141">Cree un nuevo proyecto de Blazor Server con un mecanismo de autenticación que use el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="8c85f-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="8c85f-142">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="8c85f-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="8c85f-143">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="8c85f-143">Authentication mechanism</span></span> | <span data-ttu-id="8c85f-144">Descripción</span><span class="sxs-lookup"><span data-stu-id="8c85f-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="8c85f-145">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="8c85f-145">`None` (default)</span></span>         | <span data-ttu-id="8c85f-146">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="8c85f-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="8c85f-147">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="8c85f-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="8c85f-148">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="8c85f-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="8c85f-149">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="8c85f-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="8c85f-150">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="8c85f-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="8c85f-151">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="8c85f-151">Windows Authentication</span></span> |

<span data-ttu-id="8c85f-152">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="8c85f-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="8c85f-153">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="8c85f-153">Create a folder for the project.</span></span>
* <span data-ttu-id="8c85f-154">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="8c85f-154">Name the project.</span></span>

<span data-ttu-id="8c85f-155">Para obtener más información:</span><span class="sxs-lookup"><span data-stu-id="8c85f-155">For more information:</span></span>

* <span data-ttu-id="8c85f-156">Consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c85f-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="8c85f-157">Ejecute el comando de ayuda para la plantilla de Blazor Server (`blazorserver`) en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="8c85f-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="8c85f-158">Scaffolding para Identity</span><span class="sxs-lookup"><span data-stu-id="8c85f-158">Scaffold Identity</span></span>

<span data-ttu-id="8c85f-159">Scaffolding para Identity en un proyecto de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="8c85f-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="8c85f-160">[Sin autorización existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="8c85f-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="8c85f-161">[Con autorización](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="8c85f-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8c85f-162">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8c85f-162">Additional resources</span></span>

* [<span data-ttu-id="8c85f-163">Inicio rápido: Adición del inicio de sesión con Microsoft en una aplicación web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c85f-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="8c85f-164">Inicio rápido: Protección de una API web de ASP.NET Core con la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="8c85f-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="8c85f-165"><xref:host-and-deploy/proxy-load-balancer>; incluye instrucciones relativas a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8c85f-165"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="8c85f-166">Uso de middleware de encabezados reenviados para retener la información de esquemas HTTPS en servidores proxy y redes internas.</span><span class="sxs-lookup"><span data-stu-id="8c85f-166">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="8c85f-167">Escenarios y casos de uso adicionales, incluidos la configuración manual de esquemas, los cambios en las rutas de las solicitudes para corregir el enrutamiento de las solicitudes y el reenvío de esquemas de solicitudes para Linux y proxies inversos que no sean de tipo IIS.</span><span class="sxs-lookup"><span data-stu-id="8c85f-167">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
