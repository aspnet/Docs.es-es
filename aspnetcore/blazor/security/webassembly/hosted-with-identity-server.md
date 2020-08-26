---
title: Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Identity Server
author: guardrex
description: Para crear en Visual Studio una solución hospedada Blazor con autenticación que usa un back-end de [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: ef5e9e1becb511ef383b22fc96441b0f61537354
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626225"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="5fb11-103">Protección de una aplicación hospedada Blazor WebAssembly de ASP.NET Core con Identity Server</span><span class="sxs-lookup"><span data-stu-id="5fb11-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="5fb11-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5fb11-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5fb11-105">En este artículo se explica cómo crear una [aplicación Blazor WebAssembly hospedada](xref:blazor/hosting-models#blazor-webassembly) que usa [IdentityServer](https://identityserver.io/) para autenticar usuarios y llamadas API.</span><span class="sxs-lookup"><span data-stu-id="5fb11-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="5fb11-106">Para configurar una aplicación Blazor WebAssembly independiente u hospedada para que use una instancia de Identity Server externa existente, siga las instrucciones de <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="5fb11-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5fb11-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5fb11-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5fb11-108">Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="5fb11-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="5fb11-109">Después de elegir la plantilla de aplicación de **Blazor WebAssembly** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **Cambiar** en **Autenticación**.</span><span class="sxs-lookup"><span data-stu-id="5fb11-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="5fb11-110">Seleccione **Cuentas de usuario individuales** con la opción **Almacenar cuentas de usuario en aplicación** para almacenar usuarios dentro de la aplicación mediante el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5fb11-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="5fb11-111">Active la casilla **ASP.NET Core hosted** (Hospedada en ASP.NET Core) en la sección **Avanzado**.</span><span class="sxs-lookup"><span data-stu-id="5fb11-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="5fb11-112">Visual Studio Code y CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="5fb11-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="5fb11-113">Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación en una carpeta vacía, especifique el mecanismo de autenticación `Individual` con la opción `-au|--auth` para almacenar los usuarios dentro de la aplicación con el sistema [Identity](xref:security/authentication/identity) de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5fb11-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="5fb11-114">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="5fb11-114">Placeholder</span></span>  | <span data-ttu-id="5fb11-115">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="5fb11-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="5fb11-116">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5fb11-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="5fb11-117">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5fb11-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5fb11-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5fb11-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5fb11-119">Para crear un nuevo proyecto de Blazor WebAssembly con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="5fb11-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="5fb11-120">En el paso **Configure your new Blazor WebAssembly App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="5fb11-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="5fb11-121">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core [Identity](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="5fb11-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="5fb11-122">Active la casilla **ASP.NET Core hosted** (Hospedada en ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="5fb11-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="5fb11-123">Configuración de la aplicación de servidor</span><span class="sxs-lookup"><span data-stu-id="5fb11-123">Server app configuration</span></span>

<span data-ttu-id="5fb11-124">En las siguientes secciones se describen algunas adiciones al proyecto cuando se incluye compatibilidad con la autenticación.</span><span class="sxs-lookup"><span data-stu-id="5fb11-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="5fb11-125">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="5fb11-125">Startup class</span></span>

<span data-ttu-id="5fb11-126">La clase `Startup` tiene las siguientes adiciones.</span><span class="sxs-lookup"><span data-stu-id="5fb11-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="5fb11-127">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5fb11-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="5fb11-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="5fb11-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="5fb11-129">IdentityServer con un método auxiliar <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> adicional que configura convenciones de ASP.NET Core predeterminadas por encima de IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="5fb11-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="5fb11-130">Autenticación con un método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="5fb11-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="5fb11-131">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5fb11-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="5fb11-132">El middleware IdentityServer expone los puntos de conexión de OpenID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="5fb11-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="5fb11-133">El middleware Authentication es responsable de validar las credenciales de solicitud y de configurar el usuario en el contexto de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="5fb11-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="5fb11-134">El middleware Authorization habilita capacidades de autorización:</span><span class="sxs-lookup"><span data-stu-id="5fb11-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="5fb11-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="5fb11-135">AddApiAuthorization</span></span>

<span data-ttu-id="5fb11-136">El método auxiliar <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> configura [IdentityServer](https://identityserver.io/) en escenarios de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5fb11-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="5fb11-137">IdentityServer es un marco eficaz y extensible que sirve para abordar los problemas de seguridad de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="5fb11-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="5fb11-138">IdentityServer expone las complejidades innecesarias en los escenarios más comunes.</span><span class="sxs-lookup"><span data-stu-id="5fb11-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="5fb11-139">En consecuencia, se proporciona un conjunto de convenciones y opciones de configuración que consideramos un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="5fb11-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="5fb11-140">Cuando sus necesidades de autenticación cambien, tendrá a su disposición toda la eficacia de IdentityServer para personalizar la autenticación para adaptarse a los requisitos de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5fb11-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="5fb11-141">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="5fb11-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="5fb11-142">El método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="5fb11-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="5fb11-143">La directiva está configurada para permitir que Identity controle todas las solicitudes enrutadas a cualquier subruta en el espacio de dirección URL de Identity `/Identity`.</span><span class="sxs-lookup"><span data-stu-id="5fb11-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="5fb11-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> se encarga de todas las demás solicitudes.</span><span class="sxs-lookup"><span data-stu-id="5fb11-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="5fb11-145">Este método también hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5fb11-145">Additionally, this method:</span></span>

* <span data-ttu-id="5fb11-146">Registra un recurso de API `{APPLICATION NAME}API` con IdentityServer con un ámbito predeterminado de `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="5fb11-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="5fb11-147">Configura el middleware de token de portador de JWT para validar los tokens emitidos por IdentityServer para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5fb11-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="5fb11-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="5fb11-148">WeatherForecastController</span></span>

<span data-ttu-id="5fb11-149">En `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) se aplica a la clase.</span><span class="sxs-lookup"><span data-stu-id="5fb11-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="5fb11-150">Este atributo señala que, para acceder al recurso, el usuario debe estar autorizado en función de la directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5fb11-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="5fb11-151">La directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configura.</span><span class="sxs-lookup"><span data-stu-id="5fb11-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="5fb11-152">El método auxiliar configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> como el controlador predeterminado de las solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5fb11-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="5fb11-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="5fb11-153">ApplicationDbContext</span></span>

<span data-ttu-id="5fb11-154">En `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extiende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> para incluir el esquema de IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="5fb11-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="5fb11-155">La clase <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se deriva de la clase <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="5fb11-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="5fb11-156">Para obtener el control total del esquema de la base de datos, herede de una de las clases <xref:Microsoft.EntityFrameworkCore.DbContext> de Identity disponibles y configure el contexto para incluir el esquema de Identity llamando a `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el método <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="5fb11-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="5fb11-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="5fb11-157">OidcConfigurationController</span></span>

<span data-ttu-id="5fb11-158">En `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), el punto de conexión del cliente se aprovisiona para proporcionar parámetros de OIDC.</span><span class="sxs-lookup"><span data-stu-id="5fb11-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="5fb11-159">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="5fb11-159">App settings</span></span>

<span data-ttu-id="5fb11-160">En el archivo de configuración de la aplicación (`appsettings.json`), en la raíz del proyecto, la sección `IdentityServer` describe la lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="5fb11-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="5fb11-161">En el siguiente ejemplo hay un solo cliente,</span><span class="sxs-lookup"><span data-stu-id="5fb11-161">In the following example, there's a single client.</span></span> <span data-ttu-id="5fb11-162">cuyo nombre corresponde al nombre de la aplicación y se asigna por convención al parámetro `ClientId` de OAuth.</span><span class="sxs-lookup"><span data-stu-id="5fb11-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="5fb11-163">El perfil señala el tipo de aplicación que se está configurando.</span><span class="sxs-lookup"><span data-stu-id="5fb11-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="5fb11-164">El perfil se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor.</span><span class="sxs-lookup"><span data-stu-id="5fb11-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="5fb11-165">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="5fb11-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="5fb11-166">Configuración de la aplicación cliente</span><span class="sxs-lookup"><span data-stu-id="5fb11-166">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5fb11-167">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="5fb11-167">Authentication package</span></span>

<span data-ttu-id="5fb11-168">Cuando una aplicación se crea para usar cuentas de usuario individuales (`Individual`), dicha aplicación recibe automáticamente una referencia de paquete del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5fb11-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="5fb11-169">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="5fb11-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5fb11-170">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="5fb11-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="5fb11-171">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="5fb11-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="5fb11-172">Configuración de `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="5fb11-172">`HttpClient` configuration</span></span>

<span data-ttu-id="5fb11-173">En `Program.Main` (`Program.cs`), hay configurada una instancia de <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) con nombre para proporcionar instancias de <xref:System.Net.Http.HttpClient> que incluyen tokens de acceso al realizar solicitudes a la API de servidor:</span><span class="sxs-lookup"><span data-stu-id="5fb11-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="5fb11-174">Si va a configurar una aplicación Blazor WebAssembly para usar una instancia de Identity Server existente que no forma parte de una solución hospedada por Blazor, cambie el registro de la dirección base <xref:System.Net.Http.HttpClient> de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) a la dirección URL del punto de conexión de autorización de API de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="5fb11-174">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="5fb11-175">Compatibilidad con autorización de API</span><span class="sxs-lookup"><span data-stu-id="5fb11-175">API authorization support</span></span>

<span data-ttu-id="5fb11-176">La compatibilidad para autenticar usuarios se incluye en el contenedor de servicios con el método de extensión proporcionado dentro del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="5fb11-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="5fb11-177">Este método configura los servicios necesarios para que la aplicación interactúe con el sistema de autorización existente.</span><span class="sxs-lookup"><span data-stu-id="5fb11-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="5fb11-178">La configuración de la aplicación se carga por convención de forma predeterminada desde `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="5fb11-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="5fb11-179">Por convención, el identificador de cliente se establece en el nombre de ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5fb11-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="5fb11-180">Esta dirección URL se puede cambiar para que apunte a un punto de conexión aparte llamando a la sobrecarga con opciones.</span><span class="sxs-lookup"><span data-stu-id="5fb11-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="5fb11-181">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="5fb11-181">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="5fb11-182">Página de índice</span><span class="sxs-lookup"><span data-stu-id="5fb11-182">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="5fb11-183">Componente App</span><span class="sxs-lookup"><span data-stu-id="5fb11-183">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="5fb11-184">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="5fb11-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="5fb11-185">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="5fb11-185">LoginDisplay component</span></span>

<span data-ttu-id="5fb11-186">El componente `LoginDisplay` (`Shared/LoginDisplay.razor`) se representa en el componente `MainLayout` (`Shared/MainLayout.razor`) y administra los siguientes comportamientos:</span><span class="sxs-lookup"><span data-stu-id="5fb11-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="5fb11-187">En el caso de los usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="5fb11-187">For authenticated users:</span></span>
  * <span data-ttu-id="5fb11-188">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="5fb11-188">Displays the current user name.</span></span>
  * <span data-ttu-id="5fb11-189">Proporciona un vínculo a la página de perfil de usuario en ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="5fb11-189">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="5fb11-190">Proporciona un botón para cerrar la sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5fb11-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="5fb11-191">En el caso de los usuarios anónimos:</span><span class="sxs-lookup"><span data-stu-id="5fb11-191">For anonymous users:</span></span>
  * <span data-ttu-id="5fb11-192">Ofrece la opción de registrarse.</span><span class="sxs-lookup"><span data-stu-id="5fb11-192">Offers the option to register.</span></span>
  * <span data-ttu-id="5fb11-193">Ofrece la opción de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="5fb11-193">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="5fb11-194">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="5fb11-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="5fb11-195">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="5fb11-195">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="5fb11-196">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="5fb11-196">Run the app</span></span>

<span data-ttu-id="5fb11-197">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="5fb11-197">Run the app from the Server project.</span></span> <span data-ttu-id="5fb11-198">Al usar Visual Studio, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5fb11-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="5fb11-199">Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="5fb11-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="5fb11-200">Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="5fb11-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="5fb11-201">Notificaciones name y role con autorización de API</span><span class="sxs-lookup"><span data-stu-id="5fb11-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="5fb11-202">Fábrica de usuario personalizada</span><span class="sxs-lookup"><span data-stu-id="5fb11-202">Custom user factory</span></span>

<span data-ttu-id="5fb11-203">En la aplicación cliente, cree una fábrica de usuario personalizada.</span><span class="sxs-lookup"><span data-stu-id="5fb11-203">In the Client app, create a custom user factory.</span></span> <span data-ttu-id="5fb11-204">Identity Server envía varios roles como una matriz JSON en una sola notificación `role`.</span><span class="sxs-lookup"><span data-stu-id="5fb11-204">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="5fb11-205">Se envía un único rol como un valor de cadena en la notificación.</span><span class="sxs-lookup"><span data-stu-id="5fb11-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="5fb11-206">La fábrica crea una notificación `role` individual por cada uno de los roles del usuario.</span><span class="sxs-lookup"><span data-stu-id="5fb11-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="5fb11-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="5fb11-207">`CustomUserFactory.cs`:</span></span>

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

<span data-ttu-id="5fb11-208">En la aplicación cliente, registre la fábrica en `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="5fb11-208">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="5fb11-209">En la aplicación de servidor, llame a <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> en el generador de Identity, que agrega servicios relativos a roles:</span><span class="sxs-lookup"><span data-stu-id="5fb11-209">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="5fb11-210">Configuración de Identity Server</span><span class="sxs-lookup"><span data-stu-id="5fb11-210">Configure Identity Server</span></span>

<span data-ttu-id="5fb11-211">Siga **uno** de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="5fb11-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="5fb11-212">Opciones de autorización de API</span><span class="sxs-lookup"><span data-stu-id="5fb11-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="5fb11-213">Servicio de perfil</span><span class="sxs-lookup"><span data-stu-id="5fb11-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="5fb11-214">Opciones de autorización de API</span><span class="sxs-lookup"><span data-stu-id="5fb11-214">API authorization options</span></span>

<span data-ttu-id="5fb11-215">En la aplicación de servidor:</span><span class="sxs-lookup"><span data-stu-id="5fb11-215">In the Server app:</span></span>

* <span data-ttu-id="5fb11-216">Configure Identity Server para colocar las notificaciones `name` y `role` en el token de identificador y el token de acceso.</span><span class="sxs-lookup"><span data-stu-id="5fb11-216">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="5fb11-217">Evite la asignación predeterminada de roles en el controlador de token JWT.</span><span class="sxs-lookup"><span data-stu-id="5fb11-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="5fb11-218">Servicio de perfil</span><span class="sxs-lookup"><span data-stu-id="5fb11-218">Profile Service</span></span>

<span data-ttu-id="5fb11-219">En la aplicación de servidor, cree una implementación `ProfileService`.</span><span class="sxs-lookup"><span data-stu-id="5fb11-219">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="5fb11-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="5fb11-220">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="5fb11-221">En la aplicación de servidor, registre el servicio de perfil en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5fb11-221">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="5fb11-222">Uso de mecanismos de autorización</span><span class="sxs-lookup"><span data-stu-id="5fb11-222">Use authorization mechanisms</span></span>

<span data-ttu-id="5fb11-223">En la aplicación cliente, los métodos de autorización de componentes son funcionales en este momento.</span><span class="sxs-lookup"><span data-stu-id="5fb11-223">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="5fb11-224">Cualquiera de los mecanismos de autorización de los componentes puede usar un rol para autorizar al usuario:</span><span class="sxs-lookup"><span data-stu-id="5fb11-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="5fb11-225">[Componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="5fb11-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="5fb11-226">[Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (ejemplo: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="5fb11-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="5fb11-227">[Lógica de procedimientos](xref:blazor/security/index#procedural-logic) (ejemplo: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="5fb11-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="5fb11-228">Se admiten pruebas de rol múltiple:</span><span class="sxs-lookup"><span data-stu-id="5fb11-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="5fb11-229">`User.Identity.Name` se rellena en la aplicación cliente con el nombre de usuario del usuario, que suele ser su dirección de correo electrónico de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="5fb11-229">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5fb11-230">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5fb11-230">Additional resources</span></span>

* [<span data-ttu-id="5fb11-231">Implementación en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5fb11-231">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="5fb11-232">Importación de un certificado de Key Vault (documentación de Azure)</span><span class="sxs-lookup"><span data-stu-id="5fb11-232">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="5fb11-233">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="5fb11-233">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
