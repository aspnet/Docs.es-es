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
ms.openlocfilehash: 5a3d3c6e06653de7f0d01565444d37013f347a5b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280321"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="9883b-103">Protección de aplicaciones de ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="9883b-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="9883b-104">La seguridad de las aplicaciones de Blazor Server se configura de la misma forma que la de las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9883b-104">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="9883b-105">Para obtener más información, vea los artículos en <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="9883b-105">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="9883b-106">Los temas de esta información general se aplican específicamente a Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="9883b-106">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="9883b-107">Plantilla de proyecto de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="9883b-107">Blazor Server project template</span></span>

<span data-ttu-id="9883b-108">Se puede configurar la autenticación de la plantilla de proyecto de Blazor Server cuando se crea el proyecto.</span><span class="sxs-lookup"><span data-stu-id="9883b-108">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9883b-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9883b-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9883b-110">Siga las instrucciones de Visual Studio en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="9883b-110">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="9883b-111">Después de elegir la plantilla de aplicación de **Blazor Server** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="9883b-111">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="9883b-112">Se abre un cuadro de diálogo para ofrecer el mismo conjunto de mecanismos de autenticación disponibles para otros proyectos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9883b-112">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="9883b-113">**Sin autenticación**</span><span class="sxs-lookup"><span data-stu-id="9883b-113">**No Authentication**</span></span>
* <span data-ttu-id="9883b-114">**Cuentas de usuario individuales**: las cuentas de usuario se pueden almacenar:</span><span class="sxs-lookup"><span data-stu-id="9883b-114">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="9883b-115">Dentro de la aplicación mediante el sistema de [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9883b-115">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="9883b-116">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="9883b-116">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="9883b-117">**Cuentas profesionales o educativas**</span><span class="sxs-lookup"><span data-stu-id="9883b-117">**Work or School Accounts**</span></span>
* <span data-ttu-id="9883b-118">**Autenticación de Windows**</span><span class="sxs-lookup"><span data-stu-id="9883b-118">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9883b-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9883b-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9883b-120">Siga las instrucciones de Visual Studio Code en <xref:blazor/tooling> para crear un proyecto de Blazor Server con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="9883b-120">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9883b-121">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="9883b-121">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9883b-122">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="9883b-122">Authentication mechanism</span></span> | <span data-ttu-id="9883b-123">Descripción</span><span class="sxs-lookup"><span data-stu-id="9883b-123">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9883b-124">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="9883b-124">`None` (default)</span></span>         | <span data-ttu-id="9883b-125">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="9883b-125">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9883b-126">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="9883b-126">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9883b-127">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="9883b-127">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9883b-128">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="9883b-128">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9883b-129">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="9883b-129">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9883b-130">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="9883b-130">Windows Authentication</span></span> |

<span data-ttu-id="9883b-131">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="9883b-131">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9883b-132">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="9883b-132">Create a folder for the project.</span></span>
* <span data-ttu-id="9883b-133">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="9883b-133">Name the project.</span></span>

<span data-ttu-id="9883b-134">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9883b-134">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9883b-135">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9883b-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9883b-136">Siga las instrucciones de Visual Studio para Mac en <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="9883b-136">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="9883b-137">En el paso **Configure your new Blazor Server App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="9883b-137">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="9883b-138">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="9883b-138">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9883b-139">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="9883b-139">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9883b-140">Cree un nuevo proyecto de Blazor Server con un mecanismo de autenticación que use el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="9883b-140">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9883b-141">En la tabla siguiente se muestran los valores de autenticación permitidos (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="9883b-141">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9883b-142">Mecanismo de autenticación</span><span class="sxs-lookup"><span data-stu-id="9883b-142">Authentication mechanism</span></span> | <span data-ttu-id="9883b-143">Descripción</span><span class="sxs-lookup"><span data-stu-id="9883b-143">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9883b-144">`None` (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="9883b-144">`None` (default)</span></span>         | <span data-ttu-id="9883b-145">Sin autenticación</span><span class="sxs-lookup"><span data-stu-id="9883b-145">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9883b-146">Usuarios almacenados en la aplicación con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="9883b-146">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9883b-147">Usuarios almacenados en [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="9883b-147">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9883b-148">Autenticación organizativa para un solo inquilino</span><span class="sxs-lookup"><span data-stu-id="9883b-148">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9883b-149">Autenticación organizativa para varios inquilinos</span><span class="sxs-lookup"><span data-stu-id="9883b-149">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9883b-150">Autenticación de Windows</span><span class="sxs-lookup"><span data-stu-id="9883b-150">Windows Authentication</span></span> |

<span data-ttu-id="9883b-151">Con la opción `-o|--output`, el comando usa el valor proporcionado para el marcador de posición `{APP NAME}` para:</span><span class="sxs-lookup"><span data-stu-id="9883b-151">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9883b-152">Cree una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="9883b-152">Create a folder for the project.</span></span>
* <span data-ttu-id="9883b-153">Asigne el nombre al proyecto.</span><span class="sxs-lookup"><span data-stu-id="9883b-153">Name the project.</span></span>

<span data-ttu-id="9883b-154">Para obtener más información:</span><span class="sxs-lookup"><span data-stu-id="9883b-154">For more information:</span></span>

* <span data-ttu-id="9883b-155">Consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9883b-155">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="9883b-156">Ejecute el comando de ayuda para la plantilla de Blazor Server (`blazorserver`) en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="9883b-156">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a><span data-ttu-id="9883b-157">Scaffolding para Identity</span><span class="sxs-lookup"><span data-stu-id="9883b-157">Scaffold Identity</span></span>

<span data-ttu-id="9883b-158">Scaffolding para Identity en un proyecto de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="9883b-158">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="9883b-159">[Sin autorización existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="9883b-159">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="9883b-160">[Con autorización](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="9883b-160">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="9883b-161">Azure App Service en Linux con Identity Server</span><span class="sxs-lookup"><span data-stu-id="9883b-161">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="9883b-162">Al realizar una implementación en Azure App Service en Linux con Identity Server, especifique el emisor de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="9883b-162">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="9883b-163">Para obtener más información, vea <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="9883b-163">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9883b-164">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9883b-164">Additional resources</span></span>

* [<span data-ttu-id="9883b-165">Inicio rápido: Adición del inicio de sesión con Microsoft en una aplicación web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9883b-165">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="9883b-166">Inicio rápido: Protección de una API web de ASP.NET Core con la plataforma de identidad de Microsoft</span><span class="sxs-lookup"><span data-stu-id="9883b-166">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="9883b-167"><xref:host-and-deploy/proxy-load-balancer>; incluye instrucciones relativas a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9883b-167"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="9883b-168">Uso de middleware de encabezados reenviados para retener la información de esquemas HTTPS en servidores proxy y redes internas.</span><span class="sxs-lookup"><span data-stu-id="9883b-168">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="9883b-169">Escenarios y casos de uso adicionales, incluidos la configuración manual de esquemas, los cambios en las rutas de las solicitudes para corregir el enrutamiento de las solicitudes y el reenvío de esquemas de solicitudes para Linux y proxies inversos que no sean de tipo IIS.</span><span class="sxs-lookup"><span data-stu-id="9883b-169">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
