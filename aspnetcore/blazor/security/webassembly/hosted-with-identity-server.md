---
title: Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Identity Server
author: guardrex
description: Crear en Visual Studio una aplicación hospedada Blazor con autenticación que usa un back-end de [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: cce6b6b1ec144e362415fe34645aef567269c873
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402213"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="75255-103">Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Identity Server</span><span class="sxs-lookup"><span data-stu-id="75255-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="75255-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="75255-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="75255-105">En este artículo se explica cómo crear una aplicación hospedada Blazor que usa [IdentityServer](https://identityserver.io/) para autenticar usuarios y llamadas API.</span><span class="sxs-lookup"><span data-stu-id="75255-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75255-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75255-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="75255-107">En Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="75255-107">In Visual Studio:</span></span>

1. <span data-ttu-id="75255-108">Cree una aplicación **Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="75255-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="75255-109">Para obtener más información, vea <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="75255-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="75255-110">En el cuadro de diálogo **Crear una aplicación de Blazor** , seleccione **Cambiar** en la sección **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="75255-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="75255-111">Seleccione **Cuentas de usuario individuales** y, después, **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="75255-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="75255-112">Active la casilla **ASP.NET Core hosted** (Hospedada en ASP.NET Core) en la sección **Avanzado**.</span><span class="sxs-lookup"><span data-stu-id="75255-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="75255-113">Seleccione el botón **Crear**.</span><span class="sxs-lookup"><span data-stu-id="75255-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="75255-114">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="75255-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="75255-115">Para crear una aplicación usando un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="75255-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="75255-116">Para especificar la ubicación de salida, lo que crea una carpeta de proyecto si no existe, incluya la opción de salida en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="75255-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="75255-117">El nombre de la carpeta también pasa a formar parte del nombre del proyecto.</span><span class="sxs-lookup"><span data-stu-id="75255-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="75255-118">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="75255-118">Server app configuration</span></span>

<span data-ttu-id="75255-119">En las siguientes secciones se describen algunas adiciones al proyecto cuando se incluye compatibilidad con la autenticación.</span><span class="sxs-lookup"><span data-stu-id="75255-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="75255-120">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="75255-120">Startup class</span></span>

<span data-ttu-id="75255-121">La clase `Startup` tiene las siguientes adiciones.</span><span class="sxs-lookup"><span data-stu-id="75255-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="75255-122">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="75255-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="75255-123">Identity de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="75255-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="75255-124">IdentityServer con un método auxiliar <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> adicional que configura convenciones de ASP.NET Core predeterminadas por encima de IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="75255-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="75255-125">Autenticación con un método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="75255-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="75255-126">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="75255-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="75255-127">El middleware IdentityServer expone los puntos de conexión de Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="75255-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="75255-128">El middleware Authentication es responsable de validar las credenciales de solicitud y de configurar el usuario en el contexto de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="75255-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="75255-129">El middleware Authorization habilita capacidades de autorización:</span><span class="sxs-lookup"><span data-stu-id="75255-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="75255-130">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="75255-130">AddApiAuthorization</span></span>

<span data-ttu-id="75255-131">El método auxiliar <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> configura [IdentityServer](https://identityserver.io/) en escenarios de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75255-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="75255-132">IdentityServer es un marco eficaz y extensible que sirve para abordar los problemas de seguridad de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="75255-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="75255-133">IdentityServer expone las complejidades innecesarias en los escenarios más comunes.</span><span class="sxs-lookup"><span data-stu-id="75255-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="75255-134">En consecuencia, se proporciona un conjunto de convenciones y opciones de configuración que consideramos un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="75255-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="75255-135">Cuando sus necesidades de autenticación cambien, tendrá a su disposición toda la eficacia de IdentityServer para personalizar la autenticación para adaptarse a los requisitos de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="75255-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="75255-136">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="75255-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="75255-137">El método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="75255-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="75255-138">La directiva está configurada para permitir que Identity controle todas las solicitudes enrutadas a cualquier subruta en el espacio de dirección URL de Identity `/Identity`.</span><span class="sxs-lookup"><span data-stu-id="75255-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="75255-139"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> se encarga de todas las demás solicitudes.</span><span class="sxs-lookup"><span data-stu-id="75255-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="75255-140">Este método también hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="75255-140">Additionally, this method:</span></span>

* <span data-ttu-id="75255-141">Registra un recurso de API `{APPLICATION NAME}API` con IdentityServer con un ámbito predeterminado de `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="75255-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="75255-142">Configura el middleware de token de portador de JWT para validar los tokens emitidos por IdentityServer para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="75255-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="75255-143">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="75255-143">WeatherForecastController</span></span>

<span data-ttu-id="75255-144">En `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) se aplica a la clase.</span><span class="sxs-lookup"><span data-stu-id="75255-144">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="75255-145">Este atributo señala que, para acceder al recurso, el usuario debe estar autorizado en función de la directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="75255-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="75255-146">La directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configura.</span><span class="sxs-lookup"><span data-stu-id="75255-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="75255-147">El método auxiliar configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> como el controlador predeterminado de las solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="75255-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="75255-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="75255-148">ApplicationDbContext</span></span>

<span data-ttu-id="75255-149">En `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extiende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> para incluir el esquema de IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="75255-149">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="75255-150">La clase <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se deriva de la clase <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="75255-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="75255-151">Para obtener el control total del esquema de la base de datos, herede de una de las clases <xref:Microsoft.EntityFrameworkCore.DbContext> de Identity disponibles y configure el contexto para incluir el esquema de Identity llamando a `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el método <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="75255-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="75255-152">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="75255-152">OidcConfigurationController</span></span>

<span data-ttu-id="75255-153">En `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), el punto de conexión del cliente se aprovisiona para proporcionar parámetros de OIDC.</span><span class="sxs-lookup"><span data-stu-id="75255-153">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="75255-154">Archivo de configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="75255-154">App settings files</span></span>

<span data-ttu-id="75255-155">En el archivo de configuración de la aplicación (`appsettings.json`), en la raíz del proyecto, la sección `IdentityServer` describe la lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="75255-155">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="75255-156">En el siguiente ejemplo hay un solo cliente,</span><span class="sxs-lookup"><span data-stu-id="75255-156">In the following example, there's a single client.</span></span> <span data-ttu-id="75255-157">cuyo nombre corresponde al nombre de la aplicación y se asigna por convención al parámetro `ClientId` de OAuth.</span><span class="sxs-lookup"><span data-stu-id="75255-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="75255-158">El perfil señala el tipo de aplicación que se está configurando.</span><span class="sxs-lookup"><span data-stu-id="75255-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="75255-159">El perfil se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor.</span><span class="sxs-lookup"><span data-stu-id="75255-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="75255-160">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="75255-160">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="75255-161">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="75255-161">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="75255-162">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="75255-162">Authentication package</span></span>

<span data-ttu-id="75255-163">Cuando una aplicación se crea para usar cuentas de usuario individuales (`Individual`), dicha aplicación recibe automáticamente una referencia de paquete del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="75255-163">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="75255-164">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="75255-164">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="75255-165">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="75255-165">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="75255-166">Compatibilidad con autorización de API</span><span class="sxs-lookup"><span data-stu-id="75255-166">API authorization support</span></span>

<span data-ttu-id="75255-167">La compatibilidad para autenticar usuarios se incluye en el contenedor de servicios con el método de extensión proporcionado dentro del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/).</span><span class="sxs-lookup"><span data-stu-id="75255-167">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="75255-168">Este método configura los servicios necesarios para que la aplicación interactúe con el sistema de autorización existente.</span><span class="sxs-lookup"><span data-stu-id="75255-168">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="75255-169">La configuración de la aplicación se carga por convención de forma predeterminada desde `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="75255-169">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="75255-170">Por convención, el identificador de cliente se establece en el nombre de ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="75255-170">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="75255-171">Esta dirección URL se puede cambiar para que apunte a un punto de conexión aparte llamando a la sobrecarga con opciones.</span><span class="sxs-lookup"><span data-stu-id="75255-171">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="75255-172">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="75255-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="75255-173">Página de índice</span><span class="sxs-lookup"><span data-stu-id="75255-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="75255-174">Componente App</span><span class="sxs-lookup"><span data-stu-id="75255-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="75255-175">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="75255-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="75255-176">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="75255-176">LoginDisplay component</span></span>

<span data-ttu-id="75255-177">El componente `LoginDisplay` (`Shared/LoginDisplay.razor`) se representa en el componente `MainLayout` (`Shared/MainLayout.razor`) y administra los siguientes comportamientos:</span><span class="sxs-lookup"><span data-stu-id="75255-177">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="75255-178">En el caso de los usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="75255-178">For authenticated users:</span></span>
  * <span data-ttu-id="75255-179">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="75255-179">Displays the current user name.</span></span>
  * <span data-ttu-id="75255-180">Proporciona un vínculo a la página de perfil de usuario en Identity de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75255-180">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="75255-181">Proporciona un botón para cerrar la sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="75255-181">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="75255-182">En el caso de los usuarios anónimos:</span><span class="sxs-lookup"><span data-stu-id="75255-182">For anonymous users:</span></span>
  * <span data-ttu-id="75255-183">Ofrece la opción de registrarse.</span><span class="sxs-lookup"><span data-stu-id="75255-183">Offers the option to register.</span></span>
  * <span data-ttu-id="75255-184">Ofrece la opción de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="75255-184">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="75255-185">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="75255-185">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="75255-186">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="75255-186">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="75255-187">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="75255-187">Run the app</span></span>

<span data-ttu-id="75255-188">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="75255-188">Run the app from the Server project.</span></span> <span data-ttu-id="75255-189">Al usar Visual Studio, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="75255-189">When using Visual Studio, either:</span></span>

* <span data-ttu-id="75255-190">Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="75255-190">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="75255-191">Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="75255-191">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="75255-192">Notificaciones name y role con autorización de API</span><span class="sxs-lookup"><span data-stu-id="75255-192">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="75255-193">Fábrica de usuario personalizada</span><span class="sxs-lookup"><span data-stu-id="75255-193">Custom user factory</span></span>

<span data-ttu-id="75255-194">En la aplicación cliente, cree una fábrica de usuario personalizada.</span><span class="sxs-lookup"><span data-stu-id="75255-194">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="75255-195"> Server envía varios roles como una matriz JSON en una sola notificación `role`.</span><span class="sxs-lookup"><span data-stu-id="75255-195"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="75255-196">Se envía un único rol como un valor de cadena en la notificación.</span><span class="sxs-lookup"><span data-stu-id="75255-196">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="75255-197">La fábrica crea una notificación `role` individual por cada uno de los roles del usuario.</span><span class="sxs-lookup"><span data-stu-id="75255-197">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="75255-198">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="75255-198">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="75255-199">En la aplicación cliente, registre la fábrica en `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="75255-199">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="75255-200">En la aplicación de servidor, llame a <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> en el generador de Identity, que agrega servicios relativos a roles:</span><span class="sxs-lookup"><span data-stu-id="75255-200">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="75255-201">Configuración de Identity Server</span><span class="sxs-lookup"><span data-stu-id="75255-201">Configure Identity Server</span></span>

<span data-ttu-id="75255-202">Siga **uno** de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="75255-202">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="75255-203">Opciones de autorización de API</span><span class="sxs-lookup"><span data-stu-id="75255-203">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="75255-204">Servicio de perfil</span><span class="sxs-lookup"><span data-stu-id="75255-204">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="75255-205">Opciones de autorización de API</span><span class="sxs-lookup"><span data-stu-id="75255-205">API authorization options</span></span>

<span data-ttu-id="75255-206">En la aplicación de servidor:</span><span class="sxs-lookup"><span data-stu-id="75255-206">In the Server app:</span></span>

* <span data-ttu-id="75255-207">Configure Identity Server para colocar las notificaciones `name` y `role` en el token de identificador y el token de acceso.</span><span class="sxs-lookup"><span data-stu-id="75255-207">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="75255-208">Evite la asignación predeterminada de roles en el controlador de token JWT.</span><span class="sxs-lookup"><span data-stu-id="75255-208">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="75255-209">Servicio de perfil</span><span class="sxs-lookup"><span data-stu-id="75255-209">Profile Service</span></span>

<span data-ttu-id="75255-210">En la aplicación de servidor, cree una implementación `ProfileService`.</span><span class="sxs-lookup"><span data-stu-id="75255-210">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="75255-211">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="75255-211">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="75255-212">En la aplicación de servidor, registre el servicio de perfil en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="75255-212">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="75255-213">Uso de mecanismos de autorización</span><span class="sxs-lookup"><span data-stu-id="75255-213">Use authorization mechanisms</span></span>

<span data-ttu-id="75255-214">En la aplicación cliente, los métodos de autorización de componentes son funcionales en este momento.</span><span class="sxs-lookup"><span data-stu-id="75255-214">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="75255-215">Cualquiera de los mecanismos de autorización de los componentes puede usar un rol para autorizar al usuario:</span><span class="sxs-lookup"><span data-stu-id="75255-215">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="75255-216">[Componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="75255-216">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="75255-217">[Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (ejemplo: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="75255-217">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="75255-218">[Lógica de procedimientos](xref:blazor/security/index#procedural-logic) (ejemplo: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="75255-218">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="75255-219">Se admiten pruebas de rol múltiple:</span><span class="sxs-lookup"><span data-stu-id="75255-219">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="75255-220">`User.Identity.Name` se rellena en la aplicación cliente con el nombre de usuario del usuario, que suele ser su dirección de correo electrónico de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="75255-220">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="75255-221">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="75255-221">Additional resources</span></span>

* [<span data-ttu-id="75255-222">Implementación en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="75255-222">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="75255-223">Importación de un certificado de Key Vault (documentación de Azure)</span><span class="sxs-lookup"><span data-stu-id="75255-223">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="75255-224">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="75255-224">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
