---
title: 'Scaffolding :::no-loc(Identity)::: en proyectos de ASP.net Core'
author: rick-anderson
description: 'Obtenga información sobre cómo aplicar la técnica scaffolding :::no-loc(Identity)::: en un proyecto ASP.net Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: c79dfc64d4311088c3f9ea03aad7570189000e2a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053324"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="9b03d-103">Scaffolding :::no-loc(Identity)::: en proyectos de ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="9b03d-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="9b03d-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9b03d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9b03d-105">ASP.NET Core proporciona [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) como una [ :::no-loc(Razor)::: biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="9b03d-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="9b03d-106">Las aplicaciones que incluyen :::no-loc(Identity)::: pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de clases (RCL).</span><span class="sxs-lookup"><span data-stu-id="9b03d-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="9b03d-107">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="9b03d-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="9b03d-108">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="9b03d-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="9b03d-109">El código generado tiene prioridad sobre el mismo código de la RCL de :::no-loc(Identity):::.</span><span class="sxs-lookup"><span data-stu-id="9b03d-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="9b03d-110">Para obtener un control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un [ :::no-loc(Identity)::: origen de IU completo](#full).</span><span class="sxs-lookup"><span data-stu-id="9b03d-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="9b03d-111">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="9b03d-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="9b03d-112">Tiene la opción de seleccionar el código de :::no-loc(Identity)::: que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="9b03d-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="9b03d-113">Aunque el scaffolding genera la mayor parte del código necesario, debe actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="9b03d-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="9b03d-114">En este documento se explican los pasos necesarios para completar una :::no-loc(Identity)::: actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9b03d-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="9b03d-115">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="9b03d-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="9b03d-116">Inspeccione los cambios después de ejecutar el :::no-loc(Identity)::: scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9b03d-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="9b03d-117">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="9b03d-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="9b03d-118">Los servicios o los códigos auxiliares de servicio no se generan al aplicar scaffolding :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="9b03d-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="9b03d-119">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="9b03d-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="9b03d-120">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="9b03d-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="9b03d-121">Cuando se scaffolding :::no-loc(Identity)::: con un nuevo contexto de datos en un proyecto con cuentas individuales existentes:</span><span class="sxs-lookup"><span data-stu-id="9b03d-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="9b03d-122">En `Startup.ConfigureServices` , quite las llamadas a:</span><span class="sxs-lookup"><span data-stu-id="9b03d-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="9b03d-123">Por ejemplo, `AddDbContext` y `AddDefault:::no-loc(Identity):::` están comentados en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="9b03d-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="9b03d-124">El código anterior comenta el código que está duplicado en *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS*</span><span class="sxs-lookup"><span data-stu-id="9b03d-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="9b03d-125">Normalmente, las aplicaciones que se crearon con cuentas individuales deben \* **no** _ crear un nuevo contexto de datos.</span><span class="sxs-lookup"><span data-stu-id="9b03d-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="9b03d-126">Scaffolding :::no-loc(Identity)::: en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="9b03d-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9b03d-127">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="9b03d-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="9b03d-128">Scaffolding :::no-loc(Identity)::: en un :::no-loc(Razor)::: proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="9b03d-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add Create:::no-loc(Identity):::Schema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9b03d-129">:::no-loc(Identity):::está configurado en _Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs \*.</span><span class="sxs-lookup"><span data-stu-id="9b03d-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="9b03d-130">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="9b03d-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="9b03d-131">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="9b03d-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="9b03d-132">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="9b03d-132">Enable authentication</span></span>

<span data-ttu-id="9b03d-133">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="9b03d-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="9b03d-134">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="9b03d-134">Layout changes</span></span>

<span data-ttu-id="9b03d-135">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="9b03d-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="9b03d-136">Scaffolding :::no-loc(Identity)::: en un :::no-loc(Razor)::: proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="9b03d-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="9b03d-137">Algunas :::no-loc(Identity)::: opciones se configuran en *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="9b03d-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9b03d-138">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="9b03d-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="9b03d-139">Scaffolding :::no-loc(Identity)::: en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="9b03d-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9b03d-140">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9b03d-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="9b03d-141">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="9b03d-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="9b03d-142">:::no-loc(Identity):::está configurado en *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="9b03d-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9b03d-143">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="9b03d-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="9b03d-144">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="9b03d-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="9b03d-145">Scaffolding :::no-loc(Identity)::: en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="9b03d-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="9b03d-146">Scaffolding :::no-loc(Identity)::: en un :::no-loc(Blazor Server)::: proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="9b03d-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9b03d-147">:::no-loc(Identity):::está configurado en *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="9b03d-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9b03d-148">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="9b03d-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="9b03d-149">Migraciones</span><span class="sxs-lookup"><span data-stu-id="9b03d-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="9b03d-150">Pasar un token XSRF a la aplicación</span><span class="sxs-lookup"><span data-stu-id="9b03d-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="9b03d-151">Los tokens se pueden pasar a los componentes:</span><span class="sxs-lookup"><span data-stu-id="9b03d-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="9b03d-152">Cuando los tokens de autenticación se aprovisionan y se guardan en la autenticación :::no-loc(cookie)::: , se pueden pasar a los componentes.</span><span class="sxs-lookup"><span data-stu-id="9b03d-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="9b03d-153">:::no-loc(Razor)::: los componentes no pueden usar `HttpContext` directamente, por lo que no hay manera de obtener un [token de falsificación de la solicitud (XSRF)](xref:security/anti-request-forgery) para publicar el :::no-loc(Identity)::: punto de conexión de cierre de sesión en `/:::no-loc(Identity):::/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="9b03d-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="9b03d-154">Un token XSRF se puede pasar a los componentes.</span><span class="sxs-lookup"><span data-stu-id="9b03d-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="9b03d-155">Para obtener más información, vea <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="9b03d-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="9b03d-156">En el archivo *pages/_Host. cshtml* , establezca el token después de agregarlo a las `InitialApplicationState` `TokenProvider` clases y:</span><span class="sxs-lookup"><span data-stu-id="9b03d-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="9b03d-157">Actualice el `App` componente ( *app. Razor* ) para asignar `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="9b03d-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="9b03d-158">El `TokenProvider` servicio que se muestra en el tema se usa en el `LoginDisplay` componente en la siguiente sección [diseño y cambios de flujo de autenticación](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="9b03d-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="9b03d-159">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="9b03d-159">Enable authentication</span></span>

<span data-ttu-id="9b03d-160">En la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="9b03d-160">In the `Startup` class:</span></span>

* <span data-ttu-id="9b03d-161">Confirme que :::no-loc(Razor)::: se han agregado los servicios de páginas en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9b03d-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="9b03d-162">Si usa [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registre el servicio.</span><span class="sxs-lookup"><span data-stu-id="9b03d-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="9b03d-163">Llame a `UseDatabaseErrorPage` en el generador de aplicaciones en `Startup.Configure` para el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="9b03d-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="9b03d-164">Llame a `UseAuthentication` y `UseAuthorization` después de `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="9b03d-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="9b03d-165">Agregue un extremo para :::no-loc(Razor)::: las páginas.</span><span class="sxs-lookup"><span data-stu-id="9b03d-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="9b03d-166">Cambios en el flujo de la autenticación y el diseño</span><span class="sxs-lookup"><span data-stu-id="9b03d-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="9b03d-167">Agregue un `RedirectToLogin` componente ( *RedirectToLogin. Razor* ) a la carpeta *compartida* de la aplicación en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="9b03d-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(":::no-loc(Identity):::/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Agregue un `LoginDisplay` componente ( *LoginDisplay. Razor* ) a la carpeta *compartida* de la aplicación. <span data-ttu-id="9b03d-169">El [servicio TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) proporciona el token XSRF para el formulario HTML que se envía al :::no-loc(Identity)::: punto de conexión de cierre de sesión:</span><span class="sxs-lookup"><span data-stu-id="9b03d-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href=":::no-loc(Identity):::/Account/Manage/Index">
            Hello, @context.User.:::no-loc(Identity):::.Name!
        </a>
        <form action="/:::no-loc(Identity):::/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href=":::no-loc(Identity):::/Account/Register">Register</a>
        <a href=":::no-loc(Identity):::/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="9b03d-170">En el `MainLayout` componente ( *Shared/MainLayout. Razor* ), agregue el `LoginDisplay` componente al `<div>` contenido del elemento de la fila superior:</span><span class="sxs-lookup"><span data-stu-id="9b03d-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="9b03d-171">Extremos de autenticación de estilo</span><span class="sxs-lookup"><span data-stu-id="9b03d-171">Style authentication endpoints</span></span>

<span data-ttu-id="9b03d-172">Dado :::no-loc(Blazor Server)::: que utiliza páginas :::no-loc(Razor)::: :::no-loc(Identity)::: de páginas, el estilo de la interfaz de usuario cambia cuando un visitante navega entre :::no-loc(Identity)::: páginas y componentes.</span><span class="sxs-lookup"><span data-stu-id="9b03d-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="9b03d-173">Tiene dos opciones para abordar los estilos de Incongruous:</span><span class="sxs-lookup"><span data-stu-id="9b03d-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="9b03d-174">Componentes de compilación :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="9b03d-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="9b03d-175">Un enfoque para utilizar los componentes de en :::no-loc(Identity)::: lugar de las páginas es crear :::no-loc(Identity)::: componentes de.</span><span class="sxs-lookup"><span data-stu-id="9b03d-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="9b03d-176">Dado `SignInManager` `UserManager` que y no se admiten en :::no-loc(Razor)::: los componentes, use puntos de conexión de API en la :::no-loc(Blazor Server)::: aplicación para procesar las acciones de la cuenta de usuario.</span><span class="sxs-lookup"><span data-stu-id="9b03d-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="9b03d-177">Usar un diseño personalizado con :::no-loc(Blazor)::: estilos de aplicación</span><span class="sxs-lookup"><span data-stu-id="9b03d-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="9b03d-178">El :::no-loc(Identity)::: diseño y los estilos de las páginas se pueden modificar para generar páginas que utilicen el :::no-loc(Blazor)::: tema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="9b03d-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="9b03d-179">El ejemplo de esta sección es simplemente un punto de partida para la personalización.</span><span class="sxs-lookup"><span data-stu-id="9b03d-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="9b03d-180">Es probable que se necesite trabajo adicional para la mejor experiencia de usuario.</span><span class="sxs-lookup"><span data-stu-id="9b03d-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="9b03d-181">Cree un nuevo `NavMenu_:::no-loc(Identity):::Layout` componente ( *Shared/NavMenu_ :::no-loc(Identity)::: layout. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="9b03d-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="9b03d-182">Para el marcado y el código del componente, use el mismo contenido del componente de la aplicación `NavMenu` ( *Shared/NavMenu. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="9b03d-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="9b03d-183">Desseccione cualquier `NavLink` s en componentes que no se puedan alcanzar de forma anónima porque las redirecciones automáticas del `RedirectToLogin` componente no se realizan correctamente para los componentes que requieren autenticación o autorización.</span><span class="sxs-lookup"><span data-stu-id="9b03d-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="9b03d-184">En el archivo *pages/Shared/layout. cshtml* , realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="9b03d-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="9b03d-185">Agregue :::no-loc(Razor)::: directivas a la parte superior del archivo para usar las aplicaciones auxiliares de etiquetas y los componentes de la aplicación en la carpeta *compartida* :</span><span class="sxs-lookup"><span data-stu-id="9b03d-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="9b03d-186">Reemplace `{APPLICATION ASSEMBLY}` con el nombre de ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9b03d-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="9b03d-187">Agregue una `<base>` etiqueta y una :::no-loc(Blazor)::: hoja de estilos `<link>` al `<head>` contenido:</span><span class="sxs-lookup"><span data-stu-id="9b03d-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="9b03d-188">Cambie el contenido de la `<body>` etiqueta a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9b03d-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_:::no-loc(Identity):::Layout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default :::no-loc(Identity)::: UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/:::no-loc(Identity):::/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/:::no-loc(Identity):::/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/:::no-loc(Identity):::/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="9b03d-189">Scaffolding :::no-loc(Identity)::: en un :::no-loc(Blazor Server)::: proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="9b03d-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="9b03d-190">Algunas :::no-loc(Identity)::: opciones se configuran en *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="9b03d-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9b03d-191">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="9b03d-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="9b03d-192">Crear :::no-loc(Identity)::: origen de IU completo</span><span class="sxs-lookup"><span data-stu-id="9b03d-192">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="9b03d-193">Para mantener el control total de la :::no-loc(Identity)::: interfaz de usuario, ejecute el :::no-loc(Identity)::: scaffolding y seleccione **invalidar todos los archivos** .</span><span class="sxs-lookup"><span data-stu-id="9b03d-193">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="9b03d-194">En el código resaltado siguiente se muestran los cambios para reemplazar la :::no-loc(Identity)::: interfaz de usuario predeterminada por :::no-loc(Identity)::: en una aplicación web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="9b03d-194">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="9b03d-195">Puede que desee hacer esto para tener un control total de la :::no-loc(Identity)::: interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="9b03d-195">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="9b03d-196">El valor predeterminado :::no-loc(Identity)::: se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="9b03d-196">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="9b03d-197">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="9b03d-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="9b03d-198">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b03d-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="9b03d-199">Configuración de contraseña</span><span class="sxs-lookup"><span data-stu-id="9b03d-199">Password configuration</span></span>

<span data-ttu-id="9b03d-200">Si <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> se configuran en `Startup.ConfigureServices` , la configuración de [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) podría ser necesaria para la `Password` propiedad en :::no-loc(Identity)::: las páginas con scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9b03d-200">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="9b03d-201">`InputModel`las `Password` propiedades se encuentran en los siguientes archivos:</span><span class="sxs-lookup"><span data-stu-id="9b03d-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="9b03d-202">Deshabilitar una página</span><span class="sxs-lookup"><span data-stu-id="9b03d-202">Disable a page</span></span>

<span data-ttu-id="9b03d-203">En esta sección se muestra cómo deshabilitar la página de registro, pero se puede usar el enfoque para deshabilitar cualquier página.</span><span class="sxs-lookup"><span data-stu-id="9b03d-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="9b03d-204">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="9b03d-204">To disable user registration:</span></span>

* <span data-ttu-id="9b03d-205">Scaffold :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="9b03d-205">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="9b03d-206">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="9b03d-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="9b03d-207">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b03d-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="9b03d-208">Actualice *areas/ :::no-loc(Identity)::: /pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="9b03d-208">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="9b03d-209">Actualice *areas/ :::no-loc(Identity)::: /pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="9b03d-209">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="9b03d-210">Comente o quite el vínculo de registro de las *áreas/ :::no-loc(Identity)::: /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="9b03d-210">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="9b03d-211">Actualice la página *áreas/ :::no-loc(Identity)::: /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="9b03d-211">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="9b03d-212">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="9b03d-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="9b03d-213">Quite el código de confirmación de `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="9b03d-213">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="9b03d-214">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="9b03d-214">Use another app to add users</span></span>

<span data-ttu-id="9b03d-215">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="9b03d-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="9b03d-216">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="9b03d-216">Options to add users include:</span></span>

* <span data-ttu-id="9b03d-217">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="9b03d-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="9b03d-218">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="9b03d-218">A console app.</span></span>

<span data-ttu-id="9b03d-219">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="9b03d-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="9b03d-220">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="9b03d-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="9b03d-221">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="9b03d-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="9b03d-222">El usuario se agrega a la :::no-loc(Identity)::: base de datos.</span><span class="sxs-lookup"><span data-stu-id="9b03d-222">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="9b03d-223">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="9b03d-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="9b03d-224">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="9b03d-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="9b03d-225">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="9b03d-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="9b03d-226">Impedir la publicación de :::no-loc(Identity)::: recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="9b03d-226">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="9b03d-227">Para evitar la publicación de recursos estáticos :::no-loc(Identity)::: en la raíz Web, vea <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="9b03d-227">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b03d-228">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9b03d-228">Additional resources</span></span>

* [<span data-ttu-id="9b03d-229">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="9b03d-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9b03d-230">ASP.NET Core 2,1 y las versiones posteriores proporcionan [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) como [ :::no-loc(Razor)::: biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="9b03d-230">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="9b03d-231">Las aplicaciones que incluyen :::no-loc(Identity)::: pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de clases (RCL).</span><span class="sxs-lookup"><span data-stu-id="9b03d-231">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="9b03d-232">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="9b03d-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="9b03d-233">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="9b03d-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="9b03d-234">El código generado tiene prioridad sobre el mismo código de la RCL de :::no-loc(Identity):::.</span><span class="sxs-lookup"><span data-stu-id="9b03d-234">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="9b03d-235">Para obtener el control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un origen de la [interfaz de usuario de identidad completa](#full).</span><span class="sxs-lookup"><span data-stu-id="9b03d-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="9b03d-236">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="9b03d-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="9b03d-237">Tiene la opción de seleccionar el código de :::no-loc(Identity)::: que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="9b03d-237">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="9b03d-238">Aunque el scaffolding genera la mayor parte del código necesario, tendrá que actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="9b03d-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="9b03d-239">En este documento se explican los pasos necesarios para completar una :::no-loc(Identity)::: actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9b03d-239">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="9b03d-240">Cuando :::no-loc(Identity)::: se ejecuta el scaffolding, se crea un archivo de *ScaffoldingReadme.txt* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="9b03d-240">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="9b03d-241">El archivo de *ScaffoldingReadme.txt* contiene instrucciones generales sobre lo que se necesita para completar la :::no-loc(Identity)::: actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9b03d-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="9b03d-242">Este documento contiene instrucciones más completas que el archivo de *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="9b03d-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="9b03d-243">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="9b03d-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="9b03d-244">Inspeccione los cambios después de ejecutar el :::no-loc(Identity)::: scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9b03d-244">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="9b03d-245">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="9b03d-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="9b03d-246">Los servicios o los códigos auxiliares de servicio no se generan al aplicar scaffolding :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="9b03d-246">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="9b03d-247">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="9b03d-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="9b03d-248">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="9b03d-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="9b03d-249">Scaffolding :::no-loc(Identity)::: en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="9b03d-249">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9b03d-250">Agregue las siguientes llamadas resaltadas a la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="9b03d-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="9b03d-251">Scaffolding :::no-loc(Identity)::: en un :::no-loc(Razor)::: proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="9b03d-251">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9b03d-252">:::no-loc(Identity):::está configurado en *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="9b03d-252">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9b03d-253">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="9b03d-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="9b03d-254">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="9b03d-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="9b03d-255">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="9b03d-255">Enable authentication</span></span>

<span data-ttu-id="9b03d-256">En el `Configure` método de la `Startup` clase, llame a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) después de `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="9b03d-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="9b03d-257">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="9b03d-257">Layout changes</span></span>

<span data-ttu-id="9b03d-258">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="9b03d-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="9b03d-259">Scaffolding :::no-loc(Identity)::: en un :::no-loc(Razor)::: proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="9b03d-259">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="9b03d-260">Algunas :::no-loc(Identity)::: opciones se configuran en *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="9b03d-260">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9b03d-261">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="9b03d-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="9b03d-262">Scaffolding :::no-loc(Identity)::: en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="9b03d-262">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9b03d-263">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9b03d-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="9b03d-264">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="9b03d-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="9b03d-265">:::no-loc(Identity):::está configurado en *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="9b03d-265">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9b03d-266">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="9b03d-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="9b03d-267">Llame a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) después de `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="9b03d-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="9b03d-268">Scaffolding :::no-loc(Identity)::: en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="9b03d-268">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="9b03d-269">Elimine las *páginas/carpetas compartidas* y los archivos de esa carpeta.</span><span class="sxs-lookup"><span data-stu-id="9b03d-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="9b03d-270">Crear :::no-loc(Identity)::: origen de IU completo</span><span class="sxs-lookup"><span data-stu-id="9b03d-270">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="9b03d-271">Para mantener el control total de la :::no-loc(Identity)::: interfaz de usuario, ejecute el :::no-loc(Identity)::: scaffolding y seleccione **invalidar todos los archivos** .</span><span class="sxs-lookup"><span data-stu-id="9b03d-271">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="9b03d-272">En el código resaltado siguiente se muestran los cambios para reemplazar la :::no-loc(Identity)::: interfaz de usuario predeterminada por :::no-loc(Identity)::: en una aplicación web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="9b03d-272">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="9b03d-273">Puede que desee hacer esto para tener un control total de la :::no-loc(Identity)::: interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="9b03d-273">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="9b03d-274">El valor predeterminado :::no-loc(Identity)::: se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="9b03d-274">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="9b03d-275">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="9b03d-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="9b03d-276">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b03d-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="9b03d-277">Configuración de contraseña</span><span class="sxs-lookup"><span data-stu-id="9b03d-277">Password configuration</span></span>

<span data-ttu-id="9b03d-278">Si <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> se configuran en `Startup.ConfigureServices` , la configuración de [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) podría ser necesaria para la `Password` propiedad en :::no-loc(Identity)::: las páginas con scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9b03d-278">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="9b03d-279">`InputModel`las `Password` propiedades se encuentran en los siguientes archivos:</span><span class="sxs-lookup"><span data-stu-id="9b03d-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="9b03d-280">Deshabilitar registro (página)</span><span class="sxs-lookup"><span data-stu-id="9b03d-280">Disable register page</span></span>

<span data-ttu-id="9b03d-281">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="9b03d-281">To disable user registration:</span></span>

* <span data-ttu-id="9b03d-282">Scaffold :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="9b03d-282">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="9b03d-283">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="9b03d-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="9b03d-284">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9b03d-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="9b03d-285">Actualice *areas/ :::no-loc(Identity)::: /pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="9b03d-285">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="9b03d-286">Actualice *areas/ :::no-loc(Identity)::: /pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="9b03d-286">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="9b03d-287">Comente o quite el vínculo de registro de las *áreas/ :::no-loc(Identity)::: /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="9b03d-287">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="9b03d-288">Actualice la página *áreas/ :::no-loc(Identity)::: /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="9b03d-288">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="9b03d-289">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="9b03d-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="9b03d-290">Quite el código de confirmación de `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="9b03d-290">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="9b03d-291">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="9b03d-291">Use another app to add users</span></span>

<span data-ttu-id="9b03d-292">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="9b03d-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="9b03d-293">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="9b03d-293">Options to add users include:</span></span>

* <span data-ttu-id="9b03d-294">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="9b03d-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="9b03d-295">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="9b03d-295">A console app.</span></span>

<span data-ttu-id="9b03d-296">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="9b03d-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="9b03d-297">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="9b03d-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="9b03d-298">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="9b03d-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="9b03d-299">El usuario se agrega a la :::no-loc(Identity)::: base de datos.</span><span class="sxs-lookup"><span data-stu-id="9b03d-299">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="9b03d-300">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="9b03d-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="9b03d-301">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="9b03d-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="9b03d-302">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="9b03d-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b03d-303">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9b03d-303">Additional resources</span></span>

* [<span data-ttu-id="9b03d-304">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="9b03d-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
