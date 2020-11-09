---
title: 'Scaffolding Identity en proyectos de ASP.net Core'
author: rick-anderson
description: 'Obtenga información sobre cómo aplicar la técnica scaffolding Identity en un proyecto ASP.net Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141500"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="c34a6-103">Scaffolding Identity en proyectos de ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="c34a6-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="c34a6-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c34a6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c34a6-105">ASP.NET Core proporciona [ASP.NET Core Identity](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="c34a6-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="c34a6-106">Las aplicaciones que incluyen Identity pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la Identity Razor biblioteca de clases (RCL).</span><span class="sxs-lookup"><span data-stu-id="c34a6-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="c34a6-107">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="c34a6-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="c34a6-108">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="c34a6-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="c34a6-109">El código generado tiene prioridad sobre el mismo código de la RCL de Identity.</span><span class="sxs-lookup"><span data-stu-id="c34a6-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="c34a6-110">Para obtener un control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un [ Identity origen de IU completo](#full).</span><span class="sxs-lookup"><span data-stu-id="c34a6-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="c34a6-111">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL Identity .</span><span class="sxs-lookup"><span data-stu-id="c34a6-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="c34a6-112">Tiene la opción de seleccionar el código de Identity que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="c34a6-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="c34a6-113">Aunque el scaffolding genera la mayor parte del código necesario, debe actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="c34a6-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="c34a6-114">En este documento se explican los pasos necesarios para completar una Identity actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c34a6-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="c34a6-115">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="c34a6-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="c34a6-116">Inspeccione los cambios después de ejecutar el Identity scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c34a6-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="c34a6-117">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con Identity .</span><span class="sxs-lookup"><span data-stu-id="c34a6-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="c34a6-118">Los servicios o los códigos auxiliares de servicio no se generan al aplicar scaffolding Identity .</span><span class="sxs-lookup"><span data-stu-id="c34a6-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="c34a6-119">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="c34a6-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="c34a6-120">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="c34a6-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="c34a6-121">Cuando se scaffolding Identity con un nuevo contexto de datos en un proyecto con cuentas individuales existentes:</span><span class="sxs-lookup"><span data-stu-id="c34a6-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="c34a6-122">En `Startup.ConfigureServices` , quite las llamadas a:</span><span class="sxs-lookup"><span data-stu-id="c34a6-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="c34a6-123">Por ejemplo, `AddDbContext` y `AddDefaultIdentity` están comentados en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c34a6-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="c34a6-124">El código anterior comenta el código que está duplicado en *areas/ Identity / Identity HostingStartup.CS*</span><span class="sxs-lookup"><span data-stu-id="c34a6-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="c34a6-125">Normalmente, las aplicaciones que se crearon con cuentas individuales deben \* **no** _ crear un nuevo contexto de datos.</span><span class="sxs-lookup"><span data-stu-id="c34a6-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="c34a6-126">Scaffolding Identity en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="c34a6-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="c34a6-127">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="c34a6-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="c34a6-128">Scaffolding Identity en un Razor proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="c34a6-128">Scaffold Identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="c34a6-129">Identityestá configurado en _Areas/ Identity / Identity HostingStartup.cs \*.</span><span class="sxs-lookup"><span data-stu-id="c34a6-129">Identity is configured in _Areas/Identity/IdentityHostingStartup.cs\*.</span></span> <span data-ttu-id="c34a6-130">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="c34a6-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="c34a6-131">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="c34a6-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="c34a6-132">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="c34a6-132">Enable authentication</span></span>

<span data-ttu-id="c34a6-133">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="c34a6-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="c34a6-134">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="c34a6-134">Layout changes</span></span>

<span data-ttu-id="c34a6-135">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="c34a6-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="c34a6-136">Scaffolding Identity en un Razor proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="c34a6-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="c34a6-137">Algunas Identity opciones se configuran en *areas/ Identity / Identity HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="c34a6-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="c34a6-138">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="c34a6-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="c34a6-139">Scaffolding Identity en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="c34a6-139">Scaffold Identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="c34a6-140">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c34a6-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="c34a6-141">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c34a6-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="c34a6-142">Identityestá configurado en *areas/ Identity / Identity HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="c34a6-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="c34a6-143">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="c34a6-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="c34a6-144">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="c34a6-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="c34a6-145">Scaffolding Identity en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="c34a6-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="c34a6-146">Scaffolding Identity en un Blazor Server proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="c34a6-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="c34a6-147">Identityestá configurado en *areas/ Identity / Identity HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="c34a6-147">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="c34a6-148">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="c34a6-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="c34a6-149">Migraciones</span><span class="sxs-lookup"><span data-stu-id="c34a6-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="c34a6-150">Pasar un token XSRF a la aplicación</span><span class="sxs-lookup"><span data-stu-id="c34a6-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="c34a6-151">Los tokens se pueden pasar a los componentes:</span><span class="sxs-lookup"><span data-stu-id="c34a6-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="c34a6-152">Cuando los tokens de autenticación se aprovisionan y se guardan en la autenticación cookie , se pueden pasar a los componentes.</span><span class="sxs-lookup"><span data-stu-id="c34a6-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* <span data-ttu-id="c34a6-153">Razor los componentes no pueden usar `HttpContext` directamente, por lo que no hay manera de obtener un [token de falsificación de la solicitud (XSRF)](xref:security/anti-request-forgery) para publicar el Identity punto de conexión de cierre de sesión en `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="c34a6-153">Razor components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="c34a6-154">Un token XSRF se puede pasar a los componentes.</span><span class="sxs-lookup"><span data-stu-id="c34a6-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="c34a6-155">Para obtener más información, vea <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="c34a6-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="c34a6-156">En el archivo *pages/_Host. cshtml* , establezca el token después de agregarlo a las `InitialApplicationState` `TokenProvider` clases y:</span><span class="sxs-lookup"><span data-stu-id="c34a6-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="c34a6-157">Actualice el `App` componente ( *app. Razor* ) para asignar `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="c34a6-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="c34a6-158">El `TokenProvider` servicio que se muestra en el tema se usa en el `LoginDisplay` componente en la siguiente sección [diseño y cambios de flujo de autenticación](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="c34a6-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="c34a6-159">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="c34a6-159">Enable authentication</span></span>

<span data-ttu-id="c34a6-160">En la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="c34a6-160">In the `Startup` class:</span></span>

* <span data-ttu-id="c34a6-161">Confirme que Razor se han agregado los servicios de páginas en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c34a6-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="c34a6-162">Si usa [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registre el servicio.</span><span class="sxs-lookup"><span data-stu-id="c34a6-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="c34a6-163">Llame a `UseDatabaseErrorPage` en el generador de aplicaciones en `Startup.Configure` para el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="c34a6-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="c34a6-164">Llame a `UseAuthentication` y `UseAuthorization` después de `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="c34a6-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="c34a6-165">Agregue un extremo para Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="c34a6-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="c34a6-166">Cambios en el flujo de la autenticación y el diseño</span><span class="sxs-lookup"><span data-stu-id="c34a6-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="c34a6-167">Agregue un `RedirectToLogin` componente ( *RedirectToLogin. Razor* ) a la carpeta *compartida* de la aplicación en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="c34a6-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Agregue un `LoginDisplay` componente ( *LoginDisplay. Razor* ) a la carpeta *compartida* de la aplicación. <span data-ttu-id="c34a6-169">El [servicio TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) proporciona el token XSRF para el formulario HTML que se envía al Identity punto de conexión de cierre de sesión:</span><span class="sxs-lookup"><span data-stu-id="c34a6-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="c34a6-170">En el `MainLayout` componente ( *Shared/MainLayout. Razor* ), agregue el `LoginDisplay` componente al `<div>` contenido del elemento de la fila superior:</span><span class="sxs-lookup"><span data-stu-id="c34a6-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="c34a6-171">Extremos de autenticación de estilo</span><span class="sxs-lookup"><span data-stu-id="c34a6-171">Style authentication endpoints</span></span>

<span data-ttu-id="c34a6-172">Dado Blazor Server que utiliza páginas Razor Identity de páginas, el estilo de la interfaz de usuario cambia cuando un visitante navega entre Identity páginas y componentes.</span><span class="sxs-lookup"><span data-stu-id="c34a6-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="c34a6-173">Tiene dos opciones para abordar los estilos de Incongruous:</span><span class="sxs-lookup"><span data-stu-id="c34a6-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="c34a6-174">Componentes de compilación Identity</span><span class="sxs-lookup"><span data-stu-id="c34a6-174">Build Identity components</span></span>

<span data-ttu-id="c34a6-175">Un enfoque para utilizar los componentes de en Identity lugar de las páginas es crear Identity componentes de.</span><span class="sxs-lookup"><span data-stu-id="c34a6-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="c34a6-176">Dado `SignInManager` `UserManager` que y no se admiten en Razor los componentes, use puntos de conexión de API en la Blazor Server aplicación para procesar las acciones de la cuenta de usuario.</span><span class="sxs-lookup"><span data-stu-id="c34a6-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="c34a6-177">Usar un diseño personalizado con Blazor estilos de aplicación</span><span class="sxs-lookup"><span data-stu-id="c34a6-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="c34a6-178">El Identity diseño y los estilos de las páginas se pueden modificar para generar páginas que utilicen el Blazor tema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="c34a6-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="c34a6-179">El ejemplo de esta sección es simplemente un punto de partida para la personalización.</span><span class="sxs-lookup"><span data-stu-id="c34a6-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="c34a6-180">Es probable que se necesite trabajo adicional para la mejor experiencia de usuario.</span><span class="sxs-lookup"><span data-stu-id="c34a6-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="c34a6-181">Cree un nuevo `NavMenu_IdentityLayout` componente ( *Shared/NavMenu_ Identity layout. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="c34a6-181">Create a new `NavMenu_IdentityLayout` component ( *Shared/NavMenu_IdentityLayout.razor* ).</span></span> <span data-ttu-id="c34a6-182">Para el marcado y el código del componente, use el mismo contenido del componente de la aplicación `NavMenu` ( *Shared/NavMenu. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="c34a6-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="c34a6-183">Desseccione cualquier `NavLink` s en componentes que no se puedan alcanzar de forma anónima porque las redirecciones automáticas del `RedirectToLogin` componente no se realizan correctamente para los componentes que requieren autenticación o autorización.</span><span class="sxs-lookup"><span data-stu-id="c34a6-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="c34a6-184">En el archivo *pages/Shared/layout. cshtml* , realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="c34a6-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="c34a6-185">Agregue Razor directivas a la parte superior del archivo para usar las aplicaciones auxiliares de etiquetas y los componentes de la aplicación en la carpeta *compartida* :</span><span class="sxs-lookup"><span data-stu-id="c34a6-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="c34a6-186">Reemplace `{APPLICATION ASSEMBLY}` con el nombre de ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c34a6-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="c34a6-187">Agregue una `<base>` etiqueta y una Blazor hoja de estilos `<link>` al `<head>` contenido:</span><span class="sxs-lookup"><span data-stu-id="c34a6-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="c34a6-188">Cambie el contenido de la `<body>` etiqueta a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c34a6-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
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
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="c34a6-189">Scaffolding Identity en un Blazor Server proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="c34a6-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="c34a6-190">Algunas Identity opciones se configuran en *areas/ Identity / Identity HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="c34a6-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="c34a6-191">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="c34a6-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a><span data-ttu-id="c34a6-192">Aplicaciones independientes u hospedadas Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c34a6-192">Standalone or hosted Blazor WebAssembly apps</span></span>

<span data-ttu-id="c34a6-193">Las aplicaciones del lado cliente Blazor WebAssembly usan sus propios Identity enfoques de la interfaz de usuario y no pueden usar la ASP.NET Core Identity técnica scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c34a6-193">Client-side Blazor WebAssembly apps use their own Identity UI approaches and can't use ASP.NET Core Identity scaffolding.</span></span> <span data-ttu-id="c34a6-194">Las aplicaciones de ASP.NET Core del lado servidor de soluciones hospedadas Blazor pueden seguir las Razor instrucciones de páginas o MVC de este artículo y se configuran como cualquier otro tipo de aplicación ASP.net Core que admita Identity .</span><span class="sxs-lookup"><span data-stu-id="c34a6-194">Server-side ASP.NET Core apps of hosted Blazor solutions can follow the Razor Pages/MVC guidance in this article and are configured just like any other type of ASP.NET Core app that supports Identity.</span></span>

<span data-ttu-id="c34a6-195">El Blazor marco no incluye Razor versiones de componentes de Identity las páginas de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="c34a6-195">The Blazor framework doesn't include Razor component versions of Identity UI pages.</span></span> <span data-ttu-id="c34a6-196">IdentityRazorLos componentes de interfaz de usuario se pueden compilar u obtener de orígenes de terceros no compatibles.</span><span class="sxs-lookup"><span data-stu-id="c34a6-196">Identity UI Razor components can be custom built or obtained from unsupported third-party sources.</span></span>

<span data-ttu-id="c34a6-197">Para obtener más información, consulte la [ Blazor seguridad y los Identity artículos](xref:blazor/security/index).</span><span class="sxs-lookup"><span data-stu-id="c34a6-197">For more information, see the [Blazor Security and Identity articles](xref:blazor/security/index).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="c34a6-198">Crear Identity origen de IU completo</span><span class="sxs-lookup"><span data-stu-id="c34a6-198">Create full Identity UI source</span></span>

<span data-ttu-id="c34a6-199">Para mantener el control total de la Identity interfaz de usuario, ejecute el Identity scaffolding y seleccione **invalidar todos los archivos** .</span><span class="sxs-lookup"><span data-stu-id="c34a6-199">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="c34a6-200">En el código resaltado siguiente se muestran los cambios para reemplazar la Identity interfaz de usuario predeterminada por Identity en una aplicación web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="c34a6-200">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="c34a6-201">Puede que desee hacer esto para tener un control total de la Identity interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="c34a6-201">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="c34a6-202">El valor predeterminado Identity se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c34a6-202">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="c34a6-203">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="c34a6-203">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="c34a6-204">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c34a6-204">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="c34a6-205">Configuración de contraseña</span><span class="sxs-lookup"><span data-stu-id="c34a6-205">Password configuration</span></span>

<span data-ttu-id="c34a6-206">Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> se configuran en `Startup.ConfigureServices` , la configuración de [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) podría ser necesaria para la `Password` propiedad en Identity las páginas con scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c34a6-206">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="c34a6-207">`InputModel`las `Password` propiedades se encuentran en los siguientes archivos:</span><span class="sxs-lookup"><span data-stu-id="c34a6-207">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="c34a6-208">Deshabilitar una página</span><span class="sxs-lookup"><span data-stu-id="c34a6-208">Disable a page</span></span>

<span data-ttu-id="c34a6-209">En esta sección se muestra cómo deshabilitar la página de registro, pero se puede usar el enfoque para deshabilitar cualquier página.</span><span class="sxs-lookup"><span data-stu-id="c34a6-209">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="c34a6-210">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="c34a6-210">To disable user registration:</span></span>

* <span data-ttu-id="c34a6-211">Scaffold Identity .</span><span class="sxs-lookup"><span data-stu-id="c34a6-211">Scaffold Identity.</span></span> <span data-ttu-id="c34a6-212">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="c34a6-212">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="c34a6-213">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c34a6-213">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="c34a6-214">Actualice *areas/ Identity /pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="c34a6-214">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="c34a6-215">Actualice *areas/ Identity /pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="c34a6-215">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="c34a6-216">Comente o quite el vínculo de registro de las *áreas/ Identity /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c34a6-216">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="c34a6-217">Actualice la página *áreas/ Identity /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="c34a6-217">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="c34a6-218">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="c34a6-218">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="c34a6-219">Quite el código de confirmación de `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="c34a6-219">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="c34a6-220">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="c34a6-220">Use another app to add users</span></span>

<span data-ttu-id="c34a6-221">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="c34a6-221">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="c34a6-222">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="c34a6-222">Options to add users include:</span></span>

* <span data-ttu-id="c34a6-223">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="c34a6-223">A dedicated admin web app.</span></span>
* <span data-ttu-id="c34a6-224">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="c34a6-224">A console app.</span></span>

<span data-ttu-id="c34a6-225">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="c34a6-225">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="c34a6-226">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="c34a6-226">A list of users is read into memory.</span></span>
* <span data-ttu-id="c34a6-227">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="c34a6-227">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="c34a6-228">El usuario se agrega a la Identity base de datos.</span><span class="sxs-lookup"><span data-stu-id="c34a6-228">The user is added to the Identity database.</span></span>
* <span data-ttu-id="c34a6-229">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="c34a6-229">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="c34a6-230">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="c34a6-230">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="c34a6-231">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="c34a6-231">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="c34a6-232">Impedir la publicación de Identity recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="c34a6-232">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="c34a6-233">Para evitar la publicación de recursos estáticos Identity en la raíz Web, vea <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="c34a6-233">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c34a6-234">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c34a6-234">Additional resources</span></span>

* [<span data-ttu-id="c34a6-235">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="c34a6-235">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c34a6-236">ASP.NET Core 2,1 y las versiones posteriores proporcionan [ASP.NET Core Identity](xref:security/authentication/identity) como [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="c34a6-236">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="c34a6-237">Las aplicaciones que incluyen Identity pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la Identity Razor biblioteca de clases (RCL).</span><span class="sxs-lookup"><span data-stu-id="c34a6-237">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="c34a6-238">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="c34a6-238">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="c34a6-239">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="c34a6-239">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="c34a6-240">El código generado tiene prioridad sobre el mismo código de la RCL de Identity.</span><span class="sxs-lookup"><span data-stu-id="c34a6-240">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="c34a6-241">Para obtener el control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un origen de la [interfaz de usuario de identidad completa](#full).</span><span class="sxs-lookup"><span data-stu-id="c34a6-241">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="c34a6-242">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL Identity .</span><span class="sxs-lookup"><span data-stu-id="c34a6-242">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="c34a6-243">Tiene la opción de seleccionar el código de Identity que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="c34a6-243">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="c34a6-244">Aunque el scaffolding genera la mayor parte del código necesario, tendrá que actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="c34a6-244">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="c34a6-245">En este documento se explican los pasos necesarios para completar una Identity actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c34a6-245">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="c34a6-246">Cuando Identity se ejecuta el scaffolding, se crea un archivo de *ScaffoldingReadme.txt* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c34a6-246">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="c34a6-247">El archivo de *ScaffoldingReadme.txt* contiene instrucciones generales sobre lo que se necesita para completar la Identity actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c34a6-247">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="c34a6-248">Este documento contiene instrucciones más completas que el archivo de *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="c34a6-248">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="c34a6-249">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="c34a6-249">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="c34a6-250">Inspeccione los cambios después de ejecutar el Identity scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c34a6-250">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="c34a6-251">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con Identity .</span><span class="sxs-lookup"><span data-stu-id="c34a6-251">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="c34a6-252">Los servicios o los códigos auxiliares de servicio no se generan al aplicar scaffolding Identity .</span><span class="sxs-lookup"><span data-stu-id="c34a6-252">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="c34a6-253">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="c34a6-253">Services to enable these features must be added manually.</span></span> <span data-ttu-id="c34a6-254">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="c34a6-254">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="c34a6-255">Scaffolding Identity en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="c34a6-255">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="c34a6-256">Agregue las siguientes llamadas resaltadas a la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="c34a6-256">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="c34a6-257">Scaffolding Identity en un Razor proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="c34a6-257">Scaffold Identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="c34a6-258">Identityestá configurado en *areas/ Identity / Identity HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="c34a6-258">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="c34a6-259">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="c34a6-259">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="c34a6-260">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="c34a6-260">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="c34a6-261">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="c34a6-261">Enable authentication</span></span>

<span data-ttu-id="c34a6-262">En el `Configure` método de la `Startup` clase, llame a <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> después de `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="c34a6-262">In the `Configure` method of the `Startup` class, call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="c34a6-263">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="c34a6-263">Layout changes</span></span>

<span data-ttu-id="c34a6-264">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="c34a6-264">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="c34a6-265">Scaffolding Identity en un Razor proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="c34a6-265">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="c34a6-266">Algunas Identity opciones se configuran en *areas/ Identity / Identity HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="c34a6-266">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="c34a6-267">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="c34a6-267">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="c34a6-268">Scaffolding Identity en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="c34a6-268">Scaffold Identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="c34a6-269">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c34a6-269">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="c34a6-270">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c34a6-270">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="c34a6-271">Identityestá configurado en *areas/ Identity / Identity HostingStartup.CS* .</span><span class="sxs-lookup"><span data-stu-id="c34a6-271">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="c34a6-272">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="c34a6-272">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="c34a6-273">Llamar <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> después de `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="c34a6-273">Call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="c34a6-274">Scaffolding Identity en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="c34a6-274">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="c34a6-275">Elimine las *páginas/carpetas compartidas* y los archivos de esa carpeta.</span><span class="sxs-lookup"><span data-stu-id="c34a6-275">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="c34a6-276">Crear Identity origen de IU completo</span><span class="sxs-lookup"><span data-stu-id="c34a6-276">Create full Identity UI source</span></span>

<span data-ttu-id="c34a6-277">Para mantener el control total de la Identity interfaz de usuario, ejecute el Identity scaffolding y seleccione **invalidar todos los archivos** .</span><span class="sxs-lookup"><span data-stu-id="c34a6-277">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="c34a6-278">En el código resaltado siguiente se muestran los cambios para reemplazar la Identity interfaz de usuario predeterminada por Identity en una aplicación web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="c34a6-278">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="c34a6-279">Puede que desee hacer esto para tener un control total de la Identity interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="c34a6-279">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="c34a6-280">El valor predeterminado Identity se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c34a6-280">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="c34a6-281">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="c34a6-281">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="c34a6-282">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c34a6-282">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="c34a6-283">Configuración de contraseña</span><span class="sxs-lookup"><span data-stu-id="c34a6-283">Password configuration</span></span>

<span data-ttu-id="c34a6-284">Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> se configuran en `Startup.ConfigureServices` , la configuración de [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) podría ser necesaria para la `Password` propiedad en Identity las páginas con scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c34a6-284">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="c34a6-285">`InputModel`las `Password` propiedades se encuentran en los siguientes archivos:</span><span class="sxs-lookup"><span data-stu-id="c34a6-285">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="c34a6-286">Deshabilitar registro (página)</span><span class="sxs-lookup"><span data-stu-id="c34a6-286">Disable register page</span></span>

<span data-ttu-id="c34a6-287">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="c34a6-287">To disable user registration:</span></span>

* <span data-ttu-id="c34a6-288">Scaffold Identity .</span><span class="sxs-lookup"><span data-stu-id="c34a6-288">Scaffold Identity.</span></span> <span data-ttu-id="c34a6-289">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="c34a6-289">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="c34a6-290">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c34a6-290">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="c34a6-291">Actualice *areas/ Identity /pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="c34a6-291">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="c34a6-292">Actualice *areas/ Identity /pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="c34a6-292">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="c34a6-293">Comente o quite el vínculo de registro de las *áreas/ Identity /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c34a6-293">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="c34a6-294">Actualice la página *áreas/ Identity /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="c34a6-294">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="c34a6-295">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="c34a6-295">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="c34a6-296">Quite el código de confirmación de `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="c34a6-296">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="c34a6-297">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="c34a6-297">Use another app to add users</span></span>

<span data-ttu-id="c34a6-298">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="c34a6-298">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="c34a6-299">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="c34a6-299">Options to add users include:</span></span>

* <span data-ttu-id="c34a6-300">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="c34a6-300">A dedicated admin web app.</span></span>
* <span data-ttu-id="c34a6-301">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="c34a6-301">A console app.</span></span>

<span data-ttu-id="c34a6-302">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="c34a6-302">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="c34a6-303">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="c34a6-303">A list of users is read into memory.</span></span>
* <span data-ttu-id="c34a6-304">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="c34a6-304">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="c34a6-305">El usuario se agrega a la Identity base de datos.</span><span class="sxs-lookup"><span data-stu-id="c34a6-305">The user is added to the Identity database.</span></span>
* <span data-ttu-id="c34a6-306">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="c34a6-306">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="c34a6-307">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="c34a6-307">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="c34a6-308">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="c34a6-308">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c34a6-309">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c34a6-309">Additional resources</span></span>

* [<span data-ttu-id="c34a6-310">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="c34a6-310">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
