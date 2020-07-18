---
title: Scaffolding Identity en proyectos de ASP.net Core
author: rick-anderson
description: Obtenga información sobre cómo aplicar la técnica scaffolding Identity en un proyecto ASP.net Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: a8ca520d84d382b95cd4c0e2962ba4e5c922049e
ms.sourcegitcommit: 3544941682869734ea0113e24e02ed0ec9e1a9ec
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2020
ms.locfileid: "86464571"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="8362b-103">Scaffolding Identity en proyectos de ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="8362b-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="8362b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8362b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8362b-105">ASP.NET Core proporciona [ASP.net Core Identity ](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="8362b-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="8362b-106">Las aplicaciones que incluyen Identity pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la Identity Razor biblioteca de clases (RCL).</span><span class="sxs-lookup"><span data-stu-id="8362b-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="8362b-107">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="8362b-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="8362b-108">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="8362b-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="8362b-109">El código generado tiene prioridad sobre el mismo código de la RCL de Identity.</span><span class="sxs-lookup"><span data-stu-id="8362b-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="8362b-110">Para obtener un control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un [ Identity origen de IU completo](#full).</span><span class="sxs-lookup"><span data-stu-id="8362b-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="8362b-111">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL Identity .</span><span class="sxs-lookup"><span data-stu-id="8362b-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="8362b-112">Tiene la opción de seleccionar el código de Identity que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="8362b-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="8362b-113">Aunque el scaffolding genera la mayor parte del código necesario, debe actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="8362b-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="8362b-114">En este documento se explican los pasos necesarios para completar una Identity actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8362b-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="8362b-115">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="8362b-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="8362b-116">Inspeccione los cambios después de ejecutar el Identity scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8362b-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="8362b-117">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con Identity .</span><span class="sxs-lookup"><span data-stu-id="8362b-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="8362b-118">Los servicios o los códigos auxiliares de servicio no se generan al aplicar scaffolding Identity .</span><span class="sxs-lookup"><span data-stu-id="8362b-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="8362b-119">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="8362b-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="8362b-120">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="8362b-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="8362b-121">Cuando se scaffolding Identity con un nuevo contexto de datos en un proyecto con cuentas individuales existentes:</span><span class="sxs-lookup"><span data-stu-id="8362b-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="8362b-122">En `Startup.ConfigureServices` , quite las llamadas a:</span><span class="sxs-lookup"><span data-stu-id="8362b-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="8362b-123">Por ejemplo, `AddDbContext` y `AddDefaultIdentity` están comentados en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8362b-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="8362b-124">El código anterior comenta el código que está duplicado en *areas/ Identity / Identity HostingStartup.CS*</span><span class="sxs-lookup"><span data-stu-id="8362b-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="8362b-125">Normalmente, las aplicaciones que se crearon con cuentas individuales ***no*** deben crear un nuevo contexto de datos.</span><span class="sxs-lookup"><span data-stu-id="8362b-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="8362b-126">Scaffolding Identity en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="8362b-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="8362b-127">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="8362b-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="8362b-128">Scaffolding Identity en un Razor proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="8362b-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="8362b-129">está configurado en *areas/ Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="8362b-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="8362b-130">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="8362b-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="8362b-131">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="8362b-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="8362b-132">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="8362b-132">Enable authentication</span></span>

<span data-ttu-id="8362b-133">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="8362b-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="8362b-134">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="8362b-134">Layout changes</span></span>

<span data-ttu-id="8362b-135">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="8362b-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="8362b-136">Scaffolding Identity en un Razor proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="8362b-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="8362b-137">Algunas Identity opciones se configuran en *areas/ Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="8362b-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="8362b-138">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="8362b-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="8362b-139">Scaffolding Identity en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="8362b-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="8362b-140">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8362b-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="8362b-141">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="8362b-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="8362b-142">está configurado en *areas/ Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="8362b-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="8362b-143">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="8362b-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="8362b-144">Actualice la `Startup` clase con código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="8362b-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="8362b-145">Scaffolding Identity en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="8362b-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="8362b-146">Scaffolding Identity en un Blazor Server proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="8362b-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="8362b-147">está configurado en *areas/ Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="8362b-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="8362b-148">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="8362b-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="8362b-149">Migraciones</span><span class="sxs-lookup"><span data-stu-id="8362b-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="8362b-150">Pasar un token XSRF a la aplicación</span><span class="sxs-lookup"><span data-stu-id="8362b-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="8362b-151">Los tokens se pueden pasar a los componentes:</span><span class="sxs-lookup"><span data-stu-id="8362b-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="8362b-152">Cuando los tokens de autenticación se aprovisionan y se guardan en la cookie de autenticación, se pueden pasar a los componentes.</span><span class="sxs-lookup"><span data-stu-id="8362b-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="8362b-153">los componentes no pueden usar `HttpContext` directamente, por lo que no hay manera de obtener un [token de falsificación de la solicitud (XSRF)](xref:security/anti-request-forgery) para publicar el Identity punto de conexión de cierre de sesión en `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="8362b-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="8362b-154">Un token XSRF se puede pasar a los componentes.</span><span class="sxs-lookup"><span data-stu-id="8362b-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="8362b-155">Para obtener más información, vea <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="8362b-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="8362b-156">En el archivo *pages/_Host. cshtml* , establezca el token después de agregarlo a las `InitialApplicationState` `TokenProvider` clases y:</span><span class="sxs-lookup"><span data-stu-id="8362b-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="8362b-157">Actualice el `App` componente (*app. Razor*) para asignar `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="8362b-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="8362b-158">El `TokenProvider` servicio que se muestra en el tema se usa en el `LoginDisplay` componente en la siguiente sección [diseño y cambios de flujo de autenticación](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="8362b-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="8362b-159">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="8362b-159">Enable authentication</span></span>

<span data-ttu-id="8362b-160">En la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="8362b-160">In the `Startup` class:</span></span>

* <span data-ttu-id="8362b-161">Confirme que Razor se han agregado los servicios de páginas en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8362b-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="8362b-162">Si usa [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registre el servicio.</span><span class="sxs-lookup"><span data-stu-id="8362b-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="8362b-163">Llame a `UseDatabaseErrorPage` en el generador de aplicaciones en `Startup.Configure` para el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="8362b-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="8362b-164">Llame a `UseAuthentication` y `UseAuthorization` después de `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="8362b-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="8362b-165">Agregue un extremo para Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="8362b-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="8362b-166">Cambios en el flujo de la autenticación y el diseño</span><span class="sxs-lookup"><span data-stu-id="8362b-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="8362b-167">Agregue un `RedirectToLogin` componente (*RedirectToLogin. Razor*) a la carpeta *compartida* de la aplicación en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="8362b-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

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

Agregue un `LoginDisplay` componente (*LoginDisplay. Razor*) a la carpeta *compartida* de la aplicación. <span data-ttu-id="8362b-169">El [servicio TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) proporciona el token XSRF para el formulario HTML que se envía al Identity punto de conexión de cierre de sesión:</span><span class="sxs-lookup"><span data-stu-id="8362b-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="8362b-170">En el `MainLayout` componente (*Shared/MainLayout. Razor*), agregue el `LoginDisplay` componente al `<div>` contenido del elemento de la fila superior:</span><span class="sxs-lookup"><span data-stu-id="8362b-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="8362b-171">Extremos de autenticación de estilo</span><span class="sxs-lookup"><span data-stu-id="8362b-171">Style authentication endpoints</span></span>

<span data-ttu-id="8362b-172">Dado Blazor Server que utiliza páginas Razor Identity de páginas, el estilo de la interfaz de usuario cambia cuando un visitante navega entre Identity páginas y componentes.</span><span class="sxs-lookup"><span data-stu-id="8362b-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="8362b-173">Tiene dos opciones para abordar los estilos de Incongruous:</span><span class="sxs-lookup"><span data-stu-id="8362b-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="8362b-174">Componentes de compilación Identity</span><span class="sxs-lookup"><span data-stu-id="8362b-174">Build Identity components</span></span>

<span data-ttu-id="8362b-175">Un enfoque para utilizar los componentes de en Identity lugar de las páginas es crear Identity componentes de.</span><span class="sxs-lookup"><span data-stu-id="8362b-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="8362b-176">Dado `SignInManager` `UserManager` que y no se admiten en Razor los componentes, use puntos de conexión de API en la Blazor Server aplicación para procesar las acciones de la cuenta de usuario.</span><span class="sxs-lookup"><span data-stu-id="8362b-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="8362b-177">Usar un diseño personalizado con Blazor estilos de aplicación</span><span class="sxs-lookup"><span data-stu-id="8362b-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="8362b-178">El Identity diseño y los estilos de las páginas se pueden modificar para generar páginas que utilicen el Blazor tema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="8362b-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="8362b-179">El ejemplo de esta sección es simplemente un punto de partida para la personalización.</span><span class="sxs-lookup"><span data-stu-id="8362b-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="8362b-180">Es probable que se necesite trabajo adicional para la mejor experiencia de usuario.</span><span class="sxs-lookup"><span data-stu-id="8362b-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="8362b-181">Cree un nuevo `NavMenu_IdentityLayout` componente (*Shared/NavMenu_ Identity layout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="8362b-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="8362b-182">Para el marcado y el código del componente, use el mismo contenido del componente de la aplicación `NavMenu` (*Shared/NavMenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="8362b-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="8362b-183">Desseccione cualquier `NavLink` s en componentes que no se puedan alcanzar de forma anónima porque las redirecciones automáticas del `RedirectToLogin` componente no se realizan correctamente para los componentes que requieren autenticación o autorización.</span><span class="sxs-lookup"><span data-stu-id="8362b-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="8362b-184">En el archivo *pages/Shared/layout. cshtml* , realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="8362b-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="8362b-185">Agregue Razor directivas a la parte superior del archivo para usar las aplicaciones auxiliares de etiquetas y los componentes de la aplicación en la carpeta *compartida* :</span><span class="sxs-lookup"><span data-stu-id="8362b-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="8362b-186">Reemplace `{APPLICATION ASSEMBLY}` con el nombre de ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8362b-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="8362b-187">Agregue una `<base>` etiqueta y una Blazor hoja de estilos `<link>` al `<head>` contenido:</span><span class="sxs-lookup"><span data-stu-id="8362b-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="8362b-188">Cambie el contenido de la `<body>` etiqueta a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8362b-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="8362b-189">Scaffolding Identity en un Blazor Server proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="8362b-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="8362b-190">Algunas Identity opciones se configuran en *areas/ Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="8362b-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="8362b-191">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="8362b-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="8362b-192">Crear Identity origen de IU completo</span><span class="sxs-lookup"><span data-stu-id="8362b-192">Create full Identity UI source</span></span>

<span data-ttu-id="8362b-193">Para mantener el control total de la Identity interfaz de usuario, ejecute el Identity scaffolding y seleccione **invalidar todos los archivos**.</span><span class="sxs-lookup"><span data-stu-id="8362b-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="8362b-194">En el código resaltado siguiente se muestran los cambios para reemplazar la Identity interfaz de usuario predeterminada por Identity en una aplicación web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="8362b-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="8362b-195">Puede que desee hacer esto para tener un control total de la Identity interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="8362b-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="8362b-196">El valor predeterminado Identity se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8362b-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="8362b-197">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="8362b-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="8362b-198">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8362b-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="8362b-199">Configuración de contraseña</span><span class="sxs-lookup"><span data-stu-id="8362b-199">Password configuration</span></span>

<span data-ttu-id="8362b-200">Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> se configuran en `Startup.ConfigureServices` , la configuración de [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) podría ser necesaria para la `Password` propiedad en Identity las páginas con scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8362b-200">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="8362b-201">`InputModel`las `Password` propiedades se encuentran en los siguientes archivos:</span><span class="sxs-lookup"><span data-stu-id="8362b-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="8362b-202">Deshabilitar una página</span><span class="sxs-lookup"><span data-stu-id="8362b-202">Disable a page</span></span>

<span data-ttu-id="8362b-203">En esta sección se muestra cómo deshabilitar la página de registro, pero se puede usar el enfoque para deshabilitar cualquier página.</span><span class="sxs-lookup"><span data-stu-id="8362b-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="8362b-204">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="8362b-204">To disable user registration:</span></span>

* <span data-ttu-id="8362b-205">Scaffold Identity .</span><span class="sxs-lookup"><span data-stu-id="8362b-205">Scaffold Identity.</span></span> <span data-ttu-id="8362b-206">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="8362b-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="8362b-207">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8362b-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="8362b-208">Actualice *areas/ Identity /pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="8362b-208">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="8362b-209">Actualice *areas/ Identity /pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="8362b-209">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="8362b-210">Comente o quite el vínculo de registro de las *áreas/ Identity /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8362b-210">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="8362b-211">Actualice la página *áreas/ Identity /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="8362b-211">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="8362b-212">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="8362b-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="8362b-213">Quite el código de confirmación de `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="8362b-213">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="8362b-214">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="8362b-214">Use another app to add users</span></span>

<span data-ttu-id="8362b-215">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="8362b-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="8362b-216">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="8362b-216">Options to add users include:</span></span>

* <span data-ttu-id="8362b-217">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="8362b-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="8362b-218">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="8362b-218">A console app.</span></span>

<span data-ttu-id="8362b-219">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="8362b-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="8362b-220">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="8362b-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="8362b-221">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="8362b-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="8362b-222">El usuario se agrega a la Identity base de datos.</span><span class="sxs-lookup"><span data-stu-id="8362b-222">The user is added to the Identity database.</span></span>
* <span data-ttu-id="8362b-223">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="8362b-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="8362b-224">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="8362b-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="8362b-225">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="8362b-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="8362b-226">Impedir la publicación de Identity recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="8362b-226">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="8362b-227">Para evitar la publicación de recursos estáticos Identity en la raíz Web, vea <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="8362b-227">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8362b-228">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8362b-228">Additional resources</span></span>

* [<span data-ttu-id="8362b-229">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="8362b-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8362b-230">ASP.NET Core 2,1 y versiones posteriores [proporcionan Identity ASP.net Core](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="8362b-230">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="8362b-231">Las aplicaciones que incluyen Identity pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la Identity Razor biblioteca de clases (RCL).</span><span class="sxs-lookup"><span data-stu-id="8362b-231">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="8362b-232">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="8362b-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="8362b-233">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="8362b-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="8362b-234">El código generado tiene prioridad sobre el mismo código de la RCL de Identity.</span><span class="sxs-lookup"><span data-stu-id="8362b-234">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="8362b-235">Para obtener el control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un origen de la [interfaz de usuario de identidad completa](#full).</span><span class="sxs-lookup"><span data-stu-id="8362b-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="8362b-236">Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL Identity .</span><span class="sxs-lookup"><span data-stu-id="8362b-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="8362b-237">Tiene la opción de seleccionar el código de Identity que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="8362b-237">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="8362b-238">Aunque el scaffolding genera la mayor parte del código necesario, tendrá que actualizar el proyecto para completar el proceso.</span><span class="sxs-lookup"><span data-stu-id="8362b-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="8362b-239">En este documento se explican los pasos necesarios para completar una Identity actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8362b-239">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="8362b-240">Cuando Identity se ejecuta el scaffolding, se crea un archivo de *ScaffoldingReadme.txt* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="8362b-240">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="8362b-241">El archivo de *ScaffoldingReadme.txt* contiene instrucciones generales sobre lo que se necesita para completar la Identity actualización de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8362b-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="8362b-242">Este documento contiene instrucciones más completas que el archivo de *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="8362b-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="8362b-243">Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios.</span><span class="sxs-lookup"><span data-stu-id="8362b-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="8362b-244">Inspeccione los cambios después de ejecutar el Identity scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8362b-244">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="8362b-245">Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con Identity .</span><span class="sxs-lookup"><span data-stu-id="8362b-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="8362b-246">Los servicios o los códigos auxiliares de servicio no se generan al aplicar scaffolding Identity .</span><span class="sxs-lookup"><span data-stu-id="8362b-246">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="8362b-247">Los servicios para habilitar estas características deben agregarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="8362b-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="8362b-248">Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="8362b-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="8362b-249">Scaffolding Identity en un proyecto vacío</span><span class="sxs-lookup"><span data-stu-id="8362b-249">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="8362b-250">Agregue las siguientes llamadas resaltadas a la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="8362b-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="8362b-251">Scaffolding Identity en un Razor proyecto sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="8362b-251">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="8362b-252">está configurado en *areas/ Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="8362b-252"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="8362b-253">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="8362b-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="8362b-254">Migraciones, UseAuthentication y diseño</span><span class="sxs-lookup"><span data-stu-id="8362b-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="8362b-255">Enable authentication (Habilitar autenticación)</span><span class="sxs-lookup"><span data-stu-id="8362b-255">Enable authentication</span></span>

<span data-ttu-id="8362b-256">En el `Configure` método de la `Startup` clase, llame a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) después de `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="8362b-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="8362b-257">Cambios de diseño</span><span class="sxs-lookup"><span data-stu-id="8362b-257">Layout changes</span></span>

<span data-ttu-id="8362b-258">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo de diseño:</span><span class="sxs-lookup"><span data-stu-id="8362b-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="8362b-259">Scaffolding Identity en un Razor proyecto con autorización</span><span class="sxs-lookup"><span data-stu-id="8362b-259">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="8362b-260">Algunas Identity opciones se configuran en *areas/ Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="8362b-260">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="8362b-261">Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="8362b-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="8362b-262">Scaffolding Identity en un proyecto de MVC sin autorización existente</span><span class="sxs-lookup"><span data-stu-id="8362b-262">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="8362b-263">Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8362b-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="8362b-264">Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="8362b-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="8362b-265">está configurado en *areas/ Identity / Identity HostingStartup.CS*.</span><span class="sxs-lookup"><span data-stu-id="8362b-265"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="8362b-266">Para obtener más información, vea IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="8362b-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="8362b-267">Llame a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) después de `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="8362b-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="8362b-268">Scaffolding Identity en un proyecto de MVC con autorización</span><span class="sxs-lookup"><span data-stu-id="8362b-268">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="8362b-269">Elimine las *páginas/carpetas compartidas* y los archivos de esa carpeta.</span><span class="sxs-lookup"><span data-stu-id="8362b-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="8362b-270">Crear Identity origen de IU completo</span><span class="sxs-lookup"><span data-stu-id="8362b-270">Create full Identity UI source</span></span>

<span data-ttu-id="8362b-271">Para mantener el control total de la Identity interfaz de usuario, ejecute el Identity scaffolding y seleccione **invalidar todos los archivos**.</span><span class="sxs-lookup"><span data-stu-id="8362b-271">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="8362b-272">En el código resaltado siguiente se muestran los cambios para reemplazar la Identity interfaz de usuario predeterminada por Identity en una aplicación web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="8362b-272">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="8362b-273">Puede que desee hacer esto para tener un control total de la Identity interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="8362b-273">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="8362b-274">El valor predeterminado Identity se reemplaza en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8362b-274">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="8362b-275">El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="8362b-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="8362b-276">Registre una `IEmailSender` implementación, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8362b-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="8362b-277">Configuración de contraseña</span><span class="sxs-lookup"><span data-stu-id="8362b-277">Password configuration</span></span>

<span data-ttu-id="8362b-278">Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> se configuran en `Startup.ConfigureServices` , la configuración de [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) podría ser necesaria para la `Password` propiedad en Identity las páginas con scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8362b-278">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="8362b-279">`InputModel`las `Password` propiedades se encuentran en los siguientes archivos:</span><span class="sxs-lookup"><span data-stu-id="8362b-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="8362b-280">Deshabilitar registro (página)</span><span class="sxs-lookup"><span data-stu-id="8362b-280">Disable register page</span></span>

<span data-ttu-id="8362b-281">Para deshabilitar el registro de usuario:</span><span class="sxs-lookup"><span data-stu-id="8362b-281">To disable user registration:</span></span>

* <span data-ttu-id="8362b-282">Scaffold Identity .</span><span class="sxs-lookup"><span data-stu-id="8362b-282">Scaffold Identity.</span></span> <span data-ttu-id="8362b-283">Incluya account. Register, Account. login y account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="8362b-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="8362b-284">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8362b-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="8362b-285">Actualice *areas/ Identity /pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="8362b-285">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="8362b-286">Actualice *areas/ Identity /pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:</span><span class="sxs-lookup"><span data-stu-id="8362b-286">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="8362b-287">Comente o quite el vínculo de registro de las *áreas/ Identity /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8362b-287">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="8362b-288">Actualice la página *áreas/ Identity /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="8362b-288">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="8362b-289">Quite el código y los vínculos del archivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="8362b-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="8362b-290">Quite el código de confirmación de `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="8362b-290">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="8362b-291">Usar otra aplicación para agregar usuarios</span><span class="sxs-lookup"><span data-stu-id="8362b-291">Use another app to add users</span></span>

<span data-ttu-id="8362b-292">Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="8362b-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="8362b-293">Entre las opciones para agregar usuarios se incluyen:</span><span class="sxs-lookup"><span data-stu-id="8362b-293">Options to add users include:</span></span>

* <span data-ttu-id="8362b-294">Una aplicación Web de administración dedicada.</span><span class="sxs-lookup"><span data-stu-id="8362b-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="8362b-295">Una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="8362b-295">A console app.</span></span>

<span data-ttu-id="8362b-296">En el código siguiente se describe un enfoque para agregar usuarios:</span><span class="sxs-lookup"><span data-stu-id="8362b-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="8362b-297">Una lista de usuarios se lee en la memoria.</span><span class="sxs-lookup"><span data-stu-id="8362b-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="8362b-298">Se genera una contraseña única segura para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="8362b-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="8362b-299">El usuario se agrega a la Identity base de datos.</span><span class="sxs-lookup"><span data-stu-id="8362b-299">The user is added to the Identity database.</span></span>
* <span data-ttu-id="8362b-300">Se notifica al usuario y se le indica que cambie la contraseña.</span><span class="sxs-lookup"><span data-stu-id="8362b-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="8362b-301">En el código siguiente se describe cómo agregar un usuario:</span><span class="sxs-lookup"><span data-stu-id="8362b-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="8362b-302">Se puede seguir un enfoque similar para escenarios de producción.</span><span class="sxs-lookup"><span data-stu-id="8362b-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8362b-303">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8362b-303">Additional resources</span></span>

* [<span data-ttu-id="8362b-304">Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="8362b-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
