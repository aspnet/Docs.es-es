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
ms.openlocfilehash: 147ebbeb84e1755307d627ef428d92d1b0248c74
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394868"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="9331d-103">Protección de aplicaciones de ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="9331d-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="9331d-104">La seguridad de las aplicaciones de Blazor Server se configura de la misma forma que la de las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9331d-104">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="9331d-105">Para obtener más información, vea los artículos en <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="9331d-105">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="9331d-106">Los temas de esta información general se aplican específicamente a Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="9331d-106">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="9331d-107">Plantilla de proyecto de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="9331d-107">Blazor Server project template</span></span>

<span data-ttu-id="9331d-108">Se puede configurar la autenticación de la [plantilla de proyecto de Blazor Server](xref:blazor/project-structure) cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="9331d-108">The [Blazor Server project template](xref:blazor/project-structure) can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9331d-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9331d-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9331d-110">Siga las instrucciones de Visual Studio en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="9331d-110">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="9331d-111">Después de elegir la plantilla de aplicación de **Blazor Server** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="9331d-111">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="9331d-112">Se abre un cuadro de diálogo para ofrecer el mismo conjunto de mecanismos de autenticación disponibles para otros proyectos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9331d-112">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="9331d-113">**Sin autenticación**</span><span class="sxs-lookup"><span data-stu-id="9331d-113">**No Authentication**</span></span>
* <span data-ttu-id="9331d-114">**Cuentas de usuario individuales**: las cuentas de usuario se pueden almacenar:</span><span class="sxs-lookup"><span data-stu-id="9331d-114">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="9331d-115">Dentro de la aplicación mediante el sistema de [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9331d-115">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="9331d-116">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="9331d-116">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="9331d-117">**Cuentas profesionales o educativas**</span><span class="sxs-lookup"><span data-stu-id="9331d-117">**Work or School Accounts**</span></span>
* <span data-ttu-id="9331d-118">**Autenticación de Windows**</span><span class="sxs-lookup"><span data-stu-id="9331d-118">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9331d-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9331d-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9331d-120">Siga las instrucciones de Visual Studio Code en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="9331d-120">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9331d-121">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="9331d-121">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9331d-122">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="9331d-122">Authentication mechanism</span></span> | <span data-ttu-id="9331d-123">Descripción</span><span class="sxs-lookup"><span data-stu-id="9331d-123">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9331d-124">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="9331d-124">`None` (default)</span></span>         | <span data-ttu-id="9331d-125">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="9331d-125">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9331d-126">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="9331d-126">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9331d-127">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="9331d-127">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9331d-128">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="9331d-128">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9331d-129">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="9331d-129">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9331d-130">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="9331d-130">Windows Authentication</span></span> |

<span data-ttu-id="9331d-131">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="9331d-131">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9331d-132">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="9331d-132">Create a folder for the project.</span></span>
* <span data-ttu-id="9331d-133">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="9331d-133">Name the project.</span></span>

<span data-ttu-id="9331d-134">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9331d-134">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9331d-135">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9331d-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9331d-136">Siga las instrucciones de Visual Studio para Mac en <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="9331d-136">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="9331d-137">En el paso **Configure your new Blazor Server App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="9331d-137">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="9331d-138">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="9331d-138">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9331d-139">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="9331d-139">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9331d-140">Cree un nuevo proyecto de Blazor Server con un mecanismo de autenticación que use el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="9331d-140">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9331d-141">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="9331d-141">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9331d-142">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="9331d-142">Authentication mechanism</span></span> | <span data-ttu-id="9331d-143">Descripción</span><span class="sxs-lookup"><span data-stu-id="9331d-143">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9331d-144">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="9331d-144">`None` (default)</span></span>         | <span data-ttu-id="9331d-145">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="9331d-145">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9331d-146">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="9331d-146">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9331d-147">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="9331d-147">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9331d-148">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="9331d-148">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9331d-149">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="9331d-149">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9331d-150">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="9331d-150">Windows Authentication</span></span> |

<span data-ttu-id="9331d-151">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="9331d-151">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9331d-152">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="9331d-152">Create a folder for the project.</span></span>
* <span data-ttu-id="9331d-153">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="9331d-153">Name the project.</span></span>

<span data-ttu-id="9331d-154">Para obtener más información:</span><span class="sxs-lookup"><span data-stu-id="9331d-154">For more information:</span></span>

* <span data-ttu-id="9331d-155">Consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9331d-155">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="9331d-156">Ejecute el comando de ayuda para la plantilla de Blazor Server (`blazorserver`) en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="9331d-156">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a><span data-ttu-id="9331d-157">Scaffolding para Identity</span><span class="sxs-lookup"><span data-stu-id="9331d-157">Scaffold Identity</span></span>

<span data-ttu-id="9331d-158">Scaffolding para Identity en un proyecto de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="9331d-158">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="9331d-159">[Sin autorización existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="9331d-159">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="9331d-160">[Con autorización](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="9331d-160">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-claims-and-tokens-from-external-providers"></a><span data-ttu-id="9331d-161">Notificaciones y tokens adicionales de proveedores externos</span><span class="sxs-lookup"><span data-stu-id="9331d-161">Additional claims and tokens from external providers</span></span>

<span data-ttu-id="9331d-162">Para almacenar notificaciones adicionales de proveedores externos, consulte <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="9331d-162">To store additional claims from external providers, see <xref:security/authentication/social/additional-claims>.</span></span>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="9331d-163">Azure App Service en Linux con Identity Server</span><span class="sxs-lookup"><span data-stu-id="9331d-163">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="9331d-164">Al realizar una implementación en Azure App Service en Linux con Identity Server, especifique el emisor de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="9331d-164">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="9331d-165">Para obtener más información, vea <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="9331d-165">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9331d-166">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9331d-166">Additional resources</span></span>

* [<span data-ttu-id="9331d-167">Inicio rápido: Adición del inicio de sesión con Microsoft en una aplicación web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9331d-167">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="9331d-168">Inicio rápido: Protección de una API web de ASP.NET Core con la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="9331d-168">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="9331d-169"><xref:host-and-deploy/proxy-load-balancer>; incluye instrucciones relativas a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9331d-169"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="9331d-170">Uso de middleware de encabezados reenviados para retener la información de esquemas HTTPS en servidores proxy y redes internas.</span><span class="sxs-lookup"><span data-stu-id="9331d-170">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="9331d-171">Escenarios y casos de uso adicionales, incluidos la configuración manual de esquemas, los cambios en las rutas de las solicitudes para corregir el enrutamiento de las solicitudes y el reenvío de esquemas de solicitudes para Linux y proxies inversos que no sean de tipo IIS.</span><span class="sxs-lookup"><span data-stu-id="9331d-171">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
